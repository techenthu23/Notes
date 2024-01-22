---
title: "Windows10"
draft: false
---
- [Scoop](#scoop)
  - [Scoop installation](#scoop-installation)
    - [Scoop buckets](#scoop-buckets)

# Scoop

Scoop is a command-line package manager for Windows which makes it easier to install and use common programs and tools. Scoop includes support for a wide variety of Windows software, as well as favourites from the Unix world. It addresses many of the common painpoints with Windows' software ecosystem, compared to the package manager models of Unix systems.

## Scoop installation

In any PowerShell window, no Admin necessary, the following will install Scoop, provided you have set the ExecutionPolicy as detailed at the beginning of the article:

Make sure PowerShell 5 (or later, include PowerShell Core) and .NET Framework 4.5 (or later) are installed. Then run:

```
Invoke-Expression (New-Object System.Net.WebClient).DownloadString('https://get.scoop.sh')
```

or shorter

```
iwr -useb get.scoop.sh | iex
```

Note: if you get an error you might need to change the execution policy (i.e. enable Powershell) with

```
Set-ExecutionPolicy RemoteSigned -scope CurrentUser
```

See the detailed installation docs for more info.

### Scoop buckets

I often install the extras bucket immediately:

```
scoop bucket add extras
```

A bucket is a set of apps that can be searched and installed. To see all the known community buckets:

```
scoop bucket known
```

Then add the ones you like!

```
Scoop search
scoop search sudo
Scoop install package
scoop install sudo
Scoop upgrade all currently installed packages
scoop upgrade *
Scoop help
scoop help
```
