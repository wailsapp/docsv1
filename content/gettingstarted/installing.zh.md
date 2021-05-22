---
title: "安装 Wails"
date: 2019-08-29T04:54:28+10:00
draft: false
weight: 10
disableNextPrev: true
---

安装过程非常简单，只需运行以下命令：

```
go get -u github.com/wailsapp/wails/cmd/wails
```

{{% notice tip %}}
安装后，`wails update`命令可用于后续更新。
{{% /notice %}}

{{% notice tip %}}
要获得最新功能的最新 [pre-release]({{< relref "/development/_index.md#branch-workflow" >}}) 可以在后面附加 `-pre` 标签： `wails update -pre`.
{{% /notice %}}

### 设置

要完成安装设置，请运行 [安装命令]({{< relref "/reference/cli.zh.md#setup" >}}) `wails setup`并且填写对你的名字和邮箱

## 生成新项目

使用[初始化命令]({{< relref "/reference/cli.zh.md#init" >}}) `wails init`生成一个新项目

选择默认选项

## 构建

切换到项目目录`cd my-project` 并且使用构建命令[构建命令]({{< relref "/reference/cli.zh.md#build" >}}) `wails build` 构建你的项目。

如果一切顺利，则应该在本地目录中有一个已编译的程序。如果使用 Windows，请使用./my-project 或双击 myproject.exe 运行它。

<div class="imagecontainer">
<img src="/images/app.png" style="width:65%">
</div>

### 服务

#### `wails serve`

使用 Wails 开发应用程序时，首选方式是通过服务命令 [服务命令]({{< relref "/reference/cli.zh.md#serve" >}}) `wails serve`.

{{% notice tip %}}
这样可以在 _debug_ 模式下 **更快的** 构建你的项目, 不包含 `npm` 构建脚本, 可以节省开发后端的时间，当然，你也可以运行 `npm run serve` 用于前端部分在浏览器开发
{{% /notice %}}

#### `npm run serve`

切换到目录 `cd my-project/frontend` 并且使用`npm run serve`开启 GUI 服务 .

## 下一步

如果你想马上开始制作一个应用程序，我们建议你通过我们的*awesome* [tutorials]({{% relref "../tutorials" %}}).
如果您希望在构建任何东西之前更好地了解框架，我们建议您查看一下
[concepts]({{% relref "../about/concepts.zh.md" %}}).
最后，如果您是高级用户并且希望直接进入到 [API 参考]({{% relref "../reference/api" %}}) 和[Cli 参考]({{% relref "../reference/cli" %}}) 部分

{{% notice tip %}}
来我们的 [Slack](https://gophers.slack.com/messages/CJ4P9F7MZ) 频道 ([_邀请_](https://invite.slack.golangbridge.org)) 聊天或者和我们分享你用 wails 构建起来的作品。
{{% /notice %}}
