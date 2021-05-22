+++
title = "Todo App"
date = 2019-08-29T04:56:50+10:00
weight = 30
+++

In this tutorial, we will be creating a todo app based on the [Vue MVC Todo App](http://todomvc.com/examples/vue/). We will cover the following topics:

  * Accessing the Wails runtime in both Javascript and Go
  * Using native browser file dialogs
  * Using the unified events system
  * Binding both functions and structs to the application
  * Basic usage of Vue 

The source code to the app is available [here](https://github.com/wailsapp/todo). I highly recommend not looking at it unless you really need to.

## Setup

Generate a new project using `wails init`. We will call the project 'todos' and accept the default answers:

```bash
The name of the project (My Project): todos
Project Name: todos
The output binary name (todos): 
Output binary Name: todos
Project directory name (todos): 
Project Directory: todos
Template: Vue2/Webpack Basic 
Project 'todos' built in directory 'todos'!
```

Next we will move into the `todos` directory. You should see the following files, including a todos binary:

```bash
.
├── frontend
├── go.mod
├── go.sum
├── main.go
├── project.json
└── todos
```

Now lets build our frontend!

## Basic Frontend

We are going to base our frontend on the cool [Todo MVC](http://todomvc.com/examples/vue/) app that the awesome [Evan You](evanyou.me) created. The main code repository can be located [here](https://github.com/tastejs/todomvc/tree/gh-pages/examples/vue). 

We will start by updating the template section of App.vue located in you project directory in frontend/src. If we look at [index.html](https://github.com/tastejs/todomvc/blob/gh-pages/examples/vue/index.html), we can see that the main html part of the application lies between lines 11 and 48:

```html
    <section class="todoapp" v-cloak>
      <header class="header">
        <h1>todos</h1>
        <input class="new-todo" autofocus autocomplete="off" placeholder="What needs to be done?" v-model="newTodo" @keyup.enter="addTodo">
      </header>
      <section class="main" v-show="todos.length">
        <input id="toggle-all" class="toggle-all" type="checkbox" v-model="allDone">
        <label for="toggle-all">Mark all as complete</label>
        <ul class="todo-list">
          <li class="todo" v-for="todo in filteredTodos" :key="todo.id" :class="{completed: todo.completed, editing: todo == editedTodo}">
            <div class="view">
              <input class="toggle" type="checkbox" v-model="todo.completed">
              <label @dblclick="editTodo(todo)">{{todo.title}}</label>
              <button class="destroy" @click="removeTodo(todo)"></button>
            </div>
            <input class="edit" type="text" v-model="todo.title" v-todo-focus="todo == editedTodo" @blur="doneEdit(todo)" @keyup.enter="doneEdit(todo)" @keyup.esc="cancelEdit(todo)">
          </li>
        </ul>
      </section>
      <footer class="footer" v-show="todos.length">
        <span class="todo-count">
          <strong v-text="remaining"></strong> {{pluralize('item', remaining)}} left
        </span>
        <ul class="filters">
          <li><a href="#/all" :class="{selected: visibility == 'all'}">All</a></li>
          <li><a href="#/active" :class="{selected: visibility == 'active'}">Active</a></li>
          <li><a href="#/completed" :class="{selected: visibility == 'completed'}">Completed</a></li>
        </ul>
        <button class="clear-completed" @click="removeCompleted" v-show="todos.length > remaining">
          Clear completed
        </button>
      </footer>
    </section>
    <footer class="info">
      <p>Double-click to edit a todo</p>
      <p>Written by <a href="http://evanyou.me">Evan You</a></p>
      <p>Part of <a href="http://todomvc.com">TodoMVC</a></p>
    </footer>
```

We'll base our template on this, using the bare minimum to begin with:


```html
<template>
  <div>
    <section class="todoapp" v-cloak>
      <header class="header">
        <h1>todos</h1>
        <input class="new-todo" autofocus autocomplete="off" placeholder="What needs to be done?">
      </header>
      <section class="main">
        <ul class="todo-list">
          <li class="todo">
            <div class="view">
              <label>A todo item</label>
            </div>
          </li>
        </ul>
      </section>
    </section>
  </div>
</template>
```

We will also update our script section as we will not need the HelloWorld component:

```javascript
<script>
export default {
  name: "app",
};
</script>
```

So the whole App.vue should now look like this:

```html
<template>
  <div>
    <section class="todoapp" v-cloak>
      <header class="header">
        <h1>todos</h1>
        <input class="new-todo" autofocus autocomplete="off" placeholder="What needs to be done?">
      </header>
      <section class="main">
        <ul class="todo-list">
          <li class="todo">
            <div class="view">
              <label>A todo item</label>
            </div>
          </li>
        </ul>
      </section>
    </section>
  </div>
</template>

<script>
export default {
  name: "app",
};
</script>
```

The template has 2 sections: the header where the title is displayed (Lines 4-7) and the main todo list (Lines 8-16). 

So let's now start serving the front end so we can see what it looks like. As we haven't written our backend yet, let's temporarily comment out the Wails runtime parts of `frontend/src/main.js`:

```javascript
import Vue from "vue";
import App from "./App.vue";

Vue.config.productionTip = false;
Vue.config.devtools = true;

// import Wails from '@wailsapp/runtime';

//Wails.Init((() => {
  new Vue({
    render: h => h(App)
  }).$mount("#app");
// });
```

Change into the 'frontend' directory and run `npm run serve`. If all goes well, you should see something like this on the console:

```
 DONE  Compiled successfully in 187ms                                                                              8:20:54 AM

 
  App running at:
  - Local:   http://localhost:8082/ 
  - Network: http://localhost:8082/

```

Open a browser to this location and you should see something similar to this:

<div class="imagecontainer">
  <img src="/images/todounstyled.png" style="width: 75%">
</div>

It looks underwhelming but it's early days. For it to look like the original, we need to include the original stylesheets. The original template references 2:

```html
    <link rel="stylesheet" href="node_modules/todomvc-common/base.css">
    <link rel="stylesheet" href="node_modules/todomvc-app-css/index.css">
```

Download the Base CSS from [here](https://raw.githubusercontent.com/tastejs/todomvc/gh-pages/examples/vue/node_modules/todomvc-common/base.css) and the App CSS from [here](https://raw.githubusercontent.com/tastejs/todomvc/gh-pages/examples/vue/node_modules/todomvc-app-css/index.css). 

Copy them to `frontend/src/assets/css` and rename `index.css` to `app.css`. Let's include those in the App.vue file. We can simply import them:

```javascript {2-3}
<script>
import "./assets/css/base.css";
import "./assets/css/app.css";
export default {
  name: "app",
};
</script>
```

As Vue is serving the frontend, these changes should now be reflected in the browser. You should see something like this:

<div class="imagecontainer">
  <img src="/images/todostyled.png" style="width: 75%">
</div>

That looks much better! You'll notice that whilst the app is responding, it doesn't do much. Next we'll add some code into our component to manage the list.

## Implementing the list

We'll store our todo list in an array in the component:

```javascript {6-10}
<script>
import "./assets/css/base.css";
import "./assets/css/app.css";
export default {
  name: "app",
  data() {
    return {
      todos: [],
    }
  }
};
</script>
```

We'll also add a condition to the html that we should only show the todo items if we have any items. We do this using the [v-show](https://vuejs.org/v2/guide/conditional.html#v-show) directive:
    
```html {1}
      <section class="main" v-show="todos.length">
        <ul class="todo-list">
          <li class="todo">
            <div class="view">
              <label>A todo item</label>
            </div>
          </li>
        </ul>
      </section>
``` 

If we look at our app now, we will see our original to do item is now not there. This is because the todos array is empty so our list section of the template is now hidden.

We will define our todo items in a Javascript object. To start with, this will simply be a unique number to identify the the item as well as its title:

```javascript
{
  id: <number>,
  title: <string>
}
```

Let's add a test entry into the main list:

```javascript {3}
  data() {
    return {
      todos: [{id: 0, title: "My test todo item"}]
    }
  }
```

In vue, we can iterate over a list using [v-for](https://vuejs.org/v2/guide/list.html#v-for-on-a-lt-template-gt) and we will use that to display our list items:

```html {3-7}
      <section class="main" v-show="todos.length">
        <ul class="todo-list">
          <li class="todo" v-for="todo in todos" :key="todo.id">
            <div class="view">
              <label>{{ todo.title }}</label>
            </div>
          </li>
        </ul>
      </section>
```

After saving this, you should see the todo item in the list:

<div class="imagecontainer">
  <img src="/images/todoshowitems.png" style="width: 75%">
</div>

You will notice that if you change the item title and save, it will update the title in the browser!

Next let's add a checkbox to allow us to indicate that we have completed a task. To do this, we will need to update our todo item model with a variable to store this:

```javascript
{
  id: <number>,
  title: <string>,
  completed: <boolean>
}
```

Let's add that to our test item and set it to true:

```javascript {3}
  data() {
    return {
      todos: [{id: 0, title: "My test todo item", completed: true}]
    }
  }
```

The todo-mvc stylesheet has a css class called "completed" that styles completed items. We will use the [class directive](https://vuejs.org/v2/guide/class-and-style.html) to add that styling based on our item's 'completed' property:

```html {3}
      <section class="main" v-show="todos.length">
        <ul class="todo-list">
          <li class="todo" v-for="todo in todos" :key="todo.id" :class="{completed: todo.completed}">
            <div class="view">
              <label>{{ todo.title }}</label>
            </div>
          </li>
        </ul>
      </section>
```

You should now have a styled item like so:

<div class="imagecontainer">
  <img src="/images/todocompleteditem.png" style="width: 75%">
</div>

## Toggling items

Let's now add a toggle for the todo item so we can set it as completed ourselves.

We will add an html checkbox to the item and bind it to the item's 'completed' variable using [v-bind](https://vuejs.org/v2/guide/class-and-style.html). The 'toggle' class is part of the todo-mvc css and turns a boring checkbox into a nicely styled toggle:

```html {5}
      <section class="main" v-show="todos.length">
        <ul class="todo-list">
          <li class="todo" v-for="todo in todos" :key="todo.id" :class="{completed: todo.completed}">
            <div class="view">
              <input class="toggle" type="checkbox" v-model="todo.completed">
              <label>{{ todo.title }}</label>
            </div>
          </li>
        </ul>
      </section>
```
We will also default our test item's completed variable to false:

```javascript
      todos: [{id: 0, title: "My test item", completed: false}]
```

The app should now look like this:

<div class="imagecontainer">
  <img src="/images/todostyledtoggle1.png" style="width: 75%">
</div>

and when you select the item:

<div class="imagecontainer">
  <img src="/images/todostyledtoggle2.png" style="width: 75%">
</div>

## Using the Vue Dev Tools

One of the motivators for Wails to fully support developing in the browser was so that developers could use the amazing array of developer extensions available. Vue has a brilliant extension called [Vue Dev Tools](https://github.com/vuejs/vue-devtools) which allows you to inspect your application from a Vue perspective. By default, Wails enables support for this extension.

Once you install it, right click on the browser running your app and select "inspect". There should be a "Vue" tab available. Select that and it should show you the page layout from a component perspective. We only have one (App). If you click on it, you will see your component data on the right. We can see the todos array and if we open it up, we can see our test item:

<div class="imagecontainer">
  <img src="/images/vuedevtools1.png" style="width: 100%">
</div>

Not only can we see it, we can edit it! Click the pencil/pen icon next to the variable and you will be able to edit the value. Boolean values have a handy toggle icon. 

If we click our toggle button in the page, we can see the value in dev tools toggle. And the reverse is true also!

Dev Tools is a great way to develop and debug your application. For more information, I highly recommend the awesome Flavio Copes [tutorial](https://flaviocopes.com/vue-devtools/) on it.

## Adding Todo Items

It's about time we had a way of adding todo items. We already have an input at the top of the list but it doesn't do anything. Let's add a binding between that and our component data. We will add a new data item called newTodo and it will be a string:

```javascript {3}
  data() {
    return {
      newTodo: "",
      todos: [{id: 0, title: "My test item", completed: false}]
    }
  }
```

We then add the `v-bind` directive to link the input to the newTodo variable:

```html{3}
      <header class="header">
        <h1>todos</h1>
        <input class="new-todo" autofocus autocomplete="off" placeholder="What needs to be done?" v-model="newTodo">
      </header>
```

If you reload the page, you should now see the newTodo item in dev tools. If you type in the input at the top of the list you will see its value reflected in the data object.

Now we need a way of saving this value into our todo list. To achieve this, we need to do 2 things:
  * create a method on the component that adds the item to the list
  * a mechanism to call this method when we press enter on the input

The way to declare a [method](https://flaviocopes.com/vue-methods/) in a Vue component is to add it to a `methods` object in the component. For us, we will add a method called `addTodo`:

```javascript {12-25}
<script>
import "./assets/css/base.css";
import "./assets/css/app.css";
export default {
  name: "app",
  data() {
    return {
      newTodo: "",
      todos: [{id: 0, title: "My test item", completed: false}]
    }
  },
  methods: {
    addTodo: function() {
      var value = this.newTodo && this.newTodo.trim();
      if (!value) {
        return;
      }
      this.todos.push({
        id: this.todos.length,
        title: value,
        completed: false
      });
      this.newTodo = "";
    }
  }
};
</script>
```

This will check that we have a new todo item and trim off the spaces if we do. If we don't then have a title (only spaces were input), then we just return and do nothing. If we have a title, then we push a new todo item object into the todos list (component variables are accessible through `this`). We then reset the newTodo variable to a blank string.

Now that we have a way of adding, let's add the trigger:

```html {3}
      <header class="header">
        <h1>todos</h1>
        <input class="new-todo" autofocus autocomplete="off" placeholder="What needs to be done?" v-model="newTodo" @keyup.enter="addTodo">
      </header>
```

Vue has a handy directive called [v-on](https://vuejs.org/v2/api/#v-on) which allows us to listen for events. We can use it to listen for when the enter key is released by using `v-on:keyup.enter` or the equivalent shorthand `@keyup.enter`.

Now if we enter text into the input field and press return, the item gets added to our list:

<div class="imagecontainer">
  <img src="/images/todoadditem1.png" style="width: 75%">
</div>

## Removing Todo Items

Now that we can add items and mark them as complete, we should also add the ability to remove items from the list. We will do this by using a simple button on each item:

```html {7}
      <section class="main" v-show="todos.length">
        <ul class="todo-list">
          <li class="todo" v-for="todo in todos" :key="todo.id" :class="{completed: todo.completed}">
            <div class="view">
              <input class="toggle" type="checkbox" v-model="todo.completed">
              <label>{{ todo.title }}</label>
              <button class="destroy" @click="removeTodo(todo)"></button>
            </div>
          </li>
        </ul>
      </section>
```

We are using the `v-on` shorthand again but this time instead of listening for a key event, we are listening for the click event emitted when the button is pressed. When the button is pressed, we call `removeTodo()` with our todo. Let's now add that method after `addTodo`:

```javascript {14-21}
  methods: {
    addTodo: function() {
      var value = this.newTodo && this.newTodo.trim();
      if (!value) {
        return;
      }
      this.todos.push({
        id: this.todos.length,
        title: value,
        completed: false
      });
      this.newTodo = "";
    },
    removeTodo: function(todo) {
      var index = this.todos.indexOf(todo);
      this.todos.splice(index, 1);

      for(var i=0; i<this.todos.length; i++){ 
        this.todos[i].id = i;
      }
    }
  }
```

This finds the index of our todo in the array, and removed that element. We then loop over the list and re-assign the item ids to ensure uniqueness.

If we now hover over a todo item, you will see a red 'X' on the right hand side of the item. When you click it, the item is removed.


<div class="imagecontainer">
  <img src="/images/todoremoveitem1.png" style="width: 75%">
</div>

## Editing a Todo Item

Editing a todo item will be the most complicated thing we do with Vue in this tutorial. We need to do the following:

  * Listen for a double click on an item
  * Show a text input with the item's text
  * Listen for enter signifying the edit is complete
  * Set the title of the item to the new input
  * Hide the input

Let's start by adding a listener for a double click to an entry:

```html {6}
      <section class="main" v-show="todos.length">
        <ul class="todo-list">
          <li class="todo" v-for="todo in todos" :key="todo.id" :class="{completed: todo.completed}">
            <div class="view">
              <input class="toggle" type="checkbox" v-model="todo.completed">
              <label @dblclick="editTodo(todo)">{{todo.title}}</label>
              <button class="destroy" @click="removeTodo(todo)"></button>
            </div>
          </li>
        </ul>
      </section>
```
When we double click on a todo item, it will now call a method called `editTodo`, passing in the todo we clicked on. We want to retain a reference to that item so let's add a data variable for it:

```javascript {4}
  data() {
    return {
      newTodo: "",
      editedTodo: null,
      todos: [{id: 0, title: "My test item", completed: false}]
    }
  },
```

Let's now define `editTodo` function:

```javascript
    editTodo: function(todo) {
      this.editedTodo = todo;
    },
```

Next, we need to add the input element to the template to show when editing. This needs to be bound to our todo's title. We also give it the 'edit' css class, as this is what is expected by the TodoMVC stylesheet:

```html {9-13}
      <section class="main" v-show="todos.length">
        <ul class="todo-list">
          <li class="todo" v-for="todo in todos" :key="todo.id" :class="{completed: todo.completed}">
            <div class="view">
              <input class="toggle" type="checkbox" v-model="todo.completed">
              <label @dblclick="editTodo(todo)">{{todo.title}}</label>
              <button class="destroy" @click="removeTodo(todo)"></button>
            </div>
            <input
              class="edit"
              type="text"
              v-model="todo.title"
            >
          </li>
        </ul>
      </section>
```

Hiding and showing is part of the TodoMVC stylesheet - we simply need to set the class 'editing' when editing so we can add that too. We use a simple evaluation that when the todo item is the 'editedTodo', then we add the class 'editing': 

```html {3}
      <section class="main" v-show="todos.length">
        <ul class="todo-list">
          <li class="todo" v-for="todo in todos" :key="todo.id" :class="{completed: todo.completed, editing: todo == editedTodo}">
            <div class="view">
              <input class="toggle" type="checkbox" v-model="todo.completed">
              <label @dblclick="editTodo(todo)">{{todo.title}}</label>
              <button class="destroy" @click="removeTodo(todo)"></button>
            </div>
            <input
              class="edit"
              type="text"
              v-model="todo.title"
            >
          </li>
        </ul>
      </section>
```

If we look at our app now, we notice a couple of things:

  * When we double click, the text input does appear but it is not focused.
  * When we press return, nothing happens.

Let's address the first point. Vue uses directives in standard html elements to indicate how that element is processed. It also offers the ability to create [custom directives](https://vuejs.org/v2/guide/custom-directive.html) so you can tailor them to your needs. We will define a directive that will focus the element that uses it. Insert this between the 'data' and 'methods' fields in the component's javascript section.

```javascript {4-10}
      todos: [{id: 0, title: "My test item", completed: false}]
    }
  },
  directives: {
    "todo-focus": function(el, binding) {
      if (binding.value) {
        el.focus();
      }
    }
  },
  methods: {
    addTodo: function() {
```

We can now use this in our input element:

```html {5}
            <input
              class="edit"
              type="text"
              v-model="todo.title"
              v-todo-focus="todo == editedTodo"
            >
```

Now let's address the issue of completing the edit. We want to listen for when the return key is pressed. 
Vue allows us to do this using the `v-on` directive we used earlier. We will use [keyup](https://vuejs.org/v2/guide/events.html#Key-Modifiers) to listen for the release of the return key.

```html {5}
            <input
              class="edit"
              type="text"
              v-model="todo.title"
              @keyup.enter="doneEdit(todo)"
              v-todo-focus="todo == editedTodo"
            >
```

Now, when the return (enter) key is released, the `doneEdit` method will be called with the todo. We will now define this method in the component:

```javascript {5-14}
    editTodo: function(todo) {
      this.editedTodo = todo;
    },

    doneEdit: function(todo) {
      if (!this.editedTodo) {
        return;
      }
      this.editedTodo = null;
      todo.title = todo.title.trim();
      if (!todo.title) {
        this.removeTodo(todo);
      }
    },
  }
```

In this function, we are first ensuring we are actually editing an item by checking if the editedTodo variable is set. If it isn't, we just return and ignore it ever happened. If we are legit editing a todo, we set the editedTodo variable to null as we are now not editing. We then set the todo title to be the current value, but with any whitespace at the ends trimmed off. Finally, we check to see if we actually have any text in the title (because a blank string in javascript evaluates to false), we just call our `removeTodo` function. 

If you try the app now, you will see that the item edits correctly, however there's some slight oddities: if you click off the input, it doesn't end the editing. Also, if you try to create another todo, the input flicks over to the edit box. Let's address those issues by also listening to the [blur event](https://developer.mozilla.org/en-US/docs/Web/API/Element/blur_event):

```html {6}
            <input
              class="edit"
              type="text"
              v-model="todo.title"
              @keyup.enter="doneEdit(todo)"
              @blur="doneEdit(todo)"
              v-todo-focus="todo == editedTodo"
            >
```

That's better! Now it feels right. It would be good if we could also cancel our edits. We know how to listen to keypresses, so let's listen for escape and cancel the current editing session. To do this, we are first going to keep a copy of the item's text when we first start editing:

```javascript {2}
    editTodo: function(todo) {
      this.beforeEditCache = todo.title;
      this.editedTodo = todo;
    },
```

We are using a variable called `beforeEditCache` to store the initial value. Now let's listen to the escape key:

```html {7}
            <input
              class="edit"
              type="text"
              v-model="todo.title"
              @keyup.enter="doneEdit(todo)"
              @blur="doneEdit(todo)"
              @keyup.esc="cancelEdit(todo)"
              v-todo-focus="todo == editedTodo"
            >
```

And finally, let's implement the `cancelEdit` method:

```javascript
    cancelEdit: function(todo) {
      this.editedTodo = null;
      todo.title = this.beforeEditCache;
    }
```

Now let's try editing an item and hitting `escape`. It works!

Finally, let's remove our original test item from the todo list:

```javascript {5}
  data() {
    return {
      newTodo: "",
      editedTodo: null,
      todos: []
    }
  },
```

Our final App.vue file should now look something like this:

```html
<template>
  <div>
		<section class="todoapp" v-cloak>
			<header class="header">
				<h1>todos</h1>
				<input class="new-todo" autofocus autocomplete="off" placeholder="What needs to be done?" v-model="newTodo" @keyup.enter="addTodo">
			</header>
			<section class="main" v-show="todos.length">
				<ul class="todo-list">
					<li class="todo" v-for="todo in todos" :key="todo.id" :class="{completed: todo.completed, editing: todo == editedTodo}">
						<div class="view">
              <input class="toggle" type="checkbox" v-model="todo.completed">
              <label @dblclick="editTodo(todo)">{{todo.title}}</label>
              <button class="destroy" @click="removeTodo(todo)"></button>
						</div>
            <input
              class="edit"
              type="text"
              v-model="todo.title"
              @keyup.enter="doneEdit(todo)"
              @blur="doneEdit(todo)"
              @keyup.esc="cancelEdit(todo)"
              v-todo-focus="todo == editedTodo"
            >
					</li>
				</ul>
			</section>
		</section>
  </div>
</template>

<script>
import "./assets/css/base.css";
import "./assets/css/app.css";
export default {
  name: "app",
  data() {
    return {
      newTodo: "",
      editedTodo: null,
      todos: []
    }
  },
  methods: {
    addTodo: function() {
      var value = this.newTodo && this.newTodo.trim();
      if (!value) {
        return;
      }
      this.todos.push({
        id: this.todos.length,
        title: value,
        completed: false
      });
      this.newTodo = "";
    },
    removeTodo: function(todo) {
      var index = this.todos.indexOf(todo);
      this.todos.splice(index, 1);

      for(var i=0; i<this.todos.length; i++){ 
        this.todos[i].id = i;
      }
    },

    editTodo: function(todo) {
      this.beforeEditCache = todo.title;
      this.editedTodo = todo;
    },

    doneEdit: function(todo) {
      if (!this.editedTodo) {
        return;
      }
      this.editedTodo = null;
      todo.title = todo.title.trim();
      if (!todo.title) {
        this.removeTodo(todo);
      }
    },

    cancelEdit: function(todo) {
      this.editedTodo = null;
      todo.title = this.beforeEditCache;
    }
  },
  directives: {
    "todo-focus": function(el, binding) {
      if (binding.value) {
        el.focus();
      }
    }
  }
};
</script>
```

Now that we have a basic todo app, let's build it into a desktop app by running `wails build` in the project's root directory. Once it has finished building, run the newly built `todos` binary. You should see something like this:

<div class="imagecontainer">
  <img src="/images/tododesktopapp1.png" style="width: 95%; border-radius: 5px;">
</div>

The problem we now have is that every time we start the app, we lose our previous list. Let's look at using Go to persist our list.

## Persistence using Go

Currently, our app is completely standalone from the backend. Our main app is simply wrapping the Vue project and displaying it. What we want to do is load our list up from disk when we start our app. To do that, we must be able to bridge our frontend and backend code. We do this using the Wails Bridge.

### Bridging Frontend and Backend

To load our list we will want to call a function in Go which loads a file from a default location and if it doesn't exist, it creates it with an empty list. 

To allow the frontend to talk to the backend, Wails provides a bridge. If you run `wails serve` in your project directory, you will see something like this:

<div class="imagecontainer">
  <img src="/images/todoserve1.png" style="width: 85%;">
</div>

This is now serving your backend Go functions and is waiting for the bridge to connect. We commented this out of `main.js` at the start, but now is the time to add it back in:

```javascript {7,9,13}
import Vue from "vue";
import App from "./App.vue";

Vue.config.productionTip = false;
Vue.config.devtools = true;

import Wails from "@wailsapp/runtime";

Wails.Init(() => {
  new Vue({
    render: h => h(App)
  }).$mount("#app");
});
```

Ensure your frontend is running. If not, run `npm run serve` in the frontend directory to start it. Your app will load as normal, but you may have noticed something flash up quickly. This is message to indicate that there is no connection with the backend. Very quickly, it establishes the connection and disappears. You can see this if you press `ctrl-c` in the terminal running `wails serve`. If you now look at your app, you will see something like this:

<div class="imagecontainer">
  <img src="/images/todobridgereconnect.png" style="width: 75%;">
</div>

The bridge will now be attempting to reconnect to the backend. If we run `wails serve` again, the dialog will disappear. This means the reconnection has happened. If you are uncertain, check the developer console which will have some log output from the bridge:

<div class="imagecontainer">
  <img src="/images/wailsbridgelog1.png" style="width: 75%;">
</div>

Now that we have established the connection, we can access the backend from the frontend. All bound functions are registered to the global `backend` object. The default template binds a simple function called `basic`. We can call this from the browser by calling `backend.basic()`. It is important to remember that every function on the `backend` object returns a [Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Using_promises). To quickly test this in the browser we can simply run `backend.basic().then(console.log)`. We get our response from our backend code:

<div class="imagecontainer">
  <img src="/images/wailsbridgecallbasic.png" style="width: 75%;">
</div>

Awesome! We now have an easy means to call our Go code, so let's get cracking with our Save.

## Saving the Todo List

Our todo store in the frontend is just an array with a number of javascript objects in it. We can serialise this to JSON, which makes it an easy thing to load and save. We want to save whenever there are any changes to this list, whether it's adding, removing or editing. Vue allows us to [watch properties](https://vuejs.org/v2/guide/computed.html#Watchers) and call a function when it changes. We do this by adding the `watch` property on the component:

```javascript {4-8}
      todos: []
    }
  },
  watch: {
    todos: function(todos) {
      console.log("Todo list: " + JSON.stringify(todos));
    }
  },
  methods: {
    addTodo: function() {
```

Now when we update the list, the list is printed to the console. 

Instead of just logging to the browser console, we will take our first step into using the Wails runtime from Javascript. It is accessible through the [@wailsapp/runtime](https://www.npmjs.com/package/@wailsapp/runtime) package and has some features that help with building your application:

  * Logging
  * Events
  * Browser

  To use the Runtime, we need to import it:

```javascript{5}
<script>
import "./assets/css/base.css";
import "./assets/css/app.css";

import Wails from '@wailsapp/runtime';

export default {
```

 Now, we will use the runtime logger to log an info line containing the current todo list. This log will be visible in the terminal serving the backend.

 ```javascript {4-8}
      todos: []
    }
  },
  watch: {
    todos: function(todos) {
      Wails.Log.Info("Todo list: " + JSON.stringify(todos));
    }
  },
  methods: {
    addTodo: function() {
```

If you now add a todo item, you should see something like this in your console:

<div class="imagecontainer">
  <img src="/images/wailsbridgelogging1.png" style="width: 75%;">
</div>

Adding more items will send the updated list to the backend to be printed:

<div class="imagecontainer">
  <img src="/images/wailsbridgelogging2.png" style="width: 95%;">
</div> 

Removing items also works:

<div class="imagecontainer">
  <img src="/images/wailsbridgelogging3.png" style="width: 95%;">
</div> 

Now we can easily write a function to save the todo list in Go. Initially, we will do this through a simple function in `main.go` (Also, let's remove the existing `basic` function):

```go {3-10}
)

func saveList(todos string) error {
	cwd, err := os.Getwd()
	if err != nil {
		return err
	}
	filename := path.Join(cwd, "mylist.json")
	return ioutil.WriteFile(filename, []byte(todos), 0600)
}

func main() {
```

This function simply calculates the current working directory, appends the filename "mylist.json" then saves the given todo list to it.

All that's left for us to now is to bind it to the application: 

```go {3}
		Colour: "#131313",
	})
	app.Bind(saveList)
	app.Run()
}
```

 To apply these changes, press `ctrl-c` in the terminal running `wails serve` and rerun the command. The frontend should reconnect, but if it doesn't, just reload the page in your browser.

 Now, the function `backend.saveList()` is available to us. As it simply accepts a string, we can test it right from the browser console:

 <div class="imagecontainer">
  <img src="/images/wailsbridgesaveListTest.png" style="width: 75%;">
</div> 

As discussed earlier, every call to the backend returns a [Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Using_promises) and we can see that the call worked because the status of the [Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Using_promises) is `resolved`. If you check your project directory, you will see there is a new file there called `mylist.json` containing the text `This is a test`. The last thing to do now is to call the save function each time the todoList is updated. We simply modify our `App.vue` component with the appropriate call:

```javascript {3}
  watch: {
    todos: function(todos) {
      window.backend.saveList(JSON.stringify(todos));
    }
  },
```

Now when we add items to our todo list, the list is written to `mylist.json`:

```json
[{"id":0,"title":"This is a test","completed":false}]
```

## Loading the Todo List

Each time we reload the application, we have an empty todo list. Now that we have a file containing our list, we can load this at start up. To do this we need to do 2 things:

  * Create a loadList in our Backend
  * When the component is loaded, call our backend function for a list 

For the first part, we will initially create a simple function in our `main.go` file:

```go {4-6}
	return ioutil.WriteFile(filename, []byte(todos), 0600)
}

func loadList() (string, error) {
	return "Load the list!", nil
}

func main() {
```

We also need to bind this new method to the application:

```go {5}
		CSS:    css,
		Colour: "#131313",
	})
	app.Bind(saveList)
	app.Bind(loadList)
	app.Run()
}
```

To call this from the frontend when the component is ready, we use the [mounted](https://vuejs.org/v2/api/#mounted) hook. This is a function on our component that is called when it is ready. We will add the following method to our component, which will call our new function and then log, via the Wails logger, what it received:

```go {8-12}
  directives: {
    "todo-focus": function(el, binding) {
      if (binding.value) {
        el.focus();
      }
    }
  },
  mounted() {
    window.backend.loadList().then((list) => {
      Wails.Log.Info("I got this list: " + list)
    });
  }
};
</script>
```

Stop and restart the backend running `wails serve`. You will notice that the loadList function has now been bound:

```bash {3}
INFO[0000] [Bind] Binding Go Functions/Methods          
INFO[0000] [Bind] Bound Function: main.saveList()       
INFO[0000] [Bind] Bound Function: main.loadList()       
INFO[0000] [Headless] Headless mode started.            
INFO[0000] [Headless] The Wails bridge will connect automatically. 
```

When you reload the page, you will notice the following in your terminal output:

```bash
INFO[0337] I got this list: Load the list!    
```
Great! Now let's update our loadList function to load the list file and send that back:

```go
func loadList() (string, error) {
	cwd, err := os.Getwd()
	if err != nil {
		return "", err
	}
	filename := path.Join(cwd, "mylist.json")
	result, err := ioutil.ReadFile(filename)
	return string(result), err
}
```
This is similar to our `saveList` function, but instead reads the file. Our `ioutil.ReadFile` function returns a byte array so the final line converts this into a string before returning it. Any errors are also returned (We will look at error handling) shortly.

Reserve the backend again and you will notice the following output:

```bash
INFO[0014] I got this list: []      
```

This is what is in our file (even if it is a blank list)! Last thing for us to do is to convert our loaded json into the todo list. We'll do the following updates to our `mounted` function:

```javascript
  mounted() {
    window.backend.loadList().then((list) => {
      Wails.Log.Info("I got this list: " + list)
      this.todos = JSON.parse(list);
    });
  }
```

Now every time we reload the page, the todo list is loaded! We can also see the list data that was sent from the backend to the frontend on the console. 

If your editor/IDE has automatic file reload, then open the `mylist.json` file and watch how it gets updated when you add or remove items. It's saved in a compact form which is sometimes hard to read so lets format the data saved slightly. We used `JSON.stringify` to turn the todo list into a string and it has an option to "pretty print", so let's change that in `App.vue`:

```javascript {3}
  watch: {
    todos: function(todos) {
      window.backend.saveList(JSON.stringify(todos, null, 2));
    }
  },
```
Now our `mylist.json` file will look something like this:

```json
[
  {
    "id": 0,
    "title": "I am a todo",
    "completed": false
  },
] 
```
One thing you may notice is that when you mark the item as complete, `mylist.json` isn't updated with `"completed": true`. The reason for that is that our watcher in Vue, by default, watches the array size for changes, not the elements within it. Vue offers a way to [customise this behaviour](https://vuejs.org/v2/api/#vm-watch) by providing an object describing how the watcher should work. Passing `deep: true` will indicate we want to watch for object changes within the array:

```javascript
  watch: {
    todos: {
      handler: function(todos) {
        window.backend.saveList(JSON.stringify(todos, null, 2));
      },
      deep: true
    }
  },
```

Now when we mark an item for selection, the `completed` variable for the item in `mylist.json` is updated accordingly.

## Error Handling

Now that we have most of the functionality, let's look at error handling. There are 2 placed that errors can occur: frontend and backend. When errors occur in the frontend, they are logged to the browser console, but when the app is packaged, there is no browser console, so we need to ensure we handle them correctly before packaging. Backend errors are a little easier to deal with, but we'll come to that.

### Frontend Errors

To demonstrate an error in the frontend, let's look at this piece of code:

```javascript
  mounted() {
    window.backend.loadList().then(list => {
      Wails.Log.Info("I got this list: " + list);
      this.todos = JSON.parse(list);
    });
  }
```

The [JSON.parse](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/parse) method with throw an error if the structure of the JSON isn't correct. We don't handle this case so it will error. Let's see what happens if we edit our `mylist.json` directly to contain the following and reload the page:

```
I am not a JSON file
```

In the browser console, in the `console` tab, you'll see something like this:

 <div class="imagecontainer">
  <img src="/images/wailsbridgebadjson.png" style="width: 75%;">
</div> 

The problem is, there's no indication in the app that an error occurred. We need to capture the error if it occurs and show a message to the user.

The standard way to catch [errors](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Error) in Javascript is to use [try/catch](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/try...catch) blocks. We can do that to catch the error and display an error to the user. Initially, let's just print it out to our console:

```javascript {4-8}
  mounted() {
    window.backend.loadList().then(list => {
      try {
        this.todos = JSON.parse(list);
      } catch (e) {
        Wails.Log.Info("An error was thrown: " + e.message);
      }
    });
  }
```

You should see something like this appear in your console:

```bash
INFO[3081] An error was thrown: Unexpected token I in JSON at position 0
```

Now that we are capturing the error, let's show a message to the user. We will do this by having a new component data property called 'errorMessage' and whenever that is set, we will show it. Let's first define the property, with a test error message

```javascript {5}
  data() {
    return {
      newTodo: "",
      editedTodo: null,
      errorMessage: "Guru Meditation",
      todos: []
    };
  },
```

Next, let's create an error box in the template. We want to only show it if `errorMessage` is not an empty string, so we will use the [v-if]() directive. This will only display the element it is attached to if the condition is met.

Let's add a new div for the error message in our template:

```html {3}
<template>
  <div>
    <h2 v-if="errorMessage.length > 0">{{errorMessage}}</h2>
    <section class="todoapp" v-cloak>
      <header class="header">
        <h1>todos</h1>
```

This simply displays an `h2` element containing the error message when the error message has a length greater than 0, IE: is set.

It doesn't look good, so let's style it. As App.vue is a [single file component](https://vuejs.org/v2/guide/single-file-components.html), we can simply add a `<style>` section to the component:

```css
<style>
h2 {
  text-align: center;
  color: white;
  background-color: red;
  min-width: 230px;
  max-width: 550px;
  padding: 1rem;
  border-radius: 0.5rem;
}
</style>
```

Now when you reload, you will see something like this:

<div class="imagecontainer">
  <img src="/images/todoerrormessage.png" style="width: 75%;">
</div> 

To test the error message (if you've installed the Vue dev tools), open up the inspector, select the Vue tab and select the `App` component. You may have to click `refresh` if it doesn't appear. Using the inbuilt property editor, you can set the message in realtime. Change the error message to an empty string and you should see the message disappear.

Now that we have a way of raising an error to the user, let's set the default error message to blank string:

```javascript {5}
  data() {
    return {
      newTodo: "",
      editedTodo: null,
      errorMessage: "",
      todos: []
    };
  },
```

Finally, let's update our error handling to show an error message:

```javascript {6}
  mounted() {
    window.backend.loadList().then(list => {
      try {
        this.todos = JSON.parse(list);
      } catch (e) {
        this.errorMessage = "Unable to load todo list";
      }
    });
  }
```

Now when we start the app, we get the error message telling us the todolist can't be parsed. 
It would be pretty annoying to keep that message there so let's use [setTimeout](https://developer.mozilla.org/en-US/docs/Web/API/WindowOrWorkerGlobalScope/setTimeout) to call a function after 3 seconds to reset the error message:

```javascript {7-9}
  mounted() {
    window.backend.loadList().then(list => {
      try {
        this.todos = JSON.parse(list);
      } catch (e) {
        this.errorMessage = "Unable to load todo list";
        setTimeout(() => {
          this.errorMessage = "";
        }, 3000);
      }
    });
  }
```

Reload and watch the error message disappear after 3 seconds! Of course, you may not wish to display an error message and simply set the the todos to an empty list by default. That's entirely up to you 😉

### Backend Errors

Errors can also occur in the backend and Wails provides a simple strategy for handling them. But first let's discuss Javascript Promises. As we discussed earlier, all backend functions that are bound to the application are available in the frontend as functions that return Promises. Promises are functions that eventually indicate whether they have been successful or whether they have failed. The terminology for this is Resolve (success) and Reject (error). There are a number of ways to work with Promises. The first is to tell the Promise what function to call in the event of success (then) and which function should handle the errors (catch). 

```javascript
  promiseFunction().then(mySuccessFunction).catch(myErrorHandler);
```

Another way is by using the [async](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function) and [await](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/await) keywords. Quite simply, if a function is declared with the `async` keyword, you can use the `await` keyword within its body. What `await` allows you to do is simply write your function call as if it were a standard function. To catch the errors, you use a try/catch block:

```javascript
async function myfunction() {
  try {
    let result = await promiseFunction();
  } catch(e) {
    // e contains the error
  }
}
```
As you can see, this approach can be a bit easier to read and to reason about. The way you deal with Promises is entirely up to you. The important thing from our point of view is that you understand them. 

So what does this have to do with our backend? Well, it just so happens that Go has a standard way of dealing with errors that is a bit similar: Errors are returned by the functions that the error occurred in, to be handled at the appropriate level. This leads to functions very similar to this:

```go
func myFunction() (string, error) {
  var result string
  var err error

  // Do some processing
  
  return result, err 
}
```
When you bind a function to your application, Wails will map the result of the call directly to a Promise. This means that if you return an error, the Promise in Javascript will reject. If the call is successful, the Promise will resolve. Here's a concise example:

Let's create a backend function that takes a boolean value and will either succeed or fail based on its value. Place it in your `main.go` file and remember to bind it to your app:

```go
func ErrorOrSuccess(success bool) (string, error) {
  if success {
    return "I was successful", nil
  } else {
    return "", fmt.Errorf("i am an error")
  }
}

func main() {
...
...
   app.Bind(ErrorOrSuccess)
...
```

Re-run `wails serve` so that the Go function registers with the frontend. You can see by the logs if this has happened:

```bash
INFO[0000] [Bind] Bound Function: main.ErrorOrSuccess() 
```

Let's now simply open our browser console and call the function:

```javascript
backend.ErrorOrSuccess(true)
```
You should see something like this:

<div class="imagecontainer">
  <img src="/images/todopromises1.png" style="width: 75%;">
</div> 

If you call it with `false`, you will see this instead:

<div class="imagecontainer">
  <img src="/images/todopromises2.png" style="width: 75%;">
</div> 

So now you can treat your backend code like any modern Javascript async function. Neat huh? 

Of course, sending errors back from the backend is optional - Wails will map what it is given and if there's no error, then the promise will only resolve, not reject.

So let's see this in action! You may remember that our `loadList` function was also defined to return an error:

```go
func loadList() (string, error) {
	cwd, err := os.Getwd()
	if err != nil {
		return "", err
	}
	filename := path.Join(cwd, "mylist.json")
	bytes, err := ioutil.ReadFile(filename)
	var result = string(bytes)
	return result, err
}
```

There are 2 circumstances we return an error: either the current working directory doesn't exist (tricky!) or we are unable to read the default file. So what happens if we simply delete our `mylist.json` and reload our browser window? The browser console will tell you!

<div class="imagecontainer">
  <img src="/images/todoloaderror1.png" style="width: 75%;">
</div> 

That's a standard Go error, right there in your browser console. You may notice it says "Uncaught (in promise)". That's true, we did't catch this so the error has gone rogue. That's because we aren't handling the error case for our loadList function in `App.vue`. Let's fix that:

```javascript {14-16}
  mounted() {
    window.backend
      .loadList()
      .then(list => {
        try {
          this.todos = JSON.parse(list);
        } catch (e) {
          this.errorMessage = "Unable to load todo list";
          setTimeout(() => {
            this.errorMessage = "";
          }, 3000);
        }
      })
      .catch(error => {
        this.errorMessage = error;
      });
  }
```
Now our app shows the following message:

<div class="imagecontainer">
  <img src="/images/todoloaderror2.png" style="width: 75%;">
</div> 

Let's shut that down after 3 seconds as well:

```javascript {3-5}
      .catch(error => {
        this.errorMessage = error;
        setTimeout(() => {
          this.errorMessage = "";
        }, 3000);
      });
```

Let's edit loadList to make the message a bit better by checking for an error, then rewriting it:

```go {8-10}
func loadList() (string, error) {
	cwd, err := os.Getwd()
	if err != nil {
		return "", err
	}
	filename := path.Join(cwd, "mylist.json")
	bytes, err := ioutil.ReadFile(filename)
	if err != nil {
		err = fmt.Errorf("Unable to open list: %s", filename)
	}
	var result = string(bytes)
	return result, err
}
```

Re-run `wails serve` and refresh your app (resets the 3 second timer):

<div class="imagecontainer">
  <img src="/images/todoloaderror3.png" style="width: 75%;">
</div> 

Of course, as soon as we add a todo, the file will get written again and all will be good again with the world...

Now that we've covered error handling, let's look at how we typically structure the backend - namely, using structs.

## Using Structs

Whilst our backend currently works, it's not perfect. For one, there is duplicate code in both loadList and saveList. Whilst we could create yet another function to handle this, as the app grows, it becomes a unmanageable to keep everything in the `main.go` file.

What we are going to do is move both of these functions into a common [struct](https://gobyexample.com/structs). Let's start by creating a new file in the project directory called `todos.go` and create a basic struct in there:

```go
package main

type Todos struct {
	filename string
}
```
This defines a struct with a single member variable to hold our todolist filename. Next let's create a function in `todos.go` to create a Todo struct and calculate our filename:

```go
// NewTodos attempts to create a new Todo list
func NewTodos() (*Todos, error) {
	// Create new Todos instance
	result := &Todos{}
	// Try and get the current working directory
	cwd, err := os.Getwd()
	if err != nil {
		return nil, err
	}
	// Join the cwd with our todos filename
	filename := path.Join(cwd, "mylist.json")
	// Set the filename member of our new Todo list
	result.filename = filename
	// Return it
	return result, nil
}
```
This function creates a new Todos struct, then attempts to calculate the path to the json file. If it fails, it returns an error. If it works, it sets the filename in the newly created Todos and returns it.

We have essentially copied our common code from `loadList` and `saveList`, but it is run only once.

We will create a new Todo struct on application startup so let's add some code to our `main.go`

Now let's convert our `loadList` method into a struct method. Let's first copy the whole function out of `main.go`:

```go
func loadList() (string, error) {
	cwd, err := os.Getwd()
	if err != nil {
		return "", err
	}
	filename := path.Join(cwd, "mylist.json")
	bytes, err := ioutil.ReadFile(filename)
	if err != nil {
		err = fmt.Errorf("Unable to open list: %s", filename)
	}
	var result = string(bytes)
	return result, err
}
```

Then let's make it a [method](https://gobyexample.com/methods) of the struct by adding the [receiver](https://tour.golang.org/methods/4) to the declaration:

```go {1}
func (t *Todos) loadList() (string, error) {
	cwd, err := os.Getwd()
	if err != nil {
		return "", err
	}
	filename := path.Join(cwd, "mylist.json")
	bytes, err := ioutil.ReadFile(filename)
	if err != nil {
		err = fmt.Errorf("Unable to open list: %s", filename)
	}
	var result = string(bytes)
	return result, err
}
```

We can now get rid of the code we used to calculate our filename, and simply refer to the filename that is calculated when we create a Todos instance. This simplifies the code:

```go
func (t *Todos) loadList() (string, error) {
	bytes, err := ioutil.ReadFile(t.filename)
	if err != nil {
		err = fmt.Errorf("Unable to open list: %s", t.filename)
	}
  return string(bytes), err
}
```

We now go through the same process with `saveList`. Copy and paste it from `main.go`, add the receiver and remove the filename code. This becomes radically simpler, going from:

```go
func saveList(todos string) error {
	cwd, err := os.Getwd()
	if err != nil {
		return err
	}
	filename := path.Join(cwd, "mylist.json")
	return ioutil.WriteFile(filename, []byte(todos), 0600)
}
```

to:

```go
func (t *Todos) saveList(todos string) error {
	return ioutil.WriteFile(t.filename, []byte(todos), 0600)
}
```

Our complete `todos.go` should now look like this:

```go
package main

import (
	"fmt"
	"io/ioutil"
	"os"
	"path"
)

type Todos struct {
	filename string
}

// NewTodos attempts to create a new Todo list
func NewTodos() (*Todos, error) {
	// Create new Todos instance
	result := &Todos{}
	// Try and get the current working directory
	cwd, err := os.Getwd()
	if err != nil {
		return nil, err
	}
	// Join the cwd with our todos filename
	filename := path.Join(cwd, "mylist.json")
	// Set the filename member of our new Todo list
	result.filename = filename
	// Return it
	return result, nil
}

func (t *Todos) loadList() (string, error) {
	bytes, err := ioutil.ReadFile(t.filename)
	if err != nil {
		err = fmt.Errorf("Unable to open list: %s", t.filename)
	}
	return string(bytes), err
}

func (t *Todos) saveList(todos string) error {
	return ioutil.WriteFile(t.filename, []byte(todos), 0600)
}
```

Now let's attach our new struct to our application! First, let's remove the ErrorOrSuccess function as it's now not needed. Next, we will create an instance of our Todos instance and bind it to the app:

```go {4,15-18,28}
package main

import (
	"log"

	"github.com/leaanthony/mewn"
	"github.com/wailsapp/wails"
)

func main() {

	js := mewn.String("./frontend/dist/app.js")
	css := mewn.String("./frontend/dist/app.css")

	myTodoList, err := NewTodos()
	if err != nil {
		log.Fatal(err)
	}

	app := wails.CreateApp(&wails.AppConfig{
		Width:  1024,
		Height: 768,
		Title:  "todos",
		JS:     js,
		CSS:    css,
		Colour: "#131313",
	})
	app.Bind(myTodoList)
	app.Run()
}
```
We add an import to log in line 4 as we may need to log an error. Line 15 calls our `NewTodos` function and lines 16-18 logs an error if it was unsuccessful.
We final bind our new struct to the app. It's worth noting that only struct instances may be bound to your app, not struct definitions. For example, this would have been invalid:

```go
   app.Bind(Todos)
```

If we now re-run `wails serve` we notice that there is no messages indicating that Todos has been bound. What happened? When binding structs to your app, Wails only binds those struct methods that are marked as [exported](https://www.callicoder.com/golang-structs/#exported-vs-unexported-structs-and-struct-fields). Struct method names that start with a capital letter are designated exported. This is a standard Go feature and Wails adheres to this model. It means we can create internal functions that are not exposed to the frontend.

Let's capitalise the load and save method names and re-run `wails serve`:

```bash {7,8}
INFO[0000] [App] Starting                               
INFO[0000] [Events] Starting                            
INFO[0000] [Events] Listening                           
INFO[0000] [IPC] Starting                               
INFO[0000] [Bind] Starting                              
INFO[0000] [Bind] Binding Go Functions/Methods          
INFO[0000] [Bind] Bound Method: main.Todos.LoadList()   
INFO[0000] [Bind] Bound Method: main.Todos.SaveList()   
INFO[0000] [Headless] Headless mode started.            
INFO[0000] [Headless] The Wails bridge will connect automatically. 
INFO[0000] [Headless] Connection from frontend accepted [0xc000282000]. 
INFO[0000] [Headless] Connected to frontend.            
>>>>> To connect, you will need to run 'npm run serve' in the 'frontend' directory <<<<<
```
Now we can see that our methods have been bound. Let's update our frontend to use these new methods.

In the same way functions are bound to the `backend` object in the frontend, structs are too. They are bound by name, so the `Todos` struct we have bound is accessible via `backend.Todos`. As expected, our `LoadList` and `SaveList` methods are available at `backend.Todos.LoadList` and `backend.Todos.SaveList` respectively.

We now need to update `App.vue` to use these new methods. Let's start with `LoadList`:

```javascript {2,3}
  mounted() {
    window.backend.Todos
      .LoadList()
      .then(list => {
        try {
          this.todos = JSON.parse(list);
        } catch (e) {
```
Now lets update `SaveList`:

```javascript{4}
  watch: {
    todos: {
      handler: function(todos) {
        window.backend.Todos.SaveList(JSON.stringify(todos, null, 2));
      },
      deep: true
    }
  },
```
Once we save this, we should see that the app works as expected. Add a couple of todos and check `mylist.json`. You will see it's getting updated correctly.

You're probably wondering why go to the effort of using a struct if the functionality is the same? That's a good question and there's a number of reasons including separation of concerns, modularisation, it's common practice in Go... but the main reason, is that it allows you to gain access to the Wails runtime in Go, and this provides some cool things which we are about to use...

## Wails Runtime in Go

Just as there is a Javascript runtime available to the frontend, there is a [Go runtime](https://godoc.org/github.com/wailsapp/wails#Runtime) available to structs. This provides the following features:

  * [Logging](https://godoc.org/github.com/wailsapp/wails#RuntimeLog)
  * [Events](https://godoc.org/github.com/wailsapp/wails#RuntimeEvents)
  * [Dialogs](https://godoc.org/github.com/wailsapp/wails#RuntimeDialog)
  * [Window Control](https://godoc.org/github.com/wailsapp/wails#RuntimeWindow)
  * [FileSystem](https://godoc.org/github.com/wailsapp/wails#RuntimeFileSystem)

To access this runtime, we create a struct method called "WailsInit". This method requires the following signature:

```go
WailsInit(runtime *wails.Runtime) error {
  // Initialisation code
}
```
There are multiple purposes for this function:

  * Performing initialisation tasks on structs
  * Allow structs to raise an error if something went wrong during setup
  * Presenting the runtime to the struct

Let's start by updating our struct so we keep a reference to the runtime:

```go {3}
type Todos struct {
	filename string
	runtime  *wails.Runtime
}
```
If you aren't familiar with why there is an asterisk in front of "wails.Runtime", it would be worth reading [this](https://tour.golang.org/moretypes/1). 

Now that we have a place to store the runtime, let's create our `WailsInit` method in the same file:

```go
func (t *Todos) WailsInit(runtime *wails.Runtime) error {
	t.runtime = runtime
	return nil
}
```
If we re-run our app, we will so there is nothing different, so let's use the runtime logger to output a message:

```go {3,4}
func (t *Todos) WailsInit(runtime *wails.Runtime) error {
	t.runtime = runtime
	myLog := t.runtime.Log.New("Todos")
	myLog.Info("I'm here")
	return nil
}
```
Line 3 creates a brand new Logger and we give it the prefix "Todos". The struct can now use this like the frontend logger, which we do in line 4. If we re-run our backend, we will see the following line:

```bash {6}
INFO[0000] [IPC] Starting                               
INFO[0000] [Bind] Starting                              
INFO[0000] [Bind] Binding Go Functions/Methods          
INFO[0000] [Bind] Bound Method: main.Todos.LoadList()   
INFO[0000] [Bind] Bound Method: main.Todos.SaveList()   
INFO[0000] [Todos] I'm here                             
INFO[0000] [Headless] Headless mode started.            
INFO[0000] [Headless] The Wails bridge will connect automatically. 
INFO[0000] [Headless] Connection from frontend accepted [0xc0002aa000]. 
INFO[0000] [Headless] Connected to frontend.    
```
As you can see, we have a nicely labelled message. We also can see that it is run straight after all the binding. The logging functions in the Go runtime and the Javascript runtime call the same code behind the scenes. This gives us a unified logging mechanism. Let's keep a reference to the logger in the main struct:

```go {4}
type Todos struct {
	filename string
	runtime  *wails.Runtime
	logger   *wails.CustomLogger
}
```
 And we'll update our `WailsInit` method to use it:

 ```go
 func (t *Todos) WailsInit(runtime *wails.Runtime) error {
	t.runtime = runtime
	t.logger = t.runtime.Log.New("Todos")
	t.logger.Info("I'm here")
	return nil
}
```

We can now use this in our LoadList and SaveList methods:

```go {2,11}
func (t *Todos) LoadList() (string, error) {
	t.logger.Infof("Loading list from: %s", t.filename)
	bytes, err := ioutil.ReadFile(t.filename)
	if err != nil {
		err = fmt.Errorf("Unable to open list: %s", t.filename)
	}
	return string(bytes), err
}

func (t *Todos) SaveList(todos string) error {
	t.logger.Infof("Saving list: %s", todos)
	return ioutil.WriteFile(t.filename, []byte(todos), 0600)
}
```
Now we have a means of logging, we can log anything we like there. Explore the [Logger documentation](https://godoc.org/github.com/wailsapp/wails#CustomLogger) for more details.

## Event Handling

Now that we have access to the Runtime in Go, we have access to the [Events](https://godoc.org/github.com/wailsapp/wails#RuntimeEvents) subsystem. The really powerful thing about Wails Events is that it is a unified event bus between Javascript and Go. This means that you can listen for an event in one, emit it in the other and it will work as expected. You can even pass data with events!

We will demonstrate this by allowing the backend to send an error message, and the frontend will display it in the existing error box we created earlier. First, let's update `App.vue` so that we listen for an "error" event each time we mount the app:

```javascript{2-7}
  mounted() {
    Wails.Events.On("error", message => {
      this.errorMessage = message;
      setTimeout(() => {
        this.errorMessage = "";
      }, 3000);
    });
    window.backend.Todos.LoadList()
      .then(list => {
        try {
```
This function will set the `errorMessage` property to the given message and then hide it after 3 seconds. This is the same code that we use when handling the JSON parse error. Save the file and open up the browser console. Let's test this code by using the Javascript Wails Runtime to emit the event. Type:

```javascript
window.wails.Events.Emit("error", "I am a message from Javascript!")
```
You should see the error message pop up for 3 seconds then disappear.

Now let's emit the same event from the backend:

```go {7}
func (t *Todos) LoadList() (string, error) {
	t.logger.Infof("Loading list from: %s", t.filename)
	bytes, err := ioutil.ReadFile(t.filename)
	if err != nil {
		err = fmt.Errorf("Unable to open list: %s", t.filename)
	}
	t.runtime.Events.Emit("error", "I am a message from Go!")
	return string(bytes), err
}
```
<div class="imagecontainer">
  <img src="/images/todogoevent1.png" style="width: 75%">
</div>

Emit accepts an arbitrary number of arguments and that data will be passed to any listeners in the order it was given. To demonstrate, let's send a number back with the event. On the frontend, we'll read that number, double it, then print it with the message.

Let's update our backend:

```go {7}
func (t *Todos) LoadList() (string, error) {
	t.logger.Infof("Loading list from: %s", t.filename)
	bytes, err := ioutil.ReadFile(t.filename)
	if err != nil {
		err = fmt.Errorf("Unable to open list: %s", t.filename)
	}
	t.runtime.Events.Emit("error", "I am a message from Go!", 1234)

	return string(bytes), err
}
```

Now let's update our event handler in `App.vue` to process the number:

```javascript {2-4}
  mounted() {
    Wails.Events.On("error", (message, number) => {
      let result = number * 2;
      this.errorMessage = `${message}: ${result}`;
      setTimeout(() => {
        this.errorMessage = "";
      }, 3000);
    });
```

You should now see something similar when the app starts:

<div class="imagecontainer">
  <img src="/images/todogoevent2.png" style="width: 75%">
</div>

This mechanism allows real flexibility in how you structure your application. 
In our instance, we will leave the error handling as is and we will look at using events for a different task: tracking file changes.

## Tracking File Changes

The real purpose of [Events](https://godoc.org/github.com/wailsapp/wails#RuntimeEvents) is to notify your application when something happens. As an example of this, we will run a filewatcher and let the frontend know when it has changed. To do this, we will be using the 3rd party library [fsnotify](https://github.com/fsnotify/fsnotify). To get started, let's install it using a terminal in the project root directory:

```bash
go get github.com/fsnotify/fsnotify
```

Next let's update our `todos.go` file. What we want to do is start a filewatcher during initialisation and listen for when the file is modified. We'll start by adding a private Method to our struct. This will start the watcher and listen for changes on our `mylist.json` file. It is a modified version of the fsnotify [example](https://github.com/fsnotify/fsnotify/blob/master/example_test.go):

```go
func (t *Todos) startWatcher() error {
	t.logger.Info("Starting Watcher")
	watcher, err := fsnotify.NewWatcher()
	if err != nil {
		return err
	}

  go func() {
		for {
			select {
			case event, ok := <-watcher.Events:
				if !ok {
					return
				}
				if event.Op&fsnotify.Write == fsnotify.Write {
					t.logger.Infof("modified file: %s", event.Name)
				}
			case err, ok := <-watcher.Errors:
				if !ok {
					return
				}
				t.logger.Error(err.Error())
			}
		}
	}()

	err = watcher.Add(t.filename)
	if err != nil {
		return err
	}
	return nil
}
```

If your IDE hasn't already done so, add the fsnotify import line:

```go {9}
package main

import (
	"fmt"
	"io/ioutil"
	"os"
	"path"

	"github.com/fsnotify/fsnotify"
	"github.com/wailsapp/wails"
)
```

The last thing we need to do now is to start the watcher when the struct is initialised:

```go {5}
func (t *Todos) WailsInit(runtime *wails.Runtime) error {
	t.runtime = runtime
	t.logger = t.runtime.Log.New("Todos")
	t.logger.Info("I'm here")
	return t.startWatcher()
}
```

As `startWatcher` returns a single error, we can just return whatever it returns. If you now re-serve the application, you should see some output:

```bash
INFO[0000] [Bind] Starting                              
INFO[0000] [Bind] Binding Go Functions/Methods          
INFO[0000] [Bind] Bound Method: main.Todos.LoadList()   
INFO[0000] [Bind] Bound Method: main.Todos.SaveList()   
INFO[0000] [Todos] I'm here                             
INFO[0000] [Todos] Starting Watcher    
```
Great! Now our watcher is running, try modifying the `mylist.json` file. When you save, you will notice a message:

```bash
INFO[0005] [Todos] modified file: /Users/lea/Projects/todos/mylist.json 
```
Cool! Now we are picking up the modified event, let's notify our frontend by emitting an event:

```go {6}
				if !ok {
					return
				}
				if event.Op&fsnotify.Write == fsnotify.Write {
					t.logger.Infof("modified file: %s", event.Name)
					t.runtime.Events.Emit("filemodified")
				}
			case err, ok := <-watcher.Errors:
				if !ok {
					return
        }
```
Now let's get the frontend to listen for the event. Let's edit `App.vue`:

```javascript {2-7}
  mounted() {
    Wails.Events.On("filemodified", () => {
      this.errorMessage = "File Modified";
      setTimeout(() => {
        this.errorMessage = "";
      }, 3000);
    });
    Wails.Events.On("error", (message, number) => {
      let result = number * 2;
      this.errorMessage = `${message}: ${result}`;
```

Try editing `mylist.json` and see what happens when you save. You should see something like this:

<div class="imagecontainer">
  <img src="/images/todonotify1.png" style="width: 75%">
</div>

Let's take a second to do some small refactors. We have code that sets the error message duplicated in 3 places. Let's create a component method to do this:

```javascript {5-10}
    cancelEdit: function(todo) {
      this.editedTodo = null;
      todo.title = this.beforeEditCache;
    },
    setErrorMessage: function(message) {
      this.errorMessage = message;
      setTimeout(() => {
        this.errorMessage = "";
      }, 3000);
    }
  },
```

Now we can simplify our `mounted` method:

```javascript {2-21}
  mounted() {
    Wails.Events.On("filemodified", () => {
      this.setErrorMessage("File Modified");
    });

    Wails.Events.On("error", (message, number) => {
      let result = number * 2;
      this.setErrorMessage(`${message}: ${result}`);
    });
    
    window.backend.Todos.LoadList()
      .then(list => {
        try {
          this.todos = JSON.parse(list);
        } catch (e) {
          this.setErrorMessage("Unable to load todo list");
        }
      })
      .catch(error => {
        this.setErrorMessage(error.message);
      });
  }
```

As we will be reusing the loadlist function, we'll move it from `mounted()` to the `methods` section of the component:

```javascript {7-19}
    setErrorMessage: function(message) {
      this.errorMessage = message;
      setTimeout(() => {
        this.errorMessage = "";
      }, 3000);
    },
    loadList: function() {
      window.backend.Todos.LoadList()
        .then(list => {
          try {
            this.todos = JSON.parse(list);
          } catch (e) {
            this.setErrorMessage("Unable to load todo list");
          }
        })
        .catch(error => {
          this.setErrorMessage(error.message);
        });
    }
  },
  directives: {
```
We now call this method from `mounted`:

```javascript {11-12}
  mounted() {
    Wails.Events.On("filemodified", () => {
      this.setErrorMessage("File Modified");
    });

    Wails.Events.On("error", (message, number) => {
      let result = number * 2;
      this.setErrorMessage(`${message}: ${result}`);
    });

    // Load the list at the start
    this.loadList();
  }
};
```

Now there's a small issue we need to address. We have a watcher listening for changes to the file and when it's updated it notifies the frontend that it has been modified. The frontend then reloads it. But when it updates the todo list, it also saves automatically. See the problem here?

Let's use a variable to indicate we are loading and therefore don't need to resave:

```javascript {6}
  data() {
    return {
      newTodo: "",
      editedTodo: null,
      errorMessage: "",
      loading: false,
      todos: []
    };
  },
```

Let's modiy our watcher to only write if we aren't loading:

```javascript {4-7}
 watch: {
    todos: {
      handler: function(todos) {
        if (this.loading) {
          this.loading = false;
          return;
        }
        window.backend.Todos.SaveList(JSON.stringify(todos, null, 2));
      },
      deep: true
    }
  },
```

Now let's update our `loadlist` method to set the loading flag after we load the list:

```javascript {5-7}
    loadList: function() {
      window.backend.Todos.LoadList()
        .then(list => {
          try {
            let todos = JSON.parse(list);
            this.loading = true;
            this.todos = todos;
          } catch (e) {
            this.setErrorMessage("Unable to load todo list");
          }
        })
        .catch(error => {
          this.setErrorMessage(error.message);
        });
    }
  },
```
Now the final part is to call loadList when the file has been updated:

```javascript {3}
  mounted() {
    Wails.Events.On("filemodified", () => {
      this.loadList();
    });

    Wails.Events.On("error", (message, number) => {
```

Now if you edit your JSON list outside the app, it gets reflected in realtime in the app!

This change has a side effect: the edit todo has now started exiting after every keypress. Why is this? Well, when we edit a todo item, we are updating the value of the todo item after every keypress (default behaviour of Vue's data binding). After the first keypress, the todo list data will change, our watcher will then trigger a save. Because the file has been modified, it will then trigger a load. The load will update the todo list and the editing will stop. What we want is to only update the value of the todo's title when we finish editing, not every keypress. This can be achieved through Vue's [lazy modifier](https://vuejs.org/v2/guide/forms.html#lazy). This will only sync the value after a change event, not an input event. Let's update our template:

```html {4}
            <input
              class="edit"
              type="text"
              v-model.lazy="todo.title"
              @keyup.enter="doneEdit(todo)"
              @blur="doneEdit(todo)"
              @keyup.esc="cancelEdit(todo)"
              v-todo-focus="todo == editedTodo"
            >
```
Retest the app, and you should see it now works as intended. 

Now that the app appears to be functioning well, let's build it as a native app.

## Building the App

To build the app we simply have to run `wails build` in your project directory. This will produce a binary in the current directory. If you run it, you should see something like this:

<div class="imagecontainer">
  <img src="/images/todoapp1.png" style="width: 95%">
</div>

Congratulations! You've made a single binary native app using Go and Vue!

However, what happens if you move the binary, say, to `/tmp` and run it from there? Whoah! We get an error:

```bash
ERRO[0000] [App] lstat /tmp/mylist.json: no such file or directory 
```
It's true, there's no `mylist.json`, but didn't we make it so that it will create it if it didn't exist? Yes we did, however, let's take a closer look at our struct initialisation:

```go
func (t *Todos) WailsInit(runtime *wails.Runtime) error {
	t.runtime = runtime
	t.logger = t.runtime.Log.New("Todos")
	t.logger.Info("I'm here")
	return t.startWatcher()
}
```
We setup the logger then start the file watcher. Oh. The file watcher. After we set it up, we add the file we want to watch:

```go {5-8}
			}
		}
	}()

	err = watcher.Add(t.filename)
	if err != nil {
		return err
	}
	return nil
}
```
Because the file doesn't exist, it returns an error, and this is what we are seeing. We need to test if the file exists and create a default one if it doesn't. Let's create a new method on our struct:

```go
func (t *Todos) ensureFileExists() {
	// Check status of file
	_, err := os.Stat(t.filename)
	// If it doesn't exist
	if os.IsNotExist(err) {
		// Create it with a blank list
		ioutil.WriteFile(t.filename, []byte("[]"), 0600)
	}
}
```
Now we just need to call this method before starting the watcher:

```go {5}
func (t *Todos) WailsInit(runtime *wails.Runtime) error {
	t.runtime = runtime
	t.logger = t.runtime.Log.New("Todos")
	t.logger.Info("I'm here")
	t.ensureFileExists()
	return t.startWatcher()
}
```

Now if we rebuild the app and copy it to a different directory, we can see that the app starts and it creates a new default list in the same directory. 

### Debug Build

In some cases, it may be desirable to debug the native application rather than using a web browser via the `wails serve` command. This is done by creating a debug build of your application.

To create a debug build we simply run `wails build -d`. A debug version of the app means the app will now:
  * Log messages to the console
  * Enable the webview inspector (Mac/Linux)

Now you will be able to right click and use the native inspector to debug your app as well as inspect the log messages.

## A Better List Location

It's not often practical, or desirable, to keep data files in the same location as the binary. Often the directory containing the binary is not even writable. So we will change the default location of our list to the user's home directory. This is a fairly simple change and Wails offers an easy way to get this directory using the [HomeDir](https://godoc.org/github.com/wailsapp/wails#RuntimeFileSystem.HomeDir) method exposed by the runtime. We need to do this before starting the watcher so let's add it to `WailsInit`:

```go {6-11}
func (t *Todos) WailsInit(runtime *wails.Runtime) error {
	t.runtime = runtime
	t.logger = t.runtime.Log.New("Todos")
	t.logger.Info("I'm here")

	// Set the default filename to $HOMEDIR/mylist.json
	homedir, err := runtime.FileSystem.HomeDir()
	if err != nil {
		return err
	}
	t.filename = path.Join(homedir, "mylist.json")

	t.ensureFileExists()
	return t.startWatcher()
}
```

Now if we rebuild the app and run it, we should see a blank list, as it will be a new one. Add an item to the list. Now copy the binary to a different directory and re-run it. You will see that it displays the same list.

For a bit of fun, run two copies of the app in different directories. If you edit the list in one app, it will instantly appear in the other! As we update our list for any changes, and we are listening to file changes, both apps are perfectly in sync.

## Save As

So far, our app only deals with one list. Wouldn't it be great if it dealt with multiple lists? To achieve this, we need to do the following:

  * Have a "Save As" button
  * Show a Save dialog for the user to input a filename
  * Save the current todo list into this file
  * Update the filewatcher

Let's implement it in that order.

### Save As Button

We will add a button at the top of the list, above the input box. We will reuse some of the existing styles from the mvctodo app:

```javascript {3-9}
      <header class="header">
        <h1>todos</h1>
        <div class="buttons">
          <ul class="filters">
            <li>
              <a @click="saveAs">Save As</a>
            </li>
          </ul>
        </div>
        <input
          class="new-todo"
          autofocus
          autocomplete="off"
```
We also add a listener for the click event using `@click`. We want to call the `saveAs` method when it is clicked. For now, we will just display a message that it has been clicked. Let's add that now:

```javascript {5-7}
    cancelEdit: function(todo) {
      this.editedTodo = null;
      todo.title = this.beforeEditCache;
    },
    saveAs: function() {
      this.setErrorMessage("Saving As...");
    },
    setErrorMessage: function(message) {
      this.errorMessage = message;
      setTimeout(() => {
```
Finally, let's add some styling to the `<style>` section:

```css {12-40}
<style>
h2 {
  text-align: center;
  color: white;
  background-color: red;
  min-width: 230px;
  max-width: 550px;
  padding: 1rem;
  border-radius: 0.5rem;
}

.buttons {
  height: 20px;
  padding: 10px 20px;
  box-shadow: inset 0 -2px 1px rgba(0, 0, 0, 0.1);
  text-align: center;
  border-color: rgba(175, 47, 47, 0.2);
}

.buttons ul li a {
  margin: 10px;
}

.buttons li {
  border-color: rgba(175, 47, 47, 0.1);
}

.filters li a {
  color: inherit;
  margin: 3px;
  padding: 3px 7px;
  text-decoration: none;
  border: 1px solid transparent;
  border-radius: 3px;
  border-color: rgba(100, 100, 100, 0.1);
}
.filters li a:hover {
  border-color: rgba(255, 47, 47, 0.3);
  cursor: pointer;
}
</style>
```

The app should now have a `Save As` button, that when clicked, shows a message at the top of the page:

<div class="imagecontainer">
  <img src="/images/todosaveas1.png" style="width: 65%">
</div>

### Show "Save As" Dialog

The Wails runtime provides access to a number of [native dialogs](https://godoc.org/github.com/wailsapp/wails#RuntimeDialog). We are interested in using the [SelectSaveFile dialog](https://godoc.org/github.com/wailsapp/wails#RuntimeDialog.SelectSaveFile). We will call a `saveAs` method on the backend and do all the saving logic there:

```javascript {6}
    cancelEdit: function(todo) {
      this.editedTodo = null;
      todo.title = this.beforeEditCache;
    },
    saveAs: function() {
      window.backend.Todos.SaveAs(JSON.stringify(this.todos, null, 2));
    },
    setErrorMessage: function(message) {
      this.errorMessage = message;
    }
```
Now let's create the method in the backend:

```go {6-10}
func (t *Todos) SaveList(todos string) error {
	t.logger.Infof("Saving list: %s", todos)
	return ioutil.WriteFile(t.filename, []byte(todos), 0600)
}

func (t *Todos) SaveAs(todos string) error {
	filename := t.runtime.Dialog.SelectSaveFile()
	t.logger.Info("Save As: " + filename)
	return nil
}

func (t *Todos) ensureFileExists() {
```

Now when we click on the `Save As` button....nothing appears to happen! Let's check the logs:

```bash
WARN[0010] [Bridge] SelectSaveFile() unsupported in bridge mode 
INFO[0010] [Todos] Save As:                  
```

Currently, native dialogs are unsupported in bridge mode, so let's compile to a native app again, but in debug mode: `wails build -d`.

When we run it and press the `Save As` button, we get a save dialog:

<div class="imagecontainer">
  <img src="/images/todosaveas2.png" style="width: 95%">
</div>

Enter a name and press `Save`. The logging should look similar to this:

```bash
INFO[0000] [Bind] Bound Method: main.Todos.LoadList()   
INFO[0000] [Bind] Bound Method: main.Todos.SaveAs()     
INFO[0000] [Bind] Bound Method: main.Todos.SaveList()   
INFO[0000] [Todos] I'm here                             
INFO[0000] [Todos] Starting Watcher                     
INFO[0000] [WebView] Run()                              
INFO[0000] [Todos] Loading list from: /Users/lea/mylist.json 
INFO[0077] [Todos] Save As: /Users/lea/Desktop/test.json 
```
Now that we have the filename to save the list to, let's implement that next.

### Saving the Todo List

We already have a `Save` method so saving this list is as easy as updating the filename and saving using our `SaveList` method:

```go {4-5}
func (t *Todos) SaveAs(todos string) error {
	filename := t.runtime.Dialog.SelectSaveFile()
	t.logger.Info("Save As: " + filename)
	t.filename = filename
	t.SaveList(todos)
	return nil
}
```
Rebuild the app and try saving your list. You will see that the file gets written! Any updates to the list now get written to our new file. However, we aren't tracking changes to the file so we need to update our watcher. Fortunately, the fsnotify library supports [removing files](https://godoc.org/github.com/fsnotify/fsnotify#Watcher.Remove) from being watched so let's adjust our code so that every time we update the filename, we also update the watcher. Firstly, let's keep a reference to the watcher in the Todo's struct:

```go {5}
type Todos struct {
	filename string
	runtime  *wails.Runtime
	logger   *wails.CustomLogger
	watcher  *fsnotify.Watcher
}
```
And update startWatcher to save the watcher reference:

```go {4}
func (t *Todos) startWatcher() error {
	t.logger.Info("Starting Watcher")
	watcher, err := fsnotify.NewWatcher()
	t.watcher = watcher
	if err != nil {
		return err
	}
```
Now we can create a method to update the filename:

```go
func (t *Todos) setFilename(filename string) error {
	var err error
	// Stop watching the current file and return any error
	err = t.watcher.Remove(t.filename)
	if err != nil {
		return err
	}

	// Set the filename
	t.filename = filename

	// Add the new file to the watcher and return any errors
	err = t.watcher.Add(filename)
	if err != nil {
		return err
	}
	t.logger.Info("Now watching: " + filename)
	return nil
}
```

Now the problem we have is that we are currently setting the filename before saving but when we set the filename, we now try and watch it and we know what happens if that file doesn't exist... 

To solve this, we will create a new method for saving the file that takes a filename as a parameter:

```go
func (t *Todos) saveListByName(todos string, filename string) error {
	return ioutil.WriteFile(filename, []byte(todos), 0600)
}
```
Now we need to rector our `SaveList` method to use our new method:

```go
func (t *Todos) SaveList(todos string) error {
	t.logger.Infof("Saving list: %s", todos)
	return t.saveListByName(todos, t.filename)
}
```

The last thing for us to do now is to update our `SaveAs` method:

```go {4-8}
func (t *Todos) SaveAs(todos string) error {
	filename := t.runtime.Dialog.SelectSaveFile()
	t.logger.Info("Save As: " + filename)
	err := t.saveListByName(todos, filename)
	if err != nil {
		return err
	}
	return t.setFilename(filename)
}
```
Now the list will be saved under the new name, and the file watcher updated to listen to it.

One final touch, is to put the name of the file in the title bar. This uses the [SetTitle](https://godoc.org/github.com/wailsapp/wails#RuntimeWindow.SetTitle) method of the Runtime's [Window](https://godoc.org/github.com/wailsapp/wails#RuntimeWindow) methods. We will set it initially, then every time we save the list. Let's update `WailsInit` first:

```go {12}
func (t *Todos) WailsInit(runtime *wails.Runtime) error {
	t.runtime = runtime
	t.logger = t.runtime.Log.New("Todos")
	t.logger.Info("I'm here")

	// Set the default filename to $HOMEDIR/mylist.json
	homedir, err := runtime.FileSystem.HomeDir()
	if err != nil {
		return err
	}
	t.filename = path.Join(homedir, "mylist.json")
	t.runtime.Window.SetTitle(t.filename)
	t.ensureFileExists()
	return t.startWatcher()
}
```
Now we'll update `setFilename`:

```go {7}
	// Add the new file to the watcher and return any errors
	err = t.watcher.Add(filename)
	if err != nil {
		return err
	}
	t.logger.Info("Now watching: " + filename)
	t.runtime.Window.SetTitle(t.filename)
	return nil
}
```

Now we have `Save As` working correctly, let's move on to loading a list.

## Loading a Todo List

Loading is going to require us to do the following things:

  * Add a Load button
  * Call the backend to prompt the user to select the list
  * Load the list 

### Add a Load Button

This is fairly trivial as we already have a `Save As` button:

```html {6-8}
        <div class="buttons">
          <ul class="filters">
            <li>
              <a @click="saveAs">Save As</a>
            </li>          
            <li>
              <a @click="loadNewList">Load</a>
            </li>
          </ul>
        </div>
```

### Call the backend

We will call "loadNewList" when it is pressed. This is simply going to call the backend to load the list. Let's add that:

```javascript {4-6}
    saveAs: function() {
      window.backend.Todos.SaveAs(JSON.stringify(this.todos, null, 2));
    },
    loadNewList: function() {
      window.backend.Todos.LoadNewList();
    },
    setErrorMessage: function(message) {
      this.errorMessage = message;
```

### Load the list

Now we need to implement `LoadNewList` in the backend. All we are going to do is present a [File Select](https://godoc.org/github.com/wailsapp/wails#RuntimeDialog.SelectFile) dialog and if the user selects a file, we will set our todo list's filename to that file (which will also start watching it). To get it to load into the frontend, we will cheat a little by simply emitting the 'filemodified' event we created earlier:

```go
func (t *Todos) LoadNewList() {
	filename := t.runtime.Dialog.SelectFile()
	if len(filename) > 0 {
		t.setFilename(filename)
		t.runtime.Events.Emit("filemodified")
	}
}
```

Now clicking `Load` will allow you to select a new list and load it into the app!

## Packaging the App

To package the app, we simply run `wails build -p` in the project directory. This will package up the application into a platform-native format.

On Mac, this means creating a `.app` package, with an icon.
On Windows, this means creating a `.exe` file, with an icon.
On linux, this simply means creating a binary.

You should now see a binary in your project directory. The binary should have the default icon (Mac, Windows):

<div class="imagecontainer">
  <img src="/images/appdir1.png" style="width: 75%">
</div>

You will also notice that there is another file `appicon.png`. If you want to change the icon of the application, change this file and re-run `wails build -p`. To demonstrate this, let's download [this awesome icon](http://www.iconarchive.com/show/kameleon.pics-icons-by-webalys/Checklist-icon.html) by [Kameleon Icons](http://www.kameleon.pics/). Select the 512x512 version. Name it `appicon.png` and copy it over the original `appicon.png`. Now run `wails build -p` again. You'll now see the icon updated:

<div class="imagecontainer">
  <img src="/images/appdir2.png" style="width: 75%">
</div>

Congratulations! You have now created a fully portable, single binary desktop application using Go and Vue!
