+++
title = "开发者"
date = 2019-08-29T04:51:27+10:00
weight = 30
chapter = true
disableBreadcrumb = true
+++

<!-- TODO:翻译未完成 -->

# 开发者

在本节中，我们涵盖了 Wails 开发和贡献指南的所有方面。

## 概述

- 确保您使用的是 Go 1.14+
- 克隆 [主仓库](https://github.com/wailsapp/wails) 到本地目录
- 更新本地仓库
- 更改完成后, 运行 `wails/scripts/build.sh`或者在相同目录运行 `go run build.go` 。 构建打包完成后将在本地安装 wails cli 程序
- 生成项目时，请确保更新项目的`go.mod`文件以指向 Wails 的本地安装目录。

示例:
首次生成时，go.mod 文件如下所示:

```
module test

go 1.14

require (
  github.com/leaanthony/mewn v0.10.7
  github.com/wailsapp/wails v1.0.3-pre2
)
```

使用 `replace`表示本地安装:

```
module test

go 1.14

require (
  github.com/leaanthony/mewn v0.10.7
  github.com/wailsapp/wails v1.0.3-pre2
)
replace github.com/wailsapp/wails v1.0.3-pre2 => /path/to/your/local/wails
```

## 问题驱动开发

如果有什么东西需要添加到代码中，不管是错误还是新功能，都应该新创建一个 Issue，以便进行讨论。如果继续编写代码，则应该从`develop`分支创建一个新分支，并引用 Issue ID。示例：
`64 - Support react`

提交规范应该遵循 [conventional commits](https://www.conventionalcommits.org/en/v1.0.0-beta.3/#summary)格式:

- tag[(scope)]: message
- 标签[(域)]:提交消息

| 标签            | 含义       |
| --------------- | ---------- |
| fix             | 错误修正   |
| feat            | 新功能     |
| docs            | 文档更新   |
| BREAKING CHANGE | API Change |

示例:

- fix: this is a fix for the project as a whole
- fix(cli): this is a fix for the cli
- docs: updated the contributors

ps:为了更好的交流，请尽量使用英文。

## 分支工作流

- Wails 使用类似 gitflow 的开发方法
- Feature/Bugfix 分支是从`develop`分支创建的
- 工作完成后，应针对开发分支提出拉取请求
- 随着功能的添加，`develop`分支被标记有预发行（pre-release）标签
- 每周发布一次，因此在每周周期结束时，所做的最新功能和错误修正将合并到母版中，并用下一个适当的版本进行标记。

示例:

- 在 v0.14.0 版本之后，将打开一个 Issue(#63)，请求提供支持
- 这个工作完成，将回到 `develop` 分支
- 合并后 `develop`将被标记为 `v0.14.1-pre`
- 打开一个 Issue(#64) 来请求提供支持
- 如果可以的话，PR 将合并到`develop`分支
- 合并后, `develop` 分支会有标记 `v0.14.2-pre`
- 我们在本周结束的时候将 v0.14.2-pre 合并到 master, 并标记为 v0.15.0
- 继续在`devel` 分支开发

<div class="imagecontainer">
  <img src="/images/develbranch.png">
</div>

## 工具

Wails Cli 内置了开发人员工具，但需要激活。要开发新版本，请执行以下操作：

```
cd cmd/wails
go install --tags=dev
```

这将解锁一个带有`wails dev`的开发子命令。

### 创建一个新的项目模板

在启用了开发人员的 cli 之后，您可以运行`wails dev newtemplate`来创建新的项目模板。系统将询问您一些有关模板的问题，结果将在`<project-root>/cmd/templates`中创建一个新目录。

下面是一个运行示例：

```
Wails v0.14.4-pre - Generating new project template

? Please enter the name of your template (eg: React/Webpack Basic): Mithril Basic
? Please enter a short description for the template (eg: React with Webpack 4): Mithril with Webpack 3
? Please enter a long description: Mithril v2.0.0-rc.4 with Webpack 4
? Please enter the name of the directory the frontend code resides (eg: frontend): frontend
? Please enter the install command (eg: npm install): npm install
? Please enter the build command (eg: npm run build): npm run build
? Please enter the serve command (eg: npm run serve): npm run serve
? Please enter the name of the directory to copy the wails bridge runtime (eg: src): src
? Please enter a directory name for the template: mithril-basic
Created new template 'Mithril Basic' in directory '/Users/lea/Projects/wails/cmd/templates/mithril-basic'
```

这将生成 `template.json`文件:

```json
{
  "name": "Mithril Basic",
  "version": "1.0.0",
  "shortdescription": "Mithril with Webpack 3",
  "description": "Mithril v2.0.0-rc.4 with Webpack 4",
  "install": "npm install",
  "build": "npm run build",
  "author": "Duncan Disorderly <frostyjack@sesh.com>",
  "created": "2019-05-20 20:16:30.394489 +1000 AEST m=+159.490635188",
  "frontenddir": "frontend",
  "serve": "npm run serve",
  "bridge": "src",
  "wailsdir": ""
}
```

_注意: `wailsdir` 目前未使用，但以后会使用 [near future](https://github.com/wailsapp/wails/issues/88)_
