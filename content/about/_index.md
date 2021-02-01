+++
title = "About"
date = 2019-08-29T19:33:11+10:00
weight = 1
+++

Wails is a framework to help write desktop apps using Go and web technologies. For the frontend, it uses the [Webview](https://github.com/zserge/webview) library. This in turn uses the native rendering engine for the platform (currently Webkit for Linux & Mac, MSHTML for Windows). The frontend is coded using HTML/Javascript/CSS and the backend is Go. Wails provides a binding mechanism to expose Go code to the frontend using functions that return promises. The project compiles down to a single executable, bundling all assets into it. On Windows and MacOS, it is possible to bundle the binary into the platform-specific package for distribution. 

NOTE: The rendering engines are WebViews and **not** a bundled web browser, so certain "browser APIs" will not be available to your application, such as localstorage. Most applications don't need to use these, but if you do you can achieve most things using Go instead.
