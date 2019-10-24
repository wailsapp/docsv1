+++
title = "Migrating to v1.0.0"
date = 2019-10-24T15:56:50+10:00
weight = 10
chapter = false
+++

# Migrating to Wails v1.0.0

The release of v1.0.0 brings with it some breaking changes, however it is relatively easy to migrate older projects. The steps required are:

* Change Wails version in go.mod to latest version
* Remove `frontend/src/wailsbridge.js` if you have it
* Install the latest `@wailsapp/runtime` module, EG: `npm install --save @wailsapp/runtime`
* Titlecase 
* Update main.js to look like this:

```
// import Bridge from "./wailsbridge";
import * as Wails from "@wailsapp/runtime"

//Bridge.Start(() => {
Wails.Init(() => {
  new Vue({
    router,
    render: h => h(App)
  }).$mount("#app");
});
```

If you are feeling adventurous, you could try the **highly experimental** migrate command: `wails migrate`.