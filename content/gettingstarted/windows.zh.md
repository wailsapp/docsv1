---
title: "Windows"
date: 2019-08-29T04:54:28+10:00
draft: false
weight: 4
disableNextPrev: true
---

## 先决条件

Wails 使用 cgo 绑定到原生渲染引擎，因此需要许多平台依赖项以及 Go 的安装。基本要求是：

- Go 1.12 或者更高
- gcc + 库
- npm
- Docker for Cross-Compilation support

### Go

从[Go 下载页面](https://golang.org/dl/)下载 Go。我们建议下载 MSI 软件包。

确保遵循官方的[Go 安装说明](https://golang.org/doc/install#install)。

您还需要确保您的`PATH`环境变量也包含指向`c:\Go\bin`目录的路径。还请确保启用 Go Modules-您可以通过`系统`控制面板的`高级`选项卡上的`环境变量`按钮来启用。 Windows 的某些版本通过`系统`控制面板中的`高级系统设置`选项提供此控制面板。

重新启动终端，并进行以下检查：

- 确保 Go 正确安装: `go version`
- 检查 "c:\Go\bin" 在你的 PATH 变量: `echo %PATH%`
- 如果使用 Go 1.12 而不是 1.13, 检查 "GO111MODULE" 环境变量设置为 "on": `echo %GO111MODULE%`.

{{% notice note %}}
**强烈**建议如果在 Windows 上使用终端，请使用 git bash 终端，而不要使用 VSC 终端。这是因为它可以正确处理命令。我们发现 VSC 终端有很多问题。
{{% /notice %}}

### npm

从[Node 下载页面](https://nodejs.org/en/download/)下载 NPM。最好使用最新版本，因为这是我们通常会测试的版本。

运行 `npm --version` 验证安装是否成功.

{{% notice note %}}
npx 存在一个[已知问题](https://github.com/npm/npx/issues/14) 涉及到用户配置文件路径中的空格。如果你有问题，可以查看该 Issue。
{{% /notice %}}

### GCC + 库

Windows 需要 gcc 和相关工具。推荐从 [https://jmeubank.github.io/tdm-gcc/download/](https://jmeubank.github.io/tdm-gcc/download/)下载。

{{% notice note %}}

Windows 上的 Wails 应用程序使用仅与 IE11 兼容的 mshtml 库。**强烈**建议您使用`wails serve`命令使用 IE11 来测试您的应用程序。有关更多信息，请参考我们的[Windows 开发指南]({{<ref "../guides/windows.md" >}}).
{{% /notice %}}

现在，您可以继续进行[Wails 安装]({{< ref "installing.zh.md" >}})的下一步.
