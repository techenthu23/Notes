- [Installing additional desktop environments](#installing-additional-desktop-environments)
  - [Switching desktop environments](#switching-desktop-environments)
    - [using a graphical user interface (GUI)](#using-a-graphical-user-interface-gui)
    - [using the command line interface (CLI)](#using-the-command-line-interface-cli)
  - [Switch Desktop Environment Gnome Shell To KDE Plasma](#switch-desktop-environment-gnome-shell-to-kde-plasma)
- [DNF protected\_packages Plugin](#dnf-protected_packages-plugin)

# Installing additional desktop environments

You can list available desktop environments using the default package manager, dnf. In a terminal use the dnf group list command to list all available desktop environments:

`$ dnf group list -v --available | grep desktop`

Install the required desktop environment using the dnf install command. Ensure to prefix with the @ sign, for example:

`# dnf install @kde-desktop-environment`

You can also use the full name using the groupinstall command to install the complete package set:

`# dnf groupinstall "KDE Plasma Workspaces"`

## Switching desktop environments

### using a graphical user interface (GUI)

First, install the desired desktop environment

- Using switchdesk

You also change your desktop environment using the switchdesk tool. It also allows you to change default desktop environment for individual users, and for all users.

Install the switchdesk and switchdesk-gui packages:

`# dnf install switchdesk switchdesk-gui`

Run the Desktop Switching Tool application.

Select the default desktop from the list of available desktop environments, and confirm.

OR Pass the selected desktop environment as the only argument to the switchdesk command, for example:

`# switchdesk kde`

### using the command line interface (CLI)

Change your default desktop environment using the /etc/sysconfig/desktop system configuration file. If this file does not exists, please create it. This file specifies the desktop for new users and the display manager to run when entering runlevel 5.

Please create/edit it using your preferred text editor. Note that you will need administrator (root) privileges to create or edit this file.

Correct values are:

`DESKTOP="<value>"`

where <value> is one of the following:

- GNOME - Selects the GNOME desktop environment.
- KDE - Selects the KDE desktop environment.

`DISPLAYMANAGER="<value>"`
where <value> is one of the following:

- GNOME - Selects the GNOME Display Manager.
- KDE - Selects the KDE Display Manager.
- XDM - Selects the X Display Manager.

## Switch Desktop Environment Gnome Shell To KDE Plasma

1. Change the type of the Fedora installation. This is required because Fedora uses package groups and protected packages. You allow removing the Gnome package groups by swapping them with the KDE package groups.

```
sudo dnf swap fedora-release-identity-workstation fedora-release-identity-kde
sudo dnf swap fedora-release-workstation fedora-release-kde
```

2. Install the KDE spin packages

`sudo dnf group install "KDE Plasma Workspaces"`

3. Now that KDE packages are installed disable GDM and enable the SDDM login manager on boot.

```
sudo systemctl disable gdm
sudo systemctl enable sddm
```

4. remove the Fedora Gnome packages and the remaining stragglers to turn the default Fedora Gnome installation into the Fedora KDE spin.

```
sudo dnf group remove "Fedora Workstation"
sudo dnf remove *gnome*
sudo dnf autoremove
```

About DNF autoremove : " Removes all “leaf” packages from the system that were originally installed as dependencies of user-installed packages, but which are no longer required by any such package. in case of some break dependencies the command `sudo dnf distro-sync`

`dnf` might uninstalled Libre Office during the swap. If you require the office suite, install it with the following command.

``

# DNF protected_packages Plugin

Prevents any DNF operation that would result in a removal of one of the protected packages from the system. These are typically packages essential for proper system boot and basic operation.

Deciding whether a package is protected is based on the package name. The set of packages names considered protected is loaded from configuration files under:

- /etc/dnf/protected.d
- /etc/yum/protected.d

Every line in all *.conf files there is taken as a protected package name. Moreover, the currently booted kernel package is always protected.

Complete disabling of the protected packages feature is done by disabling the plugin:

`dnf --disableplugin=protected_packages erase dnf`

```bash
#!/bin/sh

# lists protected packages
dnf repoquery --requires --resolve --recursive $(cat /etc/dnf/protected.d/*) | cut -d':' -f1 | sort | sed -E 's/-[0-9]$|-[0-9][0-9]$|-[0-9][0-9][0-9]$//g' | uniq > /tmp/protected.txt
cat /etc/dnf/protected.d/* >> /tmp/protected.txt
echo "kernel-core" >> /tmp/protected.txt

# lists installed packages
dnf list --installed | tail -n +2 | cut -d' ' -f1 | sed -E 's/\.x86_64$|\.i686$|\.noarch//g' > /tmp/installed.txt

grep -v -xF -f /tmp/protected.txt /tmp/installed.txt
rm /tmp/protected.txt
rm /tmp/installed.txt
```
