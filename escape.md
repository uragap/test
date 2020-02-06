### Click to copy

Click to copy is automatically enabled for all code blocks.



### Special Case: Templates -  &#123;&#123;}}

If you need to include templates in your code samples, be sure to escape them.

For example:
<pre class="prettyprint">
&lt;pre class="prettyprint">
&amp;lt;polymer-media-query query="max-width:640px" queryMatches="&amp;#123;{isPhone}}">
&lt;/pre>
</pre>

If it's inline, you'll need to wrap it in a `<code>` block instead of backticks.
<pre class="prettyprint">
* Declarative two-way data-binding: &lt;code>&lt;input id="input" value="&amp;#123;{foo}}">&lt;/code>
</pre>

## Images

When adding a caption, wrap images in `<figure>` blocks, and ideally, use
responsive images with the `scrset` attribute when possible. Be sure you
include `alt` attributes for your images as well.

<figure>
  <img src="https://placehold.it/350x150" alt="sample image">
  <figcaption>This caption should be used to describe the image.</figcaption>
</figure>

For example:

    <figure>
      <img src="https://placehold.it/350x150" alt="sample image">
      <figcaption>This caption should be used to describe the image.</figcaption>
    </figure>

Note: Optimized images are served automatically only for `index.yaml` pages, 
simply provide a 2x version, and the server will do the rest.

You can apply `class="screenshot"` to an image to give it a border that
offsets it from nearby text. This is typically used for screenshots that have
white backgrounds and otherwise get lost on the page. Don't use it for images
that don't need it.
