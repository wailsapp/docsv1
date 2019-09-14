+++
title = "Switch to Yarn"
date = 2019-08-29T04:56:50+10:00
weight = 10
chapter = false
+++

Wails officially supports only `npm`. But if you wish to use `Yarn` instead, switching is really easy. 

[On a newly created project]({{}}), locate the file `project.json`.


{{< highlight log "hl_lines=5" >}}
my-new-project
├── frontend
├── go.mod
├── main.go
└── project.json
 {{< /highlight >}}

 Open the file with your favourite editor and modify fields containing `npm` commands by replacing  them with the appropriate `yarn` ones.

{{< highlight json "hl_lines=15 12-13" >}}
{
 "name": "My Project",
 "description": "Enter your project description",
 "author": {
  "name": "user",
  "email": "test@wails.app"
 },
 "version": "0.1.0",
 "binaryname": "my-project",
 "frontend": {
  "dir": "frontend",
  "install": "yarn",
  "build": "yarn build",
  "bridge": "src",
  "serve": "yarn serve"
 },
 "WailsVersion": ""
}
{{< /highlight >}}

{{% notice note %}}
Different Wails templates will produce diffrent "serve" commands. For example Create-React-App template's "serve" field would be `"serve": "yarn start"`.
{{% /notice %}}

Then delete `package.json.lock` & `package.json.md5` from inside `frontend` directory.
Finally run `yarn` to create a new `yarn.lock` and that's it!

Enjoy using the much faster `Yarn` with your Wails project.