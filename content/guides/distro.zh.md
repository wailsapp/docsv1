+++
title = "添加对Linux发行版的支持"
date = 2019-08-29T04:56:50+10:00
weight = 15
chapter = false
+++

This guide shows all necessary steps needed for unspported Linux distributions to work with Wails.

_if you managed to get Wails working for your desktop please consider making a Pull Request. For more details see the [development section]({{% relref "../development" %}})._

## Identify necessary dependencies

Wails uses cgo to bind to the native rendering engines so a number of platform dependent libraries are needed.

* npm - [Official Node docs]()
* gcc
* gtk
* webkitgtk

### gcc, pkg-config

### gtk, webkit

Locate the appropriate equivalent libraries for your distribution.

_NOTE: if your distro is a *derivative* and not a *major* distribution there are good chances all necessary dependendancies to be the covered [on the installation's prerequisites section]({{% relref "../gettingstarted/linux.zh.md/#prerequisites" %}})._

## Gather system's information

Wails uses [`/etc/os-release`](https://www.freedesktop.org/software/systemd/man/os-release.html) for identification. In a terminal window run `cat /etc/os-release`.

```
$ cat /etc/os-release
NAME="Ubuntu"
VERSION="19.04 (Disco Dingo)"
ID=ubuntu
ID_LIKE=debian
PRETTY_NAME="Ubuntu 19.04"
VERSION_ID="19.04"
HOME_URL="https://www.ubuntu.com/"
SUPPORT_URL="https://help.ubuntu.com/"
BUG_REPORT_URL="https://bugs.launchpad.net/ubuntu/"
PRIVACY_POLICY_URL="https://www.ubuntu.com/legal/terms-and-policies/privacy-policy"
VERSION_CODENAME=disco
UBUNTU_CODENAME=disco
```

We are interested in `NAME` and `ID` fields. Then identify which of the bellow two commands returns gcc's full version . _This is needed for better support when filling tickets via `wails issue`._
```
gcc -dumpversion
gcc -dumpfullversion
```

_For example on Ubuntu you need to use the `-dumpfullversion` flag._
```
$ gcc -dumpversion
8
$ gcc -dumpfullversion
8.3.0
```

## Make a new entry in `linuxdb.yaml`

`linuxdb.yaml` lives in `wails/cmd/linuxdb.yaml`.

Use the `NAME` and `ID` fields of the previous step.
```yaml
`ID`:
    id: `ID`
    releases:
        name: `NAME`
```

If your are on derivative distro that shares libraries and software with it's major a new entry would look like (linux mint sample):
```yaml
linuxmint:
    id: linuxmint
    releases:
      default:
        version: default
        name: Linux Mint
        gccversioncommand: *gccdumpversion
        programs: *debiandefaultprograms
        libraries: *debiandefaultlibraries
```

If you are adding a previously unspported major release a new entry would like (centOS entry sample)
```yaml
  centos:
    id: centos
    releases:
      default:
        version: default
        name: CentOS Linux
        gccversioncommand: *gccdumpversion
        programs:
          - name: gcc
            help: Please install with `sudo yum install gcc-c++ make` and try again
          - name: pkg-config
            help: Please install with `sudo yum install pkgconf-pkg-config` and try again
          - name: npm
            help: Please install with `sudo yum install epel-release && sudo yum install nodejs` and try again
        libraries:
          - name: gtk3-devel
            help: Please install with `sudo yum install gtk3-devel` and try again
          - name: webkitgtk3-devel
            help: Please install with `sudo yum install webkitgtk3-devel` and try again
```

## Edit `linux.go`

In `wails/cmd/linux.go` you have to  

* add a new constant with your distro's name (use whatever is returned under `NAME` field two steps back)

```go
const (
	// Unknown is the catch-all distro
	Unknown LinuxDistribution = iota
	// Debian distribution
	Debian
	// Ubuntu distribution
	Ubuntu
	// Arch linux distribution
	Arch
	// CentOS linux distribution
	CentOS
	(...)
	// NewDistroName linux distribution
	NewDistroName
)
```

* and a new switch case (`case "ID":`)

```go
switch osID {
	case "fedora":
		result.Distribution = Fedora
	case "centos":
		result.Distribution = CentOS
	case "arch":
		result.Distribution = Arch
	case "debian":
		result.Distribution = Debian
	(...)
	case "newdistroID":
		result.Distribution = NewDistroName
  }
  ```

When adding a major distro you need to also add a new function to support your distro's specific package manager.

Here is a sample for `dpkg`.
```go
// DpkgInstalled uses dpkg to see if a package is installed
func DpkgInstalled(packageName string) (bool, error) {
	program := NewProgramHelper()
	dpkg := program.FindProgram("dpkg")
	if dpkg == nil {
		return false, fmt.Errorf("cannot check dependencies: dpkg not found")
	}
	_, _, exitCode, _ := dpkg.Run("-L", packageName)
	return exitCode == 0, nil
}
```

## Edit `system.go`

Finally you have to edit package manager's switch case in `wails/cmd/system.go` to also include your distro.

For example if your are on a Debian derivative you would

```go
// Linux has library deps
	if runtime.GOOS == "linux" {
		(...)
		switch distroInfo.Distribution {
		case Ubuntu, Debian, NewDistroName:
			libraryChecker = DpkgInstalled
		(...)
		}
```

If you are adding a major distro with it's own package manager you have to make a new case entry.

```go
// Linux has library deps
	if runtime.GOOS == "linux" {
		(...)
		switch distroInfo.Distribution {
		case NewDistroName:
			libraryChecker = NewPackageManagerInstalled
		(...)
		}
```

## Check that everything works

`cd` in directory `wails/cmd/wails` and install the updated Wails by running `go install`.

Now try running `wails setup`.

<!-- {{< highlight "hl_lines=2" >}}
wails
├── cmd
│   ├── cmd-mewn.go
│   ├── linuxdb.yaml
│   ├── linux.go
│   └── system.go
└── README.md
 {{< /highlight >}} -->
