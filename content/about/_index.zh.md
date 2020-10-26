+++
title = "关于"
date = 2019-08-29T19:33:11+10:00
weight = 1
+++

Wails是一个框架，可帮助使用Go and Web Technologies编写桌面应用程序， 使用 [Webview](https://github.com/zserge/webview) 库. 它使用平台的本机渲染引擎（当前用于Linux和Mac的Webkit，用于Windows的MSHTML）。 前端使用HTML / Javascript / CSS编码，后端是纯Go语言。 通过绑定机制，可以将Go代码作为返回Promise的功能公开给前端。 该项目编译为单个可执行文件，将所有资产捆绑到其中。 在Windows和MacOS上，可以将二进制文件捆绑到特定于平台的程序包中进行分发。

