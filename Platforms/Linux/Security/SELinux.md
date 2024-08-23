- [SELinux](#selinux)
- [SElinux Context Fields / Labels](#selinux-context-fields--labels)
  - [SELinux user](#selinux-user)
    - [Mapping Linux Users to SELinux Users](#mapping-linux-users-to-selinux-users)
  - [SELinux roles](#selinux-roles)
  - [SELinux type](#selinux-type)
  - [SELinux level](#selinux-level)
- [SELinux architecture](#selinux-architecture)
- [SELinux states and modes](#selinux-states-and-modes)
- [Enabling SELinux](#enabling-selinux)
- [Changing SELinux Modes at Boot Time](#changing-selinux-modes-at-boot-time)
- [Identifying SELinux denials](#identifying-selinux-denials)
- [Analyzing SELinux denial messages](#analyzing-selinux-denial-messages)
- [Fixing analyzed SELinux denials](#fixing-analyzed-selinux-denials)
  - [Labeling problems](#labeling-problems)
  - [Incorrect Context](#incorrect-context)
  - [Confined applications configured in non-standard ways](#confined-applications-configured-in-non-standard-ways)
  - [Port numbers](#port-numbers)
  - [Corner cases, evolving or broken applications, and compromised systems](#corner-cases-evolving-or-broken-applications-and-compromised-systems)
  - [Fix SELinux denials by allowing requested access](#fix-selinux-denials-by-allowing-requested-access)
- [SELinux settings with booleans](#selinux-settings-with-booleans)

# SELinux

Security-Enhanced Linux (SELinux) is a Linux kernel security module that provides a mechanism for supporting access control security policies, including mandatory access controls (MAC).

SELinux is a set of kernel modifications and user-space tools that have been added to various Linux distributions. Its architecture strives to separate enforcement of security decisions from the security policy, and streamlines the amount of software involved with security policy enforcement

SELinux can potentially control which activities a system allows each user, process, and daemon, with very precise specifications. It is used to confine daemons such as database engines or web servers that have clearly defined data access and activity rights. This limits potential harm from a confined daemon that becomes compromised.

The SELinux policy uses these contexts in a series of rules which define how processes can interact with each other and the various system resources. By default, the policy does not allow any interaction unless a rule explicitly grants access.

> It is important to remember that SELinux policy rules are checked after DAC rules. SELinux policy rules are not used if DAC rules deny access first, which means that no SELinux denial is logged if the traditional DAC rules prevent the access.

SELinux features include:

- Clean separation of policy from enforcement
- Well-defined policy interfaces
- Support for applications querying the policy and enforcing access control (for example, crond running jobs in the correct context)
- Independence of specific policies and policy languages
- Independence of specific security-label formats and contents
- Individual labels and controls for kernel objects and services
- Support for policy changes
- Separate measures for protecting system integrity (domain-type) and data confidentiality (multilevel security)
- Flexible policy
- Controls over process initialization and inheritance, and program execution
- Controls over file systems, directories, files, and open file descriptors
- Controls over sockets, messages, and network interfaces
- Controls over the use of "capabilities"
- Cached information on access-decisions via the Access Vector Cache (AVC)
- Default-deny policy (anything not explicitly specified in the policy is disallowed)

- All processes and files are labeled. SELinux policy rules define how processes interact with files, as well as how processes interact with each other. Access is only allowed if an SELinux policy rule exists that specifically allows it.

- Fine-grained access control. Stepping beyond traditional UNIX permissions that are controlled at user discretion and based on Linux user and group IDs, SELinux access decisions are based on all available information, such as an SELinux user, role, type, and, optionally, a security level.

- SELinux policy is administratively-defined and enforced system-wide.

- Improved mitigation for privilege escalation attacks. Processes run in domains, and are therefore separated from each other. SELinux policy rules define how processes access files and other processes. If a process is compromised, the attacker only has access to the normal functions of that process, and to files the process has been configured to have access to. For example, if the Apache HTTP Server is compromised, an attacker cannot use that process to read files in user home directories, unless a specific SELinux policy rule was added or configured to allow such access.

- SELinux can be used to enforce data confidentiality and integrity, as well as protecting processes from untrusted inputs.

However, SELinux is not:
    - antivirus software,
    - replacement for passwords, firewalls, and other security systems,
    - all-in-one security solution.

# SElinux Context Fields / Labels

The SELinux labels consist of four parts, User, Role, Type and Security Level.  Often look something like `user_u:role_r:type_t:level`

The SELinux type information is perhaps the most important when it comes to the SELinux policy, as the most common policy rule which defines the allowed interactions between processes and system resources uses SELinux types and not the full SELinux context. SELinux types usually end with `_t`.

For example, the type name for the web server is `httpd_t`. The type context for files and directories normally found in /`var/www/html/` is `httpd_sys_content_t`. The type contexts for files and directories normally found in /tmp and `/var/tmp/` is `tmp_t`. The type context for web server ports is `http_port_t.`

> New files typically inherit their SELinux context from the parent directory, thus ensuring that they have the proper context.

> if you create a file in a different location from the ultimate intended location and then move the file, the file still has the SELinux context of the directory where it was created, not the destination directory.

> if you copy a file preserving the SELinux context, as with the cp -a command, the SELinux context reflects the location of the original file.

## SELinux user

There are SELinux users in addition to the regular Linux users. SELinux users are part of an SELinux policy. The policy is authorised for a specific set of roles and for a specific MLS (Multi-Level Security) range. Each Linux user is mapped to an SELinux user as part of the policy. This allows Linux users to inherit the restrictions and security rules and mechanisms placed on SELinux users.

A process owned by a Linux user receives the mapped SELinux user's identity to assume the corresponding SELinux roles and levels.

The SELinux user is an identity known to the policy that's authorized for a specific set of roles and has a particular level that's designated by an MLS/MCS range.

- Confined and unconfined users

    Each Linux user is mapped to an SELinux user identity using SELinux policy. This allows Linux users to inherit the restrictions on SELinux users.

    In Red Hat Enterprise Linux, Linux users are mapped to the SELinux `__default__` login by default, which is mapped to the SELinux `unconfined_u` user.

    Confined users are restricted by SELinux rules explicitly defined in the current SELinux policy. Unconfined users are subject to only minimal restrictions by SELinux.

    Confined and unconfined Linux users are subject to executable and writable memory checks, and are also restricted by MCS or MLS.

    If an unconfined Linux user executes an application that SELinux policy defines as one that can transition from the unconfined_t domain to its own confined domain, the unconfined Linux user is still subject to the restrictions of that confined domain. The security benefit of this is that, even though a Linux user is running unconfined, the application remains confined. Therefore, the exploitation of a flaw in the application can be limited by the policy.

    Similarly, we can apply these checks to confined users. Each confined user is restricted by a confined user domain. The SELinux policy can also define a transition from a confined user domain to its own target confined domain. In such a case, confined users are subject to the restrictions of that target confined domain. The main point is that special privileges are associated with the confined users according to their role.

`sudo semanage login -l` displays a list of mappings between Linux accounts and their corresponding SELinux user identities.

```
$ sudo semanage login -l
Login Name           SELinux User         MLS/MCS Range        Service

__default__          unconfined_u         s0-s0:c0.c1023       *
root                 unconfined_u         s0-s0:c0.c1023       *
```

`sudo semanage user -l`displays a list of all SELinux users, their SELinux roles, and MLS/MCS levels and ranges

```
$ sudo semanage user -l
                Labeling   MLS/       MLS/                          
SELinux User    Prefix     MCS Level  MCS Range                      SELinux Roles

guest_u         user       s0         s0                             guest_r
root            user       s0         s0-s0:c0.c1023                 staff_r sysadm_r system_r unconfined_r
staff_u         user       s0         s0-s0:c0.c1023                 staff_r sysadm_r system_r unconfined_r
sysadm_u        user       s0         s0-s0:c0.c1023                 sysadm_r
system_u        user       s0         s0-s0:c0.c1023                 system_r unconfined_r
unconfined_u    user       s0         s0-s0:c0.c1023                 system_r unconfined_r
user_u          user       s0         s0                             user_r
xguest_u        user       s0         s0                             xguest_r
```

- `system_u` is the default specical user for all processes started at boot or started by systemd.  
- SELinux users are inherited by children processes by default.

`seinfo -u` To list the available SELinux users

`id -Z` to list the context of the user

> Note that the seinfo command is provided by the setools-console package, which is not installed by default.

A number of confined SELinux users exist in SELinux policy. The following is a list of confined SELinux users and their associated domains:

    - guest_u: The domain for the user is guest_t.
    - staff_u: The domain for the user is staff_t.
    - user_u: The domain for the user is user_t.
    - xguest_x: The domain for the user is xguest_t.
  
- Linux users in the `guest_t`, `xguest_t`, and `user_t` domains can run set user ID (setuid) applications only if the SELinux policy permits it (such as passwd). They cannot run the su and sudo setuid applications to become the root user.
- Linux users in the `guest_t` domain have no network access and can log in only from a terminal. They can log in with ssh but cannot use ssh to connect to another system. The only network access Linux users in the `xguest_t` domain have is Firefox for connecting to webpages.
- Linux users in the `xguest_t`, `user_t`, and `staff_t` domains can log in using the X Window System and a terminal.
- By default, Linux users in the `staff_t` domain do not have permissions to execute applications with the sudo command.
- By default, Linux users in the `guest_t` and `xguest_t` domains cannot execute applications in their home directories or /tmp, preventing them from executing applications in directories they have write access to. This helps prevent flawed or malicious applications from modifying files that users own.
- By default, Linux users in the `user_t` and `staff_t` domains can execute applications in their home directories and /tmp.

### Mapping Linux Users to SELinux Users

Use the `semanage login –a` command to map a Linux user to an SELinux user. For example, to map the Linux user `john` to the SELinux `user_u` user, run the following command:

```
# semanage login -a -s user_u john
```

The `-a` option adds a new record and the `-s` option specifies the SELinux user. The last argument, newuser, is the Linux user that you want mapped to the specified SELinux user.

## SELinux roles

SELinux roles are part of the RBAC security model, and they are essentially RBAC attributes. In the SELinux context hierarchy, users are authorized for roles, and roles are authorized for types or domains. In the SELinux context terminology, types refer to filesystem object types and domains refer to process types

Take Linux processes, for example. The SELinux role serves as an intermediary access layer between domains and SELinux users. An accessible role determines which domain (that is, processes) can be accessed through that role. Ultimately, this mechanism controls which object types can be accessed by the process, thus minimizing the surface for privilege escalation attacks.

SELinux has the following confined administrator roles:

- auditadm_r
    The audit administrator role allows managing the Audit subsystem.
- dbadm_r
    The database administrator role allows managing MariaDB and PostgreSQL databases.
- logadm_r
    The log administrator role allows managing logs.
- webadm_r
    The web administrator allows managing the Apache HTTP Server.
- secadm_r
    The security administrator role allows managing the SELinux database.
- sysadm_r
    The system administrator role allows doing everything of the previously listed roles and has additional privileges. In non-default configurations, security administration can be separated from system administration by disabling the sysadm_secadm module in the SELinux policy.

To list all available roles, enter the `seinfo -r` command

## SELinux type

The SELinux type is an attribute of SELinux Type Enforcement (TE) – a MAC security construct.

The type defines a type for files, and defines a domain for processes. Processes are separated from each other by running in their own domains. This separation prevents processes from accessing files used by other processes, as well as preventing processes from accessing other processes. SELinux policy rules define how types can access each other, whether it is a domain accessing a type, or a domain accessing another domain.

Targeted: Processes that are targeted run in a confined domain, and processes that are not targeted run in an unconfined domain
Multi-level security (mls): Control processes (domains) based on the level of the data they will be using
Multi-category security (mcs): Protects like processes from each other (like VMs, OpenShift Gears, SELinux sandboxes, containers, etc.)

## SELinux level

The SELinux level is an attribute of the MLS/MCS schema and an optional field in the SELinux context. A level usually refers to the security clearance of a subject's access control to an object. Levels of clearance include unclassified, confidential, secret, and top-secret and are expressed as a range.

An MLS range is a pair of levels, written as lowlevel- highlevel if the levels differ, or lowlevel if the levels are identical (s0-s0 is the same as s0). Each level is a sensitivity-category pair, with categories being optional. If there are categories, the level is written as sensitivity:category-set. If there are no categories, it is written as sensitivity.

If the category set is a contiguous series, it can be abbreviated. For example, c0.c3 is the same as c0,c1,c2,c3. The `/etc/selinux/targeted/setrans.conf` file is the Multi-Category Security translation table for SELinux and maps levels to human-readable form such as `s0:c0.c1023=SystemHigh`. Do not edit this file with a text editor; use the semanage command to make changes.

Use the `chcon` command to change the SELinux context for files. Changes made with the `chcon` command do not survive a file system relabel or the execution of the restorecon command. When using `chcon`, provide all or part of the SELinux context to change.

# SELinux architecture

SELinux is a Linux Security Module (LSM) that is built into the Linux kernel. The SELinux subsystem in the kernel is driven by a security policy which is controlled by the administrator and loaded at boot. All security-relevant, kernel-level access operations on the system are intercepted by SELinux and examined in the context of the loaded security policy. If the loaded policy allows the operation, it continues. Otherwise, the operation is blocked and the process receives an error.

SELinux decisions, such as allowing or disallowing access, are cached. This cache is known as the Access Vector Cache (AVC). When using these cached decisions, SELinux policy rules need to be checked less, which increases performance. Remember that SELinux policy rules have no effect if DAC rules deny access first. :experimental:

# SELinux states and modes

SELinux can run in one of three modes: disabled, permissive, or enforcing.

Disabled mode is strongly discouraged; not only does the system avoid enforcing the SELinux policy, it also avoids labeling any persistent objects such as files, making it difficult to enable SELinux in the future.

In permissive mode, the system acts as if SELinux is enforcing the loaded security policy, including labeling objects and emitting access denial entries in the logs, but it does not actually deny any operations. While not recommended for production systems, permissive mode can be helpful for SELinux policy development.

Enforcing mode is the default, and recommended, mode of operation; in enforcing mode SELinux operates normally, enforcing the loaded security policy on the entire system.

Use the setenforce utility to change between enforcing and permissive mode. Changes made with setenforce do not persist across reboots. To change to enforcing mode, enter the setenforce 1 command as the Linux root user. To change to permissive mode, enter the setenforce 0 command. Use the getenforce utility to view the current SELinux mode:

```
~]# getenforce
Enforcing

~]# setenforce 0
~]# getenforce
Permissive

~]# setenforce 1
~]# getenforce
Enforcing
```

> In Fedora, you can set individual domains to permissive mode while the system runs in enforcing mode. For example, to make the httpd_t domain permissive:

```
~]# semanage permissive -a httpd_t
```

The `sestatus` command returns the SELinux status and the SELinux policy being used:

```
~]$ sestatus
SELinux status:                 enabled
SELinuxfs mount:                /sys/fs/selinux
SELinux root directory:         /etc/selinux
Loaded policy name:             targeted
Current mode:                   enforcing
Mode from config file:          enforcing
Policy MLS status:              enabled
Policy deny_unknown status:     allowed
Memory protection checking:     actual (secure)
Max kernel policy version:      31
```

> When systems run SELinux in permissive mode, users and processes can label various file-system objects incorrectly. File-system objects created while SELinux is disabled are not labeled at all. This behavior causes problems when changing to enforcing mode because SELinux relies on correct labels of file-system objects.

> To prevent incorrectly labeled and unlabeled files from causing problems, file systems are automatically relabeled when changing from the disabled state to permissive or enforcing mode.

> In permissive mode, use the `fixfiles -F onboot` command as root to create `/.autorelabel` file containing the `-F` option to ensure that files are relabeled upon next reboot.

# Enabling SELinux

Prerequisites

- The selinux-policy-targeted, selinux-policy, libselinux-utils, and grubby packages are installed.
- Remove the `selinux=0` option in your kernel command line as it disables the SELinux at the kernel level

    ```
    $ cat /proc/cmdline
    BOOT_IMAGE=... ... selinux=0

    sudo grubby --update-kernel ALL --remove-args selinux
    ```

    The change applies after you restart the system

- Ensure the file system is relabeled on the next boot: `sudo fixfiles onboot`
- Enable SELinux in permissive mode.
- Restart your system
- Check for SELinux denial messages.

  ```
  sudo ausearch -m AVC,USER_AVC,SELINUX_ERR,USER_SELINUX_ERR -ts recent
  ```

- If there are no denials, switch to enforcing mode.

To run custom applications with SELinux in enforcing mode, choose one of the following scenarios:

- Run your application in the unconfined_service_t domain.
- Write a new policy for your application. Refer [Writing a custom SELinux policy](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/using_selinux/writing-a-custom-selinux-policy_using-selinux)

to permanently change SELinux mode to permissive, Ensure `selinux=0` or `enforcing=0` kernel parameters are not used. Open the `/etc/selinux/config` file in a text editor of your choice and set the option `SELINUX=permissive` and restart the system

# Changing SELinux Modes at Boot Time

- enforcing=0

    Setting this parameter causes the system to start in permissive mode, which is useful when troubleshooting issues. Using permissive mode might be the only option to detect a problem if your file system is too corrupted. Moreover, in permissive mode, the system continues to create the labels correctly. The AVC messages that are created in this mode can be different than in enforcing mode.

    In permissive mode, only the first denial from a series of the same denials is reported. However, in enforcing mode, you might get a denial related to reading a directory, and an application stops. In permissive mode, you get the same AVC message, but the application continues reading files in the directory and you get an AVC for each denial in addition.

- selinux=0

    This parameter causes the kernel to not load any part of the SELinux infrastructure. The init scripts notice that the system booted with the selinux=0 parameter and touch the /.autorelabel file. This causes the system to automatically relabel the next time you boot with SELinux enabled.

    > Using the selinux=0 parameter is not recommended. To debug your system, prefer using permissive mode.

- autorelabel=1

    This parameter forces the system to relabel similarly to the following commands:

    ```
    # touch /.autorelabel
    # reboot
    ```

    If a file system contains a large amount of mislabeled objects, start the system in permissive mode to make the autorelabel process successful.

# Identifying SELinux denials

Follow only the necessary steps from this procedure; in most cases, you need to perform just step 1.

Procedure

1. When your scenario is blocked by SELinux, the /var/log/audit/audit.log file is the first place to check for more information about a denial.

    Use aureport to get a broad overview

    ```
    aureport -a   # Get a report of all AVC denial events
    aureport -i -a | awk 'NR==4 || NR>5' | column -t   # Interpret syscall numbers to names; show in columnized list
    ```

    Use ausearch to drill down. Because the SELinux decisions, such as allowing or disallowing access, are cached and this cache is known as the Access Vector Cache (AVC), use the AVC and USER_AVC values for the message type parameter, for example:

    ```
    ausearch -i -m avc   # Show all from standard audit.log files
    ausearch -i -m avc -ts recent   # Show last 10 minutes
    ausearch -m AVC,USER_AVC,SELINUX_ERR,USER_SELINUX_ERR -ts recent
    ausearch -i -m avc -ts today -c httpd   # Show particular command since midnight
    ausearch -i -m avc -ts 16:05 -su httpd_t   # Show particular source (subject) SELinux context since 16:05 PM
    ```

    If there are no matches, check if the Audit daemon is running. If it does not, repeat the denied scenario after you start auditd and check the Audit log again.

    An SELinux denial entry in the Audit log file can look as follows:

    ```
    type=AVC msg=audit(1395177286.929:1638): avc:  denied  { read } for  pid=6591 comm="httpd" name="webpages" dev="0:37" ino=2112 scontext=system_u:system_r:httpd_t:s0 tcontext=system_u:object_r:nfs_t:s0 tclass=dir
    ```

    The most important parts of this entry are:

    - `avc`: denied - the action performed by SELinux and recorded in Access Vector Cache (AVC)

    - `{ read }` - the denied action

    - `pid=6591` - the process identifier of the subject that tried to perform the denied action

    - `comm="httpd"`- the name of the command that was used to invoke the analyzed process

    - `httpd_t` - the SELinux type of the process

    - `nfs_t`- the SELinux type of the object affected by the process action

    - `tclass=dir` - the target object class

2. In case auditd is running, but there are no matches in the output of ausearch, check messages provided by the systemd Journal:

    ```
    # journalctl -t setroubleshoot
    #   
    # sealert -l 87fea572-9f35-47cc-8c81-eb9e9ae8cad0 
    # sealert -a /var/log/audit/audit.log | less -S
    ```

3. If SELinux is active and the Audit daemon is not running on your system, then search for certain SELinux messages in the output of the dmesg command:

    ```
    # dmesg | grep -i -e type=1300 -e type=1400
    ```

4. Even after the previous three checks, it is still possible that you have not found anything. In this case, AVC denials can be silenced because of dontaudit rules.

    To temporarily disable dontaudit rules, allowing all denials to be logged:

    ```
    # semodule -DB
    ```

    After re-running your denied scenario and finding denial messages using the previous steps, the following command enables dontaudit rules in the policy again:

    ```
    # semodule -B
    ```

5. If you apply all four previous steps, and the problem still remains unidentified, consider if SELinux really blocks your scenario:

    Switch to permissive mode:

```
    # setenforce 0
    $ getenforce
    Permissive
```

    Repeat your scenario.

If the problem still occurs, something different than SELinux is blocking your scenario.

# Analyzing SELinux denial messages

After identifying that SELinux is blocking your scenario, you might need to analyze the root cause before you choose a fix.

Prerequisites

    The policycoreutils-python-utils and setroubleshoot-server packages are installed on your system.

Procedure

1. List more details about a logged denial using the sealert command, `sealert -l "*"`
2. If the output obtained in the previous step does not contain clear suggestions:
    - Enable full-path auditing to see full paths to accessed objects and to make additional Linux Audit event fields visible such as `auditctl -w /etc/shadow -p w -k shadow-write`
    - Clear the setroubleshoot cache: `rm -f /var/lib/setroubleshoot/setroubleshoot.xml`
    - Reproduce the problem.
    - Repeat step 1.
    - After you finish the process, disable full-path auditing:
3. If sealert returns only catchall suggestions or suggests adding a new rule using the audit2allow tool, match your problem with examples listed and explained in SELinux denials in the Audit log.

# Fixing analyzed SELinux denials

In most cases, suggestions provided by the sealert tool give you the right guidance about how to fix problems related to the SELinux policy.

> Be careful when the tool suggests using the `audit2allow` tool for configuration changes. You should not use `audit2allow` to generate a local policy module as your first option when you see an SELinux denial. Troubleshooting should start with a check if there is a labeling problem. The second most often case is that you have changed a process configuration, and you forgot to tell SELinux about it.

## Labeling problems

A common cause of labeling problems is when a non-standard directory is used for a service. For example, instead of using `/var/www/html/`for a website, an administrator might want to use `/srv/myweb/`. On Red Hat Enterprise Linux, the `/srv` directory is labeled with the `var_t` type. Files and directories created in `/srv` inherit this type. Also, newly-created objects in top-level directories, such as `/myserver`, can be labeled with the `default_t` type. SELinux prevents the Apache HTTP Server (httpd) from accessing both of these types. To allow access, SELinux must know that the files in `/srv/myweb/` are to be accessible by httpd:

```
semanage fcontext -a -t httpd_sys_content_t "/srv/myweb(/.*)?"
```

This semanage command adds the context for the `/srv/myweb/`directory and all files and directories under it to the SELinux file-context configuration. The semanage utility does not change the context. As `root`, use the `restorecon` utility to apply the changes:

```
# restorecon -R -v /srv/myweb
```

## Incorrect Context

he `matchpathcon` utility checks the context of a file path and compares it to the default label for that path. The following example demonstrates the use of matchpathcon on a directory that contains incorrectly labeled files:

```
$ matchpathcon -V /var/www/html/*
/var/www/html/index.html has context unconfined_u:object_r:user_home_t:s0, should be system_u:object_r:httpd_sys_content_t:s0
/var/www/html/page1.html has context unconfined_u:object_r:user_home_t:s0, should be system_u:object_r:httpd_sys_content_t:s0
```

In this example, the `index.html` and `page1.html` files are labeled with the `user_home_t` type. This type is used for files in user home directories. Using the mv command to move files from your home directory may result in files being labeled with the `user_home_t` type. This type should not exist outside of home directories. Use the `restorecon` utility to restore such files to their correct type:

```
# restorecon -v /var/www/html/index.html
restorecon reset /var/www/html/index.html context unconfined_u:object_r:user_home_t:s0->system_u:object_r:httpd_sys_content_t:s0
```

To restore the context for all files under a directory, use the `-R` option:

```
# restorecon -R -v /var/www/html/
restorecon reset /var/www/html/page1.html context unconfined_u:object_r:samba_share_t:s0->system_u:object_r:httpd_sys_content_t:s0
restorecon reset /var/www/html/index.html context unconfined_u:object_r:samba_share_t:s0->system_
```

## Confined applications configured in non-standard ways

Services can be run in a variety of ways. To account for that, you need to specify how you run your services. You can achieve this through SELinux booleans that allow parts of SELinux policy to be changed at runtime. This enables changes, such as allowing services access to NFS volumes, without reloading or recompiling SELinux policy. Also, running services on non-default port numbers requires policy configuration to be updated using the semanage command.

For example, to allow the Apache HTTP Server to communicate with MariaDB, enable the `httpd_can_network_connect_db` boolean:

```
# setsebool -P httpd_can_network_connect_db on
```

Note that the `-P` option makes the setting persistent across reboots of the system.

If access is denied for a particular service, use the `getsebool` and grep utilities to see if any booleans are available to allow access. For example, use the `getsebool -a | grep ftp` command to search for FTP related booleans:

```
$ getsebool -a | grep ftp
ftpd_anon_write --> off
ftpd_full_access --> off
ftpd_use_cifs --> off
ftpd_use_nfs --> off

ftpd_connect_db --> off
httpd_enable_ftp_server --> off
tftp_anon_write --> off
```

To get a list of booleans and to find out if they are enabled or disabled, use the `getsebool -a command`.

To get a list of booleans including their meaning, and to find out if they are enabled or disabled, install the `selinux-policy-devel package` and use the `semanage boolean -l` command as `root`.

Some Booleans are available to change user behavior when running applications in their home directories and in /tmp. Use the `setsebool –P [boolean] on|off` command:

1. To allow Linux users in the `guest_t` domain to execute applications in their home directories and /tmp:

```
# setsebool -P guest_exec_content on
```

2. To allow Linux users in the `xguest_t` domain to execute applications in their home directories and /tmp:

```
# setsebool -P xguest_exec_content on
```

3. To prevent Linux users in the `user_t` domain from executing applications in their home directories and `/tmp`:

```
# setsebool -P user_exec_content off
```

4. To prevent Linux users in the `staff_t` domain from executing applications in their home directories and `/tmp`:

```
# setsebool -P staff_exec_content off
```

## Port numbers

Depending on policy configuration, services can only be allowed to run on certain port numbers. Attempting to change the port a service runs on without changing policy may result in the service failing to start. For example, run the `semanage port -l | grep http` command as root to list http related ports:

```
# semanage port -l | grep http
http_cache_port_t              tcp      3128, 8080, 8118
http_cache_port_t              udp      3130
http_port_t                    tcp      80, 443, 488, 8008, 8009, 8443
pegasus_http_port_t            tcp      5988
pegasus_https_port_t           tcp      5989
```

The `http_port_t` port type defines the ports Apache HTTP Server can listen on, which in this case, are TCP ports 80, 443, 488, 8008, 8009, and 8443. If an administrator configures httpd.conf so that httpd listens on port 9876 (Listen 9876), but policy is not updated to reflect this and the command `systemctl start httpd.service` fails

An SELinux denial message similar to the following is logged to `/var/log/audit/audit.log`:

```
type=AVC msg=audit(1225948455.061:294): avc:  denied  { name_bind } for  pid=4997 comm="httpd" src=9876 scontext=unconfined_u:system_r:httpd_t:s0 tcontext=system_u:object_r:port_t:s0 tclass=tcp_socket
```

To allow httpd to listen on a port that is not listed for the http_port_t port type, use the semanage port command to assign a different label to the port:

```
# semanage port -a -t http_port_t -p tcp 9876
```

The `-a ``option adds a new record; the`-t`option defines a type; and the`-p` option defines a protocol. The last argument is the port number to add.

## Corner cases, evolving or broken applications, and compromised systems

Applications may contain bugs, causing SELinux to deny access. Also, SELinux rules are evolving – SELinux may not have seen an application running in a certain way, possibly causing it to deny access, even though the application is working as expected. For example, if a new version of PostgreSQL is released, it may perform actions the current policy does not account for, causing access to be denied, even though access should be allowed.

For these situations, after access is denied, use the `audit2allow` utility to create a custom policy module to allow access. You can report missing rules in the SELinux policy in Red Hat Bugzilla. For Red Hat Enterprise Linux 8, create bugs against the Red Hat Enterprise Linux 8 product, and select the selinux-policy component. Include the output of the `audit2allow -w -a` and `audit2allow -a` commands in such bug reports.

If an application asks for major security privileges, it could be a signal that the application is compromised. Use intrusion detection tools to inspect such suspicious behavior.

The Solution Engine on the Red Hat Customer Portal can also provide guidance in the form of an article containing a possible solution for the same or very similar problem you have. Select the relevant product and version and use SELinux-related keywords, such as selinux or avc, together with the name of your blocked service or application, for example: selinux samba.

## Fix SELinux denials by allowing requested access

This should be a last resort ... done sparingly & with care. The vast majority of problems can be solved by setting proper file labels or tweaking booleans or figuring out that the application/admin is doing something wrong.

    ```
    ausearch -i -m avc | grep xxxx | audit2allow
```

# Improving Security

- You can confine all regular users on your system by mapping them to the SELinux confined users. `user_u`
  
  Map the `__default__` user, which represents all users without an explicit mapping, to the `user_u` SELinux user:

    ```
    semanage login -m -s user_u -r s0 __default__
    ```

- confine a user with administrative privileges by mapping the user directly to the `sysadm_u`. When the user logs in, the session runs in the `sysadm_u:sysadm_r:sysadm_t` SELinux context.

    1. Optional: To allow sysadm_u users to connect to the system using SSH:

    ```
    # setsebool -P ssh_sysadm_login on
    ```

    2. Create a new user, add the user to the wheel user group, and map the user to the sysadm_u SELinux user:

    ```
    # adduser -G wheel -Z sysadm_u example.user
    ```

    3. Optional: Map an existing user to the sysadm_u SELinux user and add the user to the wheel user group:

    ```
    # usermod -G wheel -Z sysadm_u example.user
    ```

    4. Check that example.user is mapped to the sysadm_u SELinux user:

    ```
    # semanage login -l | grep example.user
    example.user     sysadm_u    s0-s0:c0.c1023   *
    ```

    5. Try an administrative task, for example, restarting the sshd service:

    ```
    # systemctl restart sshd
    ```

- map a specific user with administrative privileges to the staff_u SELinux user, and configure sudo so that the user can gain the sysadm_r SELinux administrator role.

    This role allows the user to perform administrative tasks without SELinux denials. When the user logs in, the session runs in the `staff_u:staff_r:staff_t` SELinux context, but when the user enters a command using sudo, the session changes to the `staff_u:sysadm_r:sysadm_t` context.

    1. Map an existing user to the `staff_u` SELinux user and add the user to the wheel user group:

    ```
    # usermod -G wheel -Z staff_u example.user
    ```

    2. To allow example.user to gain the SELinux administrator role, create a new file in the /etc/sudoers.d/ directory, for example:

    ```
    # visudo -f /etc/sudoers.d/example.user
    ```

    3. Add the following line to the new file:

    ```
    example.user ALL=(ALL) TYPE=sysadm_t ROLE=sysadm_r ALL
    ```

# Cheat Sheet

## Permissive domains

Instead of using setenforce 0 on the whole system when you suspect a problem, switch a particular process domain into permissive mode.

- Help
  
```
man semanage-permissive
```

- Immediately & permanently switch a process domain into permissive mode

```
semanage permissive --add logrotate_t
semanage permissive -a httpd_t
```

- Immediately & permanently switch a permissive domain back to enforcing

```
semanage permissive --del logrotate_t
semanage permissive -d httpd_t
```

- Check for permissive domains

```
semodule -l | grep permissive
```

- Immediately disable all permissive domains (i.e., switch them back to enforcing)

```
semodule -d permissivedomains
```

- Immediately enable any previously-set permissive domains (i.e., switch them back to permissive)

```
semodule -e permissivedomains
```

## File labels

 The `semanage fcontext` command is used to change the SELinux context of files. When using targeted policy, changes are written to files located in the `/etc/selinux/targeted/contexts/files/` directory:

- The `file_contexts` file specifies default contexts for many files, as well as contexts updated via `semanage fcontext`.
- The `file_contexts.local` file stores contexts to newly created files and directories not found in `file_contexts`.

Two utilities read these files. The `setfiles` utility is used when a file system is relabeled and the `restorecon` utility restores the default SELinux contexts. This means that changes made by `semanage fcontext` are persistent, even if the file system is relabeled. SELinux policy controls whether users are able to modify the SELinux context for any given file.

- To get help `man semanage-fcontext`
- To list current context `semanage fcontext -l`
- Set a file type on a directory

```

semanage fcontext --add -t samba_share_t "/path/to/dir(/.*)?" && restorecon -RF /path/to/dir
semanage fcontext -a -t httpd_sys_content_t "/path(/.*)?" && restorecon -RF /path

```

The `semanage fcontext -a -t samba_share_t` command adds an entry to `/etc/selinux/targeted/contexts/files/file_contexts.local`

- Create an alternate location (equivalency rule) based on an existing directory (which is useful because it recursively includes rules)

```

semanage fcontext -a -e /var/www /web && restorecon -RF /web
semanage fcontext -a -e /home /our/home && restorecon -RF /our/home

```

- Check what a particular [source] process domain can do to a particular [target] file type

```
sesearch -CA -s httpd_t -t var_log_t
```

### Make temporary changes by using chcon

`chcon` is used to changes to file context. But it is temporary, in that in case of a file system relabel or execution of restorecon, changes you made though `chcon` will be reverted. Thus make this tool a great tool to troubleshoot access denied errors.

- changing file context: `chcon -t httpd_sys_content_t /var/www/html/testfile`
- changing all files/folder’s context in html directory (Inclusive).:  `chcon -R -t  httpd_sys_content_t /var/www/html`
- restoring your changes: `restorecon -R -v  httpd_sys_content_t /var/www/html`

### Checking Default Context

use matchpathcon to compare current configuration to default : `matchpathcon -V /var/www/html/*`

## Network port labels

Policy must explicitly allow confined services specific access to certain network port labels; however, the labels can be changed just as easily as file labels.

- For help `man semanage-port`
- Check for port labels for a particular domain/service

    ```
    semanage port -l | grep http   # Look for http-labeled ports
    semanage port -l | grep ssh   # Look for ssh-labeled ports
    semanage port -l | grep 3333   # Look for a specific port
    man httpd_selinux
    man sshd_selinux
    sesearch -CA -s httpd_t -c tcp_socket -p name_bind   # Look for tcp port types that a particular domain is allowed to bind to
    ```

- Permanently set labels on network ports

```
semanage port -a -t http_port_t -p tcp 3333   # Permanently add a label to a specific port
semanage port -a -t ssh_port_t -p tcp 2222
```

## Information Gathering Tools

See stats of access vector cache : `avcstat`

Get a breakdown of a policy such as Booleans, types, number of classes, allow rools and others.

`seinfo`

This can also list number of types

```
seinfo -adomain -x

seinfo -aunconfined_domain_type -x

seinfo --permissive -x
```

Search particular type in policy

```
sesearch
sesearch --role_allow -t httpd_sys_content_t /etc/selinux/targeted/policy/policy.31
sesearch --allow # get allowed rules
```

# SELinux settings with booleans

SELinux is built around the concept of security **labels** and **types**. When you give a file an SELinux label of one type, then a process bearing a label of a different type cannot interact with it, even though the file's permissions on disk might be as permissive as 777

SELinux uses **policies** to decide what labels and types are compatible with one another. For instance, if your system has the default policy that disallows an HTTP daemon to interact with users' home directories, then user home directories are essentially untouchable by httpd even though you may have a config file saying otherwise.

When SELinux registers an attempted violation of a policy, it logs the decision as an Access Vector Cache (AVC). Emsure you installe the setroubleshoot package first

All SELinux boolean values are viewable as a file in your filesystem. They're expressed as files in the /sys/fs/selinux/booleans directory:

| Command                   | Description                         |
| ------------------------- | ----------------------------------- |
| `semanage boolean --list` | to view available boolean options   |
| `semanage boolean -l -C`  | To view default and custom settings |
