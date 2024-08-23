
How DNF works

    DNF is a package management tool that relies on repositories to install/update/downgrade/remove packages.
    So you must have a repository to be able to use dnf.
    By default, dnf uses the global configuration file at `/etc/dnf/dnf.conf` and all the repository files (.repo) present in `/etc/yum.repos.d`.
    The .repo files include repo name, repo path, gpg check and enabled parameter. Based on this parameter, dnf will query the repository for available packages.
    You can also configure your own repository.

The repository file includes the following things.

```sh
[BaseOS]
name=CentOS-$releasever - Base
mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=BaseOS&infra=$infra
#baseurl=http://mirror.centos.org/$contentdir/$releasever/BaseOS/$basearch/os/
gpgcheck=1
enabled=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-centosofficial
```

Here,

- `name`: Repository name
- `mirrorlist`/`baseurl`: Path of the repository, which contains all the packages. Supports HTTP(S), FTP(S), FILE etc. NFS is not supported directly, so you must manually first mount the NFS share and use file:// to access the repo.
- `gpgcheck`: perform a checksum match using the GOG key to ensure no one has contaminated the repo. 0 (Zero) means disable the check.
- `enabled`: You can enable or disable specific repo. 0 (Zero) means disabled and 1 (One) means enabled
- `gpgkey`: Path of the gpgkey to be used for checksum validation if `gpgcheck = 1`

<br>

dnf Cheat Sheet

A Linux package manager on RPM distributions, including Fedora, CentOS, RHEL, OpenMandriva, and Mageia.
| Command | Purpose |
| :----------------------------------------------------------------- | :------------------------------------------------------------------------------------------------------ |
| **Finding software**                                                   | Search for software by name or description. Package groups provide related applications together.       |
| dnf search foo                                                     | Search for package foo in repository                                                                    |
| dnf provides foo                                                   | Find packages that provide foo                                                                          |
| dnf download --url foo                                             | Display URL of where foo can be downloaded                                                              |
| dnf group list --verbose                                           | List available package groups                                                                           |
| gnome-software                                                     | Launch the GNOME Software center                                                                        |
| **Get info about software**                                          |
| dnf info foo                                                       | List install status and metadata of package foo                                                         |
| dnf info @design-suite                                             | List packages in the group named design-suite                                                           |
| dnf repoquery --list foo                                           | List all files included in package foo                                                                  |
| **Installing and uninstalling software**                               | Packages and groups of packages can be installed and uninstalled by name.                               |
| dnf install foo                                                    | Install package foo (use -y to skip confirmation)                                                       |
| dnf install @design-suite                                          | Install the package group named design-suite                                                            |
| dnf remove foo                                                     | Uninstall package foo                                                                                   |
| **Downloading packages**                                               | To archive a package for later use, or to modify it, you can download it using dnf.                     |
| dnf download foo                                                   | Download package foo, but do not install                                                                |
| dnf download --resolve foo                                         | Download foo and missing dependencies (do not install)                                                  |
| dnf download --resolve --alldeps foo                               | Download foo and all dependencies (even ones already installed), but do not install                     |
| dnf download --source foo                                          | Download the source RPM (SRPM) for foo                                                                  |
| **Dependencies**                                                       |
| dnf repoquery --deplist foo                                        | List dependencies (and packages) required by foo                                                        |
| dnf --allowerasing                                                 | Remove packages as needed to resolve dependencies                                                       |
| dnf --skip-broken                                                  | Skip packages with unsolvable dependencies                                                              |
| dnf builddep foo                                                   | Install build dependencies for package/spec/SRPM                                                        |
| **Repositories**                                                       |
| dnf repolist                                                       | List enabled repositories                                                                               |
| dnf repolist --all                                                 | List all registered repositories                                                                        |
| dnf config-manager --enable                                        | powertools Enable repo named powertools                                                                 |
| dnf config-manager --add-repo <https://example.com/updates/x86_64> | Add and enable repo (URL target must contain a valid repodata/repomd.xml file)                          |
| dnf config-manager --dump                                          | Show current configuration values                                                                       |
