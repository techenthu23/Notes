---
title: Network Management on RHEL7
draft: false
---

- [Network Initscript and NetworkManager](#network-initscript-and-networkmanager)
  - [What is Network Initscript and NetworkManager?](#what-is-network-initscript-and-networkmanager)
    - [When should I use Network Initscript?](#when-should-i-use-network-initscript)
      - [When should I use NetworkManager?](#when-should-i-use-networkmanager)
      - [Additional Information and Known Issues](#additional-information-and-known-issues)
      - [What does the future hold for each?](#what-does-the-future-hold-for-each)
      - [Network Device Naming](#network-device-naming)
      - [Speed Enhancement](#speed-enhancement)
      - [New Bonding Driver](#new-bonding-driver)
      - [Chrony](#chrony)
      - [Precision Time Protocol](#precision-time-protocol)
      - [TCP Performance Optimizations](#tcp-performance-optimizations)
      - [Low Latency Sockets using Busy Poll](#low-latency-sockets-using-busy-poll)
      - [Related Links](#related-links)
- [Consistent Network Device Naming in RHEL7](#consistent-network-device-naming-in-rhel7)
  - [Naming Schemes Hierarchy](#naming-schemes-hierarchy)
  - [Disable consistent network device naming in RHEL7](#disable-consistent-network-device-naming-in-rhel7)
- [NIC Teaming](#nic-teaming)

# Network Initscript and NetworkManager

There are two networking methodologies available for use in RHEL 7.

1. Network Initscript
2. NetworkManager

## What is Network Initscript and NetworkManager?

- **NetworkManager** is a suite of cooperative network management tools designed to make networking simple and straightforward by eliminating the need to manually edit network configuration files by hand.  
NetworkManager provides a flexible management interface with GUI, CLI, and TUI options for managing local, remote, or even headless systems. It provides support for broad array of network interface types including Ethernet, InfiniBand IPoIB, VLANs, Bridges, Bonds, Teams, WiFi, WiMAX, WWAN, Bluetooth, VPN, and more.

- **Network initscript** _ is a basic network interface start/stop framework that is part of the initscripts package.

### When should I use Network Initscript?

Currently, NetworkManager does not support the following device types: (review required)

- ISDN
- 56k modems
- ~~Open vSwitch~~
- IPSec
- IPv6-related tunnels (SIT)
- VXLAN encapsulation

However, some of these device types are planned to be supported in future releases of NetworkManager in RHEL 7.

#### When should I use NetworkManager?

- For managing InfiniBand IPoIB interfaces

> The network initscript will also work, but system startup times are likely to be much slower.

- Whenever an easy to use network configuration and management interface is required or preferred for configuring network devices

> Using the network initscript requires administrators to have knowledge of the ifcfg file format and edits need to be done by hand in order to configure or even make simple changes to an interface configuration (introducing the chance to make configuration errors) Unlike the network initscript, NetworkManager simplifies network configuration and provides flexible interface options using either the GUI, CLI, or TUI interfaces.

While RHEL 5/6 has device hotplug support (udev rule that runs the ifup script for newly created devices), it has been disabled for RHEL 7 since it can result in race conditions when initializing newly found devices.

#### Additional Information and Known Issues

- NetworkManager is now the default mechanism for RHEL 7. If NetworkManager is disabled by the administrator, then the fallback would be to use initscripts for network interface management.
- Systemd does not allow stopping of a service once it's already been stopped. This means you're no longer able to stop network services, bring up only a single or even just a few network interfaces and then use the network initscript to perform the necessary clean-up/stopping of those active interfaces. e.g., "service network stop; ifup eth0; service network stop" would no longer work.
- When transitioning from NetworkManager to using the network initscript, the default gateway parameter in the interface's ifcfg file will be depicted as 'GATEWAY0'. In order for the ifcfg file to be compatible with the network initscript, this parameter must be renamed to 'GATEWAY'. This limitation will be addressed in an upcoming release of RHEL7.
- Certain interface bonding configuration options as defined by the BONDING\_OPTS parameter in the interface's ifcfg file may not be compatible with NetworkManager. ( Solution 1249593 )

#### What does the future hold for each?

Due to the increased focus in NetworkManager, Red Hat may at some point deprecate Network Initscript in a future major release. In the meantime, no new features or RFEs will be accepted for Network Initscript in the RHEL 7 timeframe. New features and RFEs are planned in NetworkManager. Regardless, defects and bug fixes will be allowed for both technologies.

The network initscript methodology was originally designed to be synchronously loaded. This is not a problem for RHEL 5/6 where services are started one after another, so the system loads in a predictable way. Starting in RHEL 7, the boot process is highly parallelized (due to systemd) and it’s the duty of services to cope with and dynamically react to any changes in the system. As such, the network initscript was not intended to be loaded this way and a number of timing issues have been uncovered where network devices may not start after system initialization.

The only solution to this problem is to slow down and serialize service startup to reliably bring-up these types of devices when using the network initscript. However, doing so comes at a price, a huge impact on boot times (negating one of the major benefits of systemd) and may sometimes cause the system to appear unresponsive or stalled during boot up while waiting for certain devices to come up.

#### Network Device Naming

Because network device names could change when adding new hardware, it has been decided to apply a consistent network device naming. This new naming convention, totally predictable, relies on the type of interface, the slot used, etc.  
Although this new rule is well suited for servers, it is not really desirable for laptops with one network interface now called enp6s0 or enp4s2f0 instead of eth0.  
Hopefully, it is still possible to restore the old network interface naming convention.

#### Speed Enhancement

The Red Hat Enterprise Linux 7 now provides support for 40 Gigabit Ethernet link speeds and for WiGig (IEEE 802.11ad) specification to increase wireless performance (up to 7 Gbps).

#### New Bonding Driver

Although there was already a bonding driver in RHEL 6, RedHat has decided to create a new one called Team Driver. More modular, it is implemented as a user space daemon making debugging easier.

#### Chrony

Chrony is a different implementation of the NTP v3 protocol (Network Time Protocol). It should replace ntpd for mobile and virtual systems because it can synchonize clocks quicker and with better accuracy. Chrony also provides much better response to rapid changes in the clock frequency, which is useful for virtual machines with unstable clocks or power-saving technologies that don’t keep the clock frequency constant.

#### Precision Time Protocol

Red Hat Enterprise Linux 7 includes support for the IEEE 1588 Version 2 specification, Precision Time Protocol (PTP). When used in conjunction with hardware support found in various network interface cards and network switches, PTP is capable of sub-microsecond accuracy, far better than NTP. And, by using a GPS-based time source, PTP can even be used to synchronize disparate networks with a high-degree of accuracy.

#### TCP Performance Optimizations

Red Hat Enterprise Linux 7 brings new TCP performance optimizations aimed at reducing overall communication latency:

- **TCP Fast Open** : an experimental TCP extension designed to reduce the overhead when establishing a TCP connection by eliminating one round time trip (RTT) from certain kinds of TCP conversations. It’s useful for accelerating HTTP connection handshaking and could result in speed improvements of between 4% and 41% in the page load times on popular web sites.
- **TCP Tail Loss Probe (TLP)**: an experimental algorithm that improves the efficiency of how the TCP networking stack deals with lost packets at the end of a TCP transaction. For short transactions, TLP should be able to reduce transmission timeouts by 15% and shorten HTTP response times by an average of 6%.
- **TCP Early Retransmit** : allows the transport to use fast retransmits to recover segment losses that would otherwise require a lengthy retransmission timeout. In other words, connections recover from lost packets faster, which improves overall latency.
- **TCP Proportional Rate Reduction (PRR)**: an experimental algorithm designed to adapt transmission rates to the rates that can be processed by the recipient and by the routers along the way; especially after throttling the rate to prevent an imminent overload. It is designed to return to the maximum transfer rate faster and can help reduce HTTP response times by as much as 3-10%.

#### Low Latency Sockets using Busy Poll

Low Latency Sockets is a software implementation designed to reduce networking latency and jitter. The native protocol stack is enhanced with a low latency path in conjunction with packet classification by the NIC. This feature allows an application to enable polling for new packets directly in the device driver. It is designed to be transparent, make polling easy to use by applications and benefits applications sensitive to unpredictable latency.

#### Related Links

[Linux 10GbE Latency with Busy Poll Sockets Benchmark Study of Chelsio’s T520 and Intel’s X520 Adapters](https://www.chelsio.com/wp-content/uploads/resources/T5-10Gb-BusyPoll-Chelsio-vs-Intel.pdf)

[Open Source Kernel Enhancements for Low-Latency Sockets using Busy Poll - Intel (Whitepaper)](https://github.com/tpn/pdfs/blob/master/Open%20Source%20Kernel%20Enhancements%20for%20Low-Latency%20Sockets%20using%20Busy%20Poll%20-%20Intel%20(Whitepaper).pdf)

# Consistent Network Device Naming in RHEL7

Red Hat Enterprise Linux 7 provides methods for consistent and predictable network device naming for network interfaces. These features change the name of network interfaces on a system in order to make locating and differentiating the interfaces easier.

Traditionally, network interfaces in Linux are enumerated as eth[0123…], but these names do not necessarily correspond to actual labels on the chassis. Modern server platforms with multiple network adapters can encounter non-deterministic and counter-intuitive naming of these interfaces. This affects both network adapters embedded on the motherboard (Lan-on-Motherboard, or LOM) and add-in (single and multiport) adapters.

In Red Hat Enterprise Linux 7, udev supports a number of different naming schemes. The default is to assign fixed names based on firmware, topology, and location information. This has the advantage that the names are fully automatic, fully predictable, that they stay fixed even if hardware is added or removed (no re-enumeration takes place), and that broken hardware can be replaced seamlessly. The disadvantage is that they are sometimes harder to read than the eth0 or wlan0 names traditionally used. For example: enp5s0.

Warning: Do not disable consistent network device naming because it allows the system using ethX style names, where X is a unique number corresponding to a specific interface and may have different names of network interfaces during the boot process.

## Naming Schemes Hierarchy

By default, systemd will name interfaces using the following policy to apply the supported naming schemes:

- Scheme 1: Names incorporating Firmware or BIOS provided index numbers for on-board devices (example: eno1), are applied if that information from the firmware or BIOS is applicable and available, else falling back to scheme 2.
- Scheme 2: Names incorporating Firmware or BIOS provided PCI Express hotplug slot index numbers (example: ens1) are applied if that information from the firmware or BIOS is applicable and available, else falling back to scheme 3.
- Scheme 3: Names incorporating physical location of the connector of the hardware (example: enp2s0), are applied if applicable, else falling directly back to scheme 5 in all other cases.
- Scheme 4: Names incorporating interface's MAC address (example: enx78e7d1ea46da), is not used by default, but is available if the user chooses.
- Scheme 5: The traditional unpredictable kernel naming scheme, is used if all other methods fail (example: eth0).

This policy, the procedure outlined above, is the default. If the system has biosdevname enabled, it will be used. Note that enabling biosdevname requires passing biosdevname=1 as a kernel command-line parameter, except in the case of a Dell system, where biosdevname will be used by default as long as it is installed. If the user has added udev rules which change the name of the kernel devices, those rules will take precedence.

The device name procedure in detail is as follows:

1. A rule in /usr/lib/udev/rules.d/60-net.rules instructs the udev helper utility, /lib/udev/rename\_device, to look into all /etc/sysconfig/network-scripts/ifcfg-suffix files. If it finds an ifcfg file with a HWADDR entry matching the MAC address of an interface it renames the interface to the name given in the ifcfg file by the DEVICE directive.
2. A rule in /usr/lib/udev/rules.d/71-biosdevname.rules instructs biosdevname to rename the interface according to its naming policy, provided that it was not renamed in a previous step, biosdevname is installed, and biosdevname=0 was not given as a kernel command on the boot command line.
3. A rule in /lib/udev/rules.d/75-net-description.rules instructs udev to fill in the internal udev device property values ID\_NET\_NAME\_ONBOARD, ID\_NET\_NAME\_SLOT, ID\_NET\_NAME\_PATH, ID\_NET\_NAME\_MAC by examining the network interface device. Note, that some device properties might be undefined.
4. A rule in /usr/lib/udev/rules.d/80-net-name-slot.rules instructs udev to rename the interface, provided that it was not renamed in step 1 or 2, and the kernel parameter net.ifnames=0 was not given, according to the following priority: ID\_NET\_NAME\_ONBOARD, ID\_NET\_NAME\_SLOT, ID\_NET\_NAME\_PATH. It falls through to the next in the list, if one is unset. If none of these are set, then the interface will not be renamed.

Steps 3 and 4 are implementing the naming schemes 1, 2, 3, and optionally 4

## Disable consistent network device naming in RHEL7

To disable consistent network device naming, is only recommended for special scenarios.

To disable consistent network device naming, choose from one of the following:

- Disable the assignment of fixed names by "masking" udev's rule file for the default policy. This can be done by creating a symbolic link to /dev/null. As a result, unpredictable kernel names will be used. As root, enter the following command:

```
ln -s /dev/null /etc/udev/rules.d/80-net-name-slot.rules
```

- Create your own manual naming scheme, for example by naming your interfaces internet0, dmz0 or lan0. To do that, create your own udev rules file and set the NAME property for the devices. Make sure to order the new file above the default policy file, for example by naming it /etc/udev/rules.d/70-my-net-names.rules.
- Alter the default policy file to pick a different naming scheme, for example to name all interfaces after their MAC address by default. As root, copy the default policy file as follows:

```
cp /usr/lib/udev/rules.d/80-net-name-slot.rules /etc/udev/rules.d/80-net-name-slot.rules
```

> Edit the file in the /etc/udev/rules.d/ directory and change the lines as necessary.

- Open the /etc/default/grub/ file and find the `GRUB_CMDLINE_LINUX` variable.

Note  
`GRUB_CMDLINE_LINUX` is a variable that includes entries which are added to the kernel command line. It might already contain additional configuration depending on your system settings.

Add net.ifnames=0 as kernel parameter to the `GRUB_CMDLINE_LINUX` variable.

```
GRUB_CMDLINE_LINUX="net.ifnames=0"
```

To update all the GRUB 2 kernel menu entries in the /boot/grub2/grub.cfg file, enter the following command as root:  

```
grubby --update-kernel=ALL --args=net.ifnames=0 
```

The grubby utility is used for updating and displaying information about the configuration files for the grub boot loader.


# NIC Teaming

Red Hat Enterprise Linux 7 implements network teaming with a small kernel driver and a user-space daemon, teamd. The kernel handles network packets efficiently and teamd handles logic and interface processing. Software, called runners, implement load balancing and active-backup logic, such as roundrobin. The following runners are available for teamd.

- broadcast: a simple runner which transmits each pac ket from all ports.
- round robin: a simple runner which transmits packets in a rou nd-robin fashing from each of the ports.
- activebackup: this is a failover runner which watches for link changes and selects an active port for data transfers.
- loadbalance: this runner monitors traffic and uses a hash function to try to reach a perfect balance when selecting ports for packet transmission.
- lacp: impl ementsthe 802.3ad Link Aggregation Control Protocol. Can use the same transmit port selection possibilities as the loadbalance runner.

All network interaction is done through a team interface, composed of multiple network port interfaces. When controlling teamed port interfaces using NetworkManager, and especially when fault finding, keep the following in mind:

- Starting the network team interface does not automatically start the port interfaces.
- Starting a port interface always starts the teamed interface.
- Stopping the teamed interface also stops the port interfaces.
- A teamed interface without port scan start static IP connections.
- A team without ports waits for ports when starting DHCP connections.