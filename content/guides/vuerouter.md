+++
title = "Using Vue Router & Vuex"
date = 2020-07-15T09:10:12-06:00
weight = 20
chapter = false
+++

Wails supports both [Vue Router](https://router.vuejs.org/) for client routing and [Vuex](https://vuex.vuejs.org/) for client state management. Vue Router and Vuex can be used together, or separately.

This guide walks through a basic setup of Vue Router and Vuex with Wails and assumes that you're working with the [Basic Vue](/reference/cli/#basic-vue) template.

## Vue Router

Start by installing Vue Router in the frontend:

```sh
cd frontend
npm install vue-router
```

Create a file, `router.js` to define routes:

```sh
touch src/router.js
```

In the routes file, import `Vue`, `VueRouter`, and the `HelloWorld` component.

When instantiating the router, the mode must be set to `abstract`.

```js
// router.js
import Vue from 'vue'
import VueRouter from 'vue-router'
import HelloWorld from './components/HelloWorld.vue'

Vue.use(VueRouter)

const routes = [{ component: HelloWorld, name: 'Home', path: '/' }]

const router = new VueRouter({
  mode: 'abstract', // mode must be set to 'abstract'
  routes,
})

export default router
```
{{% notice info %}}
We've had an unconfirmed report that 'abstract' mode should be used when packing and that 'hash' and 'history' may be used during development.
https://github.com/wailsapp/wails/issues/492
{{% /notice %}}


In `main.js` import the router and pass it to the Vue constructor options. In the mounted lifecyle hook you can set the router's initial route.

```js
// main.js
import 'core-js/stable'
import 'regenerator-runtime/runtime'
import Vue from 'vue'
import App from './App.vue'
// import the router
import router from './router'

Vue.config.productionTip = false
Vue.config.devtools = true

import * as Wails from '@wailsapp/runtime'

Wails.Init(() => {
  new Vue({
    render: (h) => h(App),
    // add the router to the Vue constructor
    router,
    mounted() {
      this.$router.replace('/')
    },
  }).$mount('#app')
})
```

In the `App.vue` single file component, use the `<router-link>` tag to create links to routes. Create an entry point for the current route using the `<router-view>` tag.

```vue
// App.vue
<template>
  <div id="app">
    <img alt="Wails logo" src="./assets/images/logo.png" class="logo" />
    <router-link to="/">Home</router-link>
    <router-view></router-view>
  </div>
</template>

<script>
import './assets/css/main.css'

export default {
  name: 'app',
}
</script>
```

Now, if you have the Vue Devtools installed you can confirm VueRouter is handling routing:

![Screenshot of Vuex store in Vue.js DevTools extension](/images/vue-router.png)

## Vuex

Start by installing Vuex:

```sh
cd frontend
npm install vuex
```

```sh
touch src/store.js
```

```js
// store.js
import Vue from 'vue'
import Vuex from 'vuex'

Vue.use(Vuex)

const store = new Vuex.Store({
  state: {
    test: true,
  },
})

export default store
```

```js
// main.js
import 'core-js/stable'
import 'regenerator-runtime/runtime'
import Vue from 'vue'
import App from './App.vue'
// import the store
import store from './store'

Vue.config.productionTip = false
Vue.config.devtools = true

import * as Wails from '@wailsapp/runtime'

Wails.Init(() => {
  new Vue({
    render: (h) => h(App),
    // add the vuex store to the Vue constructor
    store,
  }).$mount('#app')
})
```

Now, if you have the Vue Devtools installed you can confirm the Vuex state:

![Screenshot of Vuex store in Vue.js DevTools extension](/images/vuex.png)
