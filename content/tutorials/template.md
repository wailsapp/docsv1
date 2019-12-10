+++
title = "Default Vue Template"
date = 2019-08-29T04:56:50+10:00
weight = 10
+++

In this tutorial, we will be looking at the default Vue template and gain an understanding of how Wails works. Whilst we do touch on Vue in this tutorial, it is assumed that the reader has a certain amount of experience with Vue. If not, we recommend the Vue tutorial [here](https://vuejs.org/v2/guide/). 

## Initialise Project

Run `wails init` to generate your project. We will call our project 'Quotes'. Accept the defaults for the following 2 questions:

```
The name of the project (My Project): Quotes
Project Name: Quotes
The output binary name (quotes): 
Output binary Name: quotes
Project directory name (quotes): 
Project Directory: quotes
Project 'Quotes' generated in directory 'quotes'!
To compile the project, run 'wails build' in the project directory.
```

## Serve Project

As we will partially be developing this in the browser, we will serve the backend in bridged mode. This means that the Go functions will be available to us in the browser!

Run `wails serve` in the project directory. 

You should have some output similar to this:

```
Quotes - Debug Build
--------------------
INFO[0000] [App] Starting                               
INFO[0000] [Events] Starting                            
INFO[0000] [IPC] Starting                               
INFO[0000] [Events] Listening                           
INFO[0000] [Bind] Starting                              
INFO[0000] [Bind] Binding Go Functions/Methods          
INFO[0000] [Bind] Bound Function: main.basic()          
INFO[0000] [Headless] Headless mode started.            
INFO[0000] [Headless] The Wails bridge will connect automatically. 
>>>>> To connect, you will need to run 'npm run serve' in the 'frontend' directory <<<<<
```

Note: In the startup messages, we can see that the function main.basic() has been bound:

```
INFO[0000] [Bind] Bound Function: main.basic()          
```

This means we can call it from the frontend, and in this instance, directly from the browser! ( The `main` part is simply the package name it was defined in. This is dropped when bound ).

To start the frontend, do the following:

```
cd frontend
npm run serve
```

This starts the standard Vue development server and will give you a URL that the application is being hosted on. It will be something like this:

```
  App running at:
  - Local:   http://localhost:8082/ 
  - Network: http://localhost:8082/
```

Open a browser to that URL and you should see something similar to the following:

<div class="imagecontainer">
<img src="/images/templatedefault.png">
</div>

If you click the button, you get a message:

<div class="imagecontainer">
<img src="/images/templatedefaulthello.png">
</div>

## Understanding the App

*What just happened?*

When you pressed the button, the frontend code called a backend function and printed the result to the page.

*How does that work?*

The sequence of operations are as follows:

  * main.js runs and waits for the runtime to Initialise. As this is running in bridged mode, it waits until a connection to the backend is established
  * Once established, it mounts the main app, defined in App.vue, just as you normally would in a Vue app
  * App.vue uses a component called "HelloWorld", defined in the components directory and this defines a message and a button. When the button is pressed, a call is made to the backend basic() function and the result is placed in the message

There is no more to the app than this. Let's look at those steps:

### Waiting for the Backend Connection

When running `wails serve`, the connection to the backend is managed by a bridge which is dynamically injected into the runtime library. This library does a lot of things, however we only need to be concerned with one function: Init(). The Init() function accepts a callback, which is invoked when the connection to the backend is established. This is demonstrated clearly in main.js:

```javascript
import Wails from '@wailsapp/runtime';

Wails.Init(() => {
  ...
  ...
  ...
});
```

The bridge sets up the following things:

  * The wails javascript runtime, available at through the `@wailsapp/runtime` module. This provides a number of useful systems that we will cover over the course of the tutorials. For now, we will not use it.
  * The bindings to backend code. These are available at `window.backend`. If you have bound the Go function `Hello` to your application, it will be available at `window.backend.Hello`. 

When the bridge has initialised, it will call the given callback. At this point you know that both the bindings and the runtime is available.

### Mount the main application

When the callback from Init is invoked, we create and mount the main Vue App Component:

```javascript
Wails.Init(() => {
  new Vue({
    render: h => h(App)
  }).$mount("#app");
});
```

The App component is defined as a [single file component](https://vuejs.org/v2/guide/single-file-components.html) and is entirely defined in App.vue. Single file components define 3 things:

  * HTML Template
  * Javascript
  * CSS

The HTML Template part of App looks like this:
```html
<template>
  <div id="app">
    <img alt="Wails logo" src="./assets/images/logo.png" class="logo zoomIn">
    <HelloWorld/>
  </div>
</template>
```
This defines a simple div containing the Wails logo and the HelloWorld component.

The Javascript part is fairly simple:
```javascript
<script>
import HelloWorld from "./components/HelloWorld.vue";
import "./assets/css/main.css";

export default {
  name: "app",
  components: {
    HelloWorld
  }
};
</script>
```

The App component imports the HelloWorld component from HelloWorld.Vue. It also imports the main css file from the assets directory. It then exports its name and what components it uses.

If we look at the HelloWorld.vue file we see the following HTML Template:

```html
<template>
  <div class="container">
    <h1>{{message}}</h1>
    <a @click="getMessage">Press Me!</a>
  </div>
</template>
```
You can see that the container comprises of an h1 header and a link tag. The header has the text <code v-pre>{{message}}</code> between its tags. This is a binding in the component. If we set this.message in any component method, the h1 header is rendered with that value. The link tag has a `@click` directive which means that when it is clicked, it will call the function `getMessage`. This will all make more sense when we look at the javascript section:

```javascript
<script>
export default {
  data() {
    return {
      message: " "
    };
  },
  methods: {
    getMessage: function() {
      var self = this;
      window.backend.basic().then(result => {
        self.message = result;
      });
    }
  }
};
</script>
```
Here we define 2 parts of the component: 

  - data defines the 'local' variables of the component
  - methods defines the methods of the component :-)

By default, message is a space. This means that a space will be rendered in the h1 tags at startup.

In the methods section, we see there is a method defined called `getMessage`. This is the function that gets called when the link tag is clicked. When it is clicked, we call `window.backend.basic()`. This is a method that we bind in the main go file! It is defined as such in main.go:

```go
func basic() string {
  return "Hello World!"
}
```

All bound Go methods are presented in Javascript as functions that return Promises. Because of this, we call `.then()` which gives us the result and we assign it to the message variable. We use `self` as `this` references the callback function, rather that the component.

## Exercises

 * See if you can get it to print a different message
 * Try changing the styling so that the message is a different colour or size
 * See if you can work out how to send a name to the backend from Vue, and get it to return you `Hello <name>!`

## Summary

Hopefully you now understand the basics of how Wails works and how the default template interacts with it.
