---
title: "Linux Quick Bits"
draft: false
---

- [Network Management](#network-management)
- [LVM](#lvm)
- [Software Management](#software-management)
- [Subscription Management](#subscription-management)
- [procfs and sysfs](#procfs-and-sysfs)
- [System Monitoring Tools](#system-monitoring-tools)
- [User Management](#user-management)
- [Managing File/Dir Ownership, Permission and Attribute](#managing-filedir-ownership-permission-and-attribute)
- [SELinux](#selinux)
- [Miscellanous](#miscellanous)
  - [Journalctl](#journalctl)
- [Reference Links](#reference-links)

# Network Management

|     |    |
| :--------------------------------------------------------------------------------- | :------------------------------------------------------ |
| /etc/sysconfig/network-scripts/ifcfg-\*                                            | IP Address and Subnet Mask, Route    |
| Rebuilding the initial      /etc/sysconfig/network                                 | This file specifies routing and host information for all network interfaces. It is used to contain directives which are to have global effect and not to be interface specific.    |
| /etc/sysconfig/network (persistent) OR hostname (temporary)                        | To set system Hostname    |
| /etc/sysconfig/network-scripts/ifcfg-\* /etc/resolv.conf /etc/hosts                | Name Resolution If DHCP is in use, /etc/resolv.conf is automatically rewritten as interface are started unless you specify PEERDNS=no in the relevant interface configuration files    |
| /etc/sysconfig/network-scripts/route-                                              | Static Route : the configuration is store per interface    |
| Ethernet Interface – eth0 Bonded network device – bond0                            | Wireless Device – wlan0 Internet Bridge set up for virtual hosts– virbr0    |
| /etc/sysconfig/network                                                             | The file specifies routing and host information for all network interfaces. It is used to contain directives which are to have a global effect and not to be interface specific    |
| /etc/sysconfig/network-scripts/ifcfg-                                              | For each network interface there is a corresponding interface configuration script and provide information specific to it    |
| /etc/sysconfig/networking/                                                         | The directory is used by the now deprecated Network Administration tool ( System-config-network) Its contenst should not be edited manually Using only one method for networking configuration is strongly encouraged, due to the risk of configuration deletion    |
| /sbin/ip                                                                           | Command to show or temporarily modif devices, routing , policy routing and tunnels    |
| ip addr show eth0                                                                  | Show the Interface values and settings. <br> Note: Avoid using the obsolete ifconfig command because if the system has a new style secondary IP addresses set on a interface that does not have a backward compatibility IP alias label then ifconfig will not show it    |
| ip –s link show eth0                                                               |    |
| ip -6 route                                                                        | Shows IPv6 Routing Table    |
| getent hosts OR dig / dig –x host –v                                               | To test hostname resolution . Looks for answers from other sources based on the hosts line in /etc/nsswitch.conf    |
| ifup eth0                                                                          | To bring up network Interface    |
| ifdown eth0                                                                        | To bring down the network interface eth0    |
| *Older Method* <br> # service NetworkManager stop<br> # chkconfig NetworkManager off<br># ip addr add 10.1.1.250/24 dev eth0 label eth0:0<br> # ip addr show eth0<br> # Create file /etc/sysconfig/network-scripts/ifcfg-eth0:0 with the following contents<br> DEVICE=eth0:0<br> IPADDR=10.1.1.250<br> PREFIX=24<br> ONPARENT=yes<br><br># service network restart<br><br>**Newer Method**<br># ip addr add 10.1.1.250/24 dev eth0<br> # ip addr show eth0<br> # Add following contents to /etc/sysconfig/network-scripts/ifcfg-eth0<br> IPADDR2=10.1.1.250<br> PREFIX2=24<br> # To delete IP alias manually<br> `/sbin/ifconfig ethX:Y down`<br> -OR-<br>`# ip addr del $(ip addr list label ethX:Y | awk ‘{ print $2 }’) dev ethX label ethX`.Y                                                                                    | **IP Aliases**<br>RHEL 6 supports 2 methods of adding IP aliases to an interface. An older method which is backward compatible with older releases RHEL but not compatible with NetworkManager, and a newer method which is compatible with NetworkManager, but with not older releases<br> Older Method : the ONBOOT parameter is replace with ONPARENT to indicate that the device is started when the parent device is brought up<br> Newer Method: if the NetwrokManager service is running then it should pickup the changes and activate the moment you save the file. If it is disabled activate the change by restarting the network service ( #service network restart)<br> Limitations of IP alias: Aliases interfaces does not support DHCP Subnet Aliasing (alias network interface IP address is preferred to be in the same network subnet as physical network interface below – if not proper network infrastructure configuration is needed) /proc/net/aliases /proc/net/alias\_types 
| create a file in the /etc/sysconfig/network-scripts/ directory called ifcfg-bondN wih following contents<br> DEVICE=bond0<br> IPADDR=192.168.1.1<br> NETMASK=255.255.255.0<br> ONBOOT=yes<br> BOOTPROTO=none<br> USERCTL=no<br> BONDING\_OPTS=”bonding parameters separated by spaces“<br> BONDiNG\_OPTS=”mode=1 mimon=50”<br><br>After the channel bonding interface, /etc/sysconfig/network-scripts/ifcfg-ethN is created, the network interfaces to be bound together must be configured by adding the MASTER and SLAVE directives to their configuration files. The configuration files for each of the channel-bonded interfaces can be nearly identical.<br> DEVICE=ethN<br> BOOTPROTO=none<br> ONBOOT=yes<br> MASTER=bond0<br> SLAVE=yes<br> USERCTL=no<br> For a channel bonding interface to be valid, the kernel module must be loaded. To ensure that the module is loaded when the channel bonding interface is brought up, create a new file as root named /etc/modprobe.d/bonding.conf with following content<br> alias bond0 bonding<br><br> *Note* that you can name this file anything you like as long as it ends with a .conf extension. Insert the following line in this new file: Query the status of the binding interface and its slaves using the virtual file cat /proc/net/bonding/bond0 | **Channel Bonding Interface**<br> RHEL allows administrators to bind multiple network interfaces together into a single channel using the bonding kernel module and a special network interface called a channel bonding interface.<br> Channel bonding enables two or more network interfaces to act as one, simultaneously increasing the bandwidth and providing redundancy<br> At this time NetworManager deos not support bonded interface therefore disable it if the channel bonding needs to be use<br> Select Linux Ethernet Bonding Modes<br>Mode 0 ( balance – Round Robin)<br>Mode 1 ( Active Backup)<br>Mode 3 ( Broadcast )<br> Do not specify options for the bonding device in /etc/modprobe.d/bonding.conf, or in the deprecated /etc/modprobe.conf file.<br> To help physical identification of NIC , blink one or more of its LEDs ( 30 seconds)<br> ethtool –p eth0 30<br> Parameters to bonded interfaces can be configured without unloading (and reloading) the bonding module by manipulating files in the sysfs file system. All bonding interfaces can be configured dynamically by interacting with and manipulating files under the /sys/class/net/ directory.<br> To view all existing bonds, even if they are not up, run:<br># cat /sys/class/net/bonding\_masters<br> You can configure each bond individually by manipulating the files located in the /sys/class/net/bond/bonding/ directory.<br> First, the bond you are configuring must be taken down:<br> ifconfig bond0 down<br> As an example, to enable MII monitoring on bond0 with a 1 second interval, you could run (as root):<br># echo 1000 &gt; /sys/class/net/bond0/bonding/miimon<br> To configure bond0 for balance-alb mode, you could run either:<br># echo 6 &gt; /sys/class/net/bond0/bonding/mode<br> …or, using the name of the mode:<br> echo balance-alb &gt; /sys/class/net/bond0/bonding/mode<br> After configuring some options for the bond in question, you can bring it up and test it by running<br> ifconfig bond up<br> If you decide to change the options, take the interface down, modify its parameters using sysfs, bring it back up, and re-test. Once you have determined the best set of parameters for your bond, add those parameters as a space-separated list to the BONDING\_OPTS= directive of the /etc/sysconfig/network-scripts/ifcfg-bond file for the bonding interface you are configuring. Whenever that bond is brought up (for example, by the system during the boot sequence if the ONBOOT=yes directive is set), the bonding options specified in the BONDING\_OPTS will take effect for that bond. |
|||


**Network Bridge**

A network bridge is a Link Layer device which forwards traffic between networks based on MAC addresses and is therefore also referred to as a Layer 2 device. It makes forwarding decisions based on tables of MAC addresses which it builds by learning what hosts are connected to each network. A software bridge can be used within a Linux host in order to emulate a hardware bridge, for example in virtualization applications for sharing a NIC with one or more virtual NICs. The differences in this example are as follows: The **DEVICE**directive is given an interface name as its argument in the format **br*N***, where ***N***is replaced with the number of the interface.

The **TYPE**directive is given an argument **Bridge**or **Ethernet**. This directive determines the device type and the argument is case sensitive.

The bridge interface configuration file now has the IP address and the physical interface has only a MAC address.

An extra directive, **DELAY=0**, is added to prevent the bridge from waiting while it monitors traffic, learns where hosts are located, and builds a table of MAC addresses on which to base its filtering decisions.

The default delay of 30 seconds is not needed if no routing loops are possible.

The `NM_CONTROLLED=no` should be added to the Ethernet interface to prevent **NetworkManager**

To create a network bridge, create a file /etc/sysconfig/network-scripts/ifcfg-brN.. The contents of the file is similar to whatever type of interface is getting bridged to, such as an Ethernet interface.

```
DEVICE=br0
TYPE=Bridge
IPADDR=192.168.1.1
NETMASK=255.255.255.0
ONBOOT=yes
BOOTPROTO=static
NM_CONTROLLED=no
DELAY=0
```

**Name Service Caching Daemon (nscd)** 

The Name Service Caching Daemon (**nscd**) is a service that runs in the background and keeps a copy of /etc/passwd, /etc/group, and /etc/hosts files in **/var/db/nscd/** directory. This can aid performance by reducing disk read overhead on a heavily loaded system dealing with many logins or name resolution requests. The file /etc/nscd.conf contains the configuration information for the nscd service. Information about the use of this file can be found by typing the command man nscd.conf

if you want to flush the cache, please use the nscd -i command
```
nscd -i hosts
```

# LVM

The underlying physical storage unit of an LVM logical volume is a block device such as a partition or whole disk. This device is initialized as an Physical volumes (PV) . PVs are combined into a *volume group*(VG). This creates a pool of disk space out of which LVM logical volumes (LVs) can be allocated.

The Clustered Logical Volume Manager (CLVM) is a set of clustering extensions to LVM and allows a cluster of computers to manage shared storage (for example, on a SAN) using LVM.CLVM is part of the Resilient Storage Add-On. In order to use CLVM, the High Availability Add-On and Resilient Storage Add-On software, including the **clvmd**daemon which is the key clustering extension to LVM must be running. The **clvmd**daemon runs in each cluster computer and distributes LVM metadata updates in a cluster, presenting each cluster computer with the same view of the logical volumes. CLVM requires changes to the **lvm.conf**file for cluster-wide locking.

**Physical Volume(PV):** It is block device such as a partition or whole disk . To use the device for an LVM logical volume the device must be initialized as a PV(PV). Initializing a block device as a PV places a label near the start of the device. By default, the LVM label is placed in the second 512-byte sector. You can overwrite this default by placing the label on any of the first 4 sectors. This allows LVM volumes to co-exist with other users of these sectors, if necessary.

An **LVM label** provides correct identification and device ordering for a physical device, since devices can come up in any order when the system is booted. An LVM label remains persistent across reboots and throughout a cluster and identifies the device as an LVM PV. It contains a random unique identifier (the UUID) for the PV. It also stores the size of the block device in bytes, and it records where the LVM metadata will be stored on the device.

The **LVM metadata** contains the configuration details of the LVM VGs on your system. It is small and stored as ASCII. By default, an identical copy of the metadata is maintained in every metadata area in every PV within the VG. Currently LVM allows you to store 0, 1 (default) or 2 identical copies of its metadata on each PV. Once you configure the number of metadata copies on the PV, you cannot change that number at a later time. The 1st copy is stored at the start of the device, shortly after the label. If there is a 2nd copy, it is placed at the end of the device. If you accidentally overwrite the area at the beginning of your disk by writing to a different disk than you intend, a 2nd copy of the metadata at the end of the device will allow you to recover the metadata.

**Volume Groups(VG):**A pool of disk space created by combining PVs out of which LVs can be allocated. Within a VG, the disk space available for allocation is divided into units of a fixed-size called extents.

An extent is the smallest unit of space that can be allocated. Within a PV , extents are referred to as physical extents. A LV is allocated into logical extents of the same size as the physical extents. The extent size is thus the same for all LVs in the VG. The VG maps the logical extents to physical extents.

**Types of LV**

There are 3 types of LVM LVs: 
1)  linear volumes, 
2)  striped volumes, and 
3)  mirrored volumes.

- **Linear LVs** 
  
  It aggregates space from one or more PVs into one LV.

- **Stripped LVs** 

  In a striped LV
  - the file system lays the data out across the underlying predetermined number of PVs in roundrobin fashion allowing to improve the efficiency of the data I/O mainly for large sequential reads & writes.
  - the size of the stripe cannot exceed the size of an extent.
  - Striped LVs can be extended by concatenating another set of devices onto the end of the first set. In order extend a striped LV, however, there must be enough free space on the underlying physical volumes that make up the volume group to support the stripe

- **Mirrored LVs :**

  A mirror maintains identical copies of data on different devices. When data is written to one device, it is written to a second device as well, mirroring the data. This provides protection for device failures. When one leg of a mirror fails, the LV becomes a linear volume & can still be accessed. An LVM mirror divides the device being copied into regions that are typically 512KB in size. LVM maintains a small log which it uses to keep track of which regions are in sync with the mirror or mirrors. This log can be kept on disk, which will keep it persistent across reboots, or it can be maintained in memory.

**Thinly-Provisioned LVs (Thin Volumes) :**

As of the RHEL 6.4 release, LVs can be thinly provisioned. This allows you to create LVs that are larger than the available extents. Using thin provisioning, you can manage a storage pool of free space, known as a thin pool, to be allocated to an arbitrary number of devices when needed by applications. You can then create devices that can be bound to the thin pool for later allocation when an application actually writes to the LV. The thin pool can be expanded dynamically when needed for cost-effective allocation of storage space. Thin volumes are not supported across the nodes in a cluster. The thin pool & all its thin volumes must be exclusively activated on only one cluster node. To make sure that all available space can be used, LVM supports data discard. This allows for re-use of the space that was formerly used by a discarded file or other block range. Thin volumes provide support for a new implementation of copy-on-write (COW) snapshot LVs, which allow many virtual devices to share the same data in the thin pool.

**Snapshot Volumes:**

The LVM snapshot feature provides the ability to create virtual images of a device at a particular instant without causing a service interruption. When a change is made to the original device (the origin) after a snapshot is taken, the snapshot feature makes a copy of the changed data area as it was prior to the change so that it can reconstruct the state of the device. Because a snapshot copies only the data areas that change after the snapshot is created, the snapshot feature requires a minimal amount of storage. For example, with a rarely updated origin, 3-5 % of the origin’s capacity is sufficient to maintain the snapshot.. The size of the snapshot governs the amount of space set aside for storing the changes to the origin volume. For example, if you made a snapshot & then completely overwrote the origin the snapshot would have to be at least as big as the origin volume to hold the changes. You need to dimension a snapshot according to the expected level of change.

Snapshot copies of a file system are virtual copies, not actual media backup for a file system. Snapshots do not provide a substitute for a backup procedure.

LVM snapshots are not supported across the nodes in a cluster. You cannot create a snapshot volume in a clustered volume group.

If a snapshot runs full, the snapshot becomes invalid, since it can no longer track changes on the origin volume. You should regularly monitor the size of the snapshot. Snapshots are fully resizeable, however, so if you have the storage capacity you can increase the size of the snapshot volume to prevent it from getting dropped. Conversely, if you find that the snapshot volume is larger than you need, you can reduce the size of the volume to free up space that is needed by other LVs.

When you create a snapshot file system, full read & write access to the origin stays possible. If a chunk on a snapshot is changed, that chunk is marked & never gets copied from the original volume. There are several uses for the snapshot feature:

Most typically, a snapshot is taken when you need to perform a backup on a LV without halting the live system that is continuously updating the data. You can execute the fsck command on a snapshot file system to check the file system integrity & determine whether the original file system requires file system repair. Because the snapshot is read/write, you can test applications against production data by taking a snapshot & running tests against the snapshot, leaving the real data untouched.

You can create LVM volumes for use with Red Hat virtualization. LVM snapshots can be used to create snapshots of virtual guest images. These snapshots can provide a convenient way to modify existing guests or create new guests with minimal additional storage.

As of the RHEL 6 release, you can use the **–merge** option of the **lvconvert** command to merge a snapshot into its origin volume. One use for this feature is to perform system rollback if you have lost data or files or otherwise need to restore your system to a previous state. After you merge the snapshot volume, the resulting LV will have the origin volume’s name, minor number, & UUID & the merged snapshot is removed.

**Thinly-Provisioned Snapshot Volumes :**Supported by RHEL 6.4 itallow many virtual devices to be stored on the same data volume. This simplifies administration and allows for the sharing of data between snapshot volumes. It is not supported across the nodes in a cluster and must be exclusively activated on only one cluster node.

- /boot
  
  The **/boot/**directory contains static files required to boot the system, e.g. the Linux kernel. These files are essential for the system to boot properly.

- /dev 
  The **/dev/**directory contains device nodes that represent 
  1) Devices attached to the system 
  2) Virtual devices provided by the kernel. 
  
  These device nodes are essential for the system to function properly. The **udevd**daemon creates and removes device nodes in **/dev/**as needed.
  
  The mounted partition **/dev/shm**represents the system’s virtual memory file system.

Most files pertaining to RPM are kept in the **/var/lib/rpm /**directory

- /proc
  
  The /proc Virtual File System main purpose is to provide a file-based interface to hardware, memory, running processes, and other system components. You can retrieve realtime information on many system components by viewing the corresponding **/proc**file. Some of the files within **/proc**can also be manipulated (by both users and applications) to configure the kernel.

The ext3 file system is essentially an enhanced version of the ext2 file system. After an unexpected power failure or system crash (also called an *unclean system shutdown*), each mounted ext2 file system on the machine must be checked for consistency by the **e2fsck**program. This is a time-consuming process that can delay system boot time significantly, especially with large volumes containing a large number of files. During this time, any data on the volumes is unreachable.

It is possible to run **fsck -n**on a live filesystem. However, it will not make any changes and may give misleading results if partially written metadata is encountered. If LVM is used in the stack, another option is to take an LVM snapshot of the filesystem and run **fsck**on it instead. Finally, there is the option to remount the filesystem as read only. All pending metadata updates (and writes) are then forced to the disk prior to the remount. This ensures the filesystem is in a consistent state, provided there is no previous corruption. It is now possible to run **fsck -n**.

The journaling provided by the ext3 file system means that this sort of file system check is no longer necessary after an unclean system shutdown. The only time a consistency check occurs using ext3 is in certain rare hardware failure cases, such as hard drive failures. The time to recover an ext3 file system after an unclean system shutdown does not depend on the size of the file system or the number of files; rather, it depends on the size of the *journal*used to maintain consistency. The default journal size takes about a second to recover, depending on the speed of the hardware.

The only journaling mode in ext3 supported by Red Hat is `data=ordered` (default).

ext3 updated features In RHEL 6:

**Default Inode Sizes Changed** The default size of the on-disk inode has increased for more efficient storage of extended attributes, for example ACLs or SELinux attributes. Along with this change, the default number of inodes created on a file system of a given size has been decreased. T he inode size may be selected with the **mke2fs -I**option, or specified in **/etc/mke2fs.conf**to set system-wide defaults for **mke2fs**.

**New Mount Option** 

- `data_err`
  
  A new mount option has been added: `data_err=abort` and it instructs ext3 to abort the journal if an error occurs in a file data (as opposed to metadata) buffer in `data=ordered` mode. This option is disabled by default (i.e. set as `data_err=ignore`).

**More Efficient Storage Use** 

When creating a file system (i.e. **mkfs**), **mke2fs**will attempt to “discard” or “trim” blocks not used by the file system metadata. This helps to optimize SSDs or thinly-provisioned storage. T o suppress this behavior, use the **mke2fs -K**option.

|                                                                           |                                                                     |
| :------------------------------------------------------------------------ | :------------------------------------------------------------------ |
| Directories<br> /etc/lvm – default lvm directory location<br> /etc/lvm/backup – where the automatic backups go<br> /etc/lvm/cache – persistent filter cache<br> /etc/lvm/archive – where automatic archives go after a volume group change<br> /var/lock/lvm – lock files to prevent metadata corruption<br> Files<br> /etc/lvm/lvm.conf – main lvm configuration file<br> $HOME/.lvm – lvm history                                                                     | Directory and Files |
| pvcreate /dev/sda1 /dev/sdb1<br> vgcreate vg\_apps /dev/sda1 /dev/sdb1<br> #create LV with all available space from vg\_apps<br> lvcreate -n lv\_apps -l 100%FREE vg\_apps<br> #create LV with specific size<br> lvcreate -L 108G -n lv\_apps vg\_apps<br> mkfs.ext4 /dev/vg\_apps/lv\_apps |
| lvmdump<br> lvmdump ‐d<br> `dmsetup [info|ls|status]`                     | Diagnostic<br> Note: by default the lvmdump command creates a tar ball
| pvdisplay ‐v<br> pvs ‐v<br> pvs ‐a<br> pvs ‐‐segments (see the disk segments used)                                                                       | PV display<br> pvs attributes are:<br> (a)llocatable<br> e(x)ported |

# Software Management

All software on a RHEL system is divided into RPM packages, which can be installed. 

**yum** (Yellowdog Updater Modified) is an interactive, rpm based, package manage and it can perform installation/removal/update of packages, including dependency analysis & obsolete processing based on “repository” metadata. Yum provides secure package management by enabling GPG (Gnu Privacy Guard; also known as GnuPG) signature verification on GPG-signed packages to be turned on for all package repositories (i.e. package sources), or for individual repositories. When signature verification is enabled, Yum will refuse to install any packages not GPG-signed with the correct key for that repository. 

|||
|:--- |:--- |
| yum history| Yum’s transaction history|
| yum [update\|install] kernel | No distinction between installing & upgrading a kernel package when you use yum : it will do the right thing, regardless of whether you are using install or update. When using RPM, on the other hand, it is important to use the rpm -i kernel command (which installs a new kernel) instead of rpm -u kernel|
| yum update| To update all packages & their dependencies|
| yum update package1 [package2] […]| only update the listed packages|
| yum update-to [package1] [package2] […]| works like “update” but always specifies the version of the package we want to update to.|
| yum list [all \| glob\_exp1] [glob\_exp2] […]| List all available & installed packages.|
| yum check-update| To see which installed packages on your system have updates available. Returns exit value of 100 if there are packages available for an update.|
| yum search| search all RPM package names, descriptions & summaries|
| yum list installed [glob\_exp1] […] yum list installed ” krb?-\*”| Lists all packages installed on your system. The rightmost column in the output lists the repository from which the package was retrieved.<br> Glob expressions are normal strings of characters which contain one or more of the wildcard characters \* (which exp&s to match any character multiple times) & ? (which exp&s to match any one character). Bash shell will interpret these expressions as pathname expansions, & potentially pass all files in the current directory that match the globs to yum . To make sure the glob expressions are passed to yum as intended, either: escape the wildcard characters by preceding them with a backslash character double-quote or single-quote the entire glob expression.|
| yum list available| Lists all available packages in all enabled repositories.|
| yum grouplist| Lists all package groups|
| yum list updates [glob\_exp1] […]| List all packages with updates available in the yum repositories.|
| yum list extras [glob\_exp1] […]| List the packages installed on the system that are not available in any yum repository listed in the config file.|
| yum list obsoletes [glob\_exp1] […]| List the packages installed on the system that are obsoleted by packages in any yum repository listed in the config file.|
| yum list recent| List packages recently added into the repositories.|
| yum repolist| Lists the repository ID, name, & number of packages it provides for each enabled repository.|
| yum info package\_name…| To display information about one or more packages. similar to the rpm -q –info package\_name comm&|
| yumdb info package\_name| query the Yum database for alternative & useful information ( checksum, repo ) about a package|
| yum install package\_name package\_name…| to install idividual packages|
| yum install /usr/sbin/named| Install the package on the name of the binary . yum then searches through its package lists, finds the package which provides /usr/sbin/named, if any, & prompts you as to whether you want to install it.|
| yum install `http://download.project.org/pub/epel/package1.rpm` |
| Yum localinstall PACKAGEFILE.RPM| To install local install package files. It automatically downloads any dependencies the package has from configured yum repositories|
| yum whatprovides “\*/semanage”| Finding which package owns a file|
| yum grouplist –v| lists the names of all package groups, &, next to each of them, their groupid in parentheses.A package group is similar to a package: it is not useful by itself, but installing one pulls a group of dependent packages that serve a common purpose. A package group has a name & a groupid. The groupid is always the term in the last pair of parentheses|
| yum groupinstall group\_name| install a package group by passing its full group name|
| yum groupinstal l groupid| install a package group by passing groupid|
| yum install @group| install a package group . If the groupid (or quoted name) is prepend it with an @- symbol (which tells yum that you want to perform a groupinstall) & pass to install:|
| yum remove package\_name…| uninstall a particular package, as well as any packages that depend on it Yum is not able to remove a package without also removing packages which depend on it. This type of operation can only be performed by RPM, is not advised, & can potentially leave your system in a non-functioning state or cause applications to misbehave &/or crash.|
| yum group remove group OR<br> yum remove @group| remove a package group|
| yum history list| list of twenty most recent transactions, as root, he yum history comm& allows users to review information about a timeline of Yum transactions, the dates & times they occurred, the number of packages affected, whether transactions succeeded or were aborted, & if the RPM database was changed between transactions.<br> Additionally, this command can be used to undo or redo certain transactions.|
| yum history list all| To display all yum transactions|
| yum history list start\_id..end\_id| To display only transactions in a given range,|
| yum history package-list glob\_expression| list transactions from the perspective of a package|
| yum history summary| To display the summary of a single transaction|
| yum history info id…| To examine a particular transaction or transactions in more detail|
| yum history addon-info id| To determine what additional information is available for a certain transaction|
| yum history addon-info last| To determine what additional information is available for last transaction|
| yum history addon-info| To display selected type of additional information|
| yum history undo id| To revert a transaction,|
| yum history redo id| To repeat a particular transaction|

>Note that both yum history undo & yum history redo commands only revert or repeat the steps that were performed during a transaction. If the transaction installed a new package, the yum history undo command will uninstall it, & if the transaction uninstalled a package the command will again install it. This command also attempts to downgrade all updated packages to their previous version, if these older packages are still available.
>If you need to restore the system to the state before an update, consider using the fs-snapshot plug-in. When managing several identical systems, Yum also allows you to perform a transaction on one of them, store the transaction details in a file, & after a period of testing, repeat the same transaction on the remaining systems as well.

|||
|--- |--- |
| `yum -q history addon-info id saved_tx > file_name`<br> `yum load-transaction file_name`| To store the transaction details to a file, Once you copy this file to the target system, you can repeat the transaction by using the following comm& as root: Note, however that the rpm db version stored in the file must be identical to the version on the target system|
| yum version nogroups                                                                    | verify the rpm db version|
| yum history new                                                                         | To start new transaction history Yum stores the transaction history in a single SQLite database file. This will create a new, empty database file in the /var/lib/yum/history/ directory. The old transaction history will be kept, but will not be accessible as long as a newer database file is present in the directory.|
| yum clean expire-cache                                                                  | Eliminate the local data saying when the metadata & mirrorlists were downloaded for each repo. This means yum will revalidate the cache for each repo. next time it is used. However if the cache is still valid, nothing sig-nificant was deleted. (/var/cache/yum/) The **/var/cache/yum/**directory contains files used by the **Package Updater**, including RPM header information for the system. This location may also be used to temporarily store RPMs downloaded while updating the system.|
| yum clean packages                                                                      | Eliminate any cached packages from the system. Note that packages are not automatically deleted after they are downloaded|
| yum clean headers                                                                       | Eliminate all of the header files which yum uses for dependency resolution.|
| yum clean metadata                                                                      | Eliminate all of the files which yum uses to determine the remote availability of packages. Using this option will force yum to download all the metadata the next time it is run.|
| yum clean dbcache                                                                       | Eliminate the sqlite cache used for faster access to metadata. Using this option will force yum to recreate the cache the next time it is run.|
| yum clean all                                                                           | Runs yum clean packages & yum clean headers, yum clean metadata & yum clean dbcache|
| For a plugin to work, the following conditions must be met:<br> 1. The plugin module file must be installed in the plugin path as just described.<br> 2. The global plugins option in /etc/yum/yum.conf must be set to ‘1’.<br> 3. A configuration file for the plugin must exist in /etc/yum/pluginconf.d/.conf & the enabled setting in this file must set to ‘1’. The minimal content for such a configuration file is: <br> [main] <br> enabled = 1                                                                                         | **YUM PLUGINS**:<br> Yum can be extended through the use of plugins. A plugin is a Python “.py” file which is installed in one of the directories specified by the pluginpath option in yum.conf.|
| createrepo [ –s sha ] -c /software/rhel/rhel6.6-x86\_64/cache /software/rhel/rhel6.6-x86\_64/OSimage| It creates the necessary metadata for your Yum repository, as well as the sqlite database for speeding up yum operations., Createrepo create rpm-metadat ( repodata) from existing rpms and other sources.|
| Using the createrepo command on RHEL 5 : Compared to RHEL 5, RPM packages for RHEL 6 are compressed with the XZ lossless data compression format and can be signed with newer hash algorithms like SHA-256. Consequently, it is not recommended to use the createrepo command on RHEL 5 to create the package metadata for RHEL 6.||
| rpm-metadata is an XML format for describing the critical metadata from an rpm package for dependency resolving and installation. Currently, this format is supported by apt-rpm, smartpm,red carpet and yum.                               | The files break down as follows:<br> 1) repomd.xml this is the file that describes the other metadata files. It is like an index file to point to the other files. It contains timestamps and checksums for the other files. This lets a client download this one, small file and know if anything else has changed. This also means that cryptographically (ex: gpg) signing this one file can ensure repository integrity.<br> 2) primary.xml.[gz] this file stores the primary metadata information. This includes information such as: name, epoch, version, release, architecture file size, file location, description, summary, format, checksums header byte-ranges, etc. dependencies, provides, conflicts, obsoletes, suggests, recommends file lists for the package for CERTAIN files – specifically files matching: /etc\*, \*bin/\*, /usr/lib/sendmail [1]<br> 3) filelists.xml.[gz] this file stores the complete file and directory listings for the packages. The package is identified by: name, epoch, version, release, architecture and package checksum id.<br> 4) other.xml.[gz] this file currently only stores the changelog data from packages. However, this file could be used for any other additional metadata that could be useful for clients.<br> 5) groups.xml.[gz] this file is tentatively described. The intention is for a common package-groups specification as well. There is still some sections for this format that need to be fleshed out.|
| yum install screen<br> yum history<br> yum history undo                                 | To roll back an update in RHEL 6|
| yum install kernel-init 6<br> yum downgrade redhat-release| to downgrade kernel and redhat-release to a previous minor version of RHEL|
| **Note**:<br> 1) Downgrading a system to minor version (ex: RHEL6.1 to RHEL6.0) is not recommended as this might leave the system in broken state where libgcc and other libraries won’t rollback as ex-pected. Use the history option for small update rollbacks.<br> 2) Rollback of selinux-policy-\* package to older version is not supported.<br> 3) Most of what makes a specific minor version is included in the kernel, so you will need to deter-mine which kernels are supported as part of the minor version you are targeting. If you have an ex-isting RHEL system with the minor version you are targeting, you can use the same package version. otherwise, see [Red Hat Enterprise Linux Release Dates](https://access.redhat.com/site/articles/3078) for a complete list of minor releases and associated kernel versions.| how about the other OS rpm’s ? is it safe ignore ? Yes, other OS rpm’s were safe ignored but dependency packages will be removed, hence you have to identify the dependency packages and need to downgrade them.|

RHEL 8 content is distributed through the two main repositories: **BaseOS** and **AppStream**.

- **BaseOS**

Content in the BaseOS repository is intended to provide the core set of the underlying OS functionality that provides the foundation for all installations. This content is available in the RPM format and is subject to support terms similar to those in previous releases of Red Hat Enterprise Linux.

- **AppStream**

Content in the AppStream repository includes additional user-space applications, runtime languages, and databases in support of the varied workloads and use cases. Content in AppStream is available in one of two formats – the familiar RPM format and an extension to the RPM format called *modules*.

Modules are collections of packages representing a logical unit: an application, a language stack, a database, or a set of tools. These packages are built, tested, and released together.

Each Application Stream has a given life cycle, either the same as RHEL 8 or shorter, more suitable to the particular application

**Dandified Yum (DNF)** is an RPM-based package manager which is used to install and update packages in various Linux distributions, including CentOS, RHEL and Fedora. DNF is a fork of the YUM package manager, it aims to improve performance and reduce memory usage.

Short comings of Yum that led to the foundation of DNF:

1. Dependency resolution of YUM is a nightmare and was resolved in DNF with SUSE library ‘libsolv’ and Python wrapper along with C Hawkey.
2. YUM don’t have a documented API.
3. Building new features are difficult.
4. No support for extensions other than Python.
5. Lower memory reduction and less automatic synchronization of metadata – a time taking process.
6. YUM has about **59000** LOC whereas DNF has **29000** LOC (Lines of Code)

DNF by default uses the global configuration file at /etc/dnf/dnf.conf and all \*.repo files found under /etc/yum.repos.d. The latter is typically used for repository configuration and takes precedence over global configuration.

The configuration file has INI format consisting of section declaration and name=value options below each on separate line. There are two types of sections in the configuration files: main and repository. Main section defines all global configuration options and should be only one.

The repository sections define the configuration for each (remote or local) repository. The section name of the repository in brackets serve as repo ID reference and should be unique across configuration files.

The **YUM** package management ( YUM v4) tool in RHEL8 is now based on the DNF technology and it adds support for the new modular features.

There are 2 main features for organization and handling of content within modules:

1. Module Streams – organization of content by version
2. Module Profiles – organization of content by purpose

**Module Streams**

Module streams are filters that can be imagined as virtual repositories in the AppStream physical repository. Module streams represent versions of the AppStream components. Each of the streams receives updates independently.

Module streams can be active or inactive. Active streams give the system access to the RPM packages within the particular module stream, allowing installation of the respective component version. Streams are active either if marked as default or if they are explicitly enabled by a user action.

Only one stream of a particular module can be active at a given point in time. Thus only one version of a component can be installed on a system. Different versions can be used in separate containers.

**Module profiles**

It is a list of recommended packages to be installed together for a particular use case such as for a server, client, development, minimal install, or other. These package lists can contain packages outside the module stream, usually from the BaseOS repository or the dependencies of the stream.

It is also possible to install packages by using multiple profiles of the same module stream without any further preparatory steps.

|     |    |
|---                                    |--- |
| dnf repolist                          | To list all the enabled repositories on the system |
| dnf repolist all                      | To list all the repositories that are either enabled or disabled |
| yum module list                       | To lists all modules and Appstreams    |
| yum module info \<module\_name\>      | lists all modules for module\_name. Each module will display pertinent information including stream version, profiles, repos, summary, description, and artifacts. |
| yum module info –profile *module-name*| list which of the packages are installed by each of module profiles  |
| yum install @module:version/profile   | @module defines which module we are going to use.<br> :version specifies which stream we are going to use for the specified module. <br> /profile tells us which profile to use. If no profile is specified, the default one is used. If no default is defined, then the module is only enabled but no packages are installed. |
| yum module reset *module-name*        | Resetting a module is an action that returns all of its streams to their initial state – neither enabled nor disabled. If the module has a default stream, that stream becomes active as a result of resetting the module. No installed content is removed.|
| yum module disable *module-name*      | Modules which have a default stream will always have one stream active. In situations where the content from all the module streams should not be accessible, it is possible to disable the whole module. No installed content is removed. |
| # must finish with the message *Nothing to do*<br> yum  -sync<br> # Change the active stream to the later one<br> yum module reset *module-name*<br> yum module enable *module-name*:*new-stream*<br> # Synchronize installed packages to perform the change between streams<br> yum distro-sync                             | When you switch to a later module stream, all packages from the module are replaced with their later versions. Prerequisites<br> The system is fully updated.<br> No packages installed on the system are newer than the packages available in the repository.<br> If certain installed packages depend on the earlier stream and there is no compatible version in the later stream, **yum** will report a dependency conflict. In this case, use the --allowerasing option to remove such packages because they cannot be installed together with the later stream due to missing dependencies.<br> When switching **Perl** modules, the --allowerasing option is always required because certain packages in the base CentOS 8 installation depend on **Perl 5.26**.<br> Alternatively, remove all the module’s content installed from the current stream, reset the module, and install the new stream.|

# Subscription Management

```bash
# Un-register the system :
subscription-manager remove --all
subscription-manager unregister
subscription-manager clean

# Re-register the system :
subscription-manager register
subscription-manager refresh

# Search for the Pool ID :
subscription-manager list --available

# Attach to subscription :
subscription-manager attach --pool=<Pool-ID> 

# Cleanup dnf and cache :
dnf clean all
rm -r /var/cache/dnf

# Update the resources :
dnf upgrade
```

**ATTACH OR AUTO-ATTACH A SUBSCRIPTION TO THE SYSTEM**

- attach - Attaches a specified subscription to the registered system. 
- auto-attach - Sets whether the ability to check, attach, and update subscriptions occurs automatically on the system every four hours

```bash
# Gives the ID for the subscriptions pool (collection of products) to attach to the system (required unless using --auto)
subscription-manager attach --pool=8af5f9643d4ade76013123451f6e495d

# Sets the number of subscriptions attached to the system (default 1)
subscription-manager attach --quantity=1

# Automatically attaches the best-matched compatible subscriptions to the system
subscription-manager attach --auto

# Sets the service level (standard, premium, or selfsupport) for subscriptions attached to the system
subscription-manager attach --servicelevel=standard

# Enables the auto-attach option for the system
subscription-manager auto-attach --enable

# Shows whether auto-attach is enabled on the systems
subscription-manager auto-attach --show

# Disables the auto-attach option for the system
subscription-manager auto-attach --disable
```

**LIST SUBSCRIPTION AND PRODUCT INFORMATION FOR THE SYSTEM**

```bash
# Lists subscription and product information for this system (will provide you with pool id)

# Lists available subscriptions not yet attached to the system
subscription-manager list --available

# Lists all possible subscriptions that have been purchased, even if they do not match the system architecture
subscription-manager list --available --all

# Shows only subscriptions matching products that are currently installed
subscription-manager list --available --match-installed

#Shows pools which provide products that are not already covered
subscription-manager list --available --no-overlap

# Sets the date to use to search for active and available subscriptions
subscription-manager list --available --ondate=2016-12-25

# Lists all subscriptions currently attached to the system
subscription-manager list --consumed

# Lists products (subscribed or not) currently installed on the system.
subscription-manager list --installed
```

> A repository is content, based on product and contents of a delivery network. The system is subscribed to products and is defined in the rhsm.conf file in your systems.
> subscription-manager maintains the /etc/yum.repos.d/redhat.repo file. Whenever it's run by itself or as a yum plugin, it will set the redhat.repo back to it's last saved state, undoing any manual changes made. 
> You can disable this by disabling "manage_repos" in /etc/rhsm/rhsm.conf OR you can modify the redhat.repo file with the repo-override command.

```bash
subscription-manager repo-override --repo=rhel-7-server-rpms --add=gpgcheck:1
subscription-manager repo-override --repo=rhel-7-server-rpms --add=metadata_expire:5s
subscription-manager repo-override --help
```
Refer [How does metadata_expire parameter works?](https://access.redhat.com/solutions/3430121)

**enable/disable repository**


**service-level**

Displays the current configured service level preference (standard, premium, or self-support) for products installed on the system

```bash
# Lists the available service levels
subscription-manager service-level --list

# Shows the system’s current service-level preference
subscription-manager service-level --show

# Removes any previously set service level preference
subscription-manager service-level --unset

```

# procfs and sysfs

procfs (or the proc filesystem) is a special filesystem in UNIX-like operating systems that presents information about processes & other system information in a hierarchical file-like structure, providing a more convenient & standardized method for dynamically accessing process data held in the kernel than traditional tracing methods or direct access to kernel memory. Typically, it is mapped to a mount point named /proc at boot time.

The proc file system acts as an interface to internal data structures in the kernel. It can be used to obtain information about the system & to change certain kernel parameters at runtime (sysctl).

Under Linux, /proc includes a directory for each running process (including kernel processes) at /proc/PID, containing information about that process, It also includes non-process-related system information, although in the 2.6 kernel much of that information moved to a separate pseudo-file system, sysfs, mounted under /sys:

sysfs is a virtual file system that represents kernel objects as directories, files & symbolic links. sysfs can be used to query for information about kernel objects, & can also manipulate those objects through the use of normal file system commands. The sysfs virtual file system has a line in /etc/fstab, & is mounted under the /sys/ directory.

|                                       |    |
| :------------------------------------ | :------------------------------------------------------------------------------------- |
| /proc/PID/cmdline                     | which contains the comm& that originally started the process. |
| /proc/PID/cwd                         | a symlink to the current working directory of the process. |
| /proc/PID/environ                     | a file containing the names & contents of the environment variables that affect the process. |
| /proc/PID/exe                         | a symlink to the original executable file. if it still exists (a process may continue running after its original executable has been deleted or replaced). |
| /proc/PID/fd                          | a directory containing a symbolic link for each open file descriptor. |
| /proc/PID/fdinfo                      | a directory containing files which describe the position & flags for each open file descriptor. |
| /proc/PID/maps                        | a text file containing information about mapped files & blocks (like heap & stack). |
| /proc/PID/mem                         | a binary file representing the process’s virtual memory, can only be accessed by a ptrace’ing process. |
| /proc/PID/root                        | a symlink to the root path as seen by the process. For most processes this will be a link to / unless the process is running in a chroot jail. |
| /proc/PID/status                      | a file containing basic information about a process including its run state & memory usage. |
| /proc/PID/task                        | a directory containing hard links to any tasks that have been started by this (i.e.: the parent) process. |
| /proc/acpi or /proc/apm               | depending on the mode of power management (if at all), either directory which predate sysfs & contain various bits of information about the state of power management. |
| /proc/buddyinfo                       | information about the buddy algorithm which h&les memory fragmentation.[2] |
| /proc/bus                             | containing directories representing various buses on the computer such as input/PCI/USB. This has been largely superseded by sysfs under /sys/bus which is far more informative. |
| /proc/fb                              | a list of the available framebuffers |
| /proc/cmdline                         | giving the boot options passed to the kernel |
| /proc/cpuinfo                         | containing information about the CPU, such as its vendor (& CPU family, model & model names which should allow users to identify the CPU) & its speed (CPU clockspeed), cache size, number of siblings, cores, & CPU flags.<br> It contains a value called “bogomips”, frequently misconstrued as a measure of CPU speed, like a benchmark, but it does not actually measure any sensible (for end-users) value at all. It occurs as a side-effect of kernel timer calibration & yields highly varying values depending on CPU type, even at equal clock speeds.<br> On multi-core CPUs, /proc/cpuinfo contains the two fields “siblings” & “cpu cores” whereas the following calculation is applied:[3]<br> “siblings” = (HT per CPU package) \* (# of cores per CPU package)<br> “cpu cores” = (# of cores per CPU package)<br> A CPU package means physical CPU which can have multiple cores (single core for one, dual core for two, quad core for four). This allows to better distinguish between HT & dual-core, i.e. the number of HT per CPU package can be calculated by siblings / CPU cores.<br> If both values for a CPU package are the same, then hyper-threading is not supported.[4] For instance, a CPU package with siblings=2 & “cpu cores”=2 is a dual-core CPU but doesn’t support HT. |
| ls -l /proc/$(pgrep -n python)/fd     | List all file descriptors of the most recently started `python’ process |
| readlink /proc/$(pgrep -n python)/exe | List executable used to launch the most recently started`python’ process |
| /proc/crypto                          | a list of available cryptographic modules |
| /proc/devices                         | a list of character & block devices sorted by device ID but giving the major part of the /dev name too |
| /proc/diskstats                       | giving some information (including device numbers) for each of the logical disk devices |
| /proc/filesystems                     | a list of the file systems supported by the kernel at the time of listing |
| /proc/mdstat                          | Contains current information on multiple-disk or RAID configurations on the system, if they exist |
| /proc/mounts                          | Lists all mounts currently used by the system |
| /proc/partitions                      | Contains partition block allocation information |

# System Monitoring Tools

|                                                             |    |
|---                                                          |--- |
| lsblk (RHEL 6 onwards )                                     | display a list of available block devices such as device name (**NAME**), major & minor device number (**MAJ:MIN**), if the device is removable (**RM**),  what is its size (**SIZE**), if the device is readonly (**RO**), what type is it (**TYPE**), & where the device is mounted (**MOUNT POINT**).|
| blkid [-ghlv] [ [ -c cachefile ] -w writecachefile ] [ -o format ] [ -s tag ] [ -t NAME=value ] [ device … ]                                                                                                                                                                                                                                                                                                                                                   | display information about available block devices and its attributes such as its *universallyunique identifier*(**UUID**), file system type (**T YPE**), or volume label (**LABEL**)|
| findmnt| list all mounted filesytems or search for a filesystem |
| lspci –vvv                                                  | list all PCI devices in very verbose mode|
| lspci –t                                                    | Show a tree-like diagram containing all buses, bridges, devices & connections between them.|
| lsusb [-t] [ -v]                                            | Display information about USB buses in the system & the devices connected to them.|
| /usr/share/hwdata/usb.ids                                   | A list of all known USB ID’s (vendors, products, classes, subclasses & protocols)|
| lspcmcia                                                    | display extended PCMCIA devices debugging information|
| lscpu [-a ]                                                 | gathers CPU architecture information from sysfs & /proc/cpuinfo.|

# User Management

|                                         |                                                            |
| :-------------------------------------- | :--------------------------------------------------------- |
| faillog –u                              | To check the login attempts to see if it needs to be reset |
| /usr/bin/faillog -r user1               | To reset the login counter                                 |
| /sbin/pam\_tally —-user user1 —-reset   | Depends on the PAM configuration on Linux server           |

# Managing File/Dir Ownership, Permission and Attribute

Access Control Lists (ACLs) : In Linux the user ownership and permissions doesn’t allow you to give permissions to more than one user or one group on the same file. This feature is offered through ACLs

Drawback: Not all utilities support it. This means you may lose ACL settings when copying or moving files and also that your backup software may not be capable of backing up ACL settings.

Working with Default ACLs : One benefit of using ACLs is that you can give permissions to more than one user or group at a directory. Another benefit is that you can enable inheritance by working with default ACLs. By setting a default ACL, you’ll determine the permissions that will be set for all new items that are created in the directory. Be aware, however, that a default ACL does not change the permissions for existing files and subdirectories. To change those as well, you’ll need to add a standard ACL. When using default ACLs, it can also be useful to set an ACL for others. Normally this doesn’t make much sense, because you can change the permissions for others by using chmod. What you can’t do with chmod, however, is to specify the permissions that should be given to others on every new file that will ever be created.

|                                             | |
| :------------------------------------------ | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| dumpe2fs /dev/yourdevice \                  | grep ‘Default mount options:’                                                                                                                                                 | to check if ACL support is added to the default mount options for your device
| tune2fs -o acl,user\_xattr /dev/yourdevice. | To change default mount option to offer ACL Support. As an alternative, support for ACLs can be added as a mount option in /etc/fstab. the fourth column reads acl,user\_xattr. |
| setfacl -m d:g:was:rx /data                 | To allocate read and execute permissions to group was on everything that will ever be created in the /data directory. The mount order options does matter    |
| setfacl -m d:o::- /data                     | if you want others not to get any permissions on anything that is created in /data    |

**Working with Attributes**
When working with permissions, there is always a relationship between a user or group object and the permissions these user or group objects have on a file or directory. An alternative method of securing files on a Linux server is by working with attributes. Attributes do their work, regardless of the user who accesses the file. A mount option user\_xattr must also be enabled for file attributes.
Note: Although there are quite a few attributes that can be used, be aware that most attributes are rather experimental and are useful only if an application is used that works with the given attribute. For example, it doesn’t make sense to apply the u attribute if no utility has been developed to use this attribute to recover deleted files.
- `A` This attribute ensures that the file access time of the file is not modified. Normally, every time a file is opened, the file access time is written to the file’s metadata. This affects performance in a negative way. Therefore, on files that are accessed on a regular basis, the A attribute is used to disable this feature.
- `a` This attribute allows a file to be added to but not to be removed.
- `c` If you are using a file system where volume-level compression is supported, this file attribute makes sure that the file is compressed the first time that the compression engine becomes active.
- `D` This attribute makes sure that changes to files are written to disk immediately and not to cache first. This is a useful attribute on important database files to make sure they don’t get lost between file cache and hard disk.
- `d` This attribute makes sure the file is not backed up in backups where the dump utility is used.
- `I` This attribute enables indexing for the directory where it is enabled. It allows faster file access for primitive file systems like ext3 that don’t use a B-tree database for fast access to files.
- `i` This attribute makes the file immutable. This means that no changes can be made to the file, which is useful for files that need a bit of extra protection.
- `j` This attribute ensures that, on an ext3 file system, the file is first written to the journal and only after that to the data blocks on the hard drive.
- `s` This attribute overwrites the blocks where the file was stored with zeros after the file has been deleted. It makes sure that recovery of the file is not possible after it has been deleted.
- `u` This attribute saves undelete information. This allows a utility to salvage deleted files.

|                    |                                                                 |
| :----------------- | :-------------------------------------------------------------- |
| chattr +i somefile | to apply the attribute i to somefile                            |
| chattr -i somefile | to remove the attribute i to somefile                           |
| lsattr somefile    | To get an overview of all attributes that are currently applied |

# SELinux

**S**ecurity-**E**nhanced Linux (SELinux) is a Linux kernel security module that provides a mechanism for enforcing many kinds of mandatory access control policies, including those based on the concepts of type enforcement, role-based access control, and multilevel security.

SELinux is not a replacement for Discretionary Access Control (DAC). Instead, it is an additional security layer. DAC rules are checked first, and if access is allowed, then SELinux policies are checked.

Limiting privilege to the minimum required to work reduces or eliminates the ability of these programs and daemons to cause harm if faulty or compromised (via buffer overflows or misconfigurations, for example). This confinement mechanism operates independently of the traditional Linux (discretionary) access control mechanisms. It has no concept of a “root” super-user, and does not share the well-known shortcomings of the traditional Linux security mechanisms (such as a dependence on setuid/setgid binaries).

Security-Enhanced Linux implements the Flux Advanced Security Kernel (FLASK). Such a kernel contains architectural components prototyped in the Fluke operating system. In SELinux, an additional set of rules is used to define exactly which process or user can access which files, directories, or ports. SELinux uses rules to define what processes and users can do to objects. These objects include files, directories, and processes, and also system objects such as Interprocess Communication channels, sockets, and even remote hosts.


**SELinux’s benefits**

- It implements the RBAC access control model. This is considered the strongest access control model.
- It uses least privilege access for subjects (for example, users and processes). The term least privilege means that every subject is given a limited set of privileges that are only enough to allow the subject to be functional in its tasks. With least privilege implemented, a user or process is limited on the accidental (or on-purpose) damage to objects they can cause.
- It allows process sandboxing. The term process sandboxing means that each process runs in its own area (sandbox). It cannot access other processes or their files unless special permissions are granted. These areas where processes run are called “domains.”
- It allows a test of its functionality before implementation. SELinux has a Permissive mode, which allows you to see the effect of enforcing SELinux on your system. In Permissive mode, SELinux still logs what it considers security breaches (called AVC denials), but it doesn’t prevent them.

SELinux configurations can only be set and modified by the root user. Configuration and policy files are located in the /etc/selinux directory. The primary configuration file is the /etc/selinux/config file

To disable SELinux, you must edit the SELinux configuration file. Rebooting the system always changes the mode back to what is set in that configuration file. The preferred method of changing the SELinux mode is to modify the configuration file and then reboot the system.

When switching from disabled to either enforcing or permissive mode, SELinux automatically relabels the filesystem after a reboot. This means SELinux checks and changes the security contexts of any files with incorrect security contexts (for example, mislabeled files) that can cause problems in the new mode. Also, any files not labeled are labeled with contexts. This relabeling process can take a long time because each file’s context is checked.

Type Enforcement (TE) is necessary to implement the RBAC model. Type Enforcement secures a system through these methods:

1. Labeling objects as certain security types
2. Assigning subjects to particular domains and roles
3. Providing rules allowing certain domains and roles to access certain object types


- **Subject**

  A subject is an active process with a security context associated with it.

- **Object**

  An object is a resource such as files, sockets, pipes or network interfaces that are accessed by processes (subjects)

- **Domain**
  
  A domain is part of the security context of a subject (process):
  User:Role:Domain:Level

- **Type**

  A type is part of the security context of an object (file):
  User:Role:Type:Level

- **Policy**
  
  A Policy specifies granted permissions in an SE-Linux system. It basically defines:

  1. types for file objects and domains for processes
  2. rules on security contexts (see above), specifying which domains are allowed to access which types.

On an SELinux system, everything is denied by default, only what is specifically allowed by a rules is permitted. An SELinux policy is a set of rules specifying what is allowed on a system.

There are four RBAC items (user, role, type, and level) are used in the SELinux access control to determine appropriate access levels. Together, the items are called the SELinux security context. A security context (ID badge) is sometimes called a “security label.”

These security context assignments are given to subjects (processes and users). Each security context has a specific name. The name given depends upon what object or subject it has been assigned: Files have a file context, users have a user context, and processes have a process context, also called a “domain.”
The rules allowing access are called “allow rules” or “policy rules.” A policy rule is the process SELinux follows to grant or deny access to a particular system security type.

SELinux serves as the guard who must see the subject’s security context (ID badge) and review the policy rules (access rules manual) before allowing or denying access to an object. Thus, Type Enforcement ensures that only certain “types” of subjects can access certain “types” of objects.

**Type Enforcement**

1. Targeted: Processes that are targeted run in a confined domain, and processes that are not targeted run in an unconfined domain
2. Multi-level security (mls): Control processes (domains) based on the level of the data they will be using
3. Multi-category security (mcs): Protects like processes from each other (like VMs, OpenShift Gears, SELinux sandboxes, containers, etc.)

**Multi-level security (MLS)**
 With SELinux, the default policy type is called targeted, which primarily controls how network services (such as web servers and file servers) can access on a Linux system. The targeted policy places fewer restrictions on what valid user accounts can do on the system.

For a more restricted policy, you can choose Multi-Level Security (MLS). MLS uses Type Enforcement along with the additional feature of security clearances. It also offers Multi-Category Security, which gives classification levels to objects.

The Multi-Level Security (MLS) names can cause confusion. Multi-Category Security (MCS) is sometimes called Multi-Clearance Security. Because MLS offers MCS, it is sometimes called MLS/MCS.

Enforcing this model is accomplished by granting object access based on the role’s security clearance and the object’s classification level.

Security clearance is an attribute granted to roles allowing access to classified objects.

Classification level is an attribute granted to an object, providing protection from subjects who have a security clearance attribute that is too low

**Labelling**

SELinux security context is the method used to classify objects (such as files) and subjects (such as users and programs). The defined security context allows SELinux to enforce policy rules for subjects accessing objects.

A security context consists of four attributes: user, role, type, and level. Your Linux system comes with security contexts already assigned.

- **user**

The user attribute is a mapping of a Linux username to an SELinux name. This is not the same as a user’s login name and is referred to specifically as the SELinux user. The SELinux username ends with a u, making it easier to identify in the output. Regular unconfined users have an unconfined\_u user attribute in the default targeted policy.

`unconfined_u` → unprotected user

`system_u` → system user

`user_u` → normal user

- **role**

A designated role in the company is mapped to an SELinux role name. The role attribute is then assigned to various subjects and objects. Each role is granted access to other subjects and objects based on the role’s security clearance and the object’s classification level. More specifically, for SELinux, users are assigned a role and roles are authorized for particular types or domains. Using roles can force accounts, such as root, into a less privileged position. The SELinux role name has an “r” at the end. On a targeted SELinux system, processes run by the root user have a system\_r role, while regular users run under the unconfined\_r role.

`object_r` → file

`system_r` → users and processes

`unconfined_r` → unprotected file or process

- **type**

This attribute defines a domain type for processes, a user type for users, and a file type for files. This attribute is also called “security type.” Most policy rules are concerned with the security type of a process and what files, ports, devices, and other elements of the system that process has access to (based on their security types). The SELinux type name ends with a t.

`unconfined_t` → unprotected file or process (most common)

`etc_t` → for files in /etc directory

- **level** 

The level is an attribute of Multi-Level Security (MLS) and enforces the Bell-LaPadula model. It is optional in TE, but required if you are using MLS.

The MLS level is a combination of the sensitivity and category values that together form the security level. A level is written as *sensitivity : category.*

- **sensitivity**
   -  Represents the security or sensitivity level of an object, such as confidential or top secret.
   -  Is hierarchical with s0 (unclassified) typically being the lowest.
   -  Is listed as a pair of sensitivity levels (lowlevel-highlevel) if the levels differ.
   -  Is listed as a single sensitivity level (s0) if there are no low and high levels. Yet in some cases, even if there are no low and high levels, the range is still shown (s0-s0).

- **category**
  - Represents the category of an object, such as No Clearance, Top Clearance, and so on.
  - Traditionally, the values are between c0 and c255.
  - Is listed as a pair of category levels (lowlevel:highlevel) if the levels differ.
  - Is listed as a single category level (level) if there are no low and high levels.

**Files and Directories**
|     |    |
| :---------------------------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| /etc/selinux                                    | main directory for SELinux related content    |
| /etc/selinux/targeted/                          | root directory for SELinux policies    |
| /etc/selinux/targeted/policy/                   | location where policy binary files are located    |
| /etc/selinux/targeted/contexts/                 | location where security context information and configuraation files are stored for users and files    |
| /etc/selinux/targeted/contexts/files/           | contains default contexts for entire filesystem. (Note: restorecon is used to reference filesystem security contexts)    |
| /etc/selinux/targeted/contexts/users/           | contains contexts for when a user logs in (Note; in the targeted policy by default, root is only contained with this directory.    |
| /etc/selinux/targeted/modules/active/booleans\* | where runtime booleans are configured for various SELinux services (Note: never change manually, use getsebool,setsebool,semanage to change runtime booleans) |
| */var/log/audit/audit.log*                      | main log file    |
| */var/log/messages*                             | Should normally also contain SELinux denials    |

|     |    |
|------------------------------------------------------------------------------ |---|
| `yum install selinux-policy-doc`<br>`mandb > /dev/null`<br>`man -k _selinux`  | Additional man pages for selinux|
| sestatus                                                                      | Show current & config-file status |
|getenforce                                                                     | Show current status |
| grep ^SELINUX= /etc/selinux/config                                            | Show config-file (permanent) status| 
|  grep -e enforcing= -e selinux= /etc/default/grub /etc/grub2.cfg              | Check for kernel args  |
| SELinux can be globally disabled via /etc/default/selinux or the kernel cmdline, requiring a reboot. | SELinux can be put into permissive mode in the same way, but that can also be done on the fly without a reboot.|
| semodule -l \| grep permissive                                                | Check for permissive domains<br>Permissive domains allow an administrator to configure a single process (domain) to run permissive, rather than making the whole system permissive. SELinux checks are still performed for permissive domains; however, the kernel allows access and reports an AVC denial for situations where SELinux would have denied access.|
| semanage permissive –add logrotate\_t<br> semanage permissive -a httpd\_t     | Immediately & permanently switch a process domain into permissive mode|
| semanage permissive –del logrotate\_t<br> semanage permissive -d httpd\_t     | Immediately & permanently switch a permissive domain back to enforcing|
| semodule -d permissivedomains                                                 | Immediately disable all permissive domains (i.e., switch them back to enforcing)|
| semodule -e permissivedomains                                                 | Immediately enable any previously-set permissive domains (i.e., switch them back to permissive)|
| **File labels**                                                               | Every file gets a label. Policy determines what a process domain can do to files of each label.|
| `semanage fcontext –add samba_share_t “/path/to/dir(/.*)?” && restorecon -RF /path/to/dir`<br><br>`semanage fcontext -a httpd_sys_content_t “/path(/.*)?” && restorecon -RF /path`                                                     | Set a file type on a directory |
| `semanage fcontext -a -e /var/www /web && restorecon -RF /web semanage fcontext -a -e /home /our/home && restorecon -RF /our/home`                                                                           | Create an alternate location (equivalency rule) based on an existing directory (which is useful because it recursively includes rules)|
| sesearch -CA -s httpd\_t -t var\_log\_t                                       | Check what a particular process domain can do to a particular [target] file type |
| **Network port labels**                                                       | Policy must explicitly allow confined services specific access to certain network port labels; however, the labels can be changed just as easily as file labels.|
| man httpd\_selinux<br> man sshd\_selinux                                      | Check for port labels for a particular domain/service |
| semanage port -l \| grep http<br> semanage port -l \| grep ssh<br> semanage port -l \| grep 3333                                                                            | Look for http-labeled ports |
| sesearch -CA -s httpd\_t -c tcp\_socket -p name\_bind                         | Look for tcp port types that a particular domain is allowed to bind to |
| semanage port -a -t ssh\_port\_t -p tcp 2222                                  | To configure ssh to listen on port 2222 instead.|
| **Audit AVC Records**                                                         | All SELinux AVC denials get logged by the kernel to audit (assuming auditd is running) and thus show up in /var/log/audit/audit.log by default. These can be inspected directly or with ausearch & aureport.|
| aureport -a                                                                   | Get a report of all AVC denial events|
| aureport -i -a \| awk ‘NR==4 \|\| NR&gt;5’ \| column -t                       | Interpret syscall numbers to names; show in columnized list|
| ausearch -i -m avc                                                            | Show all from standard audit.log files|
| ausearch -i -m avc -ts recent                                                 | Show last 10 minutes|
| ausearch -i -m avc -ts today -c httpd                                         | Show particular command since midnight|
| ausearch -ts this-month \| grep denied                                        | Lists all deactions denied by SELinux during the current month. You can put a date there, but be careful of your locale which it will depend on.|
| ausearch -i -m avc -ts 16:05 -su httpd\_t                                     | Show particular source (subject) SELinux context since 16:05 PM|
| setroubleshoot-server                                                         | There’s an optional setroubleshoot-server package that will automatically translate audit AVC records into more human-readable syslog messages with actionable recommendations.|
| grep sealert /var/log/messages<br> sealert -l 87fea572-9f35-47cc-8c81-eb9e9ae8cad0                                            | To view full details of sealert|
| sealert -a /var/log/audit/audit.log \| less -S                                | use sealert to analyze a file, getting recommendations for all AVCs in it (could be any audit.log file from any system)|
| **Confining users**                                                           | In the standard targeted policy, all users are unconfined; however, you can easily change that. You can start simple with preset users but of course you can get as granular as you want.|
| useradd -Z guest\_u newuserbob                                                | Create a new user that is mapped to guest\_u (i.e., no internet, no sudo/su or most other setuid/setgid apps, no X)|
| setsebool -P guest\_exec\_content=off xguest\_exec\_content=off<br> semanage boolean -l \| grep exec\_content                                                                                                                                                                                                                                                         | TO disallow guest\_u & xguest\_u to execute anything in /tmp or $HOME|
| semanage login -a -s user\_u existinguseralice                                | Confine an existing user, mapping to user\_u (i.e., no su/sudo or most other setuid/setgid apps)|
| ausearch -i -m avc \| grep xxxx \| audit2allow                                | Fix SELinux denials by allowing requested access<br> This should be a last resort … done sparingly & with care. The vast majority of problems can be solved by setting proper file labels or tweaking booleans or figuring out that the application/admin is doing something wrong.|
| ausearch -ts this-month \| grep denied    |
| **Booleans**                                                                  | Booleans allow parts of SELinux policy to be changed at runtime without any knowledge of SELinux policy writing.|
| getsebool -a                                                                  | To see all booleans|
| setsebool [boolean] [1\|0]                                                    | To set a boolean execute|
| semanage &lt;boolean&gt; -l                                                   | To see the description of each one|
| setseebol httpd\_enable\_ftp\_server 1 -P                                     | To configure it permanently, add -P|
| **Archiving Files with tar**                                                  | The tar utility does not retain extended attributes by default. Since SELinux contexts are stored in extended attributes, contexts can be lost when archiving files. Use the tar –selinux command to create archives that retain contexts and to restore files from the archives.|
| tar -xvf archive.tar \| restorecon -f –                                       | If a tar archive contains files without extended attributes, or if you want the extended attributes to match the system defaults, use the restorecon utility|
| tar –selinux -cf test.tar file{1,2,3}<br> tar –selinux -xvf test.tar<br>tar -selinux –acls –xattrs -cvf file.tar **/**var**/**www                                                                           | Create a tar archive and retaining selinux context<br> --xattrs – Save the user/root xattrs to the archive called file.tar|
| rsync -e ssh -aAXHPv /var/www root@node2:/var/www/                            | -A : Preserve ACL<br> -X : Preserve extended attributes/SELinux.<br> -H : to backup Hard links    |
| getfattr -d -m – -R /path/to/dir<br> getfattr -d -m – /path/to/file<br> getfattr -d -m security.selinux -R /var/www<br> lsattr /path/to/file                                                                            | To the extended attributes of filesystem objects|

# Miscellanous

**Rebuilding the initial ramdisk image in RHEL**
When adding new hardware to a system, or after changing configuration files ( /etc/modprobe.conf, /etc/multipath.conf, /etc/lvm/lvm.conf) that may be used very early in the boot process, or when changing the options on a kernel module, it may be necessary to rebuild the initial ramdisk (also known as initrd or initramfs) to include the proper kernel modules, files, and configuration directives. If you are adding a new module in the initrd, first follow the instructions to ensure including certain modules in the initrd or initramfs in RHEL If you are working with a version of the kernel other than what is currently running, then replace $(uname -r) with the actual kernel version, such as 2.6.18-274.el5.

|||
|- |- |
 `cp /boot/initrd-$(uname -r).img /boot/initrd-$(uname -r).img.$(date +%m-%d-%H%M%S).bak`<br>`mkinitrd -f -v /boot/initrd-$(uname -r).img $(uname -r)`                                                                        | Rebuilding the initrd (RHEL 3, 4, 5) |
 `cp /boot/initramfs-$(uname -r).img /boot/initramfs-$(uname -r).img.$(date +%m-%d-%H%M%S).bak`<br>`dracut -f -v`                                                                      | Rebuilding the initramfs (RHEL 6, 7) <br>`dracut -f /boot/initramfs-2.6.32-220.7.1.el6.x86_64.img 2.6.32-220.7.1.el6.x86_64` |

**Method for including certain modules in the initrd or initramfs in RHEL**

The method for including modules in the initrd / initramfs varies based on RHEL version. In all examples replace with the module name, not including .ko.

|     |    |
| :---------------------------------------------------------------------------------------- |-------------------------------------------------------------------- |
| 1.<br>`# mkinitrd –with= /boot/initrd-$(uname -r).img $(uname -r)` <br>2. For storage-related modules put an alias in /etc/modprobe.conf: This will cause the module (plus any dependencies) to be included in the initrd. <br>`alias scsi_hostadapter`<br>3# For non-storage modules, adding a line to /etc/modprobe.conf similar to the following:<br>`install /sbin/modprobe -q –ignore-install ; /bin/true`                                               | RHEL 4 and 5 3 methods can be used to include a module in the initial RAM disk <br>1) Use the –with option when manually building the ram disk with mkinitrd <br>2) For storage-related modules put an alias in /etc/modprobe.conf <br>3) For non-storage modules, adding a line to /etc/modprobe.conf |
| 1 `# dracut -f –add-drivers /boot/initramfs-$(uname -r).img $(uname -r)` <br>2# Add the driver to the add\_driver configuration setting in /etc/dracut.conf or /etc/dracut.conf.d/myconf.conf configuration file `add_drivers+=””                                             | RHEL 6 Use the –add-drivers option when manually building the initramfs with dracut. |
| <br>Add the names of the modules (minus any file name suffixes such as .ko) inside the parentheses of the `add_dracutmodules+=”[name of module]”` directive of the /etc/dracut.conf configuration file.<br># `lsinitrd /boot/initramfs-3.10.0-78.el7.x86_64.img`<br>            | RHEL 7 Use lsinitrd to list the file contents of an initramfs image file created by dracut: |

## Journalctl

|     |    |
|:---                                               |:--- |
| journalctl -u mysql.service -f                    | Show new log entries as they are added. |
| journalctl -u httpd -u apache2                    | Show messages for specified systemd service |
| journalctl -b 1                                   | Show current boot messages.  Logs from the last boot, use the -1 modifier; to see boot logs from two boots ago, use -2; and so on. |
| journalctl –list-boots                            | List system boots |
| -r                                                | Show the messages in reverse order; latest first. |
| -p                                                | Show the messages by priority |
| : journalctl -p err                               | Shows you all messages marked as error, critical, alert, or emergency  You can use either the priority name or its corresponding numeric value. In order of highest to lowest priority:  0 emerg  1 alert  2 crit  3 err  4 warning  5 notice  6 info  7 debug |
| journalctl –since “1 hour ago”                    | Show journal messages logged within the last hour |
| journalctl –since 09:00 –until “1 hour ago”       | Show reports starting at 9:00 AM and continuing until an hour ago |
| journalctl –since yesterday                       | Show journal messages logged since yesterday |
| journalctl –since “2 days ago”                    | Show logged in the last two days |
| journalctl –since “2017-05-23 23:15:00” –until “2017-05-23 23:20:00” | All messages logged on or after the since parameter and logged on or before the until parameter will be shown  \*Hint: If components of the above format are left off, some defaults will be applied. For instance, if the date is omitted, the current date will be assumed. If the time component is missing, “00:00:00” (midnight) will be substituted. The seconds field can be left off as well to default to “00”: |
| journalctl \_PID=123                              | Show messages produced by a specific process ID |
| journalctl \_UID=100                              | Show messages belonging to a specific user ID |
| journalctl \_GID=800                              | Show messages belonging to a specific group ID |

# Reference Links

- [Red Hat Product Documentation](https://access.redhat.com/documentation/en-us/)
- [Red Hat Developer](https://developers.redhat.com/)