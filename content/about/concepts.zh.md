---
title: "概述"
date: 2019-08-29T04:54:28+10:00
draft: false
---

Wails 被设计成尽可能缩小 Web 技术和 Go 之间的差异。前端是 [Webview](https://github.com/zserge/webview) 组件, 你可以使用自己喜欢的任何常见 Javascript 框架来开发前端代码，并且可以与里面的 Go 代码进行交互。 这是通过共享的 IPC 机制来实现的。

<p align="center" style="text-align: center">
   <img src="/images/Overview.svg" width="33%"><br/>
</p>

### IPC 概述

IPC 机制可以在 2 个运行时中运行-一个运行在 Javascript 中，另一个运行在 Go 中。 它们都提供了一个简单的接口，从而减轻了开发人员直接处理 IPC 机制的负担。

<p align="center" style="text-align: center">
   <img src="/images/wailsapptech.svg" width="33%"><br/>
</p>
<!-- TODO:翻译不够准确 -->
运行时开发人员可以与之交互的公共组件：绑定和事件。

<p align="center" style="text-align: center">
   <img src="/images/IPCDetail.svg" width="33%"><br/>
</p>

### 绑定

Wails 应用程序提供了一种方法，可让导出 Go 代码（绑定）到前端。 使用此方法，可以将任意函数或公开的结构方法绑定。 在启动时，Wails 将分析绑定的函数/方法并自动在 Javascript 中提供等效函数。 使您可以直接从 Javascript 调用绑定的 Go 代码。

<p align="center" style="text-align: center">
   <img src="/images/Binding.svg" width="40%"><br/>
</p>

JavaScript 包装函数，处理了调用 Go 代码的所有复杂性。 您只需使用 Javascript 调用该函数并接收一个 Promise。
绑定 Go 代码功能，处理了绑定的所有复杂性。 如果对 Go 代码的调用成功完成，则结果将传递到 resolve 函数。 如果返回错误，则将其传递给 reject 函数。

### 事件

Wails 提供了一个统一的事件系统，类似于 Javascript 的原生事件系统。 这意味着从 Go 或 Javascript 发送的任何事件都可以由另一方接收。 数据可以随任何事件一起传递。 这样，您就可以做一些简单的事情，例如让后台进程在 Go 中运行，并通知前端去更新页面。

<p align="center" style="text-align: center">
   <img src="/images/Events.svg" width="40%"><br/>
</p>
