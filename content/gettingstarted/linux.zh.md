---
title: "Linux"
date: 2019-08-29T04:54:28+10:00
weight: 2
draft: false
disableNextPrev: true
---

目前有许多 Linux 发行版，我们努力支持尽可能多的发行版。

### 官方支持的发行版:

| 发行版 | 版本                       |
| ------ | -------------------------- |
| Debian | 8, 9, 10                   |
| Ubuntu | 16.04, 18,04, 19.04, 19.10 |
| Arch   | Rolling                    |
| CentOS | 6, 7                       |
| Fedora | 29, 30                     |

### 社区支持的发行版:

| 发行版                     | 版本                |
| -------------------------- | ------------------- |
| Zorin                      | 15                  |
| Parrot                     | 4.7                 |
| Mint                       | 19                  |
| Elementary                 | 5                   |
| Kali                       | Rolling             |
| Neon                       | 5.16                |
| VoidLinux & VoidLinux-musl | Rolling             |
| Gentoo                     | Rolling             |
| OpenSuSE                   | Leap and Tumbleweed |
| Raspbian                   |
| ArcoLinux                  |
| Deepin                     |
| Manjaro + Manjaro-ARM      | Rolling             |

_如果您在下面的列表中看不到自己的发行版，你有有两种选择。一种是新开一个 Issue 寻求支持，一种是如果您喜欢探索，请按照 [如何为您的 Linux 发行版增加支持]({{}}) 并考虑进行 PR，以便让社区受益_

## 先决条件

Wails 使用 cgo 绑定到原生渲染引擎，因此需要许多平台依赖项以及 Go 的安装。基本要求是：

- Go 1.12 或者更高
- npm
- gcc, gtk, webkitgtk
- Docker for Cross-Compilation support

### Go

使用系统软件包管理器或从 [Go 下载页面](https://golang.org/dl/).

确保遵循官方的 [Go 安装说明](https://golang.org/doc/install#install).

将 `$GOPATH/bin`添加到 `PATH` 将 `on` 添加到 `GO111MODULE` 环境变量. 也可以将以下内容放到 `/etc/profile` (for a system-wide installation) or `$HOME/.profile`文件中:

```bash
export PATH=$PATH:$GOPATH/bin
export GO111MODULE=on
```

{{% notice note %}}
_注意：对配置文件的更改可能要等到下一次登录计算机后才能应用。 想要立即生效, 只需要运行如 `source $HOME/.profile`之类的 shell 命令即可_
{{% /notice %}}

### npm

从 [Node 下载界面](https://nodejs.org/en/download/) 下载 `npm`. 最好使用最新版，因为这是我们通常会测试的版本。

运行 `npm --version` 验证安装是否成功.

### gcc, gtk, webkit

对于 Linux, Wails 使用 `gcc`, `webkit` and `GTK`. 这些需要使用下面的特定于发行版的命令进行安装。

#### Debian/Ubuntu 及其衍生版本

`sudo apt install build-essential libgtk-3-dev libwebkit2gtk-4.0-dev`

#### Arch Linux 及其衍生版本

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
如果您已成功在不同版本的 Linux 上安装了这些依赖项，请考虑单击页面顶部的"编辑此页面"链接并提交 PR。
{{% /notice %}}

现在，您可以继续进行[Wails 安装]({{< ref "installing.zh.md" >}})的下一步.
