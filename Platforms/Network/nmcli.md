---
title: NMCLI
draft: false
---

- [NMCLI](#nmcli)
  - [Understanding nmcli](#understanding-nmcli)
    - [Starting or Stopping Network Services/Scripts Based on Network Connectivity](#starting-or-stopping-network-servicesscripts-based-on-network-connectivity)
  - [Compare nm-settings with ifcfg-* directives (IPv4)](#compare-nm-settings-with-ifcfg--directives-ipv4)
  - [Compare nm-settings with ifcfg-* directives (IPv6)](#compare-nm-settings-with-ifcfg--directives-ipv6)
  - [nmcli command examples (cheatsheet)](#nmcli-command-examples-cheatsheet)
  - [Connection management](#connection-management)
- [nmcli examples](#nmcli-examples)
  - [General Management](#general-management)
  - [Connection Management](#connection-management-1)
  - [Managing connection profile in interactive editor](#managing-connection-profile-in-interactive-editor)
  - [Adding a bonding master and two slave connection profiles](#adding-a-bonding-master-and-two-slave-connection-profiles)
  - [Adding a team master and two slave connection profiles](#adding-a-team-master-and-two-slave-connection-profiles)
  - [Adding a bridge and two slave profiles](#adding-a-bridge-and-two-slave-profiles)
  - [Convenient field values retrieval](#convenient-field-values-retrieval)
  - [Device management](#device-management)
  - [Dispatcher Scripts](#dispatcher-scripts)

# NMCLI

nmcli stands for **N**etwork **M**anager **C**ommand **L**ine **I**nterface.

With the Network Manager Text User Interface command called nmtui, it is part of the NetworkManager component in charge of the network subsystem

nmcli is a new way to integrate network configuration into scripts.

Five domains are available:

1. general: it deals with NetworkManager’s general status and operations,
2. networking: it’s about overall networking control,
3. radio: it manages NetworkManager radio switches,
4. connection: it’s all about NetworkManager’s connections,
5. device: it deals with devices managed by NetworkManager.

In addition, the display can be altered by some options: -p to get a nice output, -t to get a minimal one. Shortcuts are intensively used: g for general, n for networking, etc

## Understanding nmcli

- nmcli is a command-line tool for controlling NetworkManager and reporting network status.
- It can be utilised as a replacement for nm-applet or other graphical clients. nmcli is used to create, display, edit, delete, activate, and deactivate network connections, as well as control and display network device status.
- Connections are stored in configuration files
- The NetworkManager service must be running to manage these files

The NetworkManager should come preinstalled on a CentOS/RHEL 8 basic installation, otherwise, you can install it using the DNF package manager. The global configuration file for NetworkManager is located at /etc/NetworkManager/NetworkManager.conf and additional configuration files can be found in /etc/NetworkManager/.

```
# dnf install NetworkManager
# systemctl is-active NetworkManager
# systemctl is-enabled NetworkManager
# systemctl status NetworkManager 
# systemctl reload NetworkManager
```

### Starting or Stopping Network Services/Scripts Based on Network Connectivity

NetworkManager has a useful option that allows users to execute services (such as NFS, SMB, etc.) or simple scripts based on network connectivity.

For example, if you want to automatically mount a remote directory locally with sshfs, mount SMB shares, or mount NFS shares after switching between networks. You may want such network services to be executed not until NetworkManager is up and running (all connections are active).

This feature is provided by the NetworkManager-dispatcher service (which must be started and enabled to start automatically at system boot). Once the service is running, you can add your scripts to the /etc/NetworkManager/dispatcher.d directory.

All scripts must be executable and writable, and owned by root, for example:

```
# chown root:root /etc/NetworkManager/dispatcher.d/10-nfs-mount.sh
# chmod 755 /etc/NetworkManager/dispatcher.d/10-nfs-mount.sh
```

 > Note: The dispatcher scripts are will be executed in alphabetical order at connection time, and in reverse alphabetical order at disconnect times.

## Compare nm-settings with ifcfg-* directives (IPv4)

| nmcli con mod                                | ifcfg-* file             | Effect                                                                 |
| :------------------------------------------- | :----------------------- | :--------------------------------------------------------------------- |
| ipv4.method manual                           | BOOTPROTO=none           | IPv4 address configured statically                                     |
| ipv4.method auto                             | BOOTPROTO=dhcp           | Will look for configuration settings from a DHCPv4 server              |
| ipv4.address "192.168.0.10/24"               | IPADDR=192.168.0.10      | Set static IPv4 address, network prefix                                |
|                                              | PREFIX=24                |                                                                        |
| ipv4.gateway 192.168.0.1                     | GATEWAY=192.168.0.1      | Set IPv4 Gateway                                                       |
| ipv4.dns 8.8.8.8                             | DNS1=8.8.8.8             | Modify /etc/resolv.conf to use this nameserver                         |
| ipv4.dns-search example.com                  | DOMAIN=example.com       | Modify /etc/resolv.conf to use this domain in the search directive     |
| ipv4.ignore-auto-dns true                    | PEERDNS=no               | Ignore DNS Server information from the DHCP Server                     |
| connection.autoconnect yes                   | ONBOOT=yes               | Automatically activate this connection on boot                         |
| connection.id eth0                           | NAME=eth0                | The name of this connection                                            |
| connection.interface-name eth0               | DEVICE=eth0              | The connection is bound to the network interface with this name        |
| 802-3-ethernet.mac-address 08:00:27:4b:7a:80 | HWADDR=08:00:27:4b:7a:80 | The connection is bound to the network interface with this MAC Address |
| ipv4.never-default no                        | DEFROUTE=yes             | Never use provided interface's gateway as default gateway              |

## Compare nm-settings with ifcfg-* directives (IPv6)

| nmcli con mod                                | ifcfg-* file             | Effect                                                                 |
| :------------------------------------------- | :----------------------- | :--------------------------------------------------------------------- |
| ipv4.method manual                           | BOOTPROTO=none           | IPv4 address configured statically                                     |
| ipv4.method auto                             | BOOTPROTO=dhcp           | Will look for configuration settings from a DHCPv4 server              |
| ipv4.address "192.168.0.10/24"               | IPADDR=192.168.0.10      | Set static IPv4 address, network prefix                                |
|                                              | PREFIX=24                |                                                                        |
| ipv4.gateway 192.168.0.1                     | GATEWAY=192.168.0.1      | Set IPv4 Gateway                                                       |
| ipv4.dns 8.8.8.8                             | DNS1=8.8.8.8             | Modify /etc/resolv.conf to use this nameserver                         |
| ipv4.dns-search example.com                  | DOMAIN=example.com       | Modify /etc/resolv.conf to use this domain in the search directive     |
| ipv4.ignore-auto-dns true                    | PEERDNS=no               | Ignore DNS Server information from the DHCP Server                     |
| connection.autoconnect yes                   | ONBOOT=yes               | Automatically activate this connection on boot                         |
| connection.id eth0                           | NAME=eth0                | The name of this connection                                            |
| connection.interface-name eth0               | DEVICE=eth0              | The connection is bound to the network interface with this name        |
| 802-3-ethernet.mac-address 08:00:27:4b:7a:80 | HWADDR=08:00:27:4b:7a:80 | The connection is bound to the network interface with this MAC Address |
| ipv4.never-default no                        | DEFROUTE=yes             | Never use provided interface's gateway as default gateway              |

## nmcli command examples (cheatsheet)

Below are some of the chosen nmcli command examples

```
nmcli <options> <section> <action>
```

There are eight sections, each related to a specific set of network actions:

1. Help provides help about ncmcli's commands and usage.
2. General retrieves NetworkManager's status and global configuration.
3. Networking provides commands to query a network connection's status and enable or disable connections.
4. Radio provides commands to query a WiFi network connection's status and enable or disable connections.
5. Monitor provides commands to monitor NetworkManager activity and observe network connections' status changes.
6. Connection provides commands to bring network interfaces up and down, to add new connections, and to delete existing connections.
7. Device is mainly used to modify parameters associated with a device (e.g., the interface name) or to connect a device using an existing connection.
8. Secret registers nmcli as a NetworkManager secret agent listening for secret messages. This is very rarely required because nmcli does this automatically when connecting to networks.

## Connection management

It's important to understand nmcli's nomenclature. A network connection is something that holds all the information about a connection. You can think of it as a network configuration. A connection encapsulates all the information related to a connection, including the data-link layer and the IP-addressing information. That's layer 2 and layer 3 in the OSI networking model.

When you are configuring networking on Linux, you're usually configuring connections that will eventually bind to networking devices, which are the network interfaces installed in a computer. When a connection is used by a device, the connection is said to be active or up. The opposite of active is inactive or down.

# nmcli examples

## General Management

|                                               |                                                                        |
| :-------------------------------------------- | :--------------------------------------------------------------------- |
| nmcli general                                 | To verify NetworkManager is running and nmcli can communicate with it: |
| nmcli general permissions                     | Listing NetworkManager polkit permissions                              |
| nmcli general logging                         | Listing NetworkManager log level and domains                           |
| nmcli g log level DEBUG domains CORE,ETHER,IP | NetworkManager log in DEBUG level  for only CORE, ETHER and IP domains |
| nmcli g log level INFO domains DEFAULT        | restores the default logging state                                     |

## Connection Management

|                                                                      |                                                                                                                                                                                   |
| :------------------------------------------------------------------- | :-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| nmcli connection show                                                | To list all in-memory and on-disk network connection profiles:                                                                                                                    |
| nmcli connection add type ethernet ifname enp0s8                     | create network connections and specify elements of their configuration at the same time.                                                                                          |
| nmcli connection up ethernet-enp0s8                                  | Activate the connection . This will bound it  to the enp0s8 network interface device.                                                                                             |
| nmcli connection modify ethernet-enp0s8 ipv4.address 192.168.4.26/24 | Set the IP address for the connection                                                                                                                                             |
| nmcli connection modify ethernet-enp0s8 ipv4.method manual           | set the connection's method of obtaining an IP address to manual: For your changes to take effect, you need to bounce the connection by stopping it and bringing it back up again |
| nmcli connection down ethernet-enp0s8                                | Stopping the connection                                                                                                                                                           |
| nmcli connection up ethernet-enp0s8                                  | Starting the connection .                                                                                                                                                         |
| nmcli connection modify ethernet-enp0s8 ipv4.method auto             | set the connection to use DHCP                                                                                                                                                    |
| nmcli --ask con up my-vpn-con                                        | Activating a VPN connection profile requiring interactive password input                                                                                                          |
| nmcli connection delete `[id \| uuid \| path] ID...` | Delete the connection name and its configuration file | 

```
nmcli connection monitor [id | uuid |path] ID...
Monitor connection profile activity

nmcli connection reload
Reload all connection files from disk

nmcli connection load filename...
Load/reload one or more connection files from disk

nmcli connection import [--temporary] type type file file
Import an external/‐foreign configuration

nmcli connection export [id | uuid | path] ID [file]
Export a connection

```

## Managing connection profile in interactive editor

```
nmcli connection edit type ethernet
nmcli> print
nmcli> goto ethernet
nmcli> goto ipv4.addresses
nmcli> set ipv4.gateway 192.168.1.1
nmcli> verify
nmcli> print
nmcli> set ipv4.dns 8.8.8.8 8.8.4.4
nmcli> print
nmcli> verify
nmcli> save
nmcli> quit
```

To open the interactive editor on the connection you specify

```
nmcli connection edit {[id | uuid | path] ID | [type type] [conname name]}

nmcli connection edit ethernet-enp0s8
```

## Adding a bonding master and two slave connection profiles

|                                                           |                                                                                                  |
| :-------------------------------------------------------- | :----------------------------------------------------------------------------------------------- |
| nmcli con add type bond ifname mybond0 mode active-backup | adds a master bond connection, naming the bonding interface mybond0 and using active-backup mode |
| nmcli con add type ethernet ifname eth1 master mybond0    | add slaves connections, both enslaved to mybond0                                                 |
| nmcli con add type ethernet ifname eth2 master mybond0    | add slaves connections, both enslaved to mybond0                                                 |

## Adding a team master and two slave connection profiles

|                                                                                   |                                                                                                                                                             |
| :-------------------------------------------------------------------------------- | :---------------------------------------------------------------------------------------------------------------------------------------------------------- |
| nmcli con add type team con-name Team1 ifname Team1 config team1-master-json.conf | adds a master team profile, naming the team interface and the profile Team1. The team configuration for the master is read from team1-master-json.conf file |
| nmcli con add type ethernet con-name Team1-slave1 ifname em1 master Team1         | add slaves profiles bound to the em1 interface and  enslaved to Team1.                                                                                      |
| nmcli con add type ethernet con-name Team1-slave2 ifname em2 master Team1         | add slaves profiles bound to the em2 interface and  enslaved to Team1.                                                                                      |

## Adding a bridge and two slave profiles

|                                                                                |                                                                                              |
| :----------------------------------------------------------------------------- | :------------------------------------------------------------------------------------------- |
| nmcli con add type bridge con-name TowerBridge ifname TowerBridge              | adds a master bridge connection, naming the bridge interface and the profile as TowerBridge. |
| nmcli con add type ethernet con-name br-slave-1 ifname ens3 master TowerBridge | add slaves profiles tied to ens3 interface and enslaved to TowerBridge                       |
| nmcli con add type ethernet con-name br-slave-2 ifname ens4 master TowerBridge | add slaves profiles tied to ens4 interface and enslaved to TowerBridge                       |
| nmcli con modify TowerBridge bridge.stp no                                     | displays the profile so that all parameters can be reviewed                                  |

## Convenient field values retrieval

|                                                          |                                                   |
| :------------------------------------------------------- | :------------------------------------------------ |
| nmcli -g ip4.address connection show my-con-eth0         | Convenient field values retrieval for scripting   |
| nmcli -g ip4.address,ip4.dns connection show my-con-eth0 | Gets value for multiple fields seperated by comma |
| nmcli -t -f general -e yes -m tab dev show eth0          | Escaping colon characters in tabular mode         |

## Device management

|                                                       |                                                                               |
| :---------------------------------------------------- | :---------------------------------------------------------------------------- |
| nmcli device status                                   | Displays status of all the network interfaces:                                |
| nmcli device show enp0s8                              | Displays device details                                                       |
| nmcli -f all dev wifi list                            | Listing available Wi-Fi Aps                                                   |
| nmcli --ask device wifi connect "$SSID"               | Connect to a password-protected wifi network                                  |
| nmcli -p -f general,wifi-properties device show wlan0 | Showing general information and properties for a Wi-Fi interface              |
| nmcli dev dis dev                                     | Deactivate and disconnect the current connection on the network interface dev |
| nmcli device reapply ifname                           | Attempt to update device                                                      |

## Dispatcher Scripts

```

# Script should be owned by root
chown root:root /etc/NetworkManager/dispatcher.d/10-script.sh

# Must not be writable by group or other
chmod 755 /etc/NetworkManager/dispatcher.d/10-script.sh

# Each script receives two arguments
# The first argument is the interface name
$ The second argument is the network action e.g. up, down, etc.
```