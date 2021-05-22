+++
title = "调试"
date = 2019-08-29T04:56:50+10:00
weight = 5
chapter = false
+++

使用`wails build -d`编译应用程序将创建应用程序的调试版本。这意味着：

- 日志将输出到启动它的控制台
- 您可以在启动应用程序时使用`-loglevel`标志来控制日志级别
- 通过右键菜单（Linux 和 Mac）可以在您的应用中访问开发者工具

## 使用 Visual Studio Code 进行调试

在项目根目录的`.vscode`目录中修改/创建以下文件（如果不存在则创建），将`myapp`替换为项目二进制名称：

**launch.json**

```
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "Wails: build debug",
      "type": "go",
      "request": "launch",
      "mode": "exec",
      "program": "${workspaceFolder}/build/myapp",
      "preLaunchTask": "wails_debug_build",
      "env": {},
      "args": []
    }
  ]
}
```

**tasks.json**

```
{
  "version": "2.0.0",
  "tasks": [
    {
      "label": "wails_debug_build",
      "type": "shell",
      "command": "wails build -d"
    }
  ]
}
```

## Windows

在 Windows 上，Webview 组件本身没有开发人员工具，但是有两种附加远程调试器的方法：

## 使用 IEChooser/F12Chooser 进行调试

可以使用鲜为人知的实用程序将 IE 开发工具附加到您的应用程序：IEChooser.exe（或 F12Chooser.exe，具体取决于您的 Windows 版本）。附加步骤为：

1. 启动您的 Wails 应用
2. 运行 `C:\Windows\System32\F12\IEChooser.exe` (or `F12Chooser.exe`)
3. 选择应用程序-它通常显示为 `about: blank`
4. 开发工具将附加到应用程序

注意：此视频是高分辨率的。全屏观看效果最佳。

<div>
  <video style="width: 100%; max-width: 1552px; max-height: 1078px;" controls>
    <source src="/videos/windows-dev-tools.mp4" type="video/mp4">
    Your browser does not support the video tag.
  </video>
</div>

### 使用 Visual Studio 进行调试

通过执行以下操作，可以在 Visual Studio 中进行调试：

- 安装 Visual Studio 的最新版本（可以使用免费的`社区`版。以下步骤基于 VS 2017）。确保在安装程序中选择了`ASP.NET和Web开发`工作负载

- 打开 Debug -> Attach to Process...

- 单击 选择... 然后选择调试那里的代码类型: -> Script

- 在可用流程中找到并选择您的 Wails 应用程序的进程

- 单击附加。现在应该显示 DOM Explorer 和 JavaSript 控制台。如果没有，请在`调试 -> Windows`中打开它们。

非常感谢我们在 [DeskGap](https://deskgap.com/devtools/) 上的朋友提供的这些信息！
