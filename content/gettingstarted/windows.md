---
title: "Windows"
date: 2019-08-29T04:54:28+10:00
draft: false
weight: 4
disableNextPrev: true
---

## Prerequisites

Wails uses cgo to bind to the native rendering engines so a number of platform dependencies are needed as well as an installation of Go. The basic requirements are:

- Go 1.12 or above
- gcc + libraries
- npm
- Docker for Cross-Compilation support

### Go

Download Go from the [Go Downloads Page](https://golang.org/dl/). We recommend downloading the MSI package.

Ensure that you follow the official [Go installation instructions](https://golang.org/doc/install#install). You will also need to ensure that your `PATH` environment variable also includes the path to your `c:\Go\bin` directory. Also ensure Go modules are enabled - You may do so through the "Environment Variables" button on the "Advanced" tab of the "System" control panel. Some versions of Windows provide this control panel through the "Advanced System Settings" option inside the "System" control panel.

Restart your terminal and do the following checks:

 * Check Go is installed corectly: `go version`
 * Check "c:\Go\bin" is in your PATH variable: `echo %PATH%`
 * If using Go 1.12 instead of 1.13, check "GO111MODULE" environment variable is set to "on": `echo %GO111MODULE%`.

{{% notice note %}}
It is **STRONGLY** recommended that if you use a terminal on Windows, that you use the git bash terminal and *not* the VSC terminal. This is because it handles signals correctly. We have found the VSC terminal to be quite buggy.
{{% /notice %}}


### npm

Download NPM from the [Node Downloads Page](https://nodejs.org/en/download/). It is best to use the latest release as that is what we generally test against.

Run `npm --version` to verify.

### GCC + Libraries

Windows requires gcc and related tooling. The recommended download is from [http://tdm-gcc.tdragon.net/download](http://tdm-gcc.tdragon.net/download).


Now you are ready to move on to the next step of [installing Wails]({{< ref "installing.md" >}}).

