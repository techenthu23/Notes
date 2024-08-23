# systemd

Systemd is a system and service manager for Linux operating systems. It is designed to be backwards compatible with SysV init scripts, and provides a number of features such as parallel startup of system services at boot time, on-demand activation of daemons, or dependency-based service control logic.

Systemd introduces the concept of systemd units. These units are represented by unit configuration files located in one of the directories listed in below Table , and encapsulate information about system services, listening sockets, and other objects that are relevant to the init system.

It provides complete service management from startup to shutdown, starting processes at boot, on-demand after boot, and shutting down services when they are not needed. It manages functions such as system logging, automounting filesystems, automatic service dependency resolution, name services, device management, network connection management, login management, and a host of other tasks.

systemd binaries are written in C, which provides some performance enhancement. The legacy inits are masses of shell scripts, and any compiled language operates faster than shell scripts.

systemd attempts to decrease boot times and parcel out system resources more efficiently by starting processes concurrently and in parallel, and starting only necessary services, leaving other services to start after boot as needed. A service that is dependent on other services no longer has to wait to start for those services to become available because all it needs is a waiting Unix socket to become available.

some examples of the features of systemd:

- **Logging**: From the moment that the initial RAM disk is mounted to start the Linux kernel to final shutdown of the system, all log messages are stored by the new systemd journal. Before the systemd journal existed, initial boot messages were lost, requiring that you try to watch the screen as messages scrolled by to debug boot problems.
 Now, all system messages come in on a single stream and are stored in the /run directory. Messages can then be consumed by the rsyslog facility (and redirected to traditional log files in the /var/log directory or to remote log servers) or displayed using the journalctl command across a variety of attributes.

- **Dependencies**: With systemd, an explicit set of dependencies can be defined for each service, instead of being implied by boot order. This allows a service to start at any point that its dependencies are met. In this way, many services can start at the same time, making the boot process faster. Likewise, complex sets of dependencies can be set up, so the exact requirements of a service (such as storage availability or file system checking) can be met before a service starts.

- **Cgroups**: Services are identified by Cgroups, which allow every component of a service to be managed. For example, the older System V init scripts would start a service by launching a process which itself might start other child processes. When the service was killed, it was hoped that the parent process would do the right thing and kill its children. By using Cgroups, all components of a service have a tag that can be used to make sure that all of those components are properly started or stopped.

- **Activating services**: Services don't just have to be always running or not running based on runlevel, as they were previous to systemd. Services can now be activated based on path, socket, bus, timer, or hardware activation. Likewise, because systemd can set up sockets, if a process handling communications goes away, the process that starts up in its place can pick up the next message from the socket. To the clients using the service, it can look as though the service continued without interruption.

 More than services: Instead of just managing services, systemd can manage several different unit types. These unit types include:

| Unit Type      | Suffix     | Description
| -------------- | ---------- | ---------------------------------------------------------------
| Service unit   | .service   | A system service.
| Target unit    | .target    | A group of systemd units. Manage a set of services under a single unit, represented by a target name rather than a runlevel number.
| Automount unit | .automount | A file system automount point.
| Device unit    | .device    | A device file recognized by the kernel.
| Mount unit     | .mount     | A file system mount point.
| Path unit      | .path      | A file or directory in a file system.
| Scope unit     | .scope     | An externally created process.
| Slice unit     | .slice     | A group of hierarchically organized units that manage system processes. Divide up computer resources (such as CPU and memory) and apply them to selected units.
| Snapshot unit  | .snapshot  | A saved state of the systemd manager.
| Socket unit    | .socket    | An inter-process communication socket.
| Swap unit      | .swap      | A swap device or a swap file.
| Timer unit     | .timer     | A systemd timer. Trigger actions based on a timer.

 **Resource management**
 The fact that each systemd unit is always associated with its own cgroup lets you control the amount of resources each service can use. For example, you can set a percent of CPU usage by service which can put a cap on the total amount of CPU that service can use -- in other words, spinning off more processes won't allow more resources to be consumed by the service. Prior to systemd, nice levels were often used to prevent processes from hogging precious CPU time. With systemd's use of cgroups, precise limits can be set on CPU and memory usage, as well as other resources.
 A feature called slices lets you slice up many different types of system resources and assign them to users, services, virtual machines, and other units. Accounting is also done on these resources, which can allow you to charge customers for their resource usage.

systemd is backwards compatible with SysV init. Most Linux distributions retain the legacy SysV configuration files and scripts, including /etc/inittab and the /etc/rc.d/ and /etc/init.d/ directories. When a service does not have a systemd configuration file, systemd looks for a SysV configuration file. systemd is also backward compatible with Linux Standard Base (LSB) init.

## Learning if Your Linux Uses systemd

The /run/systemd/ directory may be present on your system if your distribution supports multiple init systems. But systemd is not the active init unless you see /run/systemd/system/.

There are several other ways to learn which init system your system is using. Try querying /sbin/init. Originally this was the SysV executable, and now most Linux distributions preserve the name and symlink it to the systemd executable.

The /proc/1/comm file reports your active init system. On a SysV system, it reports init:

```
$ cat /proc/1/comm
systemd
```

PID 1 is the mother of all processes on Linux systems. This is the first process to start, and then it launches all other processes. Processes are one or more running instances of a program. Every task in a Linux system is performed by a process. Processes can create independent copies of themselves, that is, they can fork. The forked copies are called children, and the original is the parent. Each child has its own unique PID, and its own allocation of system resources, such as CPU and memory. Threads are lightweight processes that run in parallel and share system resources with their parents.

Some processes run in the background and do not interact with users. Linux calls these processes services or daemons, and their names tend to end with the letter D, such as httpd, sshd, and systemd.

Processes always exist in one of several states, and these states change according to system activity.

The configuration files from configuration directories in /etc/systemd/system/ take precedence over unit files in /usr/lib/systemd/system/. Therefore, if the configuration files contain an option that can be specified only once, such as Description or ExecStart, the default value of this option is overridden. Note that in the output of the systemd-delta command, described in Monitoring overridden units ,such units are always marked as [EXTENDED], even though in sum, certain options are actually overridden.

View service processes with systemd-cgtop: To view processes associated with a particular service (cgroup), you can use the systemd-cgtop command. Like the top command (which sorts processes by such things as CPU and memory usage), systemd-cgtop lists running processes based on their service (cgroup label). Once systemd-cgtop is running, you can press keys to sort by memory (m), CPU (c), task (t), path (p), or I/O load (i). Here is an example:

```
# systemd-cgtop
```

Recursively view cgroup contents: To output a recursive list of cgroup content, use the systemd-cgls command:

```
# systemd-cgls
```

View journal (log) files: Using the journalctl command you can view messages from the systemd journal. Using different options you can select which group of messages to display. The journalctl command also supports tab completion to fill in fields for which to search. Here are some examples:

| Command                                       | Purpose
| :-------------------------------------------- | :----------------------------------------------------------------------------------------
| `journalctl –list­-boots`                      | To get a list of boots
| `journalctl -b`                               | To get all the logs for the current boot
| `journalctl -b -1`                            | To get all the log entries from the previous boot
| `journalctl --sinc­e="2­021­-01-30 18:17:16"`    | Filter logs by time we can use –since and –until parameters
| `journalctl --since "20 min ago"`             |
| `journalctl -u netcfg`                        | get the logs for a specific unit
| `journalctl -u systemd-*`                     | to see the logs of all the units starting with “systemd-“
| `journalctl --user-unit my-application`       | To check the logs if a unit isn’t a system service but a service we defined as a user
| `jour­nalctl _UID=100`                         | filter messages for units that belong to a specific user ID
| `journalctl _PID=1`                           |
| `journalctl -p err..alert`                    | to see messages with a certain priority level such as errors or warnings.
| `journalctl -p 3 -xb`                         | `-x` provides extra message information, and `-b` means since last boot. 0: emerg, 1: alert, 2: crit, 3: err, 4: warning, 5: notice, 6: info, 7: debug
| `journalctl -f`                               | To follow the logs instead of printing them
| `journalctl -u apache -n 1`                   | To just print the last 10 log messages
| `journalctl --no-pager`                       | less -S Disabling the Pager to Get Direct Output
| `journalctl –vacu­um-­siz­e=100M`                | to clean up our logs to get their disk space usage below 100 MB
| `usermod -a -G systemd-journal USER`          | Giving Permission to a User to See System Logs
| `journalctl -b -u docker -o json`             | to format our logs differently perhaps to send them to other systems to store and analyze

| Directory                | Purpose
| ------------------------ | -------
| /usr/lib/systemd/system/ | Systemd unit files distributed with installed RPM packages.
| /run/systemd/system/     | Systemd unit files created at run time. This directory takes precedence over the directory with installed service unit files.
| /etc/systemd/system/     | Systemd unit files created by systemctl enable as well as unit files added for extending a service. This directory takes precedence over the directory with runtime unit files.

Any unit file in ‘/etc/systemd/system’ will override the corresponding file in ‘/lib/systemd/system’.

`sudo journalctl --boot=-1 -g avc:..denied`
`sudo ausearch -i -m avc,user_avc,selinux_err,user_selinux_err -ts today`
# journalctl -t setroubleshoot
    #   
    # sealert -l 87fea572-9f35-47cc-8c81-eb9e9ae8cad0 
    # sealert -a /var/log/audit/audit.log | less -S