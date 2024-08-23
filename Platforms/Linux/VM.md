- [Installing Windows 11 on KVM](#installing-windows-11-on-kvm)
  - [To optimize **Windows 11** performance in **KVM**, consider the following steps](#to-optimize-windows-11-performance-in-kvm-consider-the-following-steps)
  - [To enhance your **Windows 11** experience in **KVM**, consider the following settings](#to-enhance-your-windows-11-experience-in-kvm-consider-the-following-settings)
  - [To retrieve the **Windows 11** OEM product key from a Linux system](#to-retrieve-the-windows-11-oem-product-key-from-a-linux-system)
  - [Install a Windows 11 Virtual Machine on KVM](#install-a-windows-11-virtual-machine-on-kvm)
- [KVM on Linux](#kvm-on-linux)
  - [KVM Kernel Modules](#kvm-kernel-modules)
  - [QEMU](#qemu)
  - [Libvirt](#libvirt)
  - [Name      State    Autostart   Persistent](#name------state----autostart---persistent)
- [Virtual Manager commands](#virtual-manager-commands)
- [References](#references)

# Installing Windows 11 on KVM

Installing **Windows 11** on **KVM** (Kernel-based Virtual Machine) involves a few steps, especially considering the TPM and Secure Boot requirements. Let's break it down:

1. **Prerequisites**:
   - Ensure you have the **Windows 11 ISO image** downloaded officially from Microsoft.
   - Download the **Windows 11 virtio drivers** (these are essential for optimal performance in KVM). You can find them [here](https://fedorapeople.org/groups/virt/virtio-win/direct-downloads/latest-virtio/).

2. **Install Required Packages**:
   - Depending on your Linux distribution, install the necessary packages:
     - **Ubuntu**:

       ```bash
       sudo apt install qemu-kvm bridge-utils virt-manager libosinfo-bin -y
       ```

     - **CentOS/Redhat**:

       ```bash
       sudo yum install qemu-kvm libvirt virt-install virt-manager virt-install -y
       ```

     - **Fedora**:

       ```bash
       sudo dnf -y install bridge-utils libvirt virt-install qemu-kvm
       ```

     - **Kali Linux**:

       ```bash
       sudo apt install qemu-kvm libvirt-clients libvirt-daemon-system bridge-utils
       ```

     - **Linux Mint**:

       ```bash
       sudo apt install qemu-kvm bridge-utils virt-manager libosinfo-bin -y
       ```

3. **Configure TPM and Secure Boot**:
   - To emulate TPM, install the **swtpm-tools** software. Follow the guide [here](https://getlabsdone.com/how-to-enable-tpm-and-secure-boot-on-kvm/) to set up TPM on your KVM host.
   - In the same guide, you'll find instructions for installing Secure Boot if it's not already enabled.

4. **Create the Windows 11 VM in KVM**:
   - Use either the GUI (virt-manager) or the CLI to create the VM.
   - During the installation, ensure you select the following settings:
     - **Chipset**: i440FX
     - **Firmware**: UEFI x86_64 (use `/usr/share/OVMF/OVMF_CODE.fd` as the firmware path)

5. **Install Windows 11**:
   - Proceed with the Windows 11 installation process.
   - You've now successfully added TPM and enabled Secure Boot for your Windows 11 VM in KVM.

Remember to adjust the commands based on your specific Linux distribution. If you encounter any issues, feel free to ask for further assistance!

```
egrep '^flags.*(vmx|svm)' /proc/cpuinfo

sudo vi /etc/libvirt/libvirtd.conf
# Set the domain socket group ownership to libvirt
unix_sock_group = "libvirt"

# Adjust the UNIX socket permissions for the R/W socket
unix_sock_rw_perms = "0770"

sudo systemctl start libvirtd
sudo systemctl enable libvirtd

sudo usermod -a -G libvirt $(whoami)
```

## To optimize **Windows 11** performance in **KVM**, consider the following steps

1. **Use Virtio Drivers**:
   - Instead of the standard AHCI SATA driver, select **Virtio** for disk I/O.
   - Set the cache mode to **writeback** for improved performance¹.

2. **Disable Unnecessary Features**:
   - Turn off features like **SuperFetch** and **web search** that you don't need.
   - Review and disable unnecessary **scheduled tasks** and **startup programs**.

3. **CPU Optimizations**:
   - Use CPU options like `-cpu host,hv_relaxed,hv_spinlocks=0x1fff,hv_vapic,hv_time` for Windows VMs³.

Remember to adjust these settings based on your specific requirements.

## To enhance your **Windows 11** experience in **KVM**, consider the following settings

1. **Use Virtio Drivers**:
   - Instead of the standard AHCI SATA driver, select **Virtio** for disk I/O.
   - Set the cache mode to **writeback** for improved performance⁵.

2. **Hardware-Accelerated GPU Scheduling**:
   - Enable this feature in Windows 11:
     - Navigate to **Settings** > **System** > **Display** > **Graphics** > **Change Default Graphics Settings** and enable **Hardware-Accelerated GPU Scheduling**³¹¹.
   - This can improve overall performance and responsiveness.

3. **Disable Visual Effects**:
   - To boost performance, disable animations and other visual effects:
     - Open **Settings** > **Accessibility** > **Visual effects**.
     - Turn off the **Animation effects** toggle switch⁸.

Remember to adjust these settings based on your specific needs and hardware.

## To retrieve the **Windows 11** OEM product key from a Linux system

1. Open the terminal application.

<br>

2. Run the following command as the root user:

   ```
   sudo strings /sys/firmware/acpi/tables/MSDM
   ```

   This will print the Windows 11 or Windows 8 OEM product key embedded in the BIOS.

   Remember to replace "Windows 11" with "Windows 8" if needed.

<br>

## Install a Windows 11 Virtual Machine on KVM

> Before you begin creating a Windows 11 guest virtual machine, you must first enable XML editing because you will need to add the Hyper-V XML component later in this section.

<br>

1. **Configure Windows 11 Virtual Hardware**

   - Choose Memory and CPU settings.
   - Set the disk image size for the virtual machine. The disk image that is created will be of the type QCOW2, which is a copy-on-write format. The QCOW2's initial file size will be smaller, and it will only grow as more data is added.

<br>

2. **Configure Chipset and Firmware**

   - In the Overview section, make sure the chipset is set to Q35 and the firmware is set to UEFI.
   - The Q35 chipset natively supports PCIe and provides improved PCI-E pass-through support.

         The UEFI firmware option, on the other hand, enables Secure Boot, which is required for  Windows 11. When using the UEFI firmware, you can take internal snapshots while the guest is shut down but not while it is running.

<br>

3. **Enable Hyper-V Enlightenments**

      > Hyper-V Enlightenments allow KVM to emulate the Microsoft Hyper-V hypervisor. This improves the performance of the Windows 11 virtual machine.

   - Click the XML tab and add or replace the highlighted XML in the `<hyperv>` and `<timer>` in `<clock>` sections.

      >**Note**: If you have an AMD processor, you cannot use the 'hv-evmcs' feature. This VMCS feature is only available for Intel platforms.

      The XML for `<hyperv>` is:

      ```xml
      <hyperv mode="custom">
         <relaxed state="on"/>
         <vapic state="on"/>
         <spinlocks state="on" retries="8191"/>
         <vpindex state="on"/>
         <runtime state="on"/>
         <synic state="on"/>
         <stimer state="on">
            <direct state="on"/>
         </stimer>
         <reset state="on"/>
         <vendor_id state="on" value="KVM Hv"/>
         <frequencies state="on"/>
         <reenlightenment state="on"/>
         <tlbflush state="on"/>
         <ipi state="on"/>
         <evmcs state="on"/>
      </hyperv>
      ```

      - You must remove the line `<evmcs state="on"/>` from the XML
      - The XML for the <timer> in the <clock> section is:

      ```xml
      <clock offset="localtime">
      ... 
         <timer name="hypervclock" present="yes"/>
      </clock>
      ```

<br>

4. **Enable CPU Host-Passthrough**

   When the mode is set to host-passthrough, the host CPU's model and features are exactly passed on to the guest virtual machine. This causes the virtual machine to run close to the host's native speed. This is the recommended and default option as well.

<br>

5. **Configure the Storage**

   - Change the disk bus from SATA to `VirtIO`. `VirtIO` is preferred over other emulated storage controllers as it is specifically designed and optimized for virtualization.

-
  - Set the **cache mode** to `none`. In this mode, the host page cache is bypassed, and I/O occurs directly between the hypervisor user space buffers and the storage device. In terms of performance, it is equivalent to direct disk access on your host.

  - Set the **discard mode** to `unmap`. When you delete files in the guest virtual machine, the changes are reflected immediately in the guest file system. The qcow2 disk image associated with the VM on the host, however, does not shrink to reflect the newly freed space. When you set the **discard mode** to `unmap`, the qcow2 disk image will automatically shrink to reflect the newly freed space.

<br>

6. **Mount the VirtIO-Win.ISO Image**

   VirtIO drivers are para-virtualized drivers for KVM guests. Microsoft, unfortunately, does not provide these drivers. When installing a Microsoft Windows virtual machine, you must install certain VirtIO drivers.

   As a result, you must mount the `` image file, which contains the VirtIO drivers for Windows. This requires the addition of a second CDROM.

   Click the `Add Hardware`button, then select `CDROM device`as the Device type in the window that appears and mount the `virtio-win.iso` image file.

   If you're using Fedora as your  KVM host, the ISO image will be located at `/usr/share/virtio-win/`.

   If you haven't already installed or downloaded `virtio-win.iso`

<br>

7. **Configure Virtual Network Interface**

   In the **NIC** section, change the device model to **virtio**. The network **VirtIO** driver is specifically designed and optimized for virtualization. As a result, there will be no processing overhead, and the performance of the guest virtual machine will naturally improve.

<bt>

8. **Remove the USB Tablet Device**

   In a Windows virtual machine, removing the USB tablet device can reduce idle CPU usage and context switches. As a result, the performance of the Windows 11 virtual machine will improve.

<br>

9. **Add QEMU Guest Agent Channel**

   The **QEMU** Guest Agent Channel establishes a private communication channel between the host physical machine and the guest virtual machine. This enables the host machine to issue commands to the guest operating system using libvirt. The guest operating system then responds to those commands asynchronously.

   For example, after creating the Windows 11 guest virtual machine, you can shut it down from the host by issuing the following command:

   `$ sudo virsh shutdown Windows-11 --mode=agent`

   This shutdown method is more reliable than `virsh shutdown --mode=acpi` because it guarantees to shut down a cooperative guest in a clean state. If the agent is not present, `libvirt` must rely on injecting an ACPI shutdown event, which some guests ignore and thus do not shut down. You can also use the same syntax to reboot (`virsh reboot`).

   Some of the commands you can try, among many others, are:

   ```
   ### Query the guest operating system's IP address via the guest agent.
   $ sudo virsh domifaddr Windows-11 --source agent

   ### Show a list of mounted filesystems in the running guest.
   $ sudo virsh domfsinfo Windows-11

   ### Instructs the guest to trim its filesystem.
   $ sudo virsh domfstrim Windows-11
   ```

   So, add a QEMU guest agent channel to the Windows 11 guest virtual machine.

   Click the `Add Hardware` button to open the `Add New Virtual Hardware` window, and select `Channel`. Then, from the drop-down list, select '`org.qemu.guest_agent.0`' and click `Finish` to apply.

<br>

10. **Enable Trusted Platform Module (TPM)**

   Enable the **Trusted Platform Module** (TPM). TPM technology is designed to provide hardware-based, security-related functions. Windows 11 requires TPM version 2.0.

<br>

11. **Install a Windows 11 Virtual Machine on KVM**

   All of the virtual hardware and settings needed to install Microsoft Windows 11 have been configured. To begin the installation of Windows 11, click the '`Begin Installation`' button in the upper left corner of the window.

- When you get to the type of installation screen, choose Custom: Install Windows only (advanced).

      You must now select the disk on which Windows 11 will be installed. However, as you can see, the installer was unable to find any drives. This is because you selected the VirtIO disk bus when configuring Windows 11 virtual hardware. VirtIO devices are not natively recognized by Windows, so you must manually install the drivers.

      To install the VirtIO disk driver, click `Load driver`, then `Browse`, expand the `CD Drive (E:)`, expand `Viostor`, expand w11, select `amd64`, and click `OK`.

      After installing the VirtIO storage driver the disk will be visible

      But don't proceed with the installation just yet. You still need to install the VirtIO network driver.

      Repeat the procedure for the network device as well. Click `Load driver` again, then `Browse`, expand the `CD Drive (E:)`, expand `NetKVM`, expand w11, select `amd64`, and click `OK`.

      After installing the VirtIO network device driver, click the Next button to proceed with the installation.

<br>

- The next installation steps are all about personalization. Complete the installation according to your needs, and you will be taken to the desktop environment once it is finished.
- Finally, you must install VirtIO Windows Guest Tools. This package includes some optional drivers and services that will boost SPICE performance and integration. This includes the QXL video driver as well as the SPICE guest agent for copy and paste, automatic resolution switching, and other features. So launch `Windows Explorer`, navigate to the `CD Drive (E:)`, and double-click the `virtio-win-guest-tools` package to install it.
- After installing the guest tools, on the Windows-11 KVM window, click View, Scale Display, and check the 'Auto resize VM with window' option. This will enable the Windows 11 guest window to automatically resize as you scale it.
- Now that you've installed guest tools, you don't need the second CDROM drive.
- Unmount the ISO image of the Windows 11 installer from the first CDROM drive as well.

virtio-win driver signatures

```
All the Windows binaries are from builds done on Red Hat’s internal build system, which are generated using publicly available code. Windows 8+ drivers are cryptographically signed with Red Hat’s test signature Windows 10+ drivers are signed with Microsoft attestation signature. However they are not signed with Microsoft’s WHQL signature. WHQL signed builds are only available with a paid RHEL subscription.

Warning: Due to the signing requirements of the Windows Driver Signing Policy, drivers which are not signed by Microsoft will not be loaded by some versions of Windows when Secure Boot is enabled in the virtual machine.
```

```
1) Hit shift-F10 on the keyboard which will bring up a command prompt
2) From the command line, run "regedit"
3) Under HKEY_LOCAL_MACHINE\SYSTEM\Setup add a new item named "LabConfig"
4) Within the newly created LabConfig item, make two DWORD entries setting their values both to hex 1
     BypassTPMCheck and BypassSecureBootCheck
5) Exit regedit, close the command window and back in the installer, hit the left arrow in the top left of the window (do not click on the X at the top right).  That will back up a step in the installer, and you can go Next again, and it will no longer complain about your hardware.
```

<br>

12. **Enable Hardware Security on Windows 11**

With the `Q35 chipset` selected, `Secure Boot` and T`PM 2.0` enabled, and the latest WHQL-certified VirtIO drivers installed, your Windows 11 guest virtual machine already has standard security.

You can check if your VM passes standard security by opening the Device Security page.

To access the Device Security page, navigate to `Settings` > `Privacy & Security` > `Windows Security` > `Device Security`.

To make Windows 11 even more secure, you can enable Core Isolation.

Core isolation safeguards against malware and other attacks by separating computer processes from your operating system and device.
But before attempting to enable this feature, make sure that your processor supports it.
Your processor must meet the Windows Processor Requirements to enable this feature. If your processor is not on the list, skip this section and proceed to the next one.

Shut down your Windows 11 guest virtual machine. Open the virtual hardware details page, then click the Overview option on the left panel and the XML tab on the right.

Under the <cpu> section, specify the CPU mode and add the policy flag.

Replace this:
`<cpu mode="host-passthrough" check="none" migratable="on"/>`
With this:

```xml
<cpu mode="host-passthrough" check="none" migratable="on">
  <feature policy="require" name="vmx"/>
</cpu>
```

If you're using AMD CPUs, replace vmx with the svm policy flag.

Start your Windows 11 guest virtual machine and navigate to the Core isolation details page.

To access the Core isolation details page, navigate to Settings > Privacy & Security > Windows Security > Device Security > Core isolation details.

Toggle the Memory Integrity switch to enable it. When prompted, restart the Windows 11 VM.

After the reboot, check the security level of your device once more. Go to the Device Security page by navigating to Settings > Privacy & Security > Windows Security > Device Security.

You now have a Windows 11 guest virtual machine running with enhanced hardware security.

<br>

13. **Optimize Windows 11 Performance**

Configuring or disabling a number of Windows processes and features can help optimize the performance of a Windows 11 guest virtual machine.

The following are some suggestions for improving performance:

- Disable SuperFetch
SuperFetch, also known as SysMain, is a standard Windows feature that preloads the apps you use the most frequently. Although Superfetch is useful, it consumes a significant amount of CPU and RAM as a background service.

To disable Superfetch, type `services` into the search box and press [Enter] to open the Services window.

In the Services window, look for `SysMain`. Right-click it and select `Properties`. Then disable the service.

- Disable Windows Web Search
When you search for something in the Windows Search box or Start menu, you may have to wait a few seconds as Windows retrieves your search results along with a list of suggested web results from Bing. Although this is a useful feature, you may dislike it and wish to disable it.

To disable web results on Windows 11, follow these steps:

- Enter regedit into the search box and press [Enter] to launch the Registry Editor.
- Browse to: `Computer\HKEY_CURRENT_USER\Software\Policies\Microsoft\Windows`.
- Right-click the Windows key, select New, and then select the Key option. Enter Explorer as the key name and press [Enter].
-
- Now, right-click on the newly created Explorer key, select New, and then the DWORD (32-bit) Value option. Name the `DWORD` `DisableSearchBoxSuggestions` and press [Enter].
- Double-click the newly created DWORD DisableSearchBoxSuggestions and change its value from 0 to 1.

Close the Registry Editor window and restart your computer. You will now have fast-loading search results that do not retrieve results from the web.

- Disable useplatformclock
When the Hyper-V extensions are enabled, setting the useplatformclock option in bcdedit to "yes" results in poor performance. As a result, disable this feature.

Open the Terminal as an Administrator and type the following command, then press [Enter].

`C:\> bcdedit /set useplatformclock No`

- Disable Unnecessary Scheduled Tasks
Review and disable any unnecessary scheduled tasks.

To get a list of all scheduled tasks, open the Terminal as an Administrator and run the following command:

`C:\> Get-ScheduledTask`
Use the command below to search for tasks that have the word 'schedule' in their name.

`C:\> Get-ScheduledTask -TaskName '*schedule*'`

I'm only going to disable the ScheduledDefrag task. It is entirely up to you which other scheduled tasks you wish to disable.

```
C:\> Disable-ScheduledTask -TaskPath '\Microsoft\Windows\Defrag\' -TaskName ScheduledDefrag

TaskPath                    TaskName         State
--------                    --------         -----
\Microsoft\Windows\Defrag\  ScheduledDefrag  Disabled
```

- Disable Unnecessary Startup Programs

Some programs start automatically and run in the background when you turn on your computer. You can disable these programs so that they do not start when your computer boots.

To stop a program from starting automatically, navigate to Settings > Apps > Startup. Then, turn off all programs that you don't need or use frequently.

- Adjust the Visual Effects in Windows 11
Many visual effects, such as animations and shadow effects, are included in Windows 11. These are visually appealing, but they can consume additional system resources and slow down your computer.

To disable visual effects in Windows, first type `performance` in the `Search box`, then select `Adjust the appearance and performance of Windows` from the list of results. On the `Visual Effects` tab, select `Adjust for best performance` > `Apply`.

# KVM on Linux

The Kernel-based Virtual Machine ( KVM) is a  Linux hypervisor that supports full  virtualization. When you install KVM on Linux, your Linux distribution is transformed into a Type-1 hypervisor, allowing you to run virtual machines at near-host machine speeds.

A Type-1 hypervisor, also known as a bare-metal hypervisor, interacts with the underlying machine hardware directly rather than through an operating system.

## KVM Kernel Modules

At the heart of KVM virtualization are its kernel modules. They consist of a loadable kernel module `kvm.ko`, which provides the core virtualization infrastructure, and a processor-specific kernel module kvm-intel.ko (Intel) or kvm-amd.ko (AMD).

The kernel modules will use the hardware-assisted virtualization capabilities of the CPU (Intel VT-x or AMD-Vi) to transform Linux into a Type-1 hypervisor. This allows you to run guest operating systems at speeds close to those of the host machine.

However, since the kernel modules have no interface, they provide all the resources needed to manage the virtualization to the user-space software.

## QEMU

At the user-space level, it is managed by **QEMU** (Quick EMUlator). QEMU is a generic and open source machine emulator and virtualizer. It provides hardware emulation, like hard disks, network cards, VGA, PCI, etc., and a low-level interface to the virtual machine.

QEMU alone can emulate an entire machine, including a processor and various devices in the software, with no need for hardware-assisted virtualization. As an emulator, QEMU may thus target a broad range of computer architectures, from conventional x86-64 PCs to architectures such as ARM, MIPS, SPARC, and others. For example, on your host PC with an x86_64 board, you can run a virtual machine that can emulate the Raspberry Pi with a raspi3b board. Since this emulation happens entirely through software, it will be accurate but slow.

But when QEMU is paired with KVM, it’s a different story altogether. In the **QEMU-KVM** setup, QEMU focuses on emulating hardware devices while allowing the hypervisor (KVM kernel modules) to manage the CPU. With hypervisor support, QEMU may reach virtual machine CPU performance as near to that of the host computer.

## Libvirt

Libvirt is a software suite that provides various tools to manage  virtual machines and other  virtualization functionality. These tools include an open-source API, a daemon, and a command-line utility virsh.

Some of the key capabilities of libvirt include  virtual machine management, remote machine support, storage management, network interface management, as well as virtual NAT and route-based network management.

Libvirt also provides a common means of managing multiple different virtualization platforms. Supported virtualization platforms include QEMU/KVM, Bhyve, Hyper-V, LXC, OpenVZ, PowerVM, UML, VMware ESXi and GSX, VMware Workstation, VirtualBox, and Xen.

1. **Check Virtualization Support**
   Check if your hardware supports KVM virtualization. If your hardware isn't supported, you'll get errors when trying to run a virtual machine.

   Your processor must support hardware-assisted virtualization. It will either be Intel's VT-x or AMD's AMD-V.

   ```
   $ lscpu | grep Virtualization
   Virtualization:          VT-x
   ```

   If you are using an Intel processor you will get `VT-x` as output. If you have an AMD processor, the output should be `AMD-V`.

   If the output is blank, the hardware virtualization extension is most likely disabled in the BIOS/UEFI. Before proceeding, enable it. It will be located in the CPU configuration section.

   Next, ensure that your kernel includes KVM modules.

   ```
   ## For Arch Linux
   $ zgrep CONFIG_KVM /proc/config.gz

   ## For Other Distros
   $ zgrep CONFIG_KVM /boot/config-$(uname -r)
   CONFIG_KVM_GUEST=y
   CONFIG_KVM_MMIO=y
   CONFIG_KVM_ASYNC_PF=y
   CONFIG_KVM_VFIO=y
   CONFIG_KVM_GENERIC_DIRTYLOG_READ_PROTECT=y
   CONFIG_KVM_COMPAT=y
   CONFIG_KVM_XFER_TO_GUEST_WORK=y
   CONFIG_KVM=m
   CONFIG_KVM_INTEL=m
   CONFIG_KVM_AMD=m
   CONFIG_KVM_AMD_SEV=y
   CONFIG_KVM_XEN=y
   CONFIG_KVM_EXTERNAL_WRITE_TRACKING=y
   ```

   The module is only available if it is set to y or m. If both steps are successful, proceed with the installation of KVM.

<br>

2. **Install KVM on Linux Distributions**

   To use KVM virtualization on Linux, you'll need 1) KVM kernel modules, 2) QEMU, and 3) the Libvirt software suite. Since Linux already includes KVM kernel modules, you just have to install QEMU and Libvirt to use the KVM hypervisor. However, there are a number of other virtualization management packages that are recommended when using virtualization.

   **Fedora / Rocky Linux:**

   ```
   $ sudo dnf install qemu-kvm libvirt virt-install virt-manager virt-viewer \
      edk2-ovmf swtpm qemu-img guestfs-tools libosinfo tuned
   ```

   **Ubuntu / Debian Linux:**

   ```
   $ sudo apt install qemu-system-x86 libvirt-daemon-system virtinst \
      virt-manager virt-viewer ovmf swtpm qemu-utils guestfs-tools \
      libosinfo-bin tuned
   ```

   **Arch Linux:**

   ```
   $ sudo pacman -S qemu-full libvirt virt-install virt-manager virt-viewer \
      edk2-ovmf swtpm qemu-img guestfs-tools libosinfo

   $ yay -S tuned
   ```

   A brief description of the packages listed above.

   - qemu-kvm/qemu-system-x86/qemu-full: A user-level KVM emulator that facilitates communication between hosts and VMs.
   - libvirt/libvirt-daemon-system: A daemon that manages virtual machines and the hypervisor as well as handles library calls.
   - virt-install/virtinst: A command-line tool for creating guest virtual machines.
   - virt-manager: A graphical tool for creating and managing guest virtual machines.
   - virt-viewer: A graphical console for connecting to a running virtual machine.
   - edk2-ovmf/ovmf: Enables UEFI support for Virtual Machines.
   - swtpm: A TPM emulator for Virtual Machines.
   - qemu-img/qemu-utils: Provides tools to create, convert, modify, and snapshot offline disk images.
   - guestfs-tools: Provides a set of extended command-line tools for managing virtual machines.
   - libosinfo/libosinfo-bin: A library for managing OS information for virtualization.
   - tuned: A system tuning service for Linux.

<br>

3. **Install VirtIO Drivers for Windows Guests**

   VirtIO drivers are para-virtualized drivers for  KVM guests. Unfortunately, Microsoft does not provide VirtIO drivers. If you want to create a Microsoft Windows virtual machine, you need to download the virtio-win.iso image, which contains the VirtIO drivers for the Windows operating system.

   **Fedora / Rocky  Linux:**

   Install the VirtIO repository.

   ```
   $ sudo wget https://fedorapeople.org/groups/virt/virtio-win/virtio-win.repo \
      -O /etc/yum.repos.d/virtio-win.repo
   ```

   The `virtio-win` package can now be installed.

      ```
      sudo dnf install virtio-win
      ```

   The `virtio-win.iso` file will be saved in the `/usr/share/virtio-win/` directory.

   **Ubuntu / Debian / Arch Linux:**

   For Linux distributions that are not based on Red Hat, you can directly download the stable version of virtio-win.iso.

   ```
   wget <https://fedorapeople.org/groups/virt/virtio-win/direct-downloads/archive-virtio/virtio-win-0.1.240-1/virtio-win-0.1.240.iso>
   ```

   When you create a Windows VM, you need to attach this ISO image to a CD-ROM. It includes all the VirtIO drivers necessary for Windows OS installation.

   Check out this repository for a complete list of drivers, both old and new.

<br>

4. **Enable the Modular libvirt Daemon**

   There are two types of libvirt daemons: **monolithic** and **modular**. The type of daemon(s) you use affects how granularly you can configure individual virtualization drivers.

   The traditional **monolithic libvirt daemon**, libvirtd, manages a wide range of virtualization drivers via centralized hypervisor configuration. However, this may result in inefficient use of system resources.

   In contrast, the newly introduced **modular libvirt** provides a specific daemon for each virtualization driver. As a result, modular libvirt daemons offer more flexibility in fine-tuning libvirt resource management.

   While most Linux distributions have started to offer a modular option, at the time of writing, Ubuntu and Debian continue to offer only a **monolithic** daemon.

   **Fedora / Rocky / Arch Linux:**

      ```
      $ for drv in qemu interface network nodedev nwfilter secret storage; do \
         sudo systemctl enable virt${drv}d.service; \
         sudo systemctl enable virt${drv}d{,-ro,-admin}.socket; \
      done
   ```

   **Ubuntu / Debian Linux:**

      ```
      sudo systemctl enable libvirtd.service
      ```

   A reboot is recommended so that the KVM can properly start.

      `$ sudo reboot`

<br>

5. **Validate Host Virtualization Setup**

   After the reboot, verify that your host is correctly configured to run all libvirt hypervisor drivers.

   `$ sudo virt-host-validate qemu`

   You may receive the following warning message from the validation tool:

   1. `QEMU: Checking for device assignment IOMMU support : WARN (No ACPI DMAR table found, IOMMU either disabled in BIOS or not supported by this hardware platform)`

      **Meaning**: Your Intel processor only supports the VT-x (vmx) feature and not VT-d.

   **VT-d** is used to implement a feature known as PCIe Pass-through. VT-d enables virtual machines to have direct access to specific I/O devices such as graphics cards, network adapters, and storage controllers.

   2. `QEMU: Checking if IOMMU is enabled by kernel : WARN (IOMMU appears to be disabled in kernel. Add intel_iommu=on to kernel cmdline arguments)`

      **Meaning**: Your Intel CPU supports the VT-d capability, but it must be enabled in the kernel.

   To enable VT-d, open the `/etc/default/grub` file and add '`intel_iommu=on iommu=pt`' to the GRUB_CMDLINE_LINUX line.

      ```
      #### For Intel CPU

      $ sudo vim /etc/default/grub
      ...
      GRUB_CMDLINE_LINUX="... intel_iommu=on iommu=pt"
      ...
      ```

    If you have an AMD CPU, IOMMU is enabled by default. To enable pass-through mode, just add iommu=pt.

      ```
      #### For AMD CPU

      $ sudo vim /etc/default/grub
      ...
      GRUB_CMDLINE_LINUX="... iommu=pt"
      ...
      ```

    Then, regenerate the grub configuration file.

    **Fedora / Rocky Linux:**

      `$ sudo grub2-mkconfig -o /boot/grub2/grub.cfg`

    **Ubuntu / Debian:**

      `$ sudo update-grub`

    **Arch Linux:**

      `$ sudo grub-mkconfig -o /boot/grub/grub.cfg`

    Reboot the system and validate the host again to ensure that no issues remain.

      `$ sudo virt-host-validate qemu`

    On the Intel CPU, verify that the VT-d is enabled.

      `$ dmesg | grep -i -e DMAR -e IOMMU`

    On the AMD CPU, verify that the AMD-Vi is enabled.

      `$ dmesg | grep -i -e AMD-Vi`

      `QEMU: Checking for secure guest support : WARN (Unknown if this platform has Secure Guest support)`

      **Meaning**: Enable AMD Secure Encrypted  Virtualization (SEV) support.

   If you have an Intel CPU, you can safely ignore this warning. This applies to AMD CPUs.

   The SEV feature is only supported on (most) `EPYC` and `Ryzen Pro` processors. This feature enables `libvirt/kvm` to encrypt memory.

   This is a nice feature, but if your AMD processor does not support it, you can safely ignore the warning (as with Intel processors).

   To test feature support, run the following command:

      `$ lscpu | grep sev`

   If the output returns nothing, `SEV` is not supported.

   Otherwise, enable the feature in the BIOS and activate it in the kernel (Launch security with AMD SEV).

<br>

6. **Optimize the Host with TuneD**

   **TuneD** is a system tuning service for  Linux. It provides a number of pre-configured tuning profiles, each optimized for unique workload characteristics, including CPU-intensive job needs, storage/network throughput responsiveness, or power consumption reduction.

   Enable and start the TuneD service.

      `$ sudo systemctl enable --now tuned`

   Find out which TuneD profile is currently active.

      ```
      $ tuned-adm active
      Current active profile: balanced
      ```

   List all TuneD profiles that are available on your system

      ```
      $ tuned-adm list
      Available profiles:

      - accelerator-performance     - Throughput performance based tuning with disabled higher latency STOP states
      - aws                         - Optimize for aws ec2 instances
      - balanced                    - General non-specialized tuned profile
      - desktop                     - Optimize for the desktop use-case
      - hpc-compute                 - Optimize for HPC compute workloads
      - intel-sst                   - Configure for Intel Speed Select Base Frequency
      - latency-performance         - Optimize for deterministic performance at the cost of increased power consumption
      - network-latency             - Optimize for deterministic performance at the cost of increased power consumption, focused on low latency network performance
      - network-throughput          - Optimize for streaming network throughput, generally only necessary on older CPUs or 40G+ networks
      - optimize-serial-console     - Optimize for serial console use.
      - powersave                   - Optimize for low power consumption
      - throughput-performance      - Broadly applicable tuning that provides excellent performance across a variety of common server workloads
      - virtual-guest               - Optimize for running inside a virtual guest
      - virtual-host                - Optimize for running KVM guests
      Current active profile: balanced
      ```

   I will set the profile to virtual-host. This optimizes the host for running  KVM guests.

      `$ sudo tuned-adm profile virtual-host`

   Check that the TuneD profile has been updated and that virtual-host is now active.

      `$ tuned-adm active`

   Current active profile: virtual-host

   Make sure there are no errors.

      ```
      $ sudo tuned-adm verify
      Verfication succeeded, current system settings match the preset profile.
      See TuneD log file ('/var/log/tuned/tuned.log') for details.
      ```

   Your host is now optimized to run KVM guests. When you create a Linux virtual machine, you can also enable TuneD and set the TuneD profile to virtual-guest to improve virtual guest performance.

   To learn more about the tuned-adm command, go to its [GitHub page](https://github.com/redhat-performance/tuned)

<br>

7. **Configure a Network Bridge**

   All the virtual machines on the host are by default connected to the same NAT-type virtual network, named 'default'.

   ```
   $ sudo virsh net-list --all
   Name      State    Autostart   Persistent
   --------------------------------------------

   default   active   yes         yes
   ```

   >**Note**: If you use Debian, the default network state will be inactive, and autostart will be disabled. To activate, run the command `sudo virsh net-start default` followed by `sudo virsh net-autostart default`.

   The virtual machines that use this '`default`' network will be assigned an IP address in the `192.168.124.0/24` address space, with the host OS reachable at `192.168.124.1`.

   ```
   $ sudo virsh net-dumpxml default | xmllint --xpath '//ip' -
   <ip address="192.168.124.1" netmask="255.255.255.0">
      <dhcp>
         <range start="192.168.124.2" end="192.168.124.254"/>
      </dhcp>
   </ip>
   ```

   Virtual machines using this default network will only have outbound network access. Virtual machines will have full access to network services, but devices outside the host will be unable to communicate with virtual machines inside the host. For example, the virtual machine can browse the web but cannot host a web server that is accessible to the outside world.

   If you want virtual machines to be directly visible on the same physical network as the host and visible to external devices, you must use a network bridge.

   A network bridge is a link-layer device that connects two local area networks into one network. In this case, a software bridge is used within a Linux host to simulate a hardware bridge. As a result, all other physical machines on the same physical network of the host can detect and access  virtual machines. The virtual machine, for example, can browse the web, and will also be able to host a web server that is accessible to the outside world.

      ```text
      Important:

      Unfortunately, you cannot set up a network bridge when using Wi-Fi.

      Due to the IEEE 802.11 standard which specifies the use of 3-address frames in Wi-Fi for the efficient use of airtime, you cannot configure a bridge over Wi-Fi networks operating in Ad-Hoc or Infrastructure modes.
      ```

      Source: [Configuring a network bridge](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/9/html-single/configuring_and_managing_networking/index#configuring-a-network-bridge_configuring-and-managing-networking)

   First, find the name of the interface you want to add to the bridge. In my case, it is enp2s0.

      ```
      $ sudo nmcli device status
      DEVICE             TYPE      STATE                   CONNECTION
      enp2s0             ethernet  connected               Wired connection 1
      lo                 loopback  connected (externally)  lo
      virbr0             bridge    connected (externally)  virbr0
      ```

   Create a bridge interface. I'll name it bridge0, but you can call it whatever you want.

      `$ sudo nmcli connection add type bridge con-name bridge0 ifname bridge0`

   Assign the interface to the bridge. I'm going to name this connection 'Bridge connection 1', but you can call it whatever you want.

      ```
      $ sudo nmcli connection add type ethernet slave-type bridge \
         con-name 'Bridge connection 1' ifname enp2s0 master bridge0
      ```

   The following step is optional. If you want to configure a static IP address, use the following commands; otherwise, skip this step. Change the IP address and other details to match your configuration.

      ```
      sudo nmcli connection modify bridge0 ipv4.addresses '192.168.1.7/24'
      sudo nmcli connection modify bridge0 ipv4.gateway '192.168.1.1'
      sudo nmcli connection modify bridge0 ipv4.dns '8.8.8.8,8.8.4.4'
      sudo nmcli connection modify bridge0 ipv4.dns-search 'sysguides.com'
      sudo nmcli connection modify bridge0 ipv4.method manual
      ```

   Activate the connection.

      `$ sudo nmcli connection up bridge0`

   Enable the connection.autoconnect-slaves parameter of the bridge connection.

      `$ sudo nmcli connection modify bridge0 connection.autoconnect-slaves 1`

   Reactivate the bridge.

      `$ sudo nmcli connection up bridge0`

   Verify the connection. If you get your IP address from DHCP, it may take a few seconds to lease a new one. So please be patient.

      ```
      $ sudo nmcli device status
      DEVICE             TYPE      STATE                   CONNECTION
      bridge0            bridge    connected               bridge0
      lo                 loopback  connected (externally)  lo
      virbr0             bridge    connected (externally)  virbr0
      enp2s0             ethernet  connected               Bridge connection 1

      $ ip -brief addr show dev bridge0
      bridge0          UP             192.168.1.7/24 fe80::a345:fb7b:cb67:c778/64
      ```

   You can now start using a network bridge when creating virtual machines.

   However, it is recommended that you also set up a virtual bridge network in  KVM so that the virtual machines can use this bridge interface by name.

   Create an XML file called nwbridge.xml and fill it with the following information. I'll call my host network bridge nwbridge, but you can call it whatever you want.

      ```XML
      $ vim nwbridge.xml
      <network>
      <name>nwbridge</name>
      <forward mode='bridge'/>
      <bridge name='bridge0'/>
      </network>
      ```

   Define nwbridge as a persistent virtual network.

      `$ sudo virsh net-define nwbridge.xml`

   Activate the nwbridge and set it to autostart on boot.

      ```
      sudo virsh net-start nwbridge
      sudo virsh net-autostart nwbridge
      ```

   Now you can safely delete the nwbridge.xml file. It’s not required anymore.

      `$ rm nwbridge.xml`

   Finally, verify that the virtual network bridge nwbridge is up and running.

      ```
      $ sudo virsh net-list --all
      Name       State    Autostart   Persistent
      ---------------------------------------------

      default    active   yes         yes
      nwbridge   active   yes         yes
      ```

   A network bridge has been created. You can now start using the nwbridge network bridge in your virtual machines. The virtual machines will get their IP addresses from the same pool as your host machine.

   If you ever want to remove this network bridge and return it to its previous state, then run the following commands.

      ```
      sudo virsh net-destroy nwbridge
      sudo virsh net-undefine nwbridge

      sudo nmcli connection up 'Wired connection 1'
      sudo nmcli connection down bridge0
      sudo nmcli connection del bridge0
      sudo nmcli connection del 'Bridge connection 1'
      ```

<br>

8. **Give the User System-Wide Permission**
   Libvirt provides two methods for connecting to the local qemu-kvm hypervisor.

   `qemu:///session`

   Connect as a regular user to a per-user instance locally. This is the default mode when running a  virtual machine as a regular user. This allows users to only manage their own  virtual machines.

      ```
      $ virsh uri
      qemu:///session
      qemu:///system
      ```

   Connect to a system instance as the root user locally. When run as root, it has complete access to all host resources. This is also the recommended method to connect to the local hypervisor.

      ```
      $ sudo virsh uri
      qemu:///system
      ```

   So, if you want to connect to a system instance as a regular user with full access to all host resources, do the following.

   > Note: For Ubuntu users, system-wide permissions are enabled by default. You can skip this section and move on to the next.

   Add the regular user to the libvirt group.

      `$ sudo usermod -aG libvirt $USER`

   Define the environment variable LIBVIRT_DEFAULT_URI in the local .bashrc file of the user.

      ```
      echo "export LIBVIRT_DEFAULT_URI='qemu:///system'" >> ~/.bashrc
      source ~/.bashrc
      ```

   Check again as a regular user to see which instance you are connected to.

      ```
      $ virsh uri
      qemu:///system
      ```

   You can now use the virsh command-line tool and the Virtual Machine Manager (virt-manager) without sudo.

<br>

9. **Set ACL on the Images Directory**

   By default, virtual machine disk images are stored in the /var/lib/libvirt/images directory. Only the root user has access to this directory.

      ```
      $ ls /var/lib/libvirt/images/
      ls: cannot open directory '/var/lib/libvirt/images/': Permission denied
      ```

   As a regular user, you might want access to this directory without having to type sudo every time. So, setting the ACL for this directory is the best way to access it without changing the default permissions.

   First, recursively remove any existing ACL permissions on the directory.

      `$ sudo setfacl -R -b /var/lib/libvirt/images`

   Grant regular user permission to the directory recursively.

      `$ sudo setfacl -R -m u:$USER:rwX /var/lib/libvirt/images`

   The capital 'X' above indicates that 'execute' should only be applied to child folders and not child files.

   All existing directories and files (if any) in /var/lib/libvirt/images/ now have permissions. However, any new directories and files created within this directory will not have any special permissions. To get around this, we need to enable 'default' special permissions. The 'default acls' can only be applied to directories and not to files.

      `$ sudo setfacl -m d:u:$USER:rwx /var/lib/libvirt/images`

   Now review your new ACL permissions on the directory.

      ```
      $ getfacl /var/lib/libvirt/images
      getfacl: Removing leading '/' from absolute path names

      # file: var/lib/libvirt/images

      # owner: root

      # group: root

      user::rwx
      user:madhu:rwx
      group::--x
      mask::rwx
      other::--x
      default:user::rwx
      default:user:madhu:rwx
      default:group::--x
      default:mask::rwx
      default:other::--x
      ```

   Try accessing the /var/lib/libvirt/images directory again as a regular user.

      ```
      $ touch /var/lib/libvirt/images/test_file

      $ ls -l /var/lib/libvirt/images/
      total 0
      -rw-rw----+ 1 madhu madhu 0 Feb 12 21:34 test_file
      ```

   You now have full access to the /var/lib/libvirt/images directory.

<br>

# KVM Guest Driver installation

<https://github.com/virtio-win/kvm-guest-drivers-windows/wiki/Driver-installation>

Remember to be cautious when installing or updating drivers, as improper installation or incompatible drivers can cause system instability. Make sure you have the correct driver for your hardware, and back up your data before making any significant changes to your system.

## Variant 1: using the Installation Wizard

This is the best option for regular users that just want to install the drivers on their VM

- Download Virtio-Win ISO:
  Visit the official Virtio-Win repository (<https://fedorapeople.org/groups/virt/virtio-win/direct-downloads/latest-virtio/virtio-win.iso>) and download the latest Virtio-Win ISO file to your host machine.

- Attach the Virtio-Win ISO to the VM:
  Ensure your VM is powered on and running.
  In the VM management software (e.g., VirtualBox, VMware, QEMU, etc.), locate the option to attach the Virtio-Win ISO as a virtual CD/DVD drive to the VM. The specific steps may vary depending on the virtualization software you're using.

- Inside the VM:
  Access the VM's desktop or file system.

- Open File Explorer:
  Open File Explorer (Windows Explorer) within the VM.

- Navigate to the Virtio-Win ISO:
  In File Explorer, locate and click on the virtual CD/DVD drive where the Virtio-Win ISO is mounted. It should be listed under "This PC" or "Computer."

- Launch the Installation Wizard:
  Inside the Virtio-Win ISO, locate the virtio-win-guest-tools-xxx.exe (where "xxx" represents the version number) file and run it by double-clicking it. This file contains the Virtio-Win drivers and the Installation Wizard.

- Select Components:
  The Virtio-Win Installation Wizard will launch. It will prompt you to select the components you want to install, such as network drivers, storage drivers, and balloon drivers. You can choose to install all available drivers by default or select specific components based on your requirements. Click "Install" to proceed.

- Driver Installation:
  The Installation Wizard will begin to install the selected drivers. You will see progress bars for each driver component being installed.

- Reboot the VM (if prompted):
  After the drivers are installed, the Installation Wizard may prompt you to reboot the VM for the changes to take effect. If prompted, save your work and restart the VM.

- Verify Driver Installation:
  After the VM restarts, check Device Manager to ensure that the Virtio-Win drivers are correctly installed. There should be no unknown devices or driver-related errors.

Installing drivers required for Windows installation, such as hard disk drivers

- Prepare Driver Files:
    Before you begin the Windows installation, you'll need to have the necessary driver files for your hardware component ready. This usually includes the hard disk or storage controller drivers. Download these drivers from the manufacturer's website.

- Insert Windows Installation Media:
    Insert the Windows installation media (DVD or USB) into your computer and boot from it. You may need to access the BIOS/UEFI settings to set the boot order to prioritize the installation media.

- Start the Windows Installation:
    Power on or restart your computer to initiate the Windows installation from the installation media. If it doesn't start automatically, you may need to press a key (e.g., F2, F12, Delete) to access the boot menu and select the installation media.

- Language and Region Settings:
    In the initial Windows setup screen, select your preferred language, time format, keyboard layout, and click "Next."

- Click "Install Now":
    On the next screen, click "Install Now" to start the Windows installation.

- Accept License Terms:
    Read and accept the Microsoft Software License Terms.

- Choose Installation Type:
    Select "Custom: Install Windows only (advanced)" as you will be specifying driver installation manually.

- Partitioning and Drive Selection:
    In the "Where do you want to install Windows?" window, you may not see your hard disk or storage device listed if it's not recognized. Click "Load Driver."

- Load Drivers:
    The "Load Driver" window will open. Insert the USB flash drive or attach the Virtio-Win ISO containing the driver files you prepared earlier.

- Browse for Drivers:
    Click "Browse" and navigate to the USB flash drive to find the driver files you downloaded. Select the appropriate driver and click "Next."

- Install the Driver:
    Windows will load the driver. Once the driver is loaded successfully, you should see your hard disk or storage device listed in the installation window.

- Select the Drive:
    Choose your hard disk or storage device and click "Next."

- Complete Windows Setup:
    Continue with the Windows setup, which includes creating or signing in with a Microsoft account, setting up your preferences, and configuring your system settings.

By following these steps, you can successfully install the required drivers, such as hard disk drivers, during the Windows installation process. This ensures that your storage devices are recognized, allowing you to proceed with the installation and use your computer as intended.

## Variant 2: using Device Manager

- Plug in the Device: First, make sure the device for which you want to install the driver is connected to your computer. It should be recognized as an unknown device or have an error indicator in Device Manager.

- Open Device Manager:
   Press the Windows key.
   Type "Device Manager" into the search bar.
   Click on "Device Manager" in the search results.

- Locate the Device: In Device Manager, find and expand the category that corresponds to the device you want to install a driver for. The device may appear under "Other devices" with a yellow triangle icon, indicating a problem with the driver.

- Install the Driver: Right-click on the device with the missing driver and select "Update driver."

- Choose How to Search for Drivers:
   Select "Search automatically for updated driver software" if you want Windows to search for the driver online. This option requires an active internet connection.
   Select "Browse my computer for drivers" if you have already downloaded the driver and have it saved on your computer. Then, click "Next."

- Specify the Driver Location (if needed): If you selected "Browse my computer for drivers," browse to the folder containing the downloaded driver files, or select the appropriate folder where the driver is located. Click "Next" to continue.

- Confirm Driver Installation: Windows will analyze the driver package and confirm whether it's compatible with your device. If it is, you'll see a message confirming that the driver will be installed. Click "Install" to proceed.

- Wait for Installation: Windows will install the driver. This may take a moment, and you may see a progress bar.

- Complete the Installation: Once the driver installation is complete, you'll see a message indicating the successful installation. Click "Close" or "Finish" to complete the process.

- Verify Installation: After the installation, the device should no longer have an error indicator in Device Manager. It should be listed under the appropriate category without any warnings.

- Reboot (if necessary): In some cases, Windows may prompt you to restart your computer to finalize the driver installation. If prompted, save your work and restart your computer.

## Variant 3: via an INF (Information) file

- Download the Correct INF File:
   Visit the manufacturer's website or a trusted source to download the correct INF file for the driver that matches your hardware device and Windows version. Ensure that you have the latest version of the driver if available.

- Locate the Downloaded INF File:
   Once the INF file is downloaded, locate it in your computer's file system. It is usually in the Downloads folder unless you specified a different location during the download.

- Right-Click on the INF File:
   Right-click on the downloaded INF file.

- Select "Install" from the Context Menu:
   In the context menu that appears when you right-click the INF file, choose "Install." This action will initiate the installation process.

- Driver Installation Process:
   Windows will begin the installation process for the driver based on the information provided in the INF file. During this process, you may see a series of dialogs or prompts, depending on the driver. These prompts may ask for confirmation to install the driver.

- Follow On-Screen Prompts:
   Follow any on-screen prompts or instructions provided during the installation. These may include agreeing to the driver's terms and conditions, confirming the installation, or selecting installation options.

- Complete the Installation:
   Once the installation is complete, you'll receive a message indicating the successful installation of the driver.

- Reboot (if necessary):
   In some cases, Windows may prompt you to restart your computer to finalize the driver installation. If prompted, save your work and restart your computer.

- Verify Installation:
   After installation, check Device Manager to ensure the driver is listed under the appropriate category without any warnings or errors. If the device is listed correctly, the driver installation was successful.

## Variant 4: using the pnputil command line utility

- Open a Command Prompt with administrative privileges:

   Press the Windows key.
   Type "cmd" or "Command Prompt" into the search bar.
   Right-click on "Command Prompt" and select "Run as administrator."

- Type the following command to list all the installed drivers and their package names:

`pnputil /enum-drivers`

This will provide a list of installed drivers along with their published names, which you will need to identify the correct driver package.

- Locate the INF file for the driver you want to install. INF files are typically located in the driver package folder or on the manufacturer's website.

- Install the driver using the pnputil utility. Replace `path\to\driver.inf` with the actual path to the INF file of the driver you want to install:

`pnputil /add-driver "path\to\driver.inf"`

If you want to specify a package name (use the published name from step 2), you can do so with the `/package` option:

`pnputil /add-driver "path\to\driver.inf" /package PackageName`

If the driver package contains multiple INF files, you should specify the INF file that corresponds to the specific hardware component you want to update.

Windows may prompt you to confirm the installation of the driver. Confirm and proceed with the installation if prompted.

- After a successful installation, you should see a message indicating that the driver has been added.

- You may need to restart your computer for the changes to take effect. Use the following command to check the status of the driver installation:

`pnputil /e`

This command will show a list of installed driver packages.

```
https://virtio-win.github.io/Development/Building-the-drivers-using-Windows-11-21H2-EWDK.html
virtio-win
Steps required for building

    Download Windows 11 21H2 EWDK ISO (https://go.microsoft.com/fwlink/?linkid=2202360) and mount it (let’s say to E:\). You can download the ISO to the host and connect it to the VM as CD-ROM.
    Download and install WinFSP (https://github.com/billziss-gh/winfsp/releases/tag/v1.10) with “Core”, “Developer” and “Kernel Developer” features enabled.
    Download and install CPDK 8.0 (https://www.microsoft.com/en-us/download/details.aspx?id=30688).
    Download and install .NET Framework version 4.8 (https://dotnet.microsoft.com/download/dotnet-framework/thank-you/net48-web-installer). Windows Server 2022 and Windows 11 don’t need it.
    Open command-line and run E:\LaunchBuilEnv.cmd.
    Change directory to kvm-guest-drivers-windows.
    Run build_AllNoSdv.bat [Win8|Win8.1|Win10] depending on your Windows version. Choose Win10 for Windows Server 2022 and Windows 11.
    Run Tools\signAll.bat to sign drivers. Loading of test-signed drivers must be enabled on your system (https://docs.microsoft.com/en-us/windows-hardware/drivers/install/the-testsigning-boot-configuration-option).
    Find drivers/services in folders named Install inside driver folders.
```

```
WHQL release signature

https://learn.microsoft.com/en-us/windows-hardware/drivers/install/whql-release-signature

Your Windows Hardware Quality Labs (WHQL) digitally signed driver package can be distributed through the Windows Update program. WHQL can digitally sign your driver packages if they pass Windows Hardware Lab Kit (HLK) testing.

Obtaining a WHQL release signature is part of the Windows Hardware Lab Kit (HLK). A WHQL release signature consists of a digitally signed catalog file. The digital signature doesn't change the driver binary files or the INF file that you submit for testing.

Obtaining a WHQL release signature consists of:

    Testing the driver package with the Windows HCK to verify that the driver package is compatible with Microsoft Windows. Once the HCK is installed, the Driver Test Manager (DTM) is run to test and verify the driver package. For more information, see the Windows Hardware Lab Kit (HLK).

    Submitting DTM test logs to the Windows Quality Online Services to obtain a WHQL release signature for the driver package. For more information, see the Windows Hardware Lab Kit (HLK).

For more information about WHQL, see the Windows Hardware Quality Labs website.

Note

WHQL does not embed signatures in driver files. You can embed a signature in a driver file using a third-party commercial release certificate. Embed the signature in the driver file before submitting the driver package to WHQL.
```

# Virtual Manager commands

| Command | Purpose|
|:--- |:--- |
`virsh list --all` | displays all virtual machines
`virsh list` | To list only running virtual machines,
`virsh snapshot-create VM_NAME SNAPSHOT_NAME` | create a snapshot of your virtual machine
`virt-clone --original=SOURCE_VM --name=NEW_VM_NAME --auto-clone` | To clone a virtual machine
`virsh net-list` <br> `virsh net-info default` <br> `virsh net-dhcp-leases default` | to dind the IP addresses of VMs in KVM with virsh
`virsh domifaddr VM_NAME` |
`virsh dumpxml VM_NAME \| grep "mac address"` |

# References

- Windows 11 on KVM – How to Install Step by Step? – GetLabsDone. <https://getlabsdone.com/how-to-install-windows-11-on-kvm/>.
- How to enable TPM and secure boot on KVM? – GetLabsDone. <https://getlabsdone.com/how-to-enable-tpm-and-secure-boot-on-kvm/>.
- kvm virtualization - Created vm with virt-manager but it won't boot .... <https://askubuntu.com/questions/1444894/created-vm-with-virt-manager-but-it-wont-boot-says-on-bootable-device>.
- KVM windows 11 guest won't boot when `bus="sata" and address type .... <https://unix.stackexchange.com/questions/738603/kvm-windows-11-guest-wont-boot-when-bus-sata-and-address-type-drive-chang>.
- KVM/QEMU No bootable device 0003 error and solution. <https://stackoverflow.com/questions/52740151/kvm-qemu-no-bootable-device-0003-error-and-solution>.
- Running the Windows 11 development VM images under KVM. <https://askubuntu.com/questions/1519836/running-the-windows-11-development-vm-images-under-kvm>.
- WindowsGuestDrivers/Download Drivers - KVM. <https://www.linux-kvm.org/page/WindowsGuestDrivers/Download_Drivers>.
- John Siu Blog | Windows 11 KVM Client Drivers and Tips. <https://johnsiu.com/blog/win-kvm/>.
- virtiofs - shared file system for virtual machines / Windows HowTo - GitLab. <https://virtio-fs.gitlab.io/howto-windows.html>.
- Driver installation · virtio-win/kvm-guest-drivers-windows Wiki - GitHub. <https://github.com/virtio-win/kvm-guest-drivers-windows/wiki/Driver-installation>.
- Windows VirtIO Drivers - Proxmox VE. <https://pve.proxmox.com/wiki/Windows_VirtIO_Drivers>.
- how to enable TPM and secure boot on kvm for Windows 11. <https://www.youtube.com/watch?v=_KmL4UmFmo4>.
- How To Enable TPM 2.0 on KVM and install Windows 11. <https://computingforgeeks.com/enable-tpm-on-kvm-and-install-windows/>.
- How to Enable TPM 2.0 and Secure Boot [Windows 11]. <https://www.youtube.com/watch?v=t7sAbEvWp5Y>.
- undefined. <https://fedorapeople.org/groups/virt/virtio-win/direct-downloads/latest-virtio/>.
- undefined. <http://www.linux-kvm.org/page/Virtio%29>.
- undefined. <http://ppa.launchpad.net/stefanberger/swtpm-focal/ubuntu>.
- KVM/QEMU users - use Virtio and set cache as ... - Windows 11 Forum. <https://www.elevenforum.com/t/kvm-qemu-users-use-virtio-and-set-cache-as-writeback-for-best-disk-i-o-on-w11-guests.16812/>.
- Low performance on win11 VM with QEMU/KVM - Virtualization - openSUSE .... <https://forums.opensuse.org/t/low-performance-on-win11-vm-with-qemu-kvm/168394>.
- How to install Windows 11 on a KVM in 2024 - Geeky Gadgets. <https://www.geeky-gadgets.com/installing-windows-11-on-a-kvm/>.
- Performance tuning Windows 11 VM - VM Engine (KVM) - Unraid. <https://forums.unraid.net/topic/155381-performance-tuning-windows-11-vm/>.
- KVM/QEMU users - use Virtio and set cache as ... - Windows 11 Forum. <https://www.elevenforum.com/t/kvm-qemu-users-use-virtio-and-set-cache-as-writeback-for-best-disk-i-o-on-w11-guests.16812/>.
- How to Enable Hardware-Accelerated GPU Scheduling in Windows 10 and 11. <https://www.howtogeek.com/756935/how-to-enable-hardware-accelerated-gpu-scheduling-in-windows-11/>.
- Enable Hardware-accelerated GPU Scheduling in Windows 11/10. <https://www.thewindowsclub.com/enable-hardware-accelerated-gpu-scheduling>.
- How to disable visual effects to speed up Windows 11. <https://www.windowscentral.com/how-disable-visual-effects-speed-windows-11>.
- Windows 11 on KVM – How to Install Step by Step? – GetLabsDone. <https://getlabsdone.com/how-to-install-windows-11-on-kvm/>.
- How to optimize Windows 11 for gaming - XDA Developers. <https://www.xda-developers.com/how-to-optimize-windows-11-for-gaming/>.
- Windows 11 has a hidden switch for smoother graphics and better .... <https://www.tomsguide.com/computing/how-to-boost-windows-11-performance-by-disabling-visual-effects>.
- Improving the performance of a Windows 10 Guest on QEMU. <https://scribe.rip/@leduccc/improving-the-performance-of-a-windows-10-guest-on-qemu-a5b3f54d9cf5>.
- Windows 11 Guest VM with VirtIO on Libvirt - Kevin Locke. <https://kevinlocke.name/bits/2021/12/10/windows-11-guest-virtio-libvirt/>.
- How to disable animation effects on Windows 11 - Pureinfotech. <https://pureinfotech.com/turnoff-animation-effects-windows-11/>.
- How to Turn Off Windows 11's Animation Effects to Improve Performance - MUO. <https://www.makeuseof.com/windows-11-turn-off-animation-effects/>.
- Windows 10 GPU Hardware Scheduling: Is It Worth Turning On? - MUO. <https://www.makeuseof.com/windows-10-gpu-hardware-scheduling-worth-turning-on/>.
- Turn On or Off Hardware Accelerated GPU Scheduling in Windows 11. <https://www.elevenforum.com/t/turn-on-or-off-hardware-accelerated-gpu-scheduling-in-windows-11.852/>.
- Linux find Windows 10/11 OEM product key command - nixCraft. <https://www.cyberciti.biz/faq/linux-find-windows-10-oem-product-key-command/>.
- How to Find Windows 10 OEM Serial Number in Linux. <https://r00t4bl3.com/post/how-to-find-windows-10-oem-serial-number-in-linux>.
- Extracting Windows 10 license keys from machines - Super User. <https://superuser.com/questions/1560951/extracting-windows-10-license-keys-from-machines>.
- how to enable TPM and secure boot on kvm for Windows 11. <https://www.youtube.com/watch?v=_KmL4UmFmo4>.
- How To Enable TPM 2.0 on KVM and install Windows 11. <https://computingforgeeks.com/enable-tpm-on-kvm-and-install-windows/>.
- How to Enable TPM 2.0 and Secure Boot [Windows 11]. <https://www.youtube.com/watch?v=t7sAbEvWp5Y>.
- Windows 11 on KVM – How to Install Step by Step? – GetLabsDone. <https://getlabsdone.com/how-to-install-windows-11-on-kvm/>.
- How to enable TPM and secure boot on KVM? – GetLabsDone. <https://getlabsdone.com/how-to-enable-tpm-and-secure-boot-on-kvm/>.
- WindowsGuestDrivers/Download Drivers - KVM. <https://www.linux-kvm.org/page/WindowsGuestDrivers/Download_Drivers>.
- John Siu Blog | Windows 11 KVM Client Drivers and Tips. <https://johnsiu.com/blog/win-kvm/>.
- virtiofs - shared file system for virtual machines / Windows HowTo - GitLab. <https://virtio-fs.gitlab.io/howto-windows.html>.
- Driver installation · virtio-win/kvm-guest-drivers-windows Wiki - GitHub. <https://github.com/virtio-win/kvm-guest-drivers-windows/wiki/Driver-installation>.
- Windows VirtIO Drivers - Proxmox VE. <https://pve.proxmox.com/wiki/Windows_VirtIO_Drivers>.
- undefined. <https://fedorapeople.org/groups/virt/virtio-win/direct-downloads/latest-virtio/>.
- undefined. <http://www.linux-kvm.org/page/Virtio%29>.
- undefined. <http://ppa.launchpad.net/stefanberger/swtpm-focal/ubuntu>.
