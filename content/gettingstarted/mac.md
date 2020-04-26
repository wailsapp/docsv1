---
title: "Mac"
date: 2019-08-29T04:54:28+10:00
draft: false
weight: 3
disableNextPrev: true
---

## Prerequisites

Wails uses cgo to bind to the native rendering engines so a number of platform dependencies are needed as well as an installation of Go. The basic requirements are:

- Go 1.12 or above
- gcc + libraries
- npm
- Docker for Cross-Compilation support

### Go

Download Go from the [Go Downloads Page](https://golang.org/dl/).

Ensure that you follow the official [Go installation instructions](https://golang.org/doc/install#install). You will also need to ensure that your `PATH` environment variable also includes the path to your `~/go/bin` directory. Also ensure Go modules are enabled: `echo "export GO111MODULE=on" >> ~/.bashrc` or whatever is appropriate for your shell. Restart your terminal and do the following checks:

 * Check Go is installed corectly: `go version`
 * Check "~/go/bin" is in your PATH variable: `echo $PATH | grep go/bin`
 * Check "GO111MODULE" environment variable is set to "on": `echo ${GO111MODULE}`

### npm

Download NPM from the [Node Downloads Page](https://nodejs.org/en/download/). It is best to use the latest release as that is what we generally test against.

Run `npm --version` to verify.

### GCC + Libraries

For MacOS, Wails uses `clang` and the native libraries. Make sure you have the xcode command line tools installed. This can be done by running:

`xcode-select --install`

Now you are ready to move on to the next step of [installing Wails]({{< ref "installing.md" >}}).
