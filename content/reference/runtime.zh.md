+++
title = "运行时"
date = 2019-08-29T04:56:50+10:00
weight = 10
chapter = false
+++

Wails 包含一个可以从go或者JavaScript访问的运行时，它包含下面的子系统

  * Events  
  * Logging  
  * Window  
  * Dialog  
  * Browser  
  * Filesystem  
  * Store 

**NOTE: 目前，JavaScript运行时不包含window和dialog子系统**


当使用`wails init`方法绑定结构体时，应用程序将使用Go运行时对象。
对于前端，运行时将通过`window.wails`对象访问

### Events

事件子系统提供了在整个应用程序中监听和注册事件的方法。您可以在Go和JavaScript中注册事件，也可以在Go和JavaScript中监听这些事件

在Go runtime中，通过"runtime.Events"提供`Emit` 和 `On`两种方法用于注册和监听事件

<a href="https://godoc.org/github.com/wailsapp/wails/runtime#Events"><img src="https://img.shields.io/badge/godoc-reference-blue.svg" style="margin: 0;"/></a>

#### Emit

> Emit(eventName string, optionalData ...interface{})

`Emit` 方法将在应用中注册事件

第一个参数是注册的事件名称，第二个参数是接口类型的可选列表，这意味着您可以传入您需要的任意数据。

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

`On` 方法用于在应用中监听注册的事件。

第一个参数是监听的事件名称，第二个参数是回调函数，回调函数中传入一个接口列表，用于接收注册时传入的数据。

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

日志子系统允许您将不同日志级别的消息记录到应用程序日志中。

<a href="https://godoc.org/github.com/wailsapp/wails/runtime#Log"><img src="https://img.shields.io/badge/godoc-reference-blue.svg" style="margin: 0;"/></a>

#### New

> New(prefix string)

创建一个新的自定义Logger

```go
type MyStruct struct {
	log *wails.CustomLogger
}

func (m *MyStruct) WailsInit(runtime *wails.Runtime) error {
	m.log = runtime.Log.New("MyStruct")
	return nil
}
```

一旦创建完成，您可以使用logger的任何方法

##### Standard logging

Each of these methods take a string (like fmt.Println):

你可以像fmt.Println一样使用它们

  - Debug
  - Info
  - Warn
  - Error
  - Fatal

```go
  m.Log.Info("This is fine")
```


##### Formatted logging

Each of these methods take a string and optional data (like fmt.Printf):

你可以像fmt.Printf一样使用它们

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

Each of these methods take a string and a set of fields:

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

对话框子系统允许您使用本机对话框

<a href="https://godoc.org/github.com/wailsapp/wails/runtime#Dialog"><img src="https://img.shields.io/badge/godoc-reference-blue.svg" style="margin: 0;"/></a>

可以使用`runtime.Dialog`创建对话框，它包含下列几种方法

**NOTE: 打开对话框将停止JavaScript的执行，就像浏览器中的一样**

#### SelectFile

> SelectFile(optionalTitle string, optionalFilter string)

提示用户选择文件，返回文件的路径

```go
  selectedFile := runtime.Dialog.SelectFile()
```

还可以使用提示文字和过滤文件类型

```go
  selectedFile := runtime.Dialog.SelectFile("Select your profile picture", "*.jpg,*.png")
```

或者只使用提示文字

```go
  selectedFile := runtime.Dialog.SelectFile("Select a file")
```



#### SelectDirectory

> SelectDirectory()

提示用户选择文件夹，返回文件夹的路径

```go
  selectedDirectory := runtime.Dialog.SelectDirectory()
```


#### SelectSaveFile

> SelectSaveFile(optionalTitle string, optionalFilter string)

提示用户选择要保存的文件，返回文件的路径

```go
  selectedFile := runtime.Dialog.SelectSaveFile()
```

也可以使用提示文字和文件类型过滤

```go
  selectedFile := runtime.Dialog.SelectSaveFile("Select a file", "*.jpg,*.png")
```

或者只使用提示文字

```go
  selectedFile := runtime.Dialog.SelectSaveFile("Select a file")
```

### Window

窗口子系统提供了与应用程序主窗口交互的方法。

<a href="https://godoc.org/github.com/wailsapp/wails/runtime#Window"><img src="https://img.shields.io/badge/godoc-reference-blue.svg" style="margin: 0;"/></a>

#### SetColour

> SetColour(colour string) error

将窗口的背景颜色设置为指定的颜色。颜色可按以下格式指定：

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

尝试使应用程序窗口全屏显示。如果设置了"Resizable: false"，将会失败。

```go
  runtime.Window.Fullscreen()
```



#### UnFullscreen

> UnFullscreen()

尝试在全屏调用之前还原窗口大小。如果设置了"Resizable: false"，将会失败。

```go
  UnFullscreen()
```


#### SetTitle

> SetTitle(title string)

设置应用程序标题中的标题。

```go
 runtime.Window.SetTitle("We'll need a bigger boat")
```


#### Close

关闭主窗口，从而终止应用程序。小心使用！

```go
  runtime.Window.Close()
```


### Browser

浏览器子系统提供与系统浏览器交互的方法。

<a href="https://godoc.org/github.com/wailsapp/wails/runtime#Browser"><img src="https://img.shields.io/badge/godoc-reference-blue.svg" style="margin: 0;"/></a>

#### OpenURL

> OpenURL(url string)

在系统浏览器中打开URL。

```go
runtime.Browser.OpenURL("https://wails.app")
```

### Filesystem

文件系统子系统提供了与文件系统相关的方法。目前仅限于Go。

<a href="https://godoc.org/github.com/wailsapp/wails/runtime#FileSystem"><img src="https://img.shields.io/badge/godoc-reference-blue.svg" style="margin: 0;"/></a>


#### HomeDir

> HomeDir() (string, error)

返回用户的主目录和错误。

### A Common Pattern

运行时的通用模式是将其保存为结构体的一部分，并在需要时使用它：

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
Wails有一个同步状态存储的概念：在一块区域存放前端和后端自动同步的状态。工作流程相对简单：

  * 创建一个储存
  * 设置或者更新储存中的值
  * 通过订阅存储更新来响应更新

**NOTE: 在wails运行时模块中可以找到Javascript等效方法**

#### New

> New(id string, defaultValue interface{}) (*wails.Store)

创建一个具有唯一标识符和默认值创建新存储。

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

设置储存中的值

```go
// RandomValue sets the counter to a random value
func (c *Counter) RandomValue() {
	c.store.Set(rand.Intn(1000))
}
```

#### Update 

> Update(updater interface{})

`Update`接收一个函数，函数的参数为当前储存的值，返回更新后的值

The reason `Update` accepts an interface{} is to make this as easy as possible to use in the absense of generics: The function that a store is expecting is one of the following format:

```go
func(currentValue T) T {}
```

Example - 如果你储存一个int类型的值，`Update`将接收一个函数，函数的参数将是一个int类型的值，返回一个int类型的值：

```go
c.store.Update(func(currentValue int) int {
  return currentValue * 2
})
```
{{% notice warning %}}
如果没有提供正确格式的函数，将收到致命的运行时错误。
{{% /notice %}}

#### Subscribe

> Subscribe(callback interface{})

`Subscribe` 方法接收更新存储时的回调函数。它与`Update`类似，回调函数应该使用与存储相同的数据类型:

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
回调函数在goroutine中执行
{{% /notice %}}

#### OnError

> OnError( func(error) )

`OnError` 提供为存储区发生错误处理的方法。这不是必须的，它应该只在调试同步消息的问题时使用。例如json编码发生错误时。

#### Javascript

Javascript的钩子，请使用相同的ID创建存储，并使用相同的方法：

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
