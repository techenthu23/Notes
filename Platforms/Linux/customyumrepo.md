---
title: "Local Mirror Yum Repo Copy"
draft: false
---

- [Creating a local mirror of the latest update for Red Hat Enterprise Linux 5, 6, 7, 8 without using Satellite server](#creating-a-local-mirror-of-the-latest-update-for-red-hat-enterprise-linux-5-6-7-8-without-using-satellite-server)
  - [RHEL 5,6,7](#rhel-567)
    - [Install the required packages](#install-the-required-packages)
    - [Create a basic local repository](#create-a-basic-local-repository)
    - [Create a local repository that allows clients to use groups](#create-a-local-repository-that-allows-clients-to-use-groups)
    - [Modify the repodata to define which packages are security related](#modify-the-repodata-to-define-which-packages-are-security-related)
  - [Create a local repo with Red Hat Enterprise Linux 8](#create-a-local-repo-with-red-hat-enterprise-linux-8)
- [Create an Nginx-based YUM/DNF repository on Red Hat Enterprise Linux](#create-an-nginx-based-yumdnf-repository-on-red-hat-enterprise-linux)

# Creating a local mirror of the latest update for Red Hat Enterprise Linux 5, 6, 7, 8 without using Satellite server

Red Hat provides a utility called reposync which can be used to download the packages from the CDN. In order to download all packages from a specific channel, the system should be subscribed to that channel. If the system is not subscribed to the required channel then reposync will not be able to download and sync those packages on local system.

NOTE:

- To keep the sync current, for example, cronjobs can be used. The createrepo command supports --update to efficiently update existing repositories.
- The locally created repository is typically used by other RHEL clients via LAN, for example via HTTP/HTTPS (for example provided by the apache webserver which is part of RHEL), via FTP (i.e. vsftpd) or NFS (nfs-utils package). Share this local repository with the offline systems to update the offline systems.
- reposync utility can only mirror repositories which the system is entitled to.

## RHEL 5,6,7

- Create a basic repository
- Create a repository that allows clients to install groups
- Modify repodata to identify which packages are security related

### Install the required packages

Install the "yum-utils" and "createrepo" packages on the registered system.

```
yum install yum-utils createrepo
```

### Create a basic local repository

Sync all the packages from a specified repository to a specified directory

NOTE: Change <repo-id> to the repository you intend to sync

```bash
reposync --gpgcheck -l --repoid=<repo-id>
```

for example:

```
reposync --gpgcheck -l --repoid=rhel-6-server-rpms --download_path=/var/www/html
```

In the targeted directory, there will be a new directory named after the Repository ID. All the downloaded packages will be inside this directory.

```bash
$ cd /apps/softwares/repos/<repo-id>
$ createrepo -v /apps/softwares/repos/<repo-id>
## RHEL5
```

### Create a local repository that allows clients to use groups

How to download all the metadata for the repository that is being synced which will allow the use of various plugins such as 'yum groupinstall'

On RHEL6 and later, reposync supports the --download-metadata and --downloadcomps options. For example:

```bash
$ reposync --gpgcheck -l --repoid=repo-id --downloadcomps --download-metadata

## for example:
$ reposync --gpgcheck -l --repoid=rhel-6-server-rpms --download_path=/var/www/html --downloadcomps --download-metadata
```

To have access to the group data for the newly synced repo, please run the createrepo command as follows:

```bash
cd /var/www/html/<repo-id>
createrepo -v  /apps/softwares/repos/<repo-id>/ -g comps.xml
```

### Modify the repodata to define which packages are security related

These steps require that the createrepo command has already been run.

```bash
yum clean all
yum list-sec
find /var/cache/yum/ -name updateinfo.xml            ##For RHEL 5 use '-name *updateinfo.xml*'
```

From the find command above, identify the updateinfo.xml that matches the that you ran reposync against and move that file into your repodata directory.

```bash
mv updateinfo.xml /var/www/html/<repo-id>/repodata/updateinfo.xml
modifyrepo /var/www/html/<repo-id>/repodata/updateinfo.xml /var/www/html/<repo-id>/repodata
```

```bash
mkdir /var/repo
reposync --gpgcheck -l --repoid=rhel-5-server-els-rpms --download_path=/var/repo --downloadcomps
cd /var/repo/rhel-5-server-els-rpms
createrepo -v /var/repo/rhel-5-server-els-rpms
yum clean all
yum list-sec
find /var/cache/yum/ -name *updateinfo.xml*
mv /var/cache/yum/rhel-5-server-els-rpms/365ae03ca85bb9d3bc509ea9129d1d3fb9a18381-updateinfo.xml.gz /tmp
cd /tmp
gzip -d 365ae03ca85bb9d3bc509ea9129d1d3fb9a18381-updateinfo.xml.gz
mv 365ae03ca85bb9d3bc509ea9129d1d3fb9a18381-updateinfo.xml updateinfo.xml
cp updateinfo.xml /var/repo/rhel-5-server-els-rpms/repodata/
modifyrepo /var/repo/rhel-5-server-els-rpms/repodata/updateinfo.xml /var/repo/rhel-5-server-els-rpms/repodata/
```

## Create a local repo with Red Hat Enterprise Linux 8

Ensure you have yum-utils-4.0.8-3.el8.noarch or higher installed so reposync correctly downloads all the packages.

Sync all enabled repositories and their repodata

```bash
$ reposync -p <download-path> --download-metadata --repo=<repo id>

reposync -p /var/www/html  --download-metadata --repo=rhel-8-for-x86_64-appstream-rpms
```

> NOTE: createrepo is not required for RHEL 8. reposync will download everything including the repodata.

> RHEL 8 comes with a completely new repository structure : BaseOS and AppStream contain all software packages, which were available in extras and optional repositories before.
If you want to enable the repo containing development software - enable codeready-builder-for-rhel-8-x86_64-rpms. If you are using a more advanced Red Hat product, refer to the product documentation to find which product specific repositories should be enabled.

```bash
# subscription-manager repos --list-enabled
+----------------------------------------------------------+
    Available Repositories in /etc/yum.repos.d/redhat.repo
+----------------------------------------------------------+
Repo ID:   rhel-8-for-x86_64-appstream-rpms
Repo Name: Red Hat Enterprise Linux 8 for x86_64 - AppStream (RPMs)
Repo URL:  https://cdn.redhat.com/content/dist/rhel8/$releasever/x86_64/appstream/os
Enabled:   1

Repo ID:   rhel-8-for-x86_64-baseos-rpms
Repo Name: Red Hat Enterprise Linux 8 for x86_64 - BaseOS (RPMs)
Repo URL:  https://cdn.redhat.com/content/dist/rhel8/$releasever/x86_64/baseos/os
Enabled:   1
```

CodeReady Linux Builder is for developers writing RHEL Linux applications and it also include packages for developers to use when building their applications.

```
subscription-manager list --available
subscription-manager repos --enable codeready-builder-for-rhel-8-x86_64-rpms
yum repoinfo codeready-builder-for-rhel-8-x86_64-rpms
yum module list --disablerepo=* --enablerepo=codeready-builder-for-rhel-8-x86_64-rpms
```

# Create an Nginx-based YUM/DNF repository on Red Hat Enterprise Linux

1) Install Nginx package to configure yum server

- Add Nginx official repo to yum repository to install nginx package

```bash
vim /etc/yum.repos.d/nginx.repo
```

```conf
[nginx]
name=nginx repo
baseurl=http://nginx.org/packages/centos/$releasever/$basearch/
gpgcheck=0
enabled=1
```

```
gpgcheck=1
gpgkey=https://nginx.org/keys/nginx_signing.key
        file:///etc/pki/rpm-gpg/RPM-GPG-KEY-EPEL-7
```

- Install NGINX package

```bash
yum clean all
yum makecache fast
yum install nginx -y
```

2) Configure Nginx to auto-start at boot

```bash
systemctl start nginx
systemctl enable nginx
```

3) Configure the firewall

```bash
$ firewall-cmd --zone=home --permanent --add-service={https,http}
success
$ firewall-cmd --reload
success
$ firewall-cmd --zone=home --list-all
```

4) Verify that Nginx is up and running by checking the URL on Webbrowser

5) Take backup of default.conf file and create a new configuration file for yum repository.

```
# cd /etc/nginx/conf.d
# mv default.conf default.conf_bkp
# vim yum.conf
```

Now, find the server section of the file and change it so that it looks like this

```nginx.conf
server {
  listen       80 default_server;
  listen       [::]:80 default_server;
  server_name  _;
  root         /apps/softwares/repos/;

  # Load configuration files for the default server block.
  include /etc/nginx/default.d/*.conf;
  # charset koi8-r;
  # access_log  /var/log/nginx/host.access.log  main;
  location / {
          allow all;
          sendfile on;
          sendfile_max_chunk 1m;
          autoindex on;
          autoindex_exact_size off;
          autoindex_format html;
          autoindex_localtime on;
  }
  error_page 404 /404.html;
          location = /40x.html {
      }

  error_page 500 502 503 504 /50x.html;
          location = /50x.html {
      }
  }

  location /centos7 {
      alias /apps/softwares/repos/centos7/;
      autoindex on;
  }
}
```

6) Start the Nginx service again and test its status:

```
# systemctl restart nginx
# systemctl status nginx
```

7) Change permissions and set SELinux

Continue the security configuration by changing the permissions on the local_repo directory and configuring SELinux. To change the permissions:

```
# setfacl -R -m u:apache:rwx /apps/softwares/repos/
```

Then, check if SELinux is enforcing:

```
# getenforce
Enforcing
```

If it is Enforcing, type:

```
# chcon -Rt httpd_sys_content_t /apps/softwares/repos/
```

If SELinux is not set to Enforcing, then the files will not serve from the repo.

```
# setenforce enforcing
```

And make the change permanent by editing the /etc/sysconfig/selinux file and set the following value:

```
SELINUX=enforcing
```
