+++
title = "API"
date = 2019-08-29T04:56:50+10:00
weight = 10
chapter = false
+++

### Binding

Having just a web frontend means nothing unless you can interact with the system. Wails enables this through 'binding' - making Go code callable from the frontend. There are 2 types of code you can bind to the frontend: 
  
  * Functions
  * Struct Methods
  
  When they are bound, they may be used in the frontend.

#### Functions

Binding a function is as easy as calling `Bind` with the function name:

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
    Width:  1024,
    Height: 768,
  })
  app.Bind(Greet)
  app.Run()
}
{{< /highlight >}}


When this is run, a Javascript function called 'Greet' is made available under the global 'backend' object. The function may be invoked by calling `backend.Greet`, EG: `backend.Greet("World")`. The dynamically generated functions return a standard promise. For this simple example, you could therefore print the result as so: `backend.Greet("World").then(console.log)`.

#### Struct Methods

It is possible to bind struct methods to the frontend in a similar way. This is done by binding an instance of the struct you wish to use in the frontend. When this is done, all public methods of the struct will be made available to the frontend. Wails does not attempt, or even believe, that binding data to the frontend is a good thing. Wails views the frontend as primarily a view layer with state and business logic normally handled by the backend in Go. As such, the structs that you bind to the front end should be viewed as a "wrapper" or an "interface" onto your business logic.

Binding a struct is as easy as:

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


When the Robot struct is bound, it is made available at `backend.Robot` in the frontend. As the robot struct has a public method called `Hello`, then this is available to call at `backend.Robot.Hello`. The same is true for the `Rename` method. The robot struct also has another method called `privateMethod`, but as that is not public, it is not bound.

Here is a demonstration of how this works by running the app in debug mode and using the inspector:

<div class="imagecontainer">
  <img src="/images/robot.png" style="width:65%">
</div>

#### Struct Initialisation

If your struct has a special initialisation method, Wails will call it at startup. The signature for this method is:

```go
  WailsInit(runtime *wails.Runtime) error
```

This allows you to do some initialisation before the main application is launched.

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

If an error is returned, then the application will log the error and shutdown.

The Runtime Object that is passed to it is the primary means for interacting with the application at runtime. It consists of a number of subsystems which provide access to different parts of the system. This is detailed in the [Wails Runtime](#wails-runtime) section.

### Binding Rules

Any Go function (or method) may be bound, so long as it follows the following rules:

 - The function must return 0 - 2 results.
 - If there are 2 return parameters, the last one must be an error type.
 - If you return a struct, or struct pointer, the fields you wish to access in the frontend must have Go's standard json struct tags defined.

If only one value is returned then it will either be available in the resolve or reject part of the promise depending on if it was an error type or not.

Example 1:

```go
  func (m *MyStruct) MyBoundMethod(name string) string {
    return fmt.Sprintf("Hello %s!", name)
  }
```

In Javascript, the call to `MyStruct.MyBoundMethod` will return a promise that will resolve with a string.

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

In Javascript, the call to `MyStruct.MyBoundMethod` with a new user name will return a promise that will resolve with no value. A call to `MyStruct.MyBoundMethod` with an existing user name will return a promise that will reject with the error set to `user '$name' already exists`.

It's good practice to return 2 values, a result and an error, as this maps directly to Javascript promises. If you are not returning anything, then perhaps events may be a better fit.

### Important Detail!

A very important detail to consider is that all calls to bound Go code are run in their own goroutine. Any bound functions should be authored with this in mind. The reason for this is to ensure that bound code does not block the main event loop in the application, which leads to a frozen UI.
