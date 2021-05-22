+++
title = "接口"
date = 2019-08-29T04:56:50+10:00
weight = 10
chapter = false
+++

### 创建你的应用

应用程序是使用 `wails.CreateApp`创建的。 这需要一个 [可选配置](https://pkg.go.dev/github.com/wailsapp/wails#AppConfig) ,它允许你自定义应用程序。

拥有应用程序后，可以将方法绑定到该应用程序。

### 绑定

除非您可以与系统进行交互，否则仅使用 Web 前端就没有任何意义。 Wails 通过`绑定`实现了这一点-可以从前端调用 Go 代码。 您可以将两种代码绑定到前端：

- 函数
- 结构体方法

绑定它们，就可以在前端使用它们。

#### 函数

绑定一个函数就像用函数名调用`Bind`一样容易：

{{< highlight go "hl_lines=18" >}}
package main

import (
"github.com/wailsapp/wails"
"fmt"
)

func Greet(name string) string {
return fmt.Printf("Hello %s!", name)
}

func main() {

app := wails.CreateApp(&wails.AppConfig{
Width: 1024,
Height: 768,
})
app.Bind(Greet)
app.Run()
}
{{< /highlight >}}

当这个函数运行时，一个名为`Greet`的 Javascript 函数在全局`backend`对象下可用。可以通过调用`backend.Greet`，例如：`backend.Greet("World")`。动态生成的函数返回一个标准的 Promise。对于这个简单的例子，您可以这样打印结果：`backend.Greet("World").then(console.log)`。

##### 类型转换

标量类型会自动转换为相关的 Go 类型。 对象被转换为`map[string] interface{}`。如果要在 Go 中创建这些具体类型，建议您使用 Hashicorp 的[mapstructure](https://github.com/mitchellh/mapstructure).

例如:

使用默认的 Vue 模板项目，我们更新`main.go`以包含我们的 struct 和 callback 函数：

```go
  type MyData struct {
    A string
    B float64
    C int64
  }

  //我们希望有以下形式的javascript对象：
  // { A: "", B: 0.0, C: 0 }
  func basic(data map[string]interface{}) string {
    var result MyData
    fmt.Printf("data: %#v\n", data)

    err := mapstructure.Decode(data, &result)
    if err != nil {
      // 处理错误
    }
    fmt.Printf("result: %#v\n", result)
    return "Hello World!"
  }
```

在前端，我们更新`HelloWorld.vue`组件中的`getMessage`方法以发送对象：

```go
    getMessage: function() {
      var self = this;
      var mytestStruct = {
        A: "hello",
        B: 1.1,
        C: 99
      }
      window.backend.basic(mytestStruct).then(result => {
        self.message = result;
      });
    }
```

运行此命令时，将获得以下输出：

```
data: map[string]interface {}{"A":"hello", "B":1.1, "C":99}
Result: main.MyData{A:"hello", B:1.1, C:99}
```

{{% notice warning %}}
警告：建议业务逻辑和数据结构主要位于应用程序的`执行`部分，然后使用事件将更新发送到前端。 在两个地方管理状态会导致开发体验很糟糕。
{{% /notice %}}

#### 结构体方法

可以通过类似的方式将 struct 方法绑定到前端。 这是通过绑定要在前端使用的结构实例来完成的。 完成此操作后，该结构的所有公共方法将对前端可用。 wails 没有尝试甚至不相信将数据绑定到前端是一件好事。 wails 将前端视为主要是一个视图层，其中状态和业务逻辑通常由 Go 中的后端处理。 这样，您绑定到前端的结构应被视为业务逻辑上的`wrapper`或`interface`。

绑定一个结构体很容易：

> robot.go

```go
package main

import "fmt"

type Robot struct {
	Name string
}

func NewRobot() *Robot {
	result := &Robot{
		Name: "Robbie",
	}
	return result
}

func (t *Robot) Hello(name string) string {
	return fmt.Sprintf("Hello %s! My name is %s", name, t.Name)
}

func (t *Robot) Rename(name string) string {
	t.Name = name
	return fmt.Sprintf("My name is now '%s'", t.Name)
}

func (t *Robot) privateMethod(name string) string {
	t.Name = name
	return fmt.Sprintf("My name is now '%s'", t.Name)
}

```

> main.go

```go
package main

import "github.com/wailsapp/wails"

func main() {
	app := wails.CreateApp(&wails.AppConfig{
		Width:  1024,
		Height: 768,
		Title:  "Binding Structs",
	})

	app.Bind(NewRobot())
	app.Run()
}
```

绑定 Robot 结构后，可以在前端的`backend.Robot`处使用它。 由于 robot 结构具有名为`Hello`的公共方法，因此可以在`backend.Robot.Hello`处调用该方法。 对于`Rename`方法也是如此。 robot 结构还具有另一个称为`privateMethod`的方法，但由于该方法不是公共的，因此未绑定。

这是通过以调试模式运行应用程序并使用检查器来演示其工作方式的示例：

<div class="imagecontainer">
  <img src="/images/robot.png" style="width:65%">
</div>

#### Struct Initialisation

如果您的结构具有特殊的初始化方法，Wails 将在启动时调用它。 该方法的签名是：

```go
  WailsInit(runtime *wails.Runtime) error
```

这使您可以在启动主应用程序之前进行一些初始化。

```go
  type MyStruct struct {
    runtime *wails.Runtime
  }

  func (s *MyStruct) WailsInit(runtime *wails.Runtime) error {
    // Save runtime
    s.runtime = runtime

    // Do some other initialisation

    return nil
  }

```

如果返回错误，则应用程序将记录错误并关闭。

传递给它的运行时对象是在运行时与应用程序进行交互的主要手段。 它由许多子系统组成，这些子系统提供对系统不同部分的访问。 在 [Wails Runtime](#wails-runtime) 部分中对此进行了详细说明。

#### 结构体关闭

如果您的结构具有特殊的关闭方法，那么 Wails 会在应用程序关闭期间调用它。 该方法的签名是：

```go
  WailsShutdown()
```

这使您可以在终止主应用程序时清理所有资源。

```go
  type MyStruct struct {
    runtime *wails.Runtime
  }

  func (s *MyStruct) WailsInit(runtime *wails.Runtime) error {
    // Save runtime
    s.runtime = runtime

    // Allocate some resources...

    return nil
  }


  func (s *MyStruct) WailsShutdown() {

    // De-Allocate some resources...

}

```

### 绑定规则

只要遵循以下规则，任何 Go 函数（或方法）都可以绑定：

-该函数必须返回 0-2 个结果。 -如果有 2 个返回参数，则最后一个必须为错误类型。 -如果返回结构或结构指针，则希望在前端访问的字段必须定义 Go 的标准 json 结构标签。

如果仅返回一个值，则该值将在 promise 的 resolve 或 reject 中可用，具体取决于它是否是错误类型。

示例 1:

```go
  func (m *MyStruct) MyBoundMethod(name string) string {
    return fmt.Sprintf("Hello %s!", name)
  }
```

在 Javascript 中，对`MyStruct.MyBoundMethod`的调用将返回一个将用字符串解析的 promise。

> Example 2

```go
  ...
  func (m *MyStruct) AddUser(name string) error {
    if m.userExists(name) {
      return fmt.Errorf("user '%s' already exists");
    }
    m.saveUser(name)
    return nil
  }
  ...
```

在 Javascript 中，使用新用户名调用`MyStruct.MyBoundMethod`将返回一个 Promise，该 Promise 将没有任何价值。 用现有用户名调用`MyStruct.MyBoundMethod`将返回一个 Promise，该 Promise 将被拒绝，错误设置为`\$name' already exists`。

最好返回两个值，一个结果和一个错误，因为这直接映射到 Javascript Promise。 如果您什么都没有退回，那么事件可能更合适。

### 重要细节！

要考虑的一个非常重要的细节是，对绑定的 Go 代码的所有调用都在其自己的 goroutine 中运行。 在编写任何绑定函数时都应牢记这一点。 这样做的原因是确保绑定的代码不会阻止应用程序中的主事件循环，从而导致 UI 冻结。
