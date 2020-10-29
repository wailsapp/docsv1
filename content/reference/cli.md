+++
title = "CLI"
date = 2019-08-29T04:56:50+10:00
weight = 20
chapter = false
+++

Wails comes with a CLI tool that allows you to generate, build and bundle your projects. It deals with the complexity of juggling Go and Javascript environments.

It has a number of commands:

### Help

> wails --help

This will output the cli help message with all the available commands and flags.

### Setup

> wails setup

The setup command does a number of things - it asks you for your name and email so that it can fill in project templates with your details. It also checks to see if your environment has the dependencies it needs and if not, try to suggest ways on how to install those dependencies.

Setup is also the default command so it can be invoked by simply running `wails`.

<div class="videocontainer">
<video width="727" height="454" controls>
  <source src="/videos/setup.mp4" type="video/mp4">
  Your browser does not support the video tag.
</video>
</div>

### Init

> wails init

The init command builds out a new project based on a template of your choice. We curently support a number of frameworks including Vue, Vuetify, React, Angular, Svelte and even vanilla JS. The project will be built automatically after initialisation.

<div class="videocontainer">
<video width="727" height="454" controls>
  <source src="/videos/init.mp4" type="video/mp4">
  Your browser does not support the video tag.
</video>
</div>

### Serve

> wails serve

When you run `wails serve`, it will compile up the backend and run it in headless mode. This allows you to develop the frontend using your standard tooling. When you run your app, it will connect to the backend at startup and make all your backend functions available to you.

We will cover this more in the tutorial.

<div class="videocontainer">
<video width="727" height="390" controls>
  <source src="/videos/serve.mp4" type="video/mp4">
  Your browser does not support the video tag.
</video>
</div>

### Build

> wails build

The build command is the Wails equivalent of `go build`, however it does a number of things:

- Installs frontend dependencies if needed
- Performs a build of the frontend
- Packs the frontend using Webpack
- It downloads any Go dependencies that are required
- It finally compiles and bundles everything into a single binary

<div class="videocontainer">
<video width="727" height="454" controls>
  <source src="/videos/build.mp4" type="video/mp4">
  Your browser does not support the video tag.
</video>
</div>

### Build Flags

Here is a list of all available flags:

| Flag | Description                                  |
| ---- | -------------------------------------------- |
| -f   | Force rebuild of frontend dependencies       |
| -d   | Build application in Debug mode              |
| -p   | Package application after a successful build |
| -x   | Cross-compile to different platform          |
| -gopath   | Specify your GOPATH location. Mounted to /go during cross-compilation. |
| -ldflags   | Extra options for -ldflags |
| -tags   | Build tags to pass to the go compiler (quoted and space separated) |
| -firebug | Inject the firebug console in debug builds (Good for Windows)   |
| -verbose | Increased output from the build process  |


The `-p` flag is currently supports OSX and Windows. On OSX, it bundles your binary into a .app file with the default icon. On Windows, it will generate the application resource files and compile it all into a '.exe'. When the `-p` flag is used, the packaging files are left available for editing. Any changes will be picked up by the next build (eg icon).

#### Cross-Compilation

Wails support cross-compiling to the other supported platforms. The target platform is passed using the `-x` flag. Example:

`wails build -x windows/amd64` 

This also supports the `-p` flag for packaging.

The builds are placed in a `build` directory, in your project root directory.

**NOTE: [Docker](https://www.docker.com/) is required for this feature**

Wails uses a custom [xgo](https://github.com/crazy-max/xgo) build and may be found [here](https://github.com/wailsapp/xgo). The image is published [here](https://hub.docker.com/r/wailsapp/xgo) on Dockerhub.

### Update

> wails update

This command does a check to see if the current version is the latest. If not, it will download and install the latest version. It is possible to also use it to install 'prerelease' versions by using the `-pre` flag. If a specific version is required, then it supports a `-version` flag.

Example: `wails update -pre` will update the latest prerelease version

### Issue

> wails issue

This command speeds up the process for submitting an issue to the Wails project. When you run the command, you will be asked to answer a couple of questions:

<div class="imagecontainer">
  <img src="/images/issue.png" style="width:75%">
</div>

Wails then determines some environmental details such as it's own version, opens a browser and fills in the default issue template.

<div class="imagecontainer">
  <img src="/images/issuebrowser.png" style="width:100%">
</div>

Please note: you can edit the template as you feel fit before submitting.

You now have a good basis for your template. Running `wails init` will now give you your template as an option to install. When the project is generated using the template, it will create directories, copy non-template files then copy template files. Template files end in .template and will be treated as standard Go templates in which embedded codes are substituted with values in the [Project Options](https://github.com/wailsapp/wails/blob/master/cmd/project.go#L139-L154).
