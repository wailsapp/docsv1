---
title: "Linux"
date: 2019-08-29T04:54:28+10:00
weight: 2
draft: false
disableNextPrev: true
---

There are many Linux distributions in existence and we strive to support as many as possible. 

## Prerequisites

Wails uses cgo to bind to the native rendering engines so a number of platform dependent libraries are needed as well as an installation of Go. The basic requirements are:

- Go 1.12 or above
- gcc + libraries
- npm

### Go

Download Go either using your system package manager or from the [Go Downloads Page](https://golang.org/dl/).

Ensure that you follow the official [Go installation instructions](https://golang.org/doc/install#install). You will also need to ensure that your `PATH` environment variable also includes the path to your `~/go/bin` directory. Also ensure Go modules are enabled: `echo "export GO111MODULE=on" >> ~/.bashrc` or whatever is appropriate for your shell. Restart your terminal and do the following checks:

 * Check Go is installed corectly: `go version`
 * Check "~/go/bin" is in your PATH variable: `echo $PATH | grep go/bin`
 * Check "GO111MODULE" environment variable is set to "on": `echo ${GO111MODULE}`

### npm

Download Go either using your system package manager or from the [Node Downloads Page](https://nodejs.org/en/download/). It is best to use the latest release as that is what we generally test against.

Run `npm --version` to verify.


### GCC + Libraries

For Linux, Wails uses `gcc`, webkit and GTK. These need to be installed using the distribution specific commands below.

#### Debian/Ubuntu 18.04/19.04, Pop!OS 19.04

`sudo apt install pkg-config build-essential libgtk-3-dev libwebkit2gtk-4.0-dev`

#### Arch / Manjaro Linux

`sudo pacman -S webkit2gtk gtk3`

#### Red Hat Based Distros

`sudo yum install webkit2gtk-devel gtk3-devel`

{{% notice note %}}
If you have successfully installed these dependencies on a different flavour of Linux, please consider clicking the "Edit this page" link at the top of the page and submit a PR.
{{% /notice %}}

Now you are ready to move on to the next step of [installing Wails]({{< ref "installing.md" >}}).
