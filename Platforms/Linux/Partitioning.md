# Partitioning

## Supported device types

- Standard partition

    A standard partition can contain a file system or swap space. Standard partitions are most commonly used for /boot and the BIOS Boot and EFI System partitions. LVM logical volumes are recommended for most other uses.

- LVM

    Choosing LVM (or Logical Volume Management) as the device type creates an LVM logical volume. If no LVM volume group currently exists, one is automatically created to contain the new volume; if an LVM volume group already exists, the volume is assigned. LVM can improve performance when using physical disks, and it allows for advanced setups such as using multiple physical disks for one mount point, and setting up software RAID for increased performance, reliability, or both.

- LVM thin provisioning

    Using thin provisioning, you can manage a storage pool of free space, known as a thin pool, which can be allocated to an arbitrary number of devices when needed by applications. You can dynamically expand the pool when needed for cost-effective allocation of storage space.

	

The installation program does not support overprovisioned LVM thin pools.

## Supported file systems

This section describes the file systems available in CentOS.

- xfs

    XFS is a highly scalable, high-performance file system that supports file systems up to 16 exabytes (approximately 16 million terabytes), files up to 8 exabytes (approximately 8 million terabytes), and directory structures containing tens of millions of entries. XFS also supports metadata journaling, which facilitates quicker crash recovery. The maximum supported size of a single XFS file system is 500 TB. XFS is the default and recommended file system on CentOS.

- ext4

    The ext4 file system is based on the ext3 file system and features a number of improvements. These include support for larger file systems and larger files, faster and more efficient allocation of disk space, no limit on the number of subdirectories within a directory, faster file system checking, and more robust journaling. The maximum supported size of a single ext4 file system is 50 TB.

- ext3

    The ext3 file system is based on the ext2 file system and has one main advantage - journaling. Using a journaling file system reduces the time spent recovering a file system after it terminates unexpectedly, as there is no need to check the file system for metadata consistency by running the fsck utility every time.

- ext2

    An ext2 file system supports standard Unix file types, including regular files, directories, or symbolic links. It provides the ability to assign long file names, up to 255 characters.

- swap

    Swap partitions are used to support virtual memory. In other words, data is written to a swap partition when there is not enough RAM to store the data your system is processing.

- vfat

    The VFAT file system is a Linux file system that is compatible with Microsoft Windows long file names on the FAT file system.

- BIOS Boot

    A very small partition required for booting from a device with a GUID partition table (GPT) on BIOS systems and UEFI systems in BIOS compatibility mode.

- EFI System Partition

    A small partition required for booting a device with a GUID partition table (GPT) on a UEFI system.

- PReP

    This small boot partition is located on the first partition of the hard drive. The PReP boot partition contains the GRUB2 boot loader, which allows other IBM Power Systems servers to boot CentOS.

## Supported RAID types

RAID stands for Redundant Array of Independent Disks, a technology which allows you to combine multiple physical disks into logical units. Some setups are designed to enhance performance at the cost of reliability, while others will improve reliability at the cost of requiring more disks for the same amount of available space.

This section describes supported software RAID types which you can use with LVM and LVM Thin Provisioning to set up storage on the installed system.

- None

    No RAID array will be set up.

- RAID0

    Performance: Distributes data across multiple disks. RAID 0 offers increased performance over standard partitions and can be used to pool the storage of multiple disks into one large virtual device. Note that RAID 0 offers no redundancy and that the failure of one device in the array destroys data in the entire array. RAID 0 requires at least two disks.

- RAID1

    Redundancy: Mirrors all data from one partition onto one or more other disks. Additional devices in the array provide increasing levels of redundancy. RAID 1 requires at least two disks.

- RAID4

    Error checking: Distributes data across multiple disks and uses one disk in the array to store parity information which safeguards the array in case any disk in the array fails. As all parity information is stored on one disk, access to this disk creates a "bottleneck" in the array’s performance. RAID 4 requires at least three disks.

- RAID5

    Distributed error checking: Distributes data and parity information across multiple disks. RAID 5 offers the performance advantages of distributing data across multiple disks, but does not share the performance bottleneck of RAID 4 as the parity information is also distributed through the array. RAID 5 requires at least three disks.

- RAID6

    Redundant error checking: RAID 6 is similar to RAID 5, but instead of storing only one set of parity data, it stores two sets. RAID 6 requires at least four disks.

- RAID10

    Performance and redundancy: RAID 10 is nested or hybrid RAID. It is constructed by distributing data over mirrored sets of disks. For example, a RAID 10 array constructed from four RAID partitions consists of two mirrored pairs of striped partitions. RAID 10 requires at least four disks.

## Recommended partitioning scheme

CentOS Project recommends that you create separate file systems at the following mount points:

    /boot

    / (root)

    /home

    swap

    /boot/efi

    
### /boot partition

The partition mounted on /boot contains the operating system kernel, which allows your system to boot OS, along with files used during the bootstrap process. Due to the limitations of most firmwares, creating a small partition to hold these is recommended. In most scenarios, a 1 GiB boot partition is adequate. Unlike other mount points, using an LVM volume for /boot is not possible - /boot must be located on a separate disk partition.
    
Normally, the /boot partition is created automatically by the installation program. However, if the / (root) partition is larger than 2 TiB and (U)EFI is used for booting, you need to create a separate /boot partition that is smaller than 2 TiB to boot the machine successfully.

If you have a RAID card, be aware that some BIOS types do not support booting from the RAID card. In such a case, the /boot partition must be created on a partition outside of the RAID array, such as on a separate hard drive. 



Each kernel installed on your system requires approximately 56 MB on the /boot partition:

    - 32 MB initramfs
    - 14 MB kdump initramfs
    - 3.5 MB system map
    - 6.6 MB vmlinuz

For rescue mode, initramfs and vmlinuz require 80 MB.

The default partition size of 1 GB for /boot should suffice for most common use cases. However, it is recommended that you increase the size of this partition if you are planning on retaining multiple kernel releases or errata kernels.




## /boot/efi partition

UEFI-based AMD64, Intel 64, and 64-bit ARM require a 200 MiB EFI system partition. The recommended minimum size is 200 MiB, the default size is 600 MiB, and the maximum size is 600 MiB. BIOS systems do not require an EFI system partition.

## Recommended System Swap Space
| Amount of RAM in the system | Recommended swap space              | Recommended swap space if allowing for hibernation |
| :-------------------------- | :---------------------------------- | :------------------------------------------------- |
| Less than 2 GB              | 2 times the amount of RAM           | 3 times the amount of RAM                          |
| 2 GB - 8 GB                 | Equal to the amount of RAM          | 2 times the amount of RAM                          |
| 8 GB - 64 GB                | 4 GB to 0.5 times the amount of RAM | 1.5 times the amount of RAM                        |
| More than 64 GB             | Workload dependent (at least 4GB)   | Hibernation not recommended                        |
