
# Fedora SilverBlue

- [Fedora SilverBlue](#fedora-silverblue)
- [Automatic updates](#automatic-updates)
  - [Silverblue filesystem layout](#silverblue-filesystem-layout)
  - [Editing kernel arguments](#editing-kernel-arguments)
  - [OSTree](#ostree)
    - [ostree commands](#ostree-commands)
  - [rpm-ostree](#rpm-ostree)
- [Manage Packages on Fedora Silverblue with rpm-ostree](#manage-packages-on-fedora-silverblue-with-rpm-ostree)
- [Cheatsheet](#cheatsheet)
  - [Identifier Formats](#identifier-formats)
    - [Refspec](#refspec)
  - [Branch (typical format)](#branch-typical-format)
  - [Remotes](#remotes)
    - [List configured remotes](#list-configured-remotes)
    - [List remote contents](#list-remote-contents)
  - [Basic Commands](#basic-commands)
  - [Layered Packages](#layered-packages)
  - [Debugging and Rollback](#debugging-and-rollback)
- [Searching Packages](#searching-packages)
  - [Flatpak and Flathub](#flatpak-and-flathub)
    - [Reasons to use Flatpak](#reasons-to-use-flatpak)
    - [Flathub](#flathub)

# Automatic updates

<https://docs.fedoraproject.org/en-US/iot/applying-updates-UG/>

The ostree and rpm-ostree packages shipped in Red Hat Enterprise Linux 8 AppStream are not currently supported for standalone use. They are only supported as part of composing and managing RHEL for Edge deployments. For more information about composing, installing, and managing RHEL for Edge please see <https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/composing_installing_and_managing_rhel_for_edge_images/>

## Silverblue filesystem layout

On Silverblue, the root filesystem is immutable. This means that /, /usr and everything below it is read-only.

/var is where all of Silverblue’s runtime state is stored. Symlinks are used to make traditional state-carrying directories available in their expected locations. This includes:

    /home → /var/home 

    /opt → /var/opt 

    /srv → /var/srv 

    /root → /var/roothome 

    /usr/local → /var/usrlocal 

    /mnt→ /var/mnt 

    /tmp → /sysroot/tmp 

This means that separate home partitions should be mounted on /var/home.

For a more detailed explanation of Silverblue’s filesystem layout, refer to the excellent [libostree documentation](https://ostreedev.github.io/ostree/adapting-existing/).

## Editing kernel arguments

rpm-ostree kargs --editor command is applicable only if you are editing kernel arguments. You can use grubby with Silverblue 33 & 34 to modify boot parameters without issues.

```
grub2-mkconfig -o /etc/grub2.cfg
```

or

```
grub2-mkconfig -o /etc/grub2-efi.cfg
```

## OSTree

You can use OSTree standalone in the pure replication model, but another approach is to add a package manager on top, thus creating a hybrid tree/package system.

A tool used for managing Linux-based operating system versions. The OSTree tree view is similar to Git and is based on similar concepts. It is not a package system; rather, it is intended to complement them. A primary model is composing packages on a server, and then replicating them to clients.

It’s both a **shared library** and a suite of **command line tools.** The underlying architecture might be summarized as "git for operating system binaries". It operates in userspace, and will work on top of any Linux filesystem. At its core is a git-like content-addressed object store with branches (or "refs") to track meaningful filesystem trees within the store. Similarly, one can check out or commit to these branches.

Layered on top of that is bootloader configuration, management of /etc, and other functions to perform an upgrade beyond just replicating files.

OSTree is deeply inspired by git; the core layer is a userspace content-addressed versioning filesystem

Git tracks content – files and directories. It is at its heart a collection of simple tools that implement a tree history storage and directory content management system. It is simply used as a Software Configuration Management tool, not really designed as one. When most Software Configuration Management tools store a new version of a project, they store the code delta or diff. When Git stores a new version of a project, it stores a new tree – a bunch of blobs of content and a collection of pointers that can be expanded back out into a full directory of files and subdirectories.

> A binary large object (BLOB or blob) is a collection of binary data stored as a single entity. Blobs are typically images, audio or other multimedia objects, though sometimes binary executable code is stored as a blob.

If you want a diff between two versions, it doesn’t add up all the deltas, it simply looks at the two trees and runs a new diff on them. This is what fundamentally allows the system to be easily distributed, it doesn’t have issues figuring out how to apply a complex series of deltas, it simply transfers all the directories and content that one user has and another does not have but is requesting. It is efficient about it – it only stores identical files and directories once and it can compress and transfer its content using delta-compressed packfiles – but in concept, it is a very simple beast. Git is at its heart very stupid-simple.

To rephrase, Git thinks of its data more like a series of snapshots of a miniature filesystem. With Git, every time you commit, or save the state of your project, Git basically takes a picture of what all your files look like at that moment and stores a reference to that snapshot. To be efficient, if files have not changed, Git doesn’t store the file again, just a link to the previous identical file it has already stored. Git thinks about its data more like a stream of snapshots.

| PROS                                                                             | CONS                                                     |
| -------------------------------------------------------------------------------- | -------------------------------------------------------- |
| **Shrink the size** of updates and limit the bandwidth usage                     | RAM usage (depending of the size of the files to update) |
| Dependent of the type of update but usually fast for updating an embedded system | Recovery after filesystem corruption is impossible       |

<https://witekio.com/blog/ostree-tutorial-system-updates/>

### ostree commands

| Command                                                                                                                                                                    | Description                                                                      |
| -------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------- |
| `ostree admin status`                                                                                                                                                      | shows details about the currently active deployments (root filesystems).         |
| `ostree admin upgrade`                                                                                                                                                     | command can be used to upgrade to the latest monthly release.                    |
| `ostree remote list`                                                                                                                                                       | List the remote ostree registered OSTree on the system                           |
| `ostree remote refs`                                                                                                                                                       | command allows to get a list of branches available on a remote OSTree repository |
| `ostree pull fedora:fedora/35/x86_64/silverblue`                                                                                                                           | to download the lastest version of a particular OSTree branch                    |
| `ostree admin upgrade`                                                                                                                                                     | to upgrade to the latest monthly release.                                        |
| `ostree log fedora:fedora/35/x86_64/silverblue`                                                                                                                            | To see more details about the fetched OSTree commits                             |
| `ostree pull-local --repo [path] src`  <br> `ostree pull-local <path> <rev> --repo=<repo-path>`  <br> `ostree pull <URL> <rev> --repo=<repo-path>`                         | ostree pull                                                                      |
| `ostree summary -u --repo=<repo-path>`                                                                                                                                    | ostree summary                                                                   |
| `ostree refs --repo ~/Code/src/osbuild-iot/build/repo/ --list`                                                                                                             | View refs                                                                        |
| `ostree log --repo=/home/gicmo/Code/src/osbuild-iot/build/repo/ <REV>`                                                                                                     | View commits in repo                                                             |
| `ostree show --repo build/repo <REV>`                                                                                                                                      | Inspect a commit                                                                 |
| `ostree remote list --repo <repo-path>`                                                                                                                                    | List remotes of a repo                                                           |
| `ostree rev-parse --repo ~/Code/src/osbuild-iot/build/repo fedora/x86_64/osbuild-demo`  <br> `ostree rev-parse --repo ~/Code/src/osbuild-iot/build/repo b3a008eceeddd0cfd` | Resolve a REV                                                                    |
| `ostree static-delta generate --repo=[path] --from=REV --to=REV`                                                                                                           | Create static-delta                                                              |
| `ostree gpg-sign --repo=<repo-path> --gpg-homedir <gpg_home> COMMIT KEY-ID…`                                                                                               | Sign an existing ostree commit with a GPG key                                    |

## rpm-ostree

rpm-ostree is a hybrid image or system package that hosts operating system updates. It combines libostree as a base image format, and accepts RPM on both the client and server side, sharing code with the dnf project; specifically libdnf. and thus bringing many of the benefits of both together.

```
                         +-----------------------------------------+
                         |                                         |
                         |       rpm-ostree (daemon + CLI)         |
                  +------>                                         <---------+
                  |      |     status, upgrade, rollback,          |         |
                  |      |     pkg layering, initramfs --enable    |         |
                  |      |                                         |         |
                  |      +-----------------------------------------+         |
                  |                                                          |
                  |                                                          |
                  |                                                          |
+-----------------|-------------------------+        +-----------------------|-----------------+
|                                           |        |                                         |
|         libostree (image system)          |        |            libdnf (pkg system)          |
|                                           |        |                                         |
|   C API, hardlink fs trees, system repo,  |        |    ties together libsolv (SAT solver)   |
|   commits, atomic bootloader swap         |        |    with librepo (RPM repo downloads)    |
|                                           |        |                                         |
+-------------------------------------------+        +-----------------------------------------+
```

**Features**:

    - Transactional, background image-based (versioned/checksummed) upgrades
    - OS rollback without affecting user data (/usr, /etc but not /var) via libostree
    - Client-side package layering (and overrides)
    - Easily make your own derivatives

| Command                                                                                                                                          | Description                                                                                                                                                                                                                                                                                                                                                                                                                             |
| :----------------------------------------------------------------------------------------------------------------------------------------------- | :-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `rpm-ostree --repo=/home/gicmo/Code/src/osbuild-iot/build/repo/ db list <REV>`                                                                     | This command lists the packages existing in the `<REV>` commit into the repository.                                                                                                                                                                                                                                                                                                                                                       |
| `rpm-ostree rollback`                                                                                                                              | OSTree manages an ordered list of bootloader entries, called deployments. The entry at index 0 is the default bootloader entry. Each entry has a separate /etc directory, but all the entries share a single /var directory. You can use the bootloader to choose between entries by pressing Tab to interrupt startup. This rolls back to the previous state, that is, the default deployment changes places with the non-default one. |
| `rpm-ostree status`                                                                                                                                | This command gives information about the current deployment in use. Lists the names and refspecs of all possible deployments in order, such that the first deployment in the list is the default upon boot. The deployment marked with * is the current booted deployment, and marking with 'r' indicates the most recent upgrade.                                                                                                      |
| `rpm-ostree db list`                                                                                                                               | Use this command to see which packages are within the commit or commits. You must specify at least one commit, but more than one or a range of commits also work.                                                                                                                                                                                                                                                                       |
| `rpm-ostree db diff`                                                                                                                               | Use this command to show how the packages are different between the trees in two revs (revisions). If no revs are provided, the booted commit is compared to the pending commit. If only a single rev is provided, the booted commit is compared to that rev.                                                                                                                                                                           |
| `rpm-ostree upgrade`                                                                                                                               | This command downloads the latest version of the current tree, and deploys it, setting up the current tree as the default for the next boot. This has no effect on your running filesystem tree. Due to the atomic nature of the OS, you will have to reboot into the new image to have the updates take effect.                                                                                                                        |
| `sudo rpm-ostree upgrade --preview`                                                                                                              | Preview the package differences                                                                                                                                                                                                                                                                                                                                                                                                         |
| `sudo rpm-ostree upgrade --download-only`                                                                                                        | To download the packages only and not deploy them                                                                                                                                                                                                                                                                                                                                                                                       |
| `ostree remote refs fedora` <br> `ostree remote list`.  <br>    `rpm-ostree rebase fedora:fedora/35/x86_64/silverblue` <br> `rpm-ostree upgrade` | Upgrading between major versions. First, verify the branch is available.                                                                                                                                                                                                                                                                                                                                                                |
| Term           | Definition                                                                                                                                                                                                                                                                                                                                                                  |
| :------------- | :-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| OSTree         | A tool used for managing Linux-based operating system versions. The OSTree tree view is similar to Git and is based on similar concepts.                                                                                                                                                                                                                                    |
| rpm-ostree     | A hybrid image or system package that hosts operating system updates.                                                                                                                                                                                                                                                                                                       |
| Commit         | A release or image version of the operating system. Image Builder generates an ostree commit for RHEL for Edge images. You can use these images to install or update RHEL on Edge servers.                                                                                                                                                                                  |
| Refs           | Represents a branch in ostree. Refs always resolve to the latest commit. For example, rhel/8/x86_64/edge.                                                                                                                                                                                                                                                                   |
| Revision (Rev) | SHA-256 for a specific commit.                                                                                                                                                                                                                                                                                                                                              |
| Remote         | The http or https endpoint that hosts the ostree content. This is analogous to the baseurl for a yum repository.                                                                                                                                                                                                                                                            |
| static-delta   | Updates to ostree images are always delta updates. In case of RHEL for Edge images, the TCP overhead can be higher than expected due to the updates to number of files. To avoid TCP overhead, you can generate static-delta between specific commits, and send the update in a single connection. This optimization helps large deployments with constrained connectivity. |

Hello World example
OSTree is mostly used as a library, but a quick tour of using its CLI tools can give a general idea of how it works at its most basic level.

You can create a new OSTree repository using init:

```
ostree --repo=repo init
```

This will create a new repo directory containing your repository. Feel free to inspect it.

Now, let's prepare some data to add to the repo:

```
mkdir tree
echo "Hello world!" > tree/hello.txt
```

We can now import our tree/ directory using the commit command:

```
ostree --repo=repo commit --branch=foo tree/
```

This will create a new branch foo pointing to the full tree imported from tree/. In fact, we could now delete tree/ if we wanted to.

To check that we indeed now have a foo branch, you can use the refs command:

```
$ ostree --repo=repo refs
foo
```

We can also inspect the filesystem tree using the ls and cat commands:

```
$ ostree --repo=repo ls foo
d00775 1000 1000      0 /
-00664 1000 1000     13 /hello.txt
$ ostree --repo=repo cat foo /hello.txt
Hello world!
```

And finally, we can check out our tree from the repository:

```
$ ostree --repo=repo checkout foo tree-checkout/
$ cat tree-checkout/hello.txt
Hello world!
```

# Manage Packages on Fedora Silverblue with rpm-ostree

Most (but not all) RPM packages provided by Fedora can be installed on Silverblue using this method. This works by modifying your Silverblue installation to extend the packages from which Silverblue is composed.

Package layering creates a new “deployment“, or bootable filesystem root, and the system must be rebooted after a package has been layered. This preserves rollback and the transactional model.

First, generate rpm repo metadata:

```
rpm-ostree refresh-md 
```

A package can be installed on Silverblue using:

```bash
rpm-ostree install <package name>
```

Example:

```
$ rpm-ostree install vim
$ for i in neofetch zsh feh sway; do
  rpm-ostree install $i;
done
```

You can as well replace a package with a different version using rpm-ostree override command:

```
rpm-ostree override replace <path to package>
```

To uninstall a package, run:

```
$ rpm-ostree uninstall flameshot          
Checking out tree 675ab14... done
Resolving dependencies... done
Checking out packages... done
Running pre scripts... done
Running post scripts... done
Running posttrans scripts... done
Writing rpmdb... done
Writing OSTree commit... done
Staging deployment... done
Freed: 242.4 MB (pkgcache branches: 2)
Removed:
  flameshot-0.6.0-4.fc31.x86_64
  qt5-qtsvg-5.12.5-1.fc31.x86_64
Run "systemctl reboot" to start a reboot
```

Performing Upgrades & Rollback on Fedora Silverblue
The standard behavior is to automatically download the updates and install them, but as a user, you can manually perform system updates.

```

rpm-ostree upgrade
```

Alternatively, you can check for available updates without downloading them, run:

```
$ rpm-ostree upgrade --check
Performing Rollbacks
```

Silverblue keeps a record of the previous OS version, which can be switched to instead of the latest version.

There are two ways to roll back to the previous version:

Temporary rollbacks: Just reboot your system and select the previous version from the boot menu to perform a temporarily roll back.
Permanant rollback: To switch back permanently to a previous deployment, use the command:

```
rpm-ostree rollback
```

That’s the basic ways of managing software packages on Fedora Silverblue. Other guides of interest are:

# Cheatsheet

## Identifier Formats

### Refspec

```
ostree://<REMOTE>:<BRANCH>
eg ostree://onerepo:fedora/f28/i686/host
```

## Branch (typical format)

```
<OS>/<STREAM>/<ARCH>/<TAG> 
eg fedora/rawhide/x86_64/workstation
```

## Remotes

```
ostree remote add <REMOTE> <URL>
ostree remote delete <REMOTE>
```

### List configured remotes

```
ostree remote list
```

### List remote contents

```
ostree remote refs <REMOTE>
```

## Basic Commands

Get system status

```
rpm-ostree status
```

Find available updates

```
rpm-ostree upgrade --check
```

Update to latest

```
rpm-ostree upgrade
```

Switch to a different OS

```
rpm-ostree rebase <REMOTE>:<BRANCH>
```

## Layered Packages

Install a layered package

```

rpm-ostree install <PACKAGE>
```

Uninstall a layered package

```
rpm-ostree uninstall <PACKAGE>
```

## Debugging and Rollback

Make the previous deployment the default boot entry

```
rpm-ostree rollback
```

Remove the previous deployment

```
rpm-ostree cleanup --rollback
```

Download older commits

```
$ ostree pull --commit-metadata-only \
 --depth=<n> <REMOTE> <BRANCH>
```

Diff

```
rpm-ostree db diff <COMMIT1> <COMMIT2>
```

List downloaded commits

```
ostree log <REMOTE>:<BRANCH>
```

Switch to a specific commit

```
rpm-ostree deploy <COMMIT>
or
rpm-ostree deploy <VERSION STRING>
```

Find differences between commits

```
rpm-ostree db diff <COMMIT1> <COMMIT2>
```

# Searching Packages

Fedora Silverblue doesn’t come with dnf because it’s an immutable operating system and uses a special tool called rpm-ostree to layer packages on top instead.

Most terminal work is designed to be done in containers with toolbox, but I still do a bunch of work outside of a container. Searching for packages to install with rpm-ostree still requires dnf inside a container, as it does not have that function.

Add these two aliases to my ~/.bashrc file so that using dnf to search or install into the default container is possible from a regular terminal. This just makes Silverblue a little bit more like what I’m used to with regular Fedora.

cat >> ~/.bashrc << EOF
alias sudo="sudo "
alias dnf="bash -c '#skip_sudo'; toolbox -y create 2>/dev/null; toolbox run sudo dnf"
EOF

If the default container doesn’t exist, toolbox creates it. Note that the alias for sudo has a space at the end. This tells bash to also check the next command word for alias expansion, which is what makes sudo work with aliases. Thus, we can make sure that both dnf and sudo dnf will work. The first part of the dnf alias is used to skip the sudo command so the rest is run as the regular user, which makes them both work the same.

## Flatpak and Flathub

Introduction to Flatpak

Flatpak is a framework for distributing desktop applications across various Linux distributions. It has been created by developers who have a long history of working on the Linux desktop, and is run as an independent open source project. Flatpak is installed by default on Fedora Workstation, Fedora Silverblue, and Fedora Kinoite.  

Terminology

    Flatpak: a system for building, distributing, and running sandboxed desktop applications on Linux. 

    Flatpak application: these are the applications the user installs via the flatpak command or via a different UI like GNOME Software or KDE Discover. 

    Runtime: also called platforms, these are integrated platforms to provide basic utilities needed for a Flatpak application to work. 

    BaseApp: these are integrated platforms for frameworks like Electron. 

    Flatpak bundle: a specific single-file export format which contains a Flatpak app or runtime. 

Target audience

Flatpak can be used by all kinds of desktop applications, and aims to be as agnostic as possible regarding how applications are built. There are no requirements regarding which programming languages, build tools, toolkits or frameworks can be used.

While Flatpak only runs on Linux, it can be used by applications that target other operating systems, as well as those that are Linux-specific. Applications can be open source or proprietary (although some distribution services, like Flathub, can have restrictions in this respect).

The only technical requirements made by Flatpak are that applications follow a small number of Freedesktop standards, in order to enable desktop integration (see Requirements & Conventions).

Issues of current model of packaging

It is important to understand the problems of the current model of packaging applications to understand the existence of Flatpak:

    Duplicated work packaging apps: many Linux distributions come with their own package manager, package format and repository. This requires a lot of maintainers to package the same application in various distributions, or the application developer to learn the language of each format and then package the application in those distributions, or ignore most distributions and package and support a couple of distributions. This makes the Linux desktop a difficult platform for software vendors to target. 

    Limited to apps that are packaged: not all applications are natively available in every Linux distribution. If an application is not available in a specific distribution, the user will have to rely on manually downloading the archive of the application, extracting it and hoping the application will launch. 

    Limited to distributions that have the apps: the user is limited to the number of distributions that have the needed applications for them to properly setup their workflow. This reduces the amount of distributions that can be suitable for a user. 

    Hard to innovate in OS space: the maintainers of the distributions have to spend a lot of time packaging applications to make the distribution suitable for the end user, instead of focusing on their end goals. This delays the progress of each distribution. 

    Old and outdated packages: LTS distributions often have very old versions of applications packaged natively. Bug reproducibility is hindered by the different environments that applications are run in, and application developers often have little control over how their application is packaged by distributions. 

Flatpak strives to fix the issues listed above, by conveniently enabling developers to distribute applications from one source and to target the entire Linux desktop.

### Reasons to use Flatpak

Flatpak has some major advantages over most system package managers:

    Universality: Flatpak allows applications to be installed and run on virtually any Linux distribution. This includes non-GNU distributions, systemd-free distributions, distributions with a read-only operating system (OS), and various architectures without the developer needing the relevant hardware on hand. 

    Space for innovations: Flatpak facilitates distribution maintainers to focus on their goals to innovate their distribution. 

    Stability: breakage in a Flatpak application will not risk the system from breaking. This is because Flatpak applications and runtimes are contained to not interfere with the system altogether. 

    Rootless install: elevated privileges are not required when installing a Flatpak application or a runtime. 

    Sandboxed applications: one of Flatpak’s main goals is to increase the security of desktop systems by isolating applications from one another. This is achieved using sandboxing and means that, by default, applications that are run with Flatpak have limited access to the host environment. 

Flatpak has some major advantages over other universal approaches to distributing applications on Linux:

    Decentralized by design: while Flatpak does provide a centralized service for distributing applications, it also allows decentralized hosting and distribution, so that application developers or downstreams can host their own applications and application repositories. 

    Desktop integration: Flatpak also offers native integration for the main Linux desktops, so that users can easily browse, install, run and use Flatpak applications through their existing desktop environment and tools. 

    Space efficiency: Flatpak deduplicates libraries and other files used by multiple applications to save megabytes or even gigabytes worth of storage depending on the amount of applications installed. 

    Delta updates: only changed files are downloaded for updates. 

Other benefits for developers include:

    Forward-compatibility: the same Flatpak application can be run on different versions of the same distribution, including versions that haven’t been released yet. This doesn’t require any changes or management by application developers. 

    Bundling: this allows application developers to ship almost any dependency or library as part of their application. This gives complete control over which software is used to build applications. 

    Consistent application environments: because these are the same across devices, applications perform as intended. This also makes it easier to identify bugs and to do testing. 

    Branches: this allows developers to ship applications from different branches, e.g. stable, beta, etc. while retaining the same name. 

    Maintained platforms: called runtimes, these contain collections of dependencies, which can be used by applications, and which can take a lot of the work out of application development. 

Information about Flatpak’s internals can be found in Under the Hood.

    <https://docs.flatpak.org/en/latest/introduction.html>

    <https://wiki.archlinux.org/title/Flatpak>

    <https://docs.flatpak.org/en/latest/using-flatpak.html>

    <https://computingforgeeks.com/manage-packages-on-fedora-silverblue-linux/>

    <https://miabbott.github.io/2019/01/22/silverblue-day-1.html>

### Flathub

Flathub is a  growing collection of apps which can be easily installed on any Linux distribution. Once setup, you can browse and install from an app center, like GNOME Software or KDE Discover. Alternatively, you can browse and install apps from the website or using the command line.

To get started, all you need to do is enable Flathub, which is the best way to get Flatpak apps. Just download and install the flathub repository file

To add the Flathub repository, open the Terminal, and run:

    flatpak remote-add --if-not-exists flathub <https://flathub.org/repo/flathub.flatpakrepo>

To add the Fedora Flatpaks repository (built in Fedora), run:

    flatpak remote-add --if-not-exists fedora oci+<https://registry.fedoraproject.org>

Installing Flatpaks On Gnome (GNOME Software):

GNOME Software already supports Flatpak repositories, so applications can be installed directly from GNOME Software.

Simply open “Software” from the GNOME Overview, and search for your desired application. If it is available as a flatpak, you will see it’s source labeled as “flathub.org”.

Select the flatpak entry and click “Install”.

After that, the application can be launched as usual.

Installing Flatpaks On KDE (KDE Discover):

Starting in Discover 5.12, Discover now supports Flatpak repositories, so you can directly install flatpak applications from it.

Simply open Discover and browse or search through the app lists as normal. Similar to Gnome Software, applications that are available as flatpaks will list “Flatpak” as it’s source.

Upon installations, applications can be launched as usual.

Installing Applications (Command Line):

The flatpak command also lists and installs apps and runtimes. To list all apps available in a specific repository, run the remote-ls command:

    flatpak remote-ls flathub --app

Then, install an app with the install command:

    flatpak install flathub org.gnome.Polari

Once installed, you can use the run command to run the application:

    $ flatpak run org.gnome.Polari


    <https://flathub.org/home>

    flatpak install flathub com.visualstudio.code

    flatpak run com.visualstudio.code

    flatpak install flathub com.spotify.Client

    flatpak run com.spotify.Client

Installing Google Chrome Stable version using toolbox

```
toolbox create –container google-chrome

sudo dnf install fedora-workstation-repositories

sudo dnf config-manager --set-enabled google-chrome

sudo dnf install google-chrome-stable

exit

toolbox list –containers

toolbox list –images

toolbox list

toolbox run -c google-chrome /usr/bin/google-chrome-stable --no-sandbox

podman container stop google-chrome  

podman ps –a

podman images

podman start google-chrome

podman stats

podman stop google-chrome

podman inspect google-chrome

podman rm google-chrome

podman rmi <image-name>

podman --help
```

Toolbox

As an immutable host, Silverblue is an excellent platform for container-based development and, for working with containers, buildah and podman are recommended.

Silverblue also comes with the toolbox utility, which uses containers to provide an environment where development tools and libraries can be installed and used.

<https://docs.fedoraproject.org/en-US/fedora-silverblue/toolbox/>

<https://ostechnix.com/getting-started-with-toolbox-on-fedora-silverblue/>

<https://cloud.google.com/container-optimized-os/docs/how-to/toolbox>

Easily Install Golang In Linux Using Update-golang Script

```
git clone <https://github.com/udhos/update-golang>
cd update-golang
wget -qO hash.txt <https://raw.githubusercontent.com/udhos/update-golang/master/update-golang.sh.sha256>
sha256sum -c hash.txt
sudo ./update-golang.sh
go version
go env
```

<https://blog.while-true-do.io/fedora-workstation-vs-silverblue/>

<https://www.makeuseof.com/fedora-silverblue-getting-started/>

Reference

[libostree](https://github.com/ostreedev/ostree)
