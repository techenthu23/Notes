---
layout: default
title:  "OSTree"
---

- [OSTree Overview - Introduction](#ostree-overview---introduction)
- [Manage Packages on Fedora Silverblue with rpm-ostree](#manage-packages-on-fedora-silverblue-with-rpm-ostree)
- [Anatomy of an OSTree repository](#anatomy-of-an-ostree-repository)
  - [Refs](#refs)
- [Cheatsheet](#cheatsheet)
  - [Identifier Formats](#identifier-formats)
  - [Basic Commands](#basic-commands)
  - [Layered Packages](#layered-packages)
  - [Debugging and Rollback](#debugging-and-rollback)
- [Searching Packages](#searching-packages)

# OSTree Overview - Introduction

__OSTree__ is an upgrade system for Linux-based operating systems that performs atomic upgrades of complete filesystem trees. It is not a package system; rather, it is intended to complement them. A primary model is composing packages on a server, and then replicating them to clients.

The underlying architecture might be summarized as "git for operating system binaries". It operates in userspace, and will work on top of any Linux filesystem. At its core is a git-like content-addressed object store with branches (or "refs") to track meaningful filesystem trees within the store. Similarly, one can check out or commit to these branches.

Layered on top of that is bootloader configuration, management of /etc, and other functions to perform an upgrade beyond just replicating files.

You can use OSTree standalone in the pure replication model, but another approach is to add a package manager on top, thus creating a hybrid tree/package system.

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

```
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

__Performing Upgrades & Rollback on Fedora Silverblue__

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

- Temporary rollbacks: Just reboot your system and select the previous version from the boot menu to perform a temporarily roll back.

- Permanant rollback: To switch back permanently to a previous deployment, use the command:

```
rpm-ostree rollback
```

After rolling back, you will technically be on an old OS version, and may be prompted to update. Updating will undo the rollback, so should be avoided if you want the rollback to stay in effect.

rpm-ostree only keeps one rollback version available by default. If you want to rollback to another version that the one currently available on your system, you can do so with the following commands:

Pull the ostree commit log from the remote repository:

```
sudo ostree pull --commit-metadata-only --depth=10 fedora fedora/39/x86_64/kinoite
```

Display the log:

```
$ ostree log fedora:fedora/39/x86_64/kinoite
Deploy a specific commit:
```

```
rpm-ostree deploy 39.20230716.0
```

Note that this will deploy the exact version as requested and will not include overlayed packages and other changes.

# Anatomy of an OSTree repository

## Refs

Like git, OSTree uses the terminology “references” (abbreviated “refs”) which are text files that name (refer to) particular commits. See the Git Documentation for information on how git uses them. Unlike git though, it doesn’t usually make sense to have a “main” branch. There is a convention for references in OSTree that looks like this: `exampleos/buildmain/x86_64-runtime` and `exampleos/buildmain/x86_64-devel-debug`. These two refs point to two different generated filesystem trees. In this example, the “runtime” tree contains just enough to run a basic system, and “devel-debug” contains all of the developer tools and debuginfo.

The ostree supports a simple syntax using the caret ^ to refer to the parent of a given commit. For example, `exampleos/buildmain/x86_64-runtime^` refers to the previous build, and `exampleos/buildmain/x86_64-runtime^^` refers to the one before that.

Other guides of interest are:

# Cheatsheet

## Identifier Formats

| Command                            | Description                                                    |
| :--------------------------------- | :------------------------------------------------------------- |
| `ostree://<REMOTE>:<BRANCH>`       | eg ostree://onerepo:fedora/f28/i686/host                       |
| `<OS>/<STREAM>/<ARCH>/<TAG>`       | Branch (typical format) : eg fedora/rawhide/x86_64/workstation |
| `ostree remote add <REMOTE> <URL>` | Add remotes                                                    |
| `ostree remote delete <REMOTE>`    | Delete remotes                                                 |
| `ostree remote list`               | List configured remotes                                        |
| `ostree remote refs <REMOTE>`      | List remote contents                                           |

## Basic Commands

| Command                               | Description              |
| :------------------------------------ | :----------------------- |
| `rpm-ostree status`                   | Get system status        |
| `rpm-ostree upgrade --check`          | Find available updates   |
| `rpm-ostree upgrade`                  | Update to latest         |
| `rpm-ostree rebase <REMOTE>:<BRANCH>` | Switch to a different OS |

> Your operating system vendor may provide multiple base branches. For example, Fedora Atomic Host has branches. You can use the rebase command to switch between these; this can represent a major version upgrade, or logically switching between different “testing” streams within the same release. Like every other rpm-ostree operation, All layered packages and local state will be carried across.

## Layered Packages

| Command                          | Description                 |
| :------------------------------- | :-------------------------- |
| `rpm-ostree install <PACKAGE>`   | Install a layered package   |
| `rpm-ostree uninstall <PACKAGE>` | Uninstall a layered package |

## Debugging and Rollback

| Command                                                                | Description                                         |
| :--------------------------------------------------------------------- | :-------------------------------------------------- |
| `rpm-ostree rollback`                                                  | Make the previous deployment the default boot entry |
| `rpm-ostree cleanup --rollback`                                        | Remove the previous deployment                      |
| `ostree pull --commit-metadata-only --depth=<n> <REMOTE> <BRANCH>`     | Download older commits                              |
| `rpm-ostree db diff <COMMIT1> <COMMIT2>`                               | Difference                                          |
| `ostree log <REMOTE>:<BRANCH>`                                         | List downloaded commits                             |
| `rpm-ostree deploy <COMMIT>` <br> `rpm-ostree deploy <VERSION STRING>` | Switch to a specific commit                         |
| `rpm-ostree db diff <COMMIT1> <COMMIT2>`                               | Find differences between commits                    |

# Searching Packages

Fedora Silverblue doesn’t come with dnf because it’s an immutable operating system and uses a special tool called rpm-ostree to layer packages on top instead.

Most terminal work is designed to be done in containers with toolbox, but I still do a bunch of work outside of a container. Searching for packages to install with rpm-ostree still requires dnf inside a container, as it does not have that function.

Add these two aliases to my `~/.bashrc` file so that using dnf to search or install into the default container is possible from a regular terminal. This just makes Silverblue a little bit more like what I’m used to with regular Fedora.

```
cat >> ~/.bashrc << EOF
alias sudo="sudo "
alias dnf="bash -c '#skip_sudo'; toolbox -y create 2>/dev/null; toolbox run sudo dnf"
EOF
```

If the default container doesn’t exist, toolbox creates it. Note that the alias for sudo has a space at the end. This tells bash to also check the next command word for alias expansion, which is what makes sudo work with aliases. Thus, we can make sure that both dnf and sudo dnf will work. The first part of the dnf alias is used to skip the sudo command so the rest is run as the regular user, which makes them both work the same.
