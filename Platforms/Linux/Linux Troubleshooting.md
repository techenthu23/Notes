- [What is the difference between using 'init' and 'rd.break' for reseting the root password?](#what-is-the-difference-between-using-init-and-rdbreak-for-reseting-the-root-password)

# What is the difference between using 'init' and 'rd.break' for reseting the root password?

__Method A:__

```
1) ALT + F8 , F8 to get the Grub Menu
2) Press e when you can choose the different Kernel/OStree
3) Add rw init=/bin/sh at the end of the line starting with linux(Remove rhgb and quiet tags if necessary)
4) Ctrl+x to resume the boot process
5) Once in the bash console enter:
/usr/sbin/load_policy -i
6) If rw was not specified make / rw : mount -o remount,rw /
7) Change the password : passwd root or passwd
8) Restore selinux context
/sbin/restorecon -v /etc/passwd
/sbin/restorecon -v /etc/shadow
9) mount -o remount,ro /
10) /sbin/reboot -f
```

__Method B:__

```
- At the beginning of the boot process, at the GRUB 2 menu, type the e key to edit.
- Add rd.break to the end of the line that starts with linux. (ctrl +e)
- Press Ctrl x to resume the boot process.
- mount -o remount,rw /sysroot/
- chroot /sysroot
- passwd root or passwd 
- touch /.autorelabel  # you can kip this but after the reboot do "restorecon /etc/shadow" and then rebooot it 
- exit
- exit

```

Note: Adding rd.break to the end of the line with kernel parameters in Grub stops the start up process before the regular root filesystem is mounted (hence the necessity to chroot into sysroot). Emergency mode, on the other hand, does mount the regular root filesystem, but it only mounts it in a read-only mode. Note that you can even change to emergency mode using the systemctl command on a running system (systemctl emergency).

the other way if you don’t need to force a SELinux relabel (# touch /.autorelabel or # fixfiles onboot) or load the SELinux policy (# /usr/sbin/load_policy -i) is restorecon /etc/shadow and then rebooting it

Note: When dealing with boot problems, the following options can be added to the kernel command line, bringing additional information:

rd.debug rd.udev.debug systems.log_level=debug

I think the best way is as is shown in Red Hat documentation.
This is your second method. For GRUB2/RHEL7 single/emergency mode should not work since it will use sulogin to authenticate you before presenting the command prompt.

So lets mark off different methods.

For RHEL5, RHEL6, append 1, s or init=/bin/bash to kernel cmdline

For RHEL7, RHEL8, CentOS7, CentOS8, append rd.break or init=/bin/bash to kernel cmdline
