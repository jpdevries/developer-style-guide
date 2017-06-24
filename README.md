# Developer Style Guide

HTML is initially performant, optimal, and accessible. So we recommend starting your components HTML&ndash;first. 

Within M5BP (MODX HTML5 Boilerplate), you'll find several common components that are free for you to use. All components are built with HTML+(S)CSS. Components are optionally enhanced with JavaScript. 

You are free to use the same JavaScript tooling and scripts used to enhance the default Manager theme. We feel it is important to provide creative freedom not just when building websites, but when building components for the Manager itself. Therefore, you are also free to use the tooling of your choice when making JavaScript enhancements. 

The default MODX theme uses modern JavaScript that is transpiled with Babel, HTML 5.x, and future forward CSS that is authored in Sass and vendor prefixed with autoprefixer. 

## Authoring Markup 
Semantic HTML documents are implicitly performant, optimal, and accessible. So we recommend starting coding your component as such. If you need to post or get data to and from the database start with a semantic HTML form. JavaScript should always be used as an enhancement. As you progressively enhance your markup into a more asynchronous user experience ensure that you do not degrade the initial accessibility of your semantic document. 

## Authoring Styles 
We recommend progressively enhancing CSS styles. For example, if you use modern grid layout enhance our layout from a block layout, to a flexible layout, and finally to a grid layout. You are free to use the same CSS preprocessor and postprocessor tooling found in the default theme. However you author your styles, please keep accessibility in mind. By using the MODX Theme Properties in your CSS you'll ensure that your styles automatically respond to users accessibility preferences such as high contrast modes.

To meet accessibility guidelines it is important that you use relative units for typography and layout. Do not use pixels to set type or define media queries as doing so is incompatible with text&ndash;only zoom features.

## Authoring Scripts 
We recommend using a [webpack](https://webpack.js.org) workflow to preprocess and bundle modern JavaScript modules into production code that is delivered to the browser. Bundling modules allows for code reuse. For example, you can use the same JavaScript modules found in the MODX core like so:

```js 
import { ComboBox, DataGrid, Tree } from 'modxjs';
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

fetch(`${MODX.config.mgrURL}connectors/resources`, {
  method: 'PUT',
  body: formData,
  credentials: 'include'  
}).then((resource) => {
  console.log(`resource created with an id of ${resource.id}`)
});
```

You write fantastic code. But that doesn't mean it will always work. Handle errors by catching them like so:

```js 
fetch(`${MODX.config.mgrURL}connectors/resources`, {
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

