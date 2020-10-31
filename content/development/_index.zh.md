+++
title = "开发"
date = 2019-08-29T04:51:27+10:00
weight = 30
chapter = true
disableBreadcrumb = true
+++

<!-- TODO:翻译未完成 -->

# 开发

在本节中，我们涵盖了 Wails 开发和贡献指南的所有方面。

## 概述

- 确保您使用的是 Go 1.14+
- 克隆主仓库 [main repository](https://github.com/wailsapp/wails) 到本地目录
- 更新本地仓库
- 更改完成后, 运行 `wails/scripts/build.sh`或者在相同目录运行 `go run build.go` 。 构建打包完成后将在本地安装 wails cli
- 生成项目时，请确保更新项目的`go.mod`文件以指向 Wails 的本地安装。

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

使用 `replace`标识本地安装:

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

如果有什么东西需要添加到代码中，不管是新功能还是 bug，都应该新开一个 Issue，以便进行讨论。如果继续编码，则应该从“develop”分支创建一个新分支，并引用 Issue ID。示例：
`64 - Support react`

提交规范应该遵循 [conventional commits](https://www.conventionalcommits.org/en/v1.0.0-beta.3/#summary)格式:

- tag[(scope)]: message

| Tag             | Meaning    |
| --------------- | ---------- |
| fix             | 错误修正   |
| feat            | 新功能     |
| docs            | 文档更新   |
| BREAKING CHANGE | API Change |

示例:

- fix: this is a fix for the project as a whole
- fix(cli): this is a fix for the cli
- docs: updated the contributors

## 分支工作流

- Wails uses a gitflow-like approach to development
- Feature/Bugfix branches are created from the `develop` branch
- Once the work is complete, pull requests should be made against the develop branch
- As features are added, the `develop` branch is tagged with pre-release tags
- Releases are made weekly, so at the end of the weekly cycle, the latest features and bugfixes that were made will be merged to master and tagged with the next appropriate version.

Example:

- After release v0.14.0, a ticket (#63) is opened requesting react support
- This is worked on and a PR is made back to `develop`
- Once merged, `develop` is tagged with `v0.14.1-pre`
- A ticket (#64) is opened requesting ultralight support
- This is worked on and a PR is made back to `develop`
- Once merged, `develop` is tagged with `v0.14.2-pre`
- We reach the end of our week and merge v0.14.2-pre to master, tagging it as v0.15.0
- Work continues on the `devel` branch

<div class="imagecontainer">
  <img src="/images/develbranch.png">
</div>

## Tooling

The Wails cli has developer tooling built in, but needs activating. To create a developer version, do the following:

```
cd cmd/wails
go install --tags=dev
```

This unlocks a `wails dev` command that has subcommands for development.

### Creating new project templates

With a developer enabled cli, you can run `wails dev newtemplate` to create a new project template. You will be asked a number of questions regarding your template and as a result, a new directory will be created in `<project-root>/cmd/templates`.

Here is an example run:

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

This generates the following `template.json`:

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

_Note: The `wailsdir` key is currently unused but will be used in place of bridge in the [near future](https://github.com/wailsapp/wails/issues/88)_
