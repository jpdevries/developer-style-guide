# Developer Style Guide

HTML is initially performant, optimal, and accessible. So we recommend starting your components HTML&ndash;first. 

Within M5BP (MODX HTML5 Boilerplate), you'll find several common components that are free for you to use. All components are built with HTML+(S)CSS. Components are optionally enhanced with one or more JavaScript drivers. The MODX core team and MODX Advisory Board have chosen to enhance core components using `_____` so if you do as well you can ejoy additional tooling.

The more JavaScript drivers components have the more flexible creating Manager themes becomes when authoring using different frameworks or libraries. JavaScript drivers do not dictate or influence HTML structure, style, or even functionality. They just enhance HTML components and carry out asyncronous tasks like fetching data from the server and updating the DOM. In theory, a component should be able to switch from using a React driver to an Angular driver without the user noticing the difference. Additionally, Manager themes should be able to run various web components enhanced with different JavaScript drivers without negating from the cohensiviness of the user experience. 

You are free to leverage the same JavaScript tooling and scripts used to enhance the default Manager theme. We feel it is important to provide creative freedom not just when building websites, but when building components for the Manager itself. Therefore, you are also free to use the tooling of your choice when making JavaScript enhancements. 

The default MODX theme uses modern JavaScript that is transpiled with Babel, HTML 5.x, and future forward CSS. 

To ensure that the MODX core distribution is accessible to everyone, accessibility is mandated for contributions to the core. MODX Extras that pass an accessibility audit by following the guidelines found here are awarded with the `accessibility-ready` label. The sooner accessibility is brought into the design process the better. Get feedback and support on your sketches, concepts, designs, and code by posting your work in the MODX Forums. 

While we value the importance of accessibility enough to mandate it in the core, we would be remissed if we made the process of contributing to the MODX core itself less accessible. If you need guidance on maintaining accessibility for a pull request please do not hesitate to request an accessibility advisor. 

## Authoring Markup 
Semantic HTML documents are implicitly performant, optimal, and accessible. So we recommend starting coding your component as such. If you need to post or get data to and from the database start with a semantic HTML form. JavaScript should always be used as an enhancement. As you progressively enhance your markup into a more asynchronous user experience ensure that you do not degrade the initial accessibility of your semantic document. 

## Authoring Styles 
We recommend progressively enhancing CSS styles. For example, if you use modern grid layout enhance our layout from a block layout, to a flexible layout, and finally to a grid layout. You are free to use the same CSS preprocessor and postprocessor tooling found in the default theme. However you author your styles, please keep accessibility in mind. By using the MODX Theme Properties in your CSS you'll ensure that your components automatically respond to users accessibility preferences such as high contrast modes.

CSS Properties inhereted from the `.mx-component` class allow us to style various aspects of our component in one block. Here we set a default color of blue, respond to active high contrast modes by setting the color to dark blue, and respond to black-on-white and white-on-black contrast preferences accordingly.

```css
.mx-component.my-component {
  /* layout */
  --flex-grow: 1; /* grow horizontally on mobile */
  --flex-shrink: 0; /* don't shrink */
  
  --tablet-flex-grow: 0; /* don't grow horizontally on tablet and up */
  --tablet-flex-basis: 42ch; /* set a flex basis on tablet and up */

  /* base */
  --color: blue;
  --background: white;
  
  --active-contrast-color: darkblue; /* triggered in ms-high-contrast: active mode */

  --black-on-white-background: white; /* triggered in ms-high-contrast: black-on-white mode */
  --black-on-white-color: rgb(8, 8, 8); /* almost black, triggered in ms-high-contrast: black-on-white mode */

  --white-on-black-color: white; /* almost black, triggered in ms-high-contrast: white-on-black mode */  
  --white-on-black-background: rgb(8, 8, 8); /* almost black, triggered in ms-high-contrast: black-on-white mode */
}
```



To meet accessibility guidelines it is important that you use relative units for typography and layout. Do not use declarative units such as pixels to set type or define media queries as doing so is incompatible with text&ndash;only zoom features.

```css
.my-component {
  /* use relative units for typography and layout */
  font-size: 1.2rem;
  padding: 1rem;
  max-width: 42ch;
}
```

## Authoring Scripts 
We recommend using a [webpack](https://webpack.js.org) workflow to preprocess and bundle modern JavaScript modules into production code that is delivered to the browser. Bundling modules allows for code reuse. For example, you can use the same JavaScript modules found in the MODX core like so:

```js 
import { ComboBox } from 'modxjs';
```

Modules such as the DataGrid which are JavaScript classes can be extended like so:

```js 
class CustomComboBox extends ComboBox {
  componentWillUpdate(nextProps, nextState) {
    // do some custom stuff here
  }
}
```

## Asynchronous Requests 
AJAX requests use the Promise based Fetch API. For example we'd request a JSON response containing an array of all resources like so:

```js 
fetch(`${MODX.config.mgrURL}connectors/resources`, {
  credentials: 'include'  
}).then((resources) => {
  resources.map((resource) => {
    console.log(resource.pagetitle);
  });
});
```

For security purposes it is important to include credentials with the request so that cookies can be sent and received. 

You'll likely need to `POST`, `PUT`, and `DELETE` data to your component's connectors. Set the method and body properties of the request options Object like so:

```js 
const formData = new FormData(document.getElementById('resource-create'));

fetch(`${MODX.config.baseURL}connectors/resources`, {
  method: 'PUT',
  body: formData,
  credentials: 'include'  
}).then((resource) => {
  console.log(`resource created with an id of ${resource.id}`)
});
```

You write fantastic code. But that doesn't mean it will always work. Handle errors by catching them like so:

```js 
fetch(`${MODX.config.baseURL}connectors/resources`, {
  //credentials: 'include' // whoops
}).then((resources) => {
  resources.map((resource) => {
    console.log(resource.pagetitle);
  });
}).catch((err) => {
  console.log('uh oh');
  console.error(err);
});
```

## Code Splitting 

To provide an optimal experience to all users, we recommend loading scripts only when and if they are needed. For example, imagine you have an image manager component that displays a grid of images and allows them to be cropped in a modal window. We don't want to load the cropping code until it is called upon. We can accomplish this using the modern Async/Await pattern:

```js 
export default class ImageGrid extends Component {
  async openModalWindow() {
    const ImageCropper = await import('ImageCropper');
  }
}
```

## Leveraging Browser Cache

To effectively leverage the browser cache do not bundle common frameworks and libraires such as Angular, React, or jQuery with your code. Instead, load them as seaprate files like so:

```html
<!-- load jQuery separately -->
<script src="assets/js/vendor/jquery-3.2.0.min.js"></script>
<!-- load your component -->
<script src="assets/components/mycomponent/js/mycomponent.js"></script>
```

To leverage the browser cache across MODX installations we recommend loading common frameworks and libraries from a CDN with a local fallback like so:

```html
<!-- first attempt to load jQuery from a CDN -->
<script src="//code.jquery.com/jquery-3.2.0.min.js" integrity="sha256-JAW99MJVpJBGcbzEuXk4Az05s/XyDdBomFqNlM3ic+I=" crossorigin="anonymous"></script>
<!-- if the CDN didn't work load jQuery from a local fallback -->
<script>window.jQuery || document.write('<script src="assets/js/vendor/jquery-3.2.0.min.js"><\/script>')</script>
<!-- load your component -->
<script src="assets/components/mycomponent/js/mycomponent.js"></script>
```

There is no point in loading a common framework or library if a sufficient version of it is already loaded in the page. Before loading common dependencies perform feature detection to test if they are needed like so:

```html
<!-- check if we need jQuery, then if needed attempt to load jQuery from a CDN -->
<script>window.jQuery || document.write('<script src="//code.jquery.com/jquery-3.2.0.min.js" integrity="sha256-JAW99MJVpJBGcbzEuXk4Az05s/XyDdBomFqNlM3ic+I=" crossorigin="anonymous"><\/script>')</script>
```

To leverage the browser cache and ensure that the cache is flushed when updates are made it is important to include a version number or some type of unique hash within your assets file name. For example instead of `app.js` you'd name your file `app.1.0.0.js`.

### Lazy Load Scripts

Thus far we've explored HTML patterns to load scripts. You can also load your depdencies with `lazyload-script`:

```js
import lazyLoadScript from 'lazyload-script';

const promises = [];

if(!React) promises.push(
  // try to load React from a CDN, fallback to a local copy 
  lazyLoadScript("https://cdnjs.cloudflare.com/ajax/libs/react/15.4.2/react.min.js", "react.15.4.2.min.js").catch((err => (
    lazyLoadScript(`./js/vendor/react.15.4.2.min.js`, "react.15.4.2.min.js")
  )))
);

if(!ReactDOM) promises.push(
  // try to load React DOM from a CDN, fallback to a local copy 
  lazyLoadScript("https://cdnjs.cloudflare.com/ajax/libs/react/15.4.2/react-dom.min.js", "react-dom.15.4.2.min.js").catch((err => {
    lazyLoadScript(`./js/vendor/react-dom.15.4.2.min.js`, "react-dom.15.4.2.min.js")
  }))
);

if(!Redux) promises.push(
  // try to load Redux from a CDN, fallback to a local copy 
  lazyLoadScript("https://cdnjs.cloudflare.com/ajax/libs/redux/3.6.0/redux.min.js", "redux.3.6.0.min.js").catch((err => {
    lazyLoadScript(`./js/vendor/redux.3.6.0.min.js`, "redux.3.6.0.min.js")
  }))
);

if(!ReactRedux) promises.push(
  // try to load React Redux from a CDN, fallback to a local copy 
  lazyLoadScript("https://cdnjs.cloudflare.com/ajax/libs/react-redux/5.0.3/react-redux.min.js", "react-redux.5.0.3.min.js").catch((err => {
    lazyLoadScript(`./js/vendor/react-redux.5.0.3.min.js`, "react-redux.5.0.3.min.js")
  }))
);
 
Promise.all(promises).then(() => {
  // React, React DOM, Redux, and React Redux are ready. woohoo! 
});
```

**See Also**

 - [`lazyload-script`](https://www.npmjs.com/package/lazyload-script)

### Service Workers 

To improve user experience and performance on subsequent visits to the Manger, we recommend that you include a Service Worker with your Extra that hard cache appropriate assets like versioned CSS and JavaScript files. 

```js
const VERSION = '1.0.0-pl',
STICKY_FILES = [ // these files will be stored in the service worker cache
  `css/my-component.${VERSION}.css`,
  `css/my-component.${VERSION}.min.css`,
  `img/my-component.${VERSION}.svg`,
  `img/my-component.${VERSION}.min.svg`,
  `js/my-component.${VERSION}.js`,
  `js/my-component.${VERSION}.min.js`
];

self.addEventListener('install', function(event) {
  self.skipWaiting(); // greedily install the service worker
});

function endsWithStickyFile(request) { // is the request to a sticky file?
  STICKY_FILES.forEach((file) => {
    if(request.endsWith(file)) return true;
  });
  return false;
}

self.addEventListener('fetch', function(event) {
  if(event.request.method !== 'GET' || !endsWithStickyFile(event.request)) return;
  event.respondWith(
    caches.match(event.request).then(function(resp) {
      return resp || fetch(event.request).then(function(response) {
        return caches.open(CACHE_NAME).then(function(cache) {
          cache.put(event.request, response.clone());
          return response;
        });
      });
    })
  );
});

self.addEventListener('activate', function(event) {
  var cacheWhitelist = [VERSION];

  event.waitUntil(
    caches.keys().then(function(keyList) { // purge old cache partitions
      return Promise.all(keyList.map(function(key) {
        if (!cacheWhitelist.includes(key)) return caches.delete(key);
      }));
    }).then(function() {
      self.clients.claim()
    })
  );
});
```

You only need to register your service worker once.

```js
if (!navigator.serviceWorker.controller) {
  // Register the ServiceWorker
  navigator.serviceWorker.register(`${MODX.config.baseURL}connectors/my-component/service-worker.js`, {
    scope: './'
  }).then(function(reg) {
    debug(reg.scope, 'register');
  });
}
```

**See Also**

 - [Using Service Workers](https://developer.mozilla.org/en-US/docs/Web/API/Service_Worker_API/Using_Service_Workers)

## Enhancing Forms

With a little creativity, just about any web component can be enhanced from a HTML form. Consider the following HTML form:

```html
<form id="signup" action="/core/connectors/mycomponent/signup" method="POST">
  <label for="email">
    Email
    <input required type="email" id="email" name="email" />
  </label>
  <button>Sign Up</button>
</form>
```

When the form is submitted an email address will syncronously be posted to `/core/connectors/mycomponent/signup`. Now consider you want to enhance this form to be asyncronous. You may be tempted to do something like the following:

```js
const signup = document.getElementById("signup");
signup.querySelector('button').addEventListener('click', (event) => {
  event.preventDefault();
  
  const email = document.getElementById("email");
  // send the email to the server
});
```

There are several issues here:
 - not all users click
 - we are bypassing the HTML5 Form Validation API
 - we are unnecessarily querying the DOM for the input value
 
 Instead of listening to a click event on the submit button, listen to the submit event on the form:
 
 ```js
document.getElementById("signup").addEventListener('submit', (event) => {
  event.preventDefault();
  
  const formData = new FormData(event.target),
  email = formData.get("email");
  // send the email to the server
});
```

**See Also**

 - [Give your Forms some Form](https://medium.com/front-end-hacking/give-your-forms-some-form-2ec73cb36981)

## Formatting Dates and Times

By using the `Date.toLocaleString()`, `Date.date.toLocaleDateString()`, and `Date.prototype.toLocaleTimeString()` when formatting dates they'll always be formatted in the expected local format based on the user agent or the MODX users locale setting. For example:

```js
const dateTimeString = new Date().toLocaleString(MODX.user.locale),
dateString = new Date().toLocaleDateString(MODX.user.locale),
timeString = new Date().toLocaleTimeString(MODX.user.locale);
```

## Accessibility Testing

A significant portion of accessibiilty testing can be done using automation. We recommend testing your Extras with the `axe-core` Chrome extensions prior to manual testing.

**See Also**

 - [Accessibility Testing with aXe](http://marcysutton.github.io/a11y-testing-with-axe)
