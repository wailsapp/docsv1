+++
title = "调试"
date = 2019-08-29T04:56:50+10:00
weight = 5
chapter = false
+++

Compiling your application using `wails build -d` will create a debug version of your application. This means the following:

- Logs will be output to the console it was started in
- You can control the log level by using the `-loglevel` flag when launching your application
- The Developer tools will be accessible in your app via the right click menu (Linux & Mac)

## Windows

On Windows, the Webview component doesn't have developer tools natively. To mitigate this (at least to some degree), we have a hosted version of Firebug you can inject into your app using the `-firebug` build flag.

### Debugging using Visual Studio

It's possible to debug in Visual Studio by doing the following:

- Install a recent version of Visual Studio (The free “Community” Edition is fine. The following steps are based on VS 2017). Make sure the “ASP.NET and web development” workload is selected in the installer;

- Open Debug -> Attach to Process...;

- Click Select... and choose Debug there code types: -> Script

- Find and select the process of your Wails application in Available Pocesses;

- Click Attach. The DOM Explorer and JavaSript Console should now show up. If not, open them in Debug -> Windows.

Many thanks to our friends over at [DeskGap](https://deskgap.com/devtools/) for this information!
