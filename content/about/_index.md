+++
title = "About"
date = 2019-08-29T19:33:11+10:00
weight = 1
+++

Wails is a framework to help write desktop apps using Go and Web Technologies. For the frontend, it uses the [Webview](https://github.com/zserge/webview) library. This in turn uses the native rendering engine for the platform (currently Webkit for Linux & Mac, MSHTML for Windows). The frontend is coded using HTML/Javascript/CSS and the backend is pure Go. It is possible to expose Go code to the frontend, as functions that return promises, through a binding mechanism. The project compiles down to a single executable, bundling all assets into it. On Windows and MacOS, it is possible to bundle the binary into the platform-specific package for distribution.

