---
title: "概述"
date: 2019-08-29T04:54:28+10:00
draft: false
---

Wails 被设计成尽可能缩小 web 技术和 Go 之间的差距。前端是 [Webview](https://github.com/zserge/webview) 组件, 并且您可以使用自己喜欢的任何常见 Javascript 框架来开发前端代码，并且可以与其中的 Go 代码进行无缝交互。 这是通过共享的 IPC 机制完成的。

<p align="center" style="text-align: center">
   <img src="/images/Overview.svg" width="33%"><br/>
</p>

### IPC 概述

IPC 机制可在 2 个运行时中运行-一个运行在 Javascript 中，另一个运行在 Go 中。 它们都提供了一个简单的界面，从而减轻了开发人员无需直接处理 IPC 机制的负担。

<p align="center" style="text-align: center">
   <img src="/images/wailsapptech.svg" width="33%"><br/>
</p>

运行时共享开发人员可以与之交互的公共组件：绑定和事件。

<p align="center" style="text-align: center">
   <img src="/images/IPCDetail.svg" width="33%"><br/>
</p>

### 绑定

Wails 应用程序提供了一种方法，可让您将 Go 代码公开（绑定）到前端。 使用此方法，可以将任意函数或结构与公开的方法绑定。 在启动时，Wails 将分析绑定的函数/方法并自动以 Javascript 提供等效的函数。 这使您可以直接从 Javascript 调用绑定的 Go 代码。

<p align="center" style="text-align: center">
   <img src="/images/Binding.svg" width="40%"><br/>
</p>

JavaScript 包装函数处理了调用 Go 代码的所有复杂性。 您只需使用 Javascript 调用该函数并收到一个 Promise。
绑定 Go 代码的功能处理了绑定的所有复杂性。 如果对 Go 代码的调用成功完成，则结果将传递到 resolve 函数。 如果返回错误，则将其传递给拒绝函数。

### 事件

威尔提供了一个统一的事件系统，类似于 Javascript 的本地事件系统。 这意味着从 Go 或 Javascript 发送的任何事件都可以由任何一方接收。 数据可以与任何事件一起传递。 这样，您就可以做一些整洁的事情，例如在 Go 中运行后台进程并通知任何更新的前端。

<p align="center" style="text-align: center">
   <img src="/images/Events.svg" width="40%"><br/>
</p>
