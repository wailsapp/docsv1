---
title: "Mac"
date: 2019-08-29T04:54:28+10:00
draft: false
weight: 3
disableNextPrev: true
---

## 先决条件

Wails 使用 cgo 绑定到原生渲染引擎，因此需要许多平台依赖项以及 Go 的安装。基本要求是：

- Go 1.12 或者更高
- gcc + 库
- npm
- Docker for Cross-Compilation support

### Go

从[Go 下载页面](https://golang.org/dl/)下载 Go。

确保遵循官方的[Go 安装说明](https://golang.org/doc/install#install)。您还需要确保您的`PATH`环境变量也包含您的`~/go/bin`目录的路径。同时确保启用 Go Modules：`echo "export GO111MODULE=on" >> ~/.bashrc`或任何适合您的 shell 。重新启动终端，并进行以下检查：

- 确保 Go 正确安装: `go version`
- 确保 "~/go/bin" 目录在 PATH 环境变量: `echo $PATH | grep go/bin`
- 确保 "GO111MODULE" 环境变量设置为 "on": `echo ${GO111MODULE}`

### npm

从[Node 下载页面](https://nodejs.org/en/download/)下载 NPM。最好使用最新版本，因为这是我们通常会测试的版本。

运行 `npm --version` 验证安装是否成功.

### GCC + 库

对于 MacOS，Wails 使用`clang`和原生库。确保已安装 xcode 命令行工具。这可以通过运行以下命令来完成：
`xcode-select --install`

现在，您可以继续进行[Wails 安装]({{< ref "installing.zh.md" >}})的下一步.
