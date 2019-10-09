+++
title = "Quotes Generator"
date = 2019-08-29T04:56:50+10:00
weight = 20
+++

In this tutorial, we will be building on the default Vue/Webpack template to create a quotes generator. We shall cover how we can group methods into a struct in Go and how to use the Go runtime object. 

It is assumed you have completed and understood the [template tutorial](./template.md). We will continue from where we left off.

## Creating the Quotes Struct

Wails allows you to bind structs to your application. Methods that are exposed, ie those with their first letters capitalised, will be bound to the application.

We will start by creating a new file: quotes.go.

```go
package main

// Quotes is our bound Quote Struct
type Quotes struct {
}

// NewQuotes creates a new Quotes Struct
func NewQuotes() *Quotes {
	return &Quotes{}
}

// GetQuote returns a quote
func (q *Quotes) GetQuote() string {
	return "s/be/in - Mat Ryer"
}
```

Our Quotes struct has one method `GetQuote`. When this is bound to the application, Wails will bind it to the frontend as `backend.Quotes`. Because `GetQuote` is an exposed method, it will be bound in the frontend as `backend.Quotes.GetQuote`. 

The NewQuotes method is simply a convention for creating a new instance of a struct. Wails requires that structs are instantiated before binding, so we will use this function in the main app.

## Binding it to the application

Now that we have our initial struct, let's bind it to the app:

```go
package main

import (
	"github.com/leaanthony/mewn"
	"github.com/wailsapp/wails"
)

func basic() string {
	return "Hello World!"
}

func main() {

	js := mewn.String("./frontend/dist/app.js")
	css := mewn.String("./frontend/dist/app.css")

	app := wails.CreateApp(&wails.AppConfig{
		Width:  1024,
		Height: 768,
		Title:  "Quotes",
		JS:     js,
		CSS:    css,
		Colour: "#131313",
	})
	app.Bind(basic)
	app.Bind(NewQuotes())   // Add this
	app.Run()
}
```

Binding our quotes struct is a single line change to our existing main function. We can test this in the front end by serving the project again:

 * Run `wails serve` in the project directory
 * When it is ready, run `npm run serve` in the `frontend` directory

 When it is ready, open the browser to the project url and you should see the same familiar screen as in the template tutorial:

<div class="imagecontainer">
  <img src="/media/templatedefault.png">
</div>

If you open up the browser's inspector window and select console, you should be able to access the bound Quotes struct:

<div class="imagecontainer">
  <img src="/media/quotesconsole.png" style="width: 50%">
</div>

Note that the `GetQuote()` method is available to us. This, just like bound functions, returns a Javascript promise. We can run the method and print the output like so:


<div class="imagecontainer">
  <img src="/media/quotesconsolerun.png" style="width: 50%">
</div>

Now that we have access to our Quotes struct, let's add more quotes.

### Better Quotes

A quote traditionally has a text and an author. Currently we are returning a single string from our `GetQuote` method, but it would be better to return a struct. Let's define it:

```go
// Quote holds a single quote and the author who said it
type Quote struct {
	Text   string `json:"text"`
	Author string `json:"author"`
}
```

We can now update our `GetQuote` method to return a Quote:

```go
// GetQuote returns a quote
func (q *Quotes) GetQuote() *Quote {
	return &Quote{Text: "s/be/in", Author: "Mat Ryer"}
}
```

If the previous `wails serve` is still running, press `ctrl-c` to stop it and re-run the serve command. The frontend does not need recompiling and will automatically reconnect to the backend when it becomes available. During that connection time, you will see a screen like this letting you know it is trying to reconnect to the backend:

<div class="imagecontainer">
  <img src="/media/reconnect.png" style="width: 50%">
</div>

Once reconnected, open the console again and re-issue the `GetQuote` command. You should see something like the following:

<div class="imagecontainer">
  <img src="/media/quotestruct.png" style="width: 40%">
</div>

Now that we have the data in a format we can manipulate, we can update our frontend code to use it.

### Rendering the Quotes

Let's create a new component for rendering the quotes. It will be based on our HelloWorld component, so make a copy of this file and name it `Quote.vue`.

The first thing we will do is register our new Quote component in `App.vue`:

```javascript
<template>
  <div id="app">
    <img alt="Wails logo" src="./assets/images/logo.png" class="logo zoomIn">
    <Quote />
  </div>
</template>

<script>
import Quote from "./components/Quote.vue";
import "./assets/css/main.css";

export default {
  name: "app",
  components: {
    Quote
  }
};
</script>
```

Then we will update the component to get and store our quote:

```javascript
<script>
export default {
  data() {
    return {
      quote: null
    };
  },
  methods: {
    getNewQuote: function() {
      var self = this;
      window.backend.Quotes.GetQuote().then(result => {
        self.quote = result;
      });
    }
  },
};
</script>
```

We are simply changing the name of the data element from `message` to `quote`, and changing the name of the function we are calling to retrieve the quote. We store it in the quote variable defined in `data()`.

Recap: The quote struct looks like this:
```json
{
	text: 'the quote text',
	author: 'the quote author'
}
```

Next, we'll update the template to use the quote object:

```javascript
<template>
  <div class="container">
    <blockquote v-if="quote != null" :cite="quote.author">{{ quote.text }}</blockquote>
    <a @click="getNewQuote">Press Me!</a>
  </div>
</template>
``` 

Let's look at Line 3. We are using a blockquote element to encapsulate the quote. Within this element, we are using the `v-if` Vue directive. This will conditionally render the element based on the condition given to it. In our case, this is `quote != nil`. We will define `quote` in the component. This will be what stores our quote struct from the backend. The next directive we use is `:cite`. This simply sets an attribute on the blockquote element. In our case, we are setting it to `quote.author`. This references the author field of the quote struct we are getting from the backend. Within the element tags, we are using a template directive. This will output text based on the data within the double braces. In our case this will be `quote.text`.

In Line 4, we simply update the name of the component's method to call when the button is clicked.

If we serve the project now, we can see something like this:

<div class="imagecontainer">
  <img src="/media/unformattedquote1.png" style="width: 50%">
</div>

If we press the button, we can see the quote!

<div class="imagecontainer">
  <img src="/media/unformattedquote2.png" style="width: 50%">
</div>

The styling is terrible. Let's fix that!

### Styling the component

We already have some styling in the `<style>` section of the component. We will update this:

```css
<style scoped>
/**
  Credit: https://codepen.io/harmputman/pen/IpAnb
**/
.container {
  width: 100%;
  max-width: 480px;
  min-width: 320px;
  margin: 2em auto 0;
  padding: 1.5em;
  opacity: 0.8;
  border-radius: 1em;
  border-color: #117;
}

p { margin-bottom: 1.5em; }
p:last-child { margin-bottom: 0; }

blockquote {
  display: block;
  border-width: 2px 0;
  border-style: solid;
  border-color: #eee;
  padding: 2.5em 0 0.5em;
  margin: 1.5em 0;
  position: relative;
  color: #fffb04;
}
blockquote:before {
  content: '\201C';
  position: absolute;
  top: 0em;
  left: 50%;
  transform: translate(-50%, -50%);
  background: #131313;
  width: 3rem;
  height: 2rem;
  font: 6em/1.08em sans-serif;
  color: #eee;
  text-align: center;
}
blockquote:after {
  content: "\2013 \2003" attr(cite);
  display: block;
  text-align: right;
  font-size: 1.15em;
  color: #53cdff;
}

/* https://fdossena.com/?p=html5cool/buttons/i.frag */

a:hover {
  font-size: 1.7em;
  border-color: blue;
  background-color: blue;
  color: white;
  border: 3px solid white;
  border-radius: 10px;
  padding: 9px;
  cursor: pointer;
  transition: 500ms;
}
a {
  font-size: 1.7em;
  border-color: white;
  background-color: #121212;
  color: white;
  border: 3px solid white;
  border-radius: 10px;
  padding: 9px;
  cursor: pointer;
  display: inline-block;
}
</style>
```

When we reload the app now, we should see something like this:

<div class="imagecontainer">
  <img src="/media/formattedquote1.png" style="width: 50%">
</div>

Pressing the button should yield the following:

<div class="imagecontainer">
  <img src="/media/formattedquote2.png" style="width: 50%">
</div>

### Adding more quotes

Whilst Mat's quote is iconic (and potentially career ending), we are going to add in a few more quotes. What we want is to retrieve a random quote, every time the button is pressed.

To do this, we are going to add a Quote slice to our Quotes struct in our quotes.go file:

```go
// Quotes is our bound Quote Struct
type Quotes struct {
	quotes []*Quote
}
```

We will also create a small function to create and store our quotes:

```go
// AddQuote creates a Quote object with the given inputs and
// adds it to the Quotes collection
func (q *Quotes) AddQuote(text, author string) {
	q.quotes = append(q.quotes, &Quote{Text: text, Author: author})
}
```

Now that we have our helper functions in place, let's populate the quotes. We can do this in the NewQuotes function:

```go
// NewQuotes creates a new Quotes Struct
func NewQuotes() *Quotes {
	result := &Quotes{}
	result.AddQuote("Age is an issue of mind over matter. If you don't mind, it doesn't matter", "Mark Twain")
	result.AddQuote("Anyone who stops learning is old, whether at twenty or eighty. Anyone who keeps learning stays young. The greatest thing in life is to keep your mind young", "Henry Ford")
	result.AddQuote("Wrinkles should merely indicate where smiles have been", "Mark Twain")
	result.AddQuote("True terror is to wake up one morning and discover that your high school class is running the country", "Kurt Vonnegut")
	result.AddQuote("A diplomat is a man who always remembers a woman's birthday but never remembers her age", "Robert Frost")
	result.AddQuote("As I grow older, I pay less attention to what men say. I just watch what they do", "Andrew Carnegie")
	result.AddQuote("How incessant and great are the ills with which a prolonged old age is replete", "C. S. Lewis")
	result.AddQuote("Old age, believe me, is a good and pleasant thing. It is true you are gently shouldered off the stage, but then you are given such a comfortable front stall as spectator", "Confucius")
	result.AddQuote("Old age has deformities enough of its own. It should never add to them the deformity of vice", "Eleanor Roosevelt")
	result.AddQuote("Nobody grows old merely by living a number of years. We grow old by deserting our ideals. Years may wrinkle the skin, but to give up enthusiasm wrinkles the soul", "Samuel Ullman")
	result.AddQuote("An archaeologist is the best husband a woman can have. The older she gets the more interested he is in her", "Agatha Christie")
	result.AddQuote("All diseases run into one, old age", "Ralph Waldo Emerson")
	result.AddQuote("Bashfulness is an ornament to youth, but a reproach to old age", "Aristotle")
	result.AddQuote("Like everyone else who makes the mistake of getting older, I begin each day with coffee and obituaries", "Bill Cosby")
	result.AddQuote("Age appears to be best in four things old wood best to burn, old wine to drink, old friends to trust, and old authors to read", "Francis Bacon")
	result.AddQuote("None are so old as those who have outlived enthusiasm", "Henry David Thoreau")
	result.AddQuote("Every man over forty is a scoundrel", "George Bernard Shaw")
	result.AddQuote("Forty is the old age of youth fifty the youth of old age", "Victor Hugo")
	result.AddQuote("You can't help getting older, but you don't have to get old", "George Burns")
	result.AddQuote("Alas, after a certain age every man is responsible for his face", "Albert Camus")
	result.AddQuote("Youth is when you're allowed to stay up late on New Year's Eve. Middle age is when you're forced to", "Bill Vaughan")
	result.AddQuote("Old age is like everything else. To make a success of it, you've got to start young", "Theodore Roosevelt")
	result.AddQuote("A comfortable old age is the reward of a well-spent youth. Instead of its bringing sad and melancholy prospects of decay, it would give us hopes of eternal youth in a better world", "Maurice Chevalier")
	result.AddQuote("A man growing old becomes a child again", "Sophocles")
	result.AddQuote("I will never be an old man. To me, old age is always 15 years older than I am", "Francis Bacon")
	result.AddQuote("Age considers youth ventures", "Rabindranath Tagore")
	result.AddQuote("s/be/in", "Mat Ryer")
	return result
}
```

The only thing left to do now is to update our `GetQuote()` method to return a random quote:

```go
// GetQuote returns a quote
func (q *Quotes) GetQuote() *Quote {
	return q.quotes[rand.Intn(len(q.quotes))]
}
```

You will also need to ensure that you import `math/rand`, if your IDE hasn't already:

```go
import "math/rand"
```

Run `wails serve` again to recompile and serve the app. 

Now when we press the button, we get a fabulous quote:

<div class="imagecontainer">
  <img src="/media/quote.png" style="width: 50%">
</div>

Now with a slight tweak to the CSS, we can make this look even better. In the component CSS, let's add a margin to the author:

```css
blockquote:after {
  content: "\2013 \2003" attr(cite);
  display: block;
  text-align: right;
  font-size: 1.15em;
  color: #53cdff;
  margin: 1em;
}
```

Now we have a great looking quotes app:

<div class="imagecontainer">
  <img src="/media/quote2.png" style="width: 50%">
</div>

## Building the app

Now that we have the app working, we want to build it as a standalone app. We do this by running `wails build`. You should now have a `quotes` executable (or `quotes.exe` if on Windows). 

<div class="imagecontainer">
  <img src="/media/quotesls.png" style="width: 75%">
</div>

Running this should run the app. On MacOS, it looks like this:

<div class="imagecontainer">
  <img src="/media/app1.png" style="width: 75%">
</div>

Pressing the button works as expected:

<div class="imagecontainer">
  <img src="/media/app2.png" style="width: 75%">
</div>

## Packaging the app

Wails provides the ability to package your application into a platform native format. Packaing is indicated by running `wails build -p`.

### MacOS

On MacOS, running `wails build -p` will generate a .app bundle. If we run this in the quotes project directory you should end up with a quotes.app bundle:

<div class="imagecontainer">
  <img src="/media/quotesapp1.png" style="width: 75%">
</div>

If we open this in finder, you will see your application as a standard Mac app:

<div class="imagecontainer">
  <img src="/media/quotesapp2.png" style="width: 75%">
</div>

Double clicking this will launch the app as expected. Minimising the app to the dock will show you that the icon works as expected:

<div class="imagecontainer">
  <img src="/media/quotesdock.png" style="width: 15%">
</div>

Of course, it's unlikely that you'll want to use the default icon, so Wails makes it easy for you to replace it. Just replace `appicon.png` with your own icon and rebuild.

There's a cool icon [here](https://www.onlinewebfonts.com/icon/299634) which can be used. I made some changes so that it works better on my dark desktop, renamed the icon to `appicon.png` and rebuilt:

<div class="imagecontainer">
  <img src="/media/quotesicon.png" style="width: 95%">
</div>

### Windows

Due to the nature of Windows, a standard build will also package the app with the default icon. If you run `wails build -p` it will leave the build artifacts, including the default icon, so that you can customise the build. Make your changes and run `wails build` again.

### Linux

Currently, packing on Linux isn't supported as it could mean many things. There is the potential to support snap packages in the future.

## Exercises

 * See if you can animate the quotes using something like [animate.css](https://daneden.github.io/animate.css/)
 * See if you can pull quotes from the [They Said So](https://quotes.rest/) quotes API from the backend

## Summary

Hopefully you now understand how to build and package a basic application using Wails.
