---
title: "Installing Wails"
date: 2019-08-29T04:54:28+10:00
draft: false
weight: 10
disableNextPrev: true
---

Installation is as simple as running the following command:

```
go get github.com/wailsapp/wails/cmd/wails
```

{{% notice tip %}}
Once installed, the `wails update` command may be used for subsequent updates.
{{% /notice %}}

{{% notice tip %}}
To get the latest [pre-release]({{< relref "/development/_index.md#branch-workflow" >}}) with bleeding-edge features the `-pre` flag can be appended `wails update -pre`.
{{% /notice %}}

### Setup

To finish the installation setup your Wails system by running the [setup command]({{< relref "/reference/cli.md#setup" >}}) `wails setup` and filling your handle and email.

## Generate a new project

Generate a new project using the [init command]({{< relref "/reference/cli.md#init" >}}) `wails init`.

Select the default options.

## Build it!

Change into the project directory `cd my-project` and compile your application using the [build command]({{< relref "/reference/cli.md#build" >}}) `wails build`.

If all went well, you should have a compiled program in your local directory. Run it with `./my-project` or double click `myproject.exe` if on windows.

<div class="imagecontainer">
<img src="/images/app.png" style="width:65%">
</div>

### Serve

#### `wails serve`

While developing your apps using wails the preferred method is by the [serve command]({{< relref "/reference/cli.md#serve" >}}) `wails serve`.

{{% notice tip %}}
This produces a much **faster** lightweight build in _debug_ mode, excluding `npm` build scripts, saving time when developing the backend and also enabling use of `npm run serve` for partial browser development of frontend!
{{% /notice %}}

#### `npm run serve`

Change into the frontend directory `cd my-project/frontend` and serve your GUI using `npm run serve`.


## Next Steps

If you would like to start making an app right away, we suggest you explore Wails via our _awesome_ [tutorials]({{% relref "../tutorials" %}}).
If you would prefer to get to know the framework a little better before building anything, we suggest having a look through the
[concepts]({{% relref "../about/concepts.md" %}}).
Finally if you are advanced user and would like to get right in to it head over to the [API reference]({{% relref "../reference/api" %}}) & [Cli reference]({{% relref "../reference/cli" %}}) sections.


{{% notice tip %}}
Come by our [Slack](https://gophers.slack.com/messages/CJ4P9F7MZ) channel ([_Invite_](https://invite.slack.golangbridge.org)) for 24/7 support or just to share with us what you've built with wails!
{{% /notice %}}