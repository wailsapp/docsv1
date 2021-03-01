+++
title = "Runtime"
date = 2019-08-29T04:56:50+10:00
weight = 10
chapter = false
+++

Wails comes with a runtime library that may be accessed from JavaScript or Go. It has the following subsystems:

  * [Events](#events)
  * [Log](#log)
  * [Window](#window)
  * [Dialog](#dialog)
  * [Browser](#browser)
  * [Filesystem](#filesystem)
  * [Store](#store)

**NOTE: At this time, the JavaScript runtime does not include the Window and Dialog subsystems**

When binding a struct with the `WailsInit` method, the Go runtime object is presented by the Application.

For the frontend, the runtime is accessed through the `window.wails` object.

### Events

The Events subsystem provides a means of listening and emitting events across the application as a whole. This means that you can listen for events emitted in both JavaScript and Go, and events that you emit will be received by listeners in both Go and JavaScript.

In the Go runtime, it is accessible via `runtime.Events` and provides 2 methods: `Emit` and `On`.

<a href="https://godoc.org/github.com/wailsapp/wails/runtime#Events"><img src="https://img.shields.io/badge/godoc-reference-blue.svg" style="margin: 0;"/></a>

#### Emit

> Emit(eventName string, optionalData ...interface{})

The `Emit` method is used to emit named events across the application.

The first parameter is the name of the event to emit. The second parameter is an optional list of interface{} types, meaning you can pass arbitrary data along with the event.

Example 1:

```go
func (m *MyStruct) WailsInit(runtime *wails.Runtime) error {
  runtime.Events.Emit("initialised")
}
```

Example 2:

```go
func (m *MyStruct) WailsInit(runtime *wails.Runtime) error {
  t := time.Now()
  message := fmt.Sprintf("I was initialised at %s", t.String())
  runtime.Events.Emit("initialised", message)
}
```

#### On

> On(eventName string, callback func(optionalData ...interface{}))

The `On` method is used to listen for events emitted across the application.  

The first parameter is the name of the event to listen for. The second parameter is a function to call when the event is emitted. This function has an optional parameter which will contain any data that was sent with the event. To listen to the 2 events emitted in the [emit](####emit) examples:

Example with no data:

```go
func (m *MyStruct) WailsInit(runtime *wails.Runtime) error {
  runtime.Events.On("initialised", func(...interface{}) {
    fmt.Println("I received the 'initialised' event!")
  })
  return nil
}
```

Example with data:

```go
func (m *MyStruct) WailsInit(runtime *wails.Runtime) error {
  runtime.Events.On("hello", func(data ...interface{}) {
    // You should probably do better error checking
    fmt.Printf("I received the 'initialised' event with the message '%s'!\n", data[0])
  })
  return nil
}
```

### Log

The Log subsystem allows you to log messages at various log levels to the application log.

<a href="https://godoc.org/github.com/wailsapp/wails/runtime#Log"><img src="https://img.shields.io/badge/godoc-reference-blue.svg" style="margin: 0;"/></a>

#### New

> New(prefix string)

Creates a new custom Logger with the given prefix.

```go
type MyStruct struct {
  log *wails.CustomLogger
}

func (m *MyStruct) WailsInit(runtime *wails.Runtime) error {
  m.log = runtime.Log.New("MyStruct")
  return nil
}
```

Once created, you may use any of the logger's methods:

##### Standard logging

Each of these methods takes a string (like fmt.Println):

  - Debug
  - Info
  - Warn
  - Error
  - Fatal

```go
  m.Log.Info("This is fine")
```

##### Formatted logging

Each of these methods takes a string and optional data (like fmt.Printf):

  - Debugf
  - Infof
  - Warnf
  - Errorf
  - Fatalf
  

```go
  feeling := "okay"
  m.Log.Infof("I'm %s with the events that are currently unfolding", feeling)
```

##### Field logging

Each of these methods takes a string and a set of fields:

  - DebugFields
  - InfoFields
  - WarnFields
  - ErrorFields
  - FatalFields

```go
  m.Log.InfoFields("That's okay", wails.Fields{
    "things are going to be": "okay",
  })
```

### Dialog

The Dialog subsystem allows you to activate the Webview's native dialogs. 

<a href="https://godoc.org/github.com/wailsapp/wails/runtime#Dialog"><img src="https://img.shields.io/badge/godoc-reference-blue.svg" style="margin: 0;"/></a>

It is accessible via `runtime.Dialog` and has the following methods:

**NOTE: Opening a Dialog will halt JavaScript execution, just like a browser**

#### SelectFile

> SelectFile(optionalTitle string, optionalFilter string)

Prompts the user to select a file for opening. Returns the path to the file.

```go
  selectedFile := runtime.Dialog.SelectFile()
```

There is also the option to provide a title and filter:

```go
  selectedFile := runtime.Dialog.SelectFile("Select your profile picture", "*.jpg,*.png")
```

Or if you want just a title:

```go
  selectedFile := runtime.Dialog.SelectFile("Select a file")
```


#### SelectDirectory

> SelectDirectory()

Prompts the user to select a directory. Returns the path to the directory.

```go
  selectedDirectory := runtime.Dialog.SelectDirectory()
```

#### SelectSaveFile

> SelectSaveFile(optionalTitle string, optionalFilter string)

Prompts the user to select a file for saving. Returns the path to the file.

```go
  selectedFile := runtime.Dialog.SelectSaveFile()
```

There is also the option to provide a title and filter:

```go
  selectedFile := runtime.Dialog.SelectSaveFile("Select a file", "*.jpg,*.png")
```

Or if you want just a title:

```go
  selectedFile := runtime.Dialog.SelectSaveFile("Select a file")
```

### Window

The Window subsystem provides methods to interact with the application's main window.

<a href="https://godoc.org/github.com/wailsapp/wails/runtime#Window"><img src="https://img.shields.io/badge/godoc-reference-blue.svg" style="margin: 0;"/></a>

#### SetColour

> SetColour(colour string) error

Sets the background colour of the window to the colour given to it (string). The colour may be specified in the following formats:

|  Colour Type | Example  |
| ------------ | -------- |
|   RGB       | rgb(0, 0, 0) |
|   RGBA      | rgba(0, 0, 0, 0.8) |
|   HEX       | #fff |

```go
  runtime.Window.SetColour("#eee")
```


#### Fullscreen

> Fullscreen()

Attempts to make the application window fullscreen. Will fail if the application was started with the option "Resizable: false".

```go
  runtime.Window.Fullscreen()
```


#### UnFullscreen

> UnFullscreen()

Attempts to revert the window back to its size prior to a Fullscreen call. Will fail if the application was started with the option "Resize: false"

```go
  UnFullscreen()
```

#### SetTitle

> SetTitle(title string)

Sets the title in the application title bar.

```go
 runtime.Window.SetTitle("We'll need a bigger boat")
```

#### Close

Closes the main window and thus terminates the application. Use with care!

```go
  runtime.Window.Close()
```

### Browser

The browser subsystem provides methods to interact with the system browser.

<a href="https://godoc.org/github.com/wailsapp/wails/runtime#Browser"><img src="https://img.shields.io/badge/godoc-reference-blue.svg" style="margin: 0;"/></a>

#### OpenURL

> OpenURL(url string)

Opens the given URL in the system browser.

```go
runtime.Browser.OpenURL("https://wails.app")
```

### Filesystem

The Filesystem subsystem provides a means of accessing filesystem related methods. Currently this is limited to Go.

<a href="https://godoc.org/github.com/wailsapp/wails/runtime#FileSystem"><img src="https://img.shields.io/badge/godoc-reference-blue.svg" style="margin: 0;"/></a>

#### HomeDir

> HomeDir() (string, error)

Returns the user's home directory, or an error.

### A Common Pattern

A common pattern for the Runtime is to simply save it as part of the struct and use it when needed:

```go
type MyStruct struct {
  Runtime  *wails.Runtime
}

func (m *MyStruct) WailsInit(r *wails.Runtime) error {
  m.Runtime = r
}
```

### Store

Wails has the concept of a synchronised state store: a place to put state that is *automatically* synchronised between your frontend and backend. The workflow is relatively simple: 

  * Create a store 
  * Set or Update the value in the store
  * React to updates by Subscribing to store updates

**NOTE: The JavaScript equivalent methods are found in the wails runtime module**

#### New

> New(id string, defaultValue interface{}) (*wails.Store)

Creates a new store with the given (unique) identifier and the given value.

```go
type Counter struct {
  r     *wails.Runtime
  store *wails.Store
}

// WailsInit is called when the component is being initialised
func (c *Counter) WailsInit(runtime *wails.Runtime) error {
  c.r = runtime
  c.store = runtime.Store.New("Counter", 0)
  return nil
}
```

#### Set

> Set(value interface{}) error

Sets the store's state to the given value. 

```go
// RandomValue sets the counter to a random value
func (c *Counter) RandomValue() {
  c.store.Set(rand.Intn(1000))
}
```

#### Update 

> Update(updater interface{})

`Update` accepts a function that is called to update the current value of the store. The function accepts the current store value and returns a value, which is the new value of the store.

The reason `Update` accepts an interface{} is to make this as easy as possible to use in the absence of generics: The function that a store is expecting is one of the following format:

```go
func(currentValue T) T {}
```

Example - If you are storing an int in your store, then `Update` will expect a function that receives an int and returns an int:

```go
c.store.Update(func(currentValue int) int {
  return currentValue * 2
})
```
{{% notice warning %}}
If you do not provide a function in the correct format, you will receive a fatal runtime error. You need to ensure your update functions are correct. This will be mitigated by generics.
{{% /notice %}}

#### Subscribe

> Subscribe(callback interface{})

The `Subscribe` method takes a callback that is called whenever the store has been updated. It is similar to `Update` in that the callback signature should use the same datatype as the store. This signature is:

```go
func(currentValue T) {}
```

Example:
```go
// WailsInit is called when the component is being initialised
func (c *Counter) WailsInit(runtime *wails.Runtime) error {
  c.r = runtime
  c.store = runtime.Store.New("Counter", 0)

  c.store.Subscribe(func(data int) {
    println("New Value:", data)
  })
  return nil
}
```

{{% notice warning %}}
Callbacks are executed in goroutines.
{{% /notice %}}

#### OnError

> OnError( func(error) )

`OnError` may be used to provide an error handler for the store. This should never be needed and should only be used when debugging issues with synchronisation messages. Example: There may have been a json encoding error that you need to debug.

#### JavaScript

To hook into the store in JavaScript, you create a store with the same ID and use the same methods:

```js
const runtime = require('@wailsapp/runtime');

// Main entry point
function start() {

  // New
  var mystore = runtime.Store.New('Counter');
  
  // Set
  mystore.set(0);

  // Subscribe
  mystore.subscribe( function(state) {
    document.getElementById('counter').innerText = state;
  });
  
  // Update
  mystore.update( function(state) {
    return state * 2;
  })

```

