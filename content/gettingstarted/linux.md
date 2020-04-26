---
title: "Linux"
date: 2019-08-29T04:54:28+10:00
weight: 2
draft: false
disableNextPrev: true
---

There are many Linux distributions in existence and we strive to support as many as possible. 

### Officially supported distros:

Distro  | Version
--------|--------
Debian  | 8, 9, 10
Ubuntu  | 16.04, 18,04, 19.04, 19.10
Arch    | Rolling
CentOS  | 6, 7
Fedora  | 29, 30


### Community supported distros:

Distro  | Version
--------|--------
Zorin   | 15
Parrot  | 4.7
Mint    | 19
Elementary | 5
Kali    | Rolling
Neon    | 5.16
VoidLinux & VoidLinux-musl | Rolling
Gentoo  | Rolling
OpenSuSE| Leap and Tumbleweed
Raspbian | default
ArcoLinux | default



_If you don't see your distro in the lists bellow you have two options. Either open a ticket asking for support or if you feel adventurus follow the [guide how to add support for your linux distro]({{}}) and also consider making a PR so the community  can benefit._


## Prerequisites

Wails uses cgo to bind to the native rendering engines so a number of platform dependencies are needed as well as an installation of Go. The basic requirements are:

- Go 1.12 or above
- npm
- gcc, gtk, webkitgtk
- Docker for Cross-Compilation support

### Go

Download Go either using your system package manager or from the [Go Downloads Page](https://golang.org/dl/).

Ensure that you follow the official [Go installation instructions](https://golang.org/doc/install#install). 

Add `$GOPATH/bin` to the `PATH` and `on` to the `GO111MODULE` environment variables. You can do this by adding these lines to your `/etc/profile` (for a system-wide installation) or `$HOME/.profile`:

```bash
export PATH=$PATH:$GOPATH/bin
export GO111MODULE=on
```

{{% notice note %}}
_Note: changes made to a profile file may not apply until the next time you log into your computer. To apply the changes immediately, just run the shell commands directly or execute them from the profile using a command such as `source $HOME/.profile`._
{{% /notice %}}

### npm

Download `npm` either using your system package manager or from the [Node Downloads Page](https://nodejs.org/en/download/). It is best to use the latest release as that is what we generally test against.

Run `npm --version` to verify.

### gcc, gtk, webkit

For Linux, Wails uses `gcc`, `webkit` and `GTK`. These need to be installed using the distribution specific commands below.

#### Debian/Ubuntu & derivatives

`sudo apt install build-essential libgtk-3-dev libwebkit2gtk-4.0-dev`

#### Arch Linux & derivatives

`sudo pacman -S gcc pkgconf webkit2gtk gtk3`

#### Centos

`sudo yum install gcc-c++ make pkgconf-pkg-config webkitgtk3-devel gtk3-devel`

#### Fedora

`sudo yum install gcc-c++ make pkgconf-pkg-config webkit2gtk3-devel gtk3-devel`

#### VoidLinux & VoidLinux-musl

`xbps-install base-devel gtk+3-devel webkit2gtk-devel`

#### Gentoo

`sudo emerge gtk+:3 webkit-gtk`


{{% notice note %}}
If you have successfully installed these dependencies on a different flavour of Linux, please consider clicking the "Edit this page" link at the top of the page and submit a PR.
{{% /notice %}}

Now you are ready to move on to the next step of [installing Wails]({{< ref "installing.md" >}}).
