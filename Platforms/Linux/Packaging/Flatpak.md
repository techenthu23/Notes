# Flatpak

Flatpak is a framework for distributing desktop applications across various Linux distributions. It has been created by developers who have a long history of working on the Linux desktop, and is run as an independent open source project.

## Terminology

- Flatpak: a system for building, distributing, and running sandboxed desktop applications on Linux.

- Flatpak application: these are the applications the user installs via the flatpak command or via a different UI like GNOME Software or KDE Discover.
- Runtime: also called platforms, these are integrated platforms to provide basic utilities needed for a Flatpak application to work.

- BaseApp: these are integrated platforms for frameworks like Electron.

- Flatpak bundle: a specific single-file export format which contains a Flatpak app or runtime.

Just like traditional package managers, **Flatpak** relies on software repositories in order to download applications. Repositories are a necessary component of Flatpak as they allow users to install applications and dependencies from a central location. A repository contains a catalog of installable software and will provide future updates to the packages as needed.

Flatpak does not automatically come with repositories added in its configuration. It is up to the user to find Flatpak repositories (such as FlatHub) and add them to Flatpak on their system. To keep track of all of the added repositories, users can get a list of them in terminal.

- Flatpak has some major advantages over other universal approaches to distributing applications on Linux:
- Decentralized by design: while Flatpak does provide a centralized service for distributing applications, it also allows decentralized hosting and distribution, so that application developers or downstreams can host their own applications and application repositories.
- Desktop integration: Flatpak also offers native integration for the main Linux desktops, so that users can easily browse, install, run and use Flatpak applications through their existing desktop environment and tools.
- Space efficiency: Flatpak deduplicates libraries and other files used by multiple applications to save megabytes or even gigabytes worth of storage depending on the amount of applications installed.
- Delta updates: only changed files are downloaded for updates.

Other benefits for developers include:

- Forward-compatibility: the same Flatpak application can be run on different versions of the same distribution, including versions that haven’t been released yet. This doesn’t require any changes or management by application developers.
- Bundling: this allows application developers to ship almost any dependency or library as part of their application. This gives complete control over which software is used to build applications.
- Consistent application environments: because these are the same across devices, applications perform as intended. This also makes it easier to identify bugs and to do testing.
- Branches: this allows developers to ship applications from different branches, e.g. stable, beta, etc. while retaining the same name.
- Maintained platforms: called runtimes, these contain collections of dependencies, which can be used by applications, and which can take a lot of the work out of application development.

To get a brief list of all configured repositories (remotes) for Flatpak execute the following command:

```
flatpak remotes
```

To get a list of all repositories along with extra details like the home page of each one, we can use:

```
flatpak remotes --show-details
```

If you want to see only the names of the repositories, we can isolate a column of information with the --columns option:

```
flatpak remotes --columns=name
```

In case you need to modify one of the configured repositories, use the remote-modify option. This option will then accept certain arguments, depending on what you wish to modify about the repository. See a full list of options with this command:

```
flatpak remote-modify --help
```

To add a new repository, use the following command syntax:

```
sudo flatpak remote-add --if-not-exists [name] [url]
```

To delete an existing repository, use this syntax:

```
sudo flatpak remote-delete [name]
```

```
flatpak list                          # List all installed the Flatpak applications
flatpak run <APPLICATION>             # Launch an <APPLICATION>   
flatpak uninstall <APPLICATION>       # Remove a installed <APPLICATION>
flatpak update                        # Update all your installed applications
flatpak update <APPLICATION>          # Update a individual <APPLICATION>
flatpak remote-list                   # List remote configured Repositories
flatpak remote-ls --app <REPOSITORY>  # List apps of a <REPOSITORY>
flatpak remote-delete <REPOSITORY>    # Remove a <REPOSITORY>
```

# Issues

## Warning: You are running an unofficial Flatpak version of Visual Studio Code !!! |

This version is running inside a container and is therefore not able
to access SDKs on your host system!

To execute commands on the host system, run inside the sandbox:

  ```
  host-spawn <COMMAND>
  ```
  
To make the Integrated Terminal automatically use the host system's shell,
you can add this to the settings:

```
{
  "terminal.integrated.defaultProfile.linux": "bash",
  "terminal.integrated.profiles.linux": {
    "bash": {
      "path": "host-spawn",
      "args": ["bash"]
    }
  }
}
```

This Flatpak provides a standard development environment (gcc, python, etc).
To see what's available:

```
  flatpak run --command=sh com.visualstudio.code
  ls /usr/bin (shared runtime)
  ls /app/bin (bundled with this flatpak)
```

To get support for additional languages or tools within the Flatpak, you have to install SDK extensions, e.g.

```
  flatpak install flathub org.freedesktop.Sdk.Extension.dotnet
  flatpak install flathub org.freedesktop.Sdk.Extension.golang
  FLATPAK_ENABLE_SDK_EXT=dotnet,golang flatpak run com.visualstudio.code
```

Similarly for shells and other tools:

```
  flatpak install com.visualstudio.code.tool.fish
  flatpak install com.visualstudio.code.tool.podman
```

You can use

```
  flatpak search <TEXT>
```

to find others.

References:

- Flatpak’s documentation <https://docs.flatpak.org/en/latest/index.html>
