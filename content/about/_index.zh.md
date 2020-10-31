+++
title = "关于"
date = 2019-08-29T19:33:11+10:00
weight = 1
+++

Wails 是一个框架，可以使用 Go 和 Web 技术帮助编写桌面应用程序。对于前端，使用 [Webview](https://github.com/zserge/webview) 库. 不过它使用平台的本机渲染引擎（当前用于 Linux 和 Mac 的 Webkit，用于 Windows 的 MSHTML）。 前端使用 HTML / Javascript / CSS 编码，后端是纯 Go 语言。 通过绑定机制，可以将 Go 代码作为返回 Promise 的功能公开给前端。 该项目编译为单个可执行文件，将所有资源捆绑到其中。 在 Windows 和 MacOS 上，可以将二进制文件捆绑到特定于平台的程序包中进行分发。

注意：渲染引擎是 WebView，不是捆绑的 Web 浏览器，因此某些“浏览器 API”将对您的应用程序不可用，例如 localstorage。 大多数应用程序不需要使用它们，如果您仍然想这样做，可以使用 Go 来完成大多数事情。
