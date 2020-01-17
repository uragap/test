## Building your app shell {: #building-your-app-shell }

Structure your app for a clear distinction between the page shell and the
dynamic content. In general, your app should load the simplest shell possible
but include enough meaningful page content with the initial download. Determine
the right balance between speed and data freshness for each of your data
sources.

<figure>
  <img src="images/wikipedia.jpg"
    alt="Offline Wikipedia app using an application shell with content caching">
  <figcaption>Jake Archibald’s <a href="https://wiki-offline.jakearchibald.com/wiki/Rick_and_Morty">offline Wikipedia application</a> is a good example of a PWA that uses an app shell model. It loads instantly on repeat visits, but dynamically fetches content using JS. This content is then cached offline for future visits.
</figcaption>
</figure>

### Example HTML for an app shell {: #example-html-for-appshell }

This example separates the core application infrastructure and UI from the data.
It is important to keep the initial load as simple as possible to display just
the page’s layout as soon as the web app is opened. Some of it comes from your
application’s index file (inline DOM, styles) and the rest is loaded from
external scripts and stylesheets.

All of the UI and infrastructure is cached locally using a service worker so
that on subsequent loads, only new or changed data is retrieved, instead of
having to load everything.

Your `index.html` file in your work directory should look something like the
following code. This is a subset of the actual contents and is not a complete
index file. Let's look at what it contains.

* HTML and CSS for the "skeleton" of your user interface complete with navigation
  and content placeholders.
* An external JavaScript file (app.js) for handling navigation and UI logic as
  well as the code to display posts retrieved from the server and store them
  locally using a storage mechanism like IndexedDB.
* A web app manifest and service worker loader to enable off-line capabilities.

<div class="clearfix"></div>

    <!DOCTYPE html>
    <html>
    <head>
      <meta charset="utf-8">
      <title>App Shell</title>
      <link rel="manifest" href="/manifest.json">
      <meta http-equiv="X-UA-Compatible" content="IE=edge">
      <meta name="viewport" content="width=device-width, initial-scale=1.0">
      <link rel="stylesheet" type="text/css" href="styles/inline.css">
    </head>

    <body>
      <header class="header">
        <h1 class="header__title">App Shell</h1>
      </header>

      <nav class="nav">
      ...
      </nav>

      <main class="main">
      ...
      </main>

      <div class="dialog-container">
      ...
      </div>

      <div class="loader">
        <!-- Show a spinner or placeholders for content -->
      </div>

      <script src="app.js" async></script>
      <script>
      if ('serviceWorker' in navigator) {
        navigator.serviceWorker.register('/sw.js').then(function(registration) {
          // Registration was successful
          console.log('ServiceWorker registration successful with scope: ', registration.scope);
        }).catch(function(err) {
          // registration failed :(
          console.log('ServiceWorker registration failed: ', err);
        });
      }
      </script>
    </body>
    </html>

<div class="clearfix"></div>


Note: See [Your First Progressive Web App](/web/fundamentals/codelabs/your-first-pwapp/) for more on using an application shell and server-side
rendering for content. An app shell can be implemented using any library or
framework as covered in our <a
href="https://www.youtube.com/watch?v=srdKq0DckXQ">Progressive Web Apps across
all frameworks</a> talk. Samples are available using Polymer (<a
href="https://shop.polymer-project.org">Shop</a>) and React (<a
href="https://github.com/insin/react-hn">ReactHN</a>,
<a
href="https://github.com/GoogleChrome/sw-precache/tree/master/app-shell-demo">iFixit</a>).


### Caching the application shell {: #app-shell-caching }

An app shell can be cached using a manually written service worker or a
generated service worker using a static asset precaching tool like
[sw-precache](https://github.com/googlechrome/sw-precache).

Note: The examples are provided for general information and illustrative
purposes only. The actual resources used will likely be different for your
application.
