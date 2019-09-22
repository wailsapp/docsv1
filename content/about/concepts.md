---
title: "Concepts"
date: 2019-08-29T04:54:28+10:00
draft: false
---

Wails has been designed to be make the gap between web technologies and Go as minimal as possible. The frontend is a [Webview](https://github.com/zserge/webview) component, and you may develop your frontend code using any common Javascript framework you like, and seemlessly interact with your Go code from it. This is done through a shared IPC mechanism.

<p align="center" style="text-align: center">
   <img src="/images/Overview.svg" width="33%"><br/>
</p>

### IPC Overview

The IPC mechanism operates across 2 runtimes - one in Javascript and the other in Go. They both provide a simple interface, removing the burden from the developer from needing to deal with the IPC mechanism directly.

<p align="center" style="text-align: center">
   <img src="/images/wailsapptech.svg" width="33%"><br/>
</p>


The runtimes share common components which the developer can interact with: Binding and Events.

<p align="center" style="text-align: center">
   <img src="/images/IPCDetail.svg" width="33%"><br/>
</p>


### Binding

A Wails application provides a single method that allows you to expose (bind) your Go code to the frontend. Using this method, you may bind an arbitrary function or a struct with exposed methods. At startup, Wails will analyse bound functions/methods and automatically provide the equivalent functions in Javascript. This allows you to call your bound Go code directly from Javascript.


<p align="center" style="text-align: center">
   <img src="/images/Binding.svg" width="40%"><br/>
</p>

The Javascript wrapper functions deal with all of the complexity of calling the Go code. You simply call the function in Javascript and receive a promise back.
The function to bind your Go code deals with all the complexity of binding. If the call to your Go code completes successfully, the result will be passed to the resolve function. If an error is returned, this will be passed to the reject function.

### Events

Wails provides a unified Events system similar to Javascript's native events system. This means that any event that is sent from either Go or Javascript can be picked up by either side. Data may be passed along with any event. This allows you to do neat things like have background processes running in Go and notifying the frontend of any updates.

<p align="center" style="text-align: center">
   <img src="/images/Events.svg" width="40%"><br/>
</p>