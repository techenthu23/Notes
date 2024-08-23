- [Display Server Protocol](#display-server-protocol)
    - [Purpose](#purpose)
    - [Use Cases](#use-cases)
- [Other display servers similar to Wayland](#other-display-servers-similar-to-wayland)
    - [1. X.Org (X11)](#1-xorg-x11)
    - [2. Mir](#2-mir)
    - [3. DirectFB](#3-directfb)
    - [4. SurfaceFlinger](#4-surfaceflinger)
    - [Comparison](#comparison)
- [Mir and Wayland Comparison](#mir-and-wayland-comparison)
    - [Performance Comparison](#performance-comparison)
    - [Summary](#summary)
- [Enabling Wayland on Fedorra Workstation](#enabling-wayland-on-fedorra-workstation)
    - [Steps to Enable Wayland on Fedora Workstation](#steps-to-enable-wayland-on-fedora-workstation)
    - [Additional Notes](#additional-notes)
- [How to Enable Wayland for Hybrid NVIDIA Graphics](#how-to-enable-wayland-for-hybrid-nvidia-graphics)
- [GDM3 and KDM](#gdm3-and-kdm)
    - [GDM3 (GNOME Display Manager)](#gdm3-gnome-display-manager)
    - [KDM (KDE Display Manager)](#kdm-kde-display-manager)
    - [1. Install SDDM](#1-install-sddm)
    - [2. Enable SDDM](#2-enable-sddm)
    - [3. Configure SDDM for Performance](#3-configure-sddm-for-performance)
      - [\[General\] Section](#general-section)
      - [\[Theme\] Section](#theme-section)
      - [\[X11\] Section](#x11-section)
    - [4. Use a Lightweight Greeter](#4-use-a-lightweight-greeter)
    - [5. Enable Autologin (Optional)](#5-enable-autologin-optional)
    - [6. Restart SDDM](#6-restart-sddm)
    - [Additional Tips](#additional-tips)
    - [1. Check System Logs](#1-check-system-logs)
    - [2. Verify Configuration](#2-verify-configuration)
    - [3. Optimize Graphics Settings](#3-optimize-graphics-settings)
    - [4. Add SDDM User to Video Group](#4-add-sddm-user-to-video-group)
    - [5. Monitor CPU and Memory Usage](#5-monitor-cpu-and-memory-usage)
    - [6. Check for Multiple Screens Configuration](#6-check-for-multiple-screens-configuration)
    - [7. Update SDDM and Drivers](#7-update-sddm-and-drivers)
    - [8. Disable Unnecessary Services](#8-disable-unnecessary-services)
    - [9. Test with a Different Greeter](#9-test-with-a-different-greeter)
    - [10. Reboot and Test](#10-reboot-and-test)

---

# Display Server Protocol

**Wayland** is a modern display server protocol designed to replace the older X11 system on Linux. It facilitates communication between the display server and client applications, enabling them to render graphical elements on the screen¹².

### Purpose

Wayland aims to address several limitations of X11, including:

- **Improved Performance**: By reducing the complexity and overhead associated with X11, Wayland can offer smoother and faster graphical performance.
- **Enhanced Security**: Wayland isolates applications better, reducing the risk of one application interfering with another.
- **Modern Features**: It supports modern graphics technologies and hardware more efficiently.

### Use Cases

- **Desktop Environments**: Many Linux desktop environments, such as GNOME and KDE, are transitioning to Wayland to leverage its performance and security benefits.
- **Embedded Systems**: Wayland's lightweight nature makes it suitable for embedded systems and devices with limited resources.
- **Gaming and Multimedia**: Applications that require high-performance graphics, such as games and multimedia software, can benefit from Wayland's efficient rendering capabilities.

# Other display servers similar to Wayland

### 1. X.Org (X11)

**X.Org** is the most widely used display server protocol in the Linux world. It has been around for decades and is known for its flexibility and network transparency. However, it has a complex codebase and some security concerns¹².

### 2. Mir

**Mir** was initially developed by Canonical for Ubuntu. It aims to provide a simpler and more efficient alternative to X.Org. While it started as a competitor to Wayland, it now supports Wayland clients and can act as a Wayland compositor¹.

### 3. DirectFB

**DirectFB** is a lightweight graphics library that provides a thin layer of hardware abstraction. It is designed for embedded systems and offers high performance with low resource usage. DirectFB is not as widely used as X.Org or Wayland but is suitable for specific use cases¹.

### 4. SurfaceFlinger

**SurfaceFlinger** is the display server used in Android. It manages the display of windows and surfaces on Android devices. While not typically used in traditional Linux desktop environments, it is an essential component of the Android graphics stack¹.

### Comparison

- **X.Org**: Highly flexible, supports remote applications, but has a complex and older codebase.
- **Wayland**: Modern, efficient, and secure, but still gaining full application support.
- **Mir**: Initially a competitor to Wayland, now supports Wayland clients.
- **DirectFB**: Lightweight and efficient, ideal for embedded systems.
- **SurfaceFlinger**: Specific to Android, manages display surfaces on mobile devices.

# Mir and Wayland Comparison

When comparing Mir and Wayland Comparison in terms of performance, there are several factors to consider:

### Performance Comparison

1. **Efficiency and Latency**:
   - **Wayland**: Designed to minimize latency and improve performance by allowing direct communication between applications and the compositor. This results in smoother graphics and lower input latency¹³.
   - **Mir**: Also aims to provide efficient performance, particularly optimized for Ubuntu and Unity environments. However, it may not be as universally efficient as Wayland due to its specific design².

2. **Compositing**:
   - **Wayland**: Manages compositing itself, which simplifies the graphics stack and can lead to better performance, especially in handling modern graphics technologies¹.
   - **Mir**: Initially developed to support Unity, it also handles compositing but may not be as streamlined as Wayland in non-Unity environments².

3. **Hardware Support**:
   - **Wayland**: Generally has broader support for modern hardware and graphics technologies, making it a preferred choice for many distributions¹.
   - **Mir**: While optimized for specific hardware configurations, particularly those used by Canonical, it may not offer the same level of universal hardware support as Wayland².

4. **Application Support**:
   - **Wayland**: Increasingly becoming the default in many Linux distributions, leading to better support from applications and drivers¹.
   - **Mir**: Initially had limited support outside of Ubuntu, but now supports Wayland clients, which helps improve its compatibility².

### Summary

- **Wayland**: Offers better overall performance, lower latency, and broader hardware and application support.
- **Mir**: Optimized for specific use cases, particularly within the Ubuntu ecosystem, but may not match Wayland's performance in all scenarios.

# Enabling Wayland on Fedorra Workstation

Configuring Wayland on Fedora Workstation is straightforward, as Wayland is often enabled by default. However, if you need to enable or verify Wayland, follow these steps:

Wayland is usually pre-installed. To ensure it’s installed, run

```sh
dnf install wayland
```

### Steps to Enable Wayland on Fedora Workstation

1. **Open Terminal**: You can find Terminal in your apps menu or press `Ctrl + Alt + T`.

2. **Edit the GDM Configuration File**:
   - Run the following command to open the configuration file in a text editor (e.g., nano):

     ```bash
     sudo nano /etc/gdm/custom.conf
     ```

   - Look for the line that says `#WaylandEnable=false`. Uncomment this line and change it to:

     ```bash
     WaylandEnable=true
     ```

3. **Save and Exit**:
   - Save the file by pressing `Ctrl + O`, then press `Enter`.
   - Exit the editor by pressing `Ctrl + X`.

4. **Restart GDM**:
   - Apply the changes by restarting the GDM service:

     ```bash
     sudo systemctl restart gdm
     ```

5. **Log In to Wayland Session**:
   - On the login screen, click on the gear icon and select the "GNOME on Wayland" option before logging in.

6. **Verify the Session**:
   - After logging in, you can verify that you are using Wayland by running:

     ```bash
     echo $XDG_SESSION_TYPE
     ```

   - It should return `wayland`.

### Additional Notes

- **Compatibility**: Some applications and drivers may not fully support Wayland yet. If you encounter issues, you can switch back to Xorg by following similar steps and setting `WaylandEnable=false`.
- **Hybrid Graphics**: If you have a hybrid NVIDIA graphics setup, you might need additional configuration. For example, you may need to copy and modify specific udev rules².

# How to Enable Wayland for Hybrid NVIDIA Graphics

1. Ensure the line `WaylandEnable=true` is there in `/etc/gdm/custom.conf`

2. Ensure `/usr/lib/udev/rules.d/61-gdm.rules` file is there in  `/etc/udev/rules.d/`

3. Comment out the entire `# Check if suspend/resume services necessary for working wayland support is available section` by adding an # in front of each line. It should look like this:

```sh
# Check if suspend/resume services necessary for working wayland support is available
#TEST{0711}!="/usr/bin/nvidia-sleep.sh", GOTO="gdm_disable_wayland"
#TEST{0711}!="/usr/lib/systemd/system-sleep/nvidia", GOTO="gdm_disable_wayland"
#IMPORT{program}="/bin/sh -c \"sed -e 's/: /=/g' -e 's/\([^[:upper:]]\)\([[:upper:]]\)/\1_\2/g' -e 's/[[:lower:]]/\U&/g' -e 's/^/NVIDIA_/' /p>
#ENV{NVIDIA_PRESERVE_VIDEO_MEMORY_ALLOCATIONS}!="1", GOTO="gdm_disable_wayland"
#IMPORT{program}="/bin/sh -c 'echo NVIDIA_HIBERNATE=`systemctl is-enabled nvidia-hibernate`'"
#ENV{NVIDIA_HIBERNATE}!="enabled", GOTO="gdm_disable_wayland"
#IMPORT{program}="/bin/sh -c 'echo NVIDIA_RESUME=`systemctl is-enabled nvidia-resume`'"
#ENV{NVIDIA_RESUME}!="enabled", GOTO="gdm_disable_wayland"
#IMPORT{program}="/bin/sh -c 'echo NVIDIA_SUSPEND=`systemctl is-enabled nvidia-suspend`'"
#ENV{NVIDIA_SUSPEND}!="enabled", GOTO="gdm_disable_wayland"
#LABEL="gdm_nvidia_end"
```

This is required due to bug from GNOME 43 and may have been done intentionally on Fedora Linux 38 to prevent sleep/hibernate issues on certain systems using NVIDIA GPU

# GDM3 and KDM

**GDM3** and **KDM** are both display managers used in Linux systems. They provide graphical logins and handle user authentication.

### GDM3 (GNOME Display Manager)

- **Purpose**: GDM3 is the default display manager for GNOME-based Linux distributions like Ubuntu.
- **Features**:
  - Provides a graphical login screen.
  - Manages user sessions.
  - Supports both Xorg and Wayland.
  - Offers accessibility options and user switching.
- **Installation**:

  ```bash
  sudo apt install gdm3
  ```

  - To enable GDM3:

    ```bash
    sudo systemctl enable gdm
    ```

### KDM (KDE Display Manager)

- **Purpose**: KDM was the display manager for KDE desktop environments.
- **Features**:
  - Provided a graphical login screen.
  - Managed user sessions.
  - Supported various themes and customization.
- **Status**: KDM has been deprecated in favor of SDDM (Simple Desktop Display Manager) in KDE5.
- **Installation**:
  - Instead of KDM, you can install SDDM:

    ```bash
    sudo apt install sddm
    ```

  - To enable SDDM:

    ```bash
    sudo systemctl enable sddm
    ```

**KDM** (KDE Display Manager) has been deprecated and replaced by **SDDM** (Simple Desktop Display Manager) in KDE Plasma 5. Since KDM is deprecated, it is highly recommended to use SDDM for better support and compatibility with Wayland. Here are the steps to configure SDDM with Wayland:

Configuring SDDM (Simple Desktop Display Manager) for performance involves optimizing its settings and ensuring it runs efficiently. Here are some steps you can take:

### 1. Install SDDM

Ensure SDDM is installed on your system. For example, on Debian-based systems:

```bash
sudo apt install sddm
```

On Fedora:

```bash
sudo dnf install sddm
```

On Arch Linux:

```bash
sudo pacman -S sddm
```

### 2. Enable SDDM

Set SDDM as the default display manager:

```bash
sudo systemctl enable sddm
sudo systemctl start sddm
```

### 3. Configure SDDM for Performance

Edit the SDDM configuration file to optimize performance:

```bash
sudo nano /etc/sddm.conf
```

Here are some key settings to adjust:

#### [General] Section

- **Numlock**: Enable numlock for faster numeric input.

  ```ini
  [General]
  Numlock=on
  ```

#### [Theme] Section

- **Current**: Use a lightweight theme to reduce resource usage.

  ```ini
  [Theme]
  Current=maya
  ```

#### [X11] Section

- **ServerArguments**: Optimize X11 server arguments for better performance.

  ```ini
  [X11]
  ServerArguments=-nolisten tcp -dpi 96
  ```

### 4. Use a Lightweight Greeter

Choose a lightweight greeter to minimize resource usage. For example, `lightdm-gtk-greeter` is a good option:

```bash
sudo apt install lightdm-gtk-greeter
```

Configure SDDM to use this greeter:

```ini
[Greeter]
Session=lightdm-gtk-greeter
```

### 5. Enable Autologin (Optional)

If you want to speed up the login process, you can enable autologin:

```ini
[Autologin]
User=your_username
Session=plasma
```

### 6. Restart SDDM

Apply the changes by restarting SDDM:

```bash
sudo systemctl restart sddm
```

### Additional Tips

- **Disable Unnecessary Services**: Ensure that unnecessary services are disabled to free up system resources.
- **Monitor Performance**: Use tools like `htop` or `systemd-analyze` to monitor system performance and identify bottlenecks.

Troubleshooting performance issues with SDDM (Simple Desktop Display Manager) involves several steps to identify and resolve potential problems. Here are some common troubleshooting steps:

### 1. Check System Logs

Examine system logs for any errors or warnings related to SDDM:

```bash
journalctl -u sddm.service
```

Look for any recurring errors or warnings that might indicate the source of the problem.

### 2. Verify Configuration

Ensure that your SDDM configuration is correct. Edit the configuration file:

```bash
sudo nano /etc/sddm.conf
```

Check for any misconfigurations or unnecessary options that might be affecting performance.

### 3. Optimize Graphics Settings

Ensure that SDDM is using optimal graphics settings. For example, you can enforce the use of OpenGL for better performance:

```ini
[General]
DisplayServer=wayland

[X11]
ServerArguments=-nolisten tcp -dpi 96
```

### 4. Add SDDM User to Video Group

Adding the SDDM user to the video group can help with performance issues:

```bash
sudo usermod -a -G video sddm
```

### 5. Monitor CPU and Memory Usage

Use tools like `htop` or `top` to monitor CPU and memory usage. Identify any processes that are consuming excessive resources and investigate further.

### 6. Check for Multiple Screens Configuration

If you are using multiple screens, ensure that they are configured correctly. Misconfigured screens can cause performance issues. You can set up the correct configuration in the `/usr/share/sddm/scripts/Xsetup` file:

```bash
sudo nano /usr/share/sddm/scripts/Xsetup
```

Add the appropriate `xrandr` commands to configure your screens:

```bash
#!/bin/sh
xrandr --output DP-1 --off
xrandr --output DP-5 --mode 1920x1080 --pos 0x0 --rotate normal
xrandr --output DP-3 --mode 1920x1080 --pos 1920x0 --rotate normal
```

### 7. Update SDDM and Drivers

Ensure that you are using the latest version of SDDM and your graphics drivers. Outdated software can cause performance issues:

```bash
sudo apt update
sudo apt upgrade
```

### 8. Disable Unnecessary Services

Disable any unnecessary services that might be running in the background and consuming resources:

```bash
sudo systemctl disable <service_name>
```

### 9. Test with a Different Greeter

Sometimes, the greeter used by SDDM can cause performance issues. Try switching to a different greeter, such as `lightdm-gtk-greeter`:

```bash
sudo apt install lightdm-gtk-greeter
```

Configure SDDM to use this greeter:

```ini
[Greeter]
Session=lightdm-gtk-greeter
```

### 10. Reboot and Test

After making these changes, reboot your system and test the performance of SDDM.

Would you like more detailed instructions on any specific step or additional tips for optimizing your system?¹²³⁵⁶

Source: Conversation with Copilot, 18/8/2024
(1) SDDM - Gentoo wiki. <https://wiki.gentoo.org/wiki/SDDM>.
(2) SDDM fails to start when DisplayServer=x11 is set on distro ... - GitHub. <https://github.com/sddm/sddm/issues/1835>.
(3) high cpu when sddm-greeter is idle · Issue #323 - GitHub. <https://github.com/sddm/sddm/issues/323>.
(4) How to Fix Sddm on Multiple Screens · Victor's Blog. <https://blog.victormendonca.com/2018/06/29/how-to-fix-sddm-on-multiple-screens/>.
(5) Fix Wrong Resolution on Login Screen: SDDM and LightDM Troubleshooting .... <https://www.youtube.com/watch?v=Uw-lp9eTrlA>.
(6) undefined. <https://bugzilla.redhat.com/show_bug.cgi?id=2251988>.
(7) SDDM - ArchWiki. <https://wiki.archlinux.org/title/SDDM>.

Source: Conversation with Copilot, 18/8/2024
(1) How to customize the SDDM display manager on Linux. <https://linuxconfig.org/how-to-customize-the-sddm-display-manager-on-linux>.
(2) SDDM - ArchWiki. <https://wiki.archlinux.org/title/SDDM>.
(3) SDDM - Debian Wiki. <https://wiki.debian.org/SDDM>.
(4) What Is a Linux Display Manager? How to Choose and Set One - MUO. <https://www.makeuseof.com/tag/choose-switch-linux-display-managers/>.
(5) en.wikipedia.org. <https://en.wikipedia.org/wiki/Simple_Desktop_Display_Manager>.

Source: Conversation with Copilot, 18/8/2024
(1) Tutorials/Using Other Window Managers with Plasma - KDE. <https://userbase.kde.org/Tutorials/Using_Other_Window_Managers_with_Plasma>.
(2) Remote Desktop using the RDP protocol for Plasma Wayland. <https://discuss.kde.org/t/remote-desktop-using-the-rdp-protocol-for-plasma-wayland/3616>.
(3) Stable DE to use with Wayland? How (without systemd)? - Artix Linux Forum. <https://forum.artixlinux.org/index.php/topic,4430.0.html>.
(4) undefined. <https://quantumproductions.info/articles/2023-08/remote-desktop-using-rdp-protocol-plasma-wayland>.
(5) undefined. <https://askubuntu.com/questions/1017249/samsung-tv-mirror-screen>.
(6) en.wikipedia.org. <https://en.wikipedia.org/wiki/Wayland_(protocol)>.

Source: Conversation with Copilot, 18/8/2024
(1) What is gdm3, kdm, lightdm? How to install and remove them?. <https://askubuntu.com/questions/829108/what-is-gdm3-kdm-lightdm-how-to-install-and-remove-them/1365991>.
(2) GDM3 vs LightDM Which One Is Better? - LinuxForDevices. <https://www.linuxfordevices.com/tutorials/linux/gdm3-vs-lightdm>.
(3) What is gdm3, kdm, lightdm? How to Install and Remove them on Ubuntu?. <https://itslinuxfoss.com/gdm3-kdm-lightdm-install-and-remove/>.

Source: Conversation with Copilot, 18/8/2024
(1) How to Enable Wayland for Hybrid NVIDIA Graphics on Fedora ... - 9to5Linux. <https://9to5linux.com/how-to-enable-wayland-for-hybrid-nvidia-graphics-on-fedora-linux-38-workstation>.
(2) The Wayland Protocol :: Fedora Docs. <https://docs.fedoraproject.org/en-US/fedora/latest/system-administrators-guide/Wayland/>.
(3) Enable Wayland for Hybrid NVIDIA Graphics on Fedora 38 Workstation. <https://www.linuxtoday.com/developer/enable-wayland-for-hybrid-nvidia-graphics-on-fedora-38-workstation/>.
(4) Configuring Xorg as the default GNOME session :: Fedora Docs. <https://docs.fedoraproject.org/en-US/quick-docs/configuring-xorg-as-default-gnome-session/>.
(5) Changes/WaylandByDefault - Fedora Project Wiki. <https://fedoraproject.org/wiki/Changes/WaylandByDefault>.
(6) undefined. <https://wayland.freedesktop.org/>.
(7) undefined. <https://fedoraproject.org/wiki/How_to_debug_Wayland_problems>.
(8) en.wikipedia.org. <https://en.wikipedia.org/wiki/Wayland_(protocol)>.

Source: Conversation with Copilot, 18/8/2024
(1) Wayland vs Mir detailed comparison as of 2024 - Slant. <https://www.slant.co/versus/8634/8633/~wayland_vs_mir>.
(2) Wayland vs. X11: The Battle of Display Protocols - linuxMO. <https://www.linuxmo.com/wayland-vs-x11-the-battle-of-display-protocols/>.
(3) Xorg vs. Wayland vs. Mir - Ubunlog. <https://ubunlog.com/en/xorg-vs-mir-vs-wayland/>.
(4) Does The Display Server Matter? The Latest Mir vs. Wayland Argument. <https://www.phoronix.com/news/MTY0MTM>.

Source: Conversation with Copilot, 18/8/2024
(1) 3 Best Linux display servers as of 2024 - Slant. <https://www.slant.co/topics/2413/~best-linux-display-servers>.
(2) Wayland v/s Xorg : How Are They Similar & How Are They Different - Secjuice. <https://www.secjuice.com/wayland-vs-xorg/>.
(3) Using Linux With Wayland? What You Need to Know - MUO. <https://www.makeuseof.com/tag/using-linux-with-wayland/>.
(4) Windowing system - Wikipedia. <https://en.wikipedia.org/wiki/Windowing_system>.

Source: Conversation with Copilot, 18/8/2024
(1) What Is Wayland on Linux, and How Is It Different From X? - How-To Geek. <https://www.howtogeek.com/900698/what-is-wayland-on-linux-and-how-is-it-different-from-x/>.
(2) What Is Wayland and What Does It Mean for Linux Users?. <https://www.maketecheasier.com/what-is-wayland/>.
(3) Wayland (protocol) - Wikipedia. <https://en.wikipedia.org/wiki/Wayland_%28protocol%29>.
(4) What is Wayland? - Medium. <https://medium.com/@deepinlinux/what-is-wayland-7e316b42ef13>.
(5) en.wikipedia.org. <https://en.wikipedia.org/wiki/Wayland_(protocol)>.
