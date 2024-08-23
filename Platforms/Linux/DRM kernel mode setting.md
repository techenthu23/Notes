DRM kernel mode setting
Since NVIDIA does not support automatic KMS late loading, enabling DRM (Direct Rendering Manager) kernel mode setting is required to make Wayland compositors function properly, or to allow for Xorg#Rootless Xorg.
To enable it, set the modeset=1 kernel module parameter for the nvidia_drm module.      




To enable it, set the modeset=1 kernel module parameter for the nvidia_drm module.


To verify nvidia_drm.modeset=1 was correctly applied after a reboot, execute the following:

# cat /sys/module/nvidia_drm/parameters/modeset
Which should now return Y, and not N anymore.

Additionally, with the driver version 545 and above, you can also set the experimental nvidia_drm.fbdev=1 parameter, which is required to tell the NVIDIA driver to provide its own framebuffer device instead of relying on efifb or vesafb, which do not work under simpledrm.

For NVIDIA driver version < 545 or when not also using nvidia_drm.fbdev=1, the nvidia_drm.modeset=1 option must be set through kernel parameters, in order to disable simpledrm [1] (for more information, refer to FS#73720).

Note:
nvidia_drm.fbdev=1 has known issues that are only possibly fixed in the driver version 550 and above, see Talk:NVIDIA#Framebuffer consoles experimental support for more information.
NVIDIA drivers prior to version 470 (e.g. nvidia-390xx-dkmsAUR) do not support hardware accelerated Xwayland, causing non-Wayland-native applications to suffer from poor performance in Wayland sessions.
Early loading
For basic functionality, just adding the kernel parameter should suffice. If you want to ensure it is loaded at the earliest possible occasion, or are noticing startup issues (such as the nvidia kernel module being loaded after the display manager) you can add nvidia, nvidia_modeset, nvidia_uvm and nvidia_drm to the initramfs.

https://wiki.archlinux.org/title/NVIDIA#DRM_kernel_mode_setting 

# Check video drivers in use
lspci -n -n -k | grep -A 2 -e VGA -e 3D

# Check active GPU driver
glxinfo | grep -e OpenGL.vendor -e OpenGL.renderer

# List available and default GPU
switcherooctl list      



delver@192:/etc/modprobe.d$ lspci -n -n -k | grep -A 2 -e VGA -e 3D
0000:00:02.0 VGA compatible controller [0300]: Intel Corporation TigerLake-LP GT2 [Iris Xe Graphics] [8086:9a49] (rev 01)
        DeviceName: Onboard - Video
        Subsystem: Dell Device [1028:0a02]
--
0000:2b:00.0 3D controller [0302]: NVIDIA Corporation GP108M [GeForce MX330] [10de:1d16] (rev a1)
        Subsystem: Dell Device [1028:0a02]
        Kernel modules: nouveau
delver@192:/etc/modprobe.d$ 

To tell the truth, I’m not sure as my default GPU is Intel, but you can try the following:

sudo tee /etc/profile.d/custom.sh << "EOF" > /dev/null
export DRI_PRIME="1"
EOF
Or try disabling the NVIDIA GPU in the BIOS/EFI settings.







Pacman is a package manager for the arch Linux and arch-based Linux distributions. If you have used Debian-based OS like ubuntu, then the  Pacman is similar to the apt command of Debian-based operating systems. Pacman contains the compressed files as a package format and maintains a text-based package database. Pacman keeps the system up to date by synchronizing package lists with the master server. Pacman can install the packages from the official repositories or your own build packages.

In this article, we are going to see how to use Pacman to manage the software in arch-based systems. Now let’s see how to use Pacman.

Installing Packages using the Pacman
When we install any new operating system on our machine, the first task we do is to install the software packages on the system. Now, to install the packages on Arch Linux, use the command Pacman with -S option and mention the package name. The -S option tells the Pacman to synchronize and to continue. Here is one example 

sudo pacman -S cmatrix
We can mention the many package names after the -S option, separated by space.

sudo pacman -S package1 package2 package3
Then Pacman will show the download and install size of the package and ask for to proceed, then simply press the Y key. Pacman categorizes the installed packages into two categories.

Implicitly Installed: The package that was installed using the -S or -U option.
Dependencies: The package is installed because it is required by another package.