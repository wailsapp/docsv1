+++
title = "关于"
date = 2019-08-29T19:33:11+10:00
weight = 1
+++

Wails 允许你使用 Go 和 Web 技术编写桌面应用程序。对于前端，使用 [Webview](https://github.com/zserge/webview) 库. 不过它使用平台的原生渲染引擎（当前 Linux 和 Mac 使用 Webkit，Windows 使用 MSHTML）。 前端使用 HTML / Javascript / CSS 编码，后端是纯 Go 语言。 Wails 提供了一种绑定机制，可以将返回 Promise 对象的方法导出给前端。 该项目编译为单个可执行文件，将所有资源打包到其中。 在 Windows 和 MacOS 上，可以将二进制文件捆绑到特定于平台的程序包中进行分发。

注意：渲染引擎是 WebView，不是捆绑的 Web 浏览器，因此某些`浏览器 API`将对您的应用程序不可用，例如 localstorage。 大多数应用程序不需要使用它们，如果您仍然想这样做，可以使用 Go 来完成大多数功能。
