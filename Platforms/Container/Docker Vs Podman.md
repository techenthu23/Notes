# Podman

- [Podman](#podman)
- [Buildah](#buildah)
    - [Building a container](#building-a-container)
    - [Building with a Dockerfile](#building-with-a-dockerfile)
    - [Build and run Buildah inside a Podman container](#build-and-run-buildah-inside-a-podman-container)
      - [Running a buildah container inside of a Podman container](#running-a-buildah-container-inside-of-a-podman-container)
- [Podman vs Docker](#podman-vs-docker)
  - [Architecture](#architecture)
  - [Root privileges](#root-privileges)
  - [Security](#security)
  - [Systemd](#systemd)
  - [Building images](#building-images)
  - [Docker Swarm](#docker-swarm)
  - [All in one vs modular](#all-in-one-vs-modular)
  - [Podman vs Docker: Can they work together?](#podman-vs-docker-can-they-work-together)
  - [Is Podman a replacement for Docker?](#is-podman-a-replacement-for-docker)
  - [Can Docker and Podman coexist?](#can-docker-and-podman-coexist)
  - [Podman and container images](#podman-and-container-images)
  - [Podman Cheatsheet](#podman-cheatsheet)

# Buildah

If you're looking to build Open Container Initiative (OCI) container images without a full container runtime or daemon installed, Buildah is the perfect solution. Now, Buildah is an open source, Linux-based tool that can build Docker- and Kubernetes-compatible images, and is easy to incorporate into scripts and build pipelines. In addition, Buildah has overlap functionality with Podman, Skopeo, and CRI-O.

Buildah has the ability to create a working container from scratch, but also from a pre-existing Dockerfile. Plus, with it not needing a daemon, you'll never have to worry about Docker daemon issues when building container images.

```
# Install buildah
yum -y install buildah

# Current version of buildah
buildah --version

# to pull a container image from a repository centos
buildah from centos

# List current images
buildah images

# to get list of running containers which are provisioned as soon as the image pull is completed
buildah containers

# clean up and remove our running containers
buildah rm -all

```

### Building a container

Buildah and build an Apache web server that will run inside a container. To get things started, let's pull a CentOS base image and start working:

```
buildah from centos
```

You'll see the default image name as output in the console like centos-working-container, giving us the ability to run commands within the specified container. For our case, we'll be installing an httpd package, which can be done using the following command:

```
buildah run centos-working-container yum install httpd -y
```

Once we've installed httpd, we can take our attention to creating a main page to be directed to on our web server, commonly known as an index.html file. To create a simple file without having to worry about formatting, use the echo command below:

```
echo "Hello from Red Hat" > index.html
```

In addition, after creating this new file, let's copy it into our current working container with the Buildah copy function. The default location for publicly accessible files is also included:

```

buildah copy centos-working-container index.html /var/www/html/index.html
```

To start this container, we must configure an entry point for a container, which is used to start httpd as the container begins and keep it in the foreground:

```
buildah config --entrypoint "/usr/sbin/httpd -DFOREGROUND" centos-working-container
```

Finally, let's commit our changes to the container, and prepare it to be pushed to any container registry you'd like (ex. Docker and Quay.io):

```
buildah commit centos-working-container redhat-website
```

Your redhat-website image is ready to run with Podman, or push to your registry of choice.

Another example 
```bash
#!/usr/bin/env bash

set -o errexit

# Create a container
container=$(buildah from fedora:28)

# Labels are part of the "buildah config" command
buildah config --label maintainer="Chris Collins <collins.christopher@gmail.com>" $container

# Grab the source code outside of the container
curl -sSL http://ftpmirror.gnu.org/hello/hello-2.10.tar.gz -o hello-2.10.tar.gz

buildah copy $container hello-2.10.tar.gz /tmp/hello-2.10.tar.gz

buildah run $container dnf install -y tar gzip gcc make
Buildah run $container dnf clean all
buildah run $container tar xvzf /tmp/hello-2.10.tar.gz -C /opt

# Workingdir is also a "buildah config" command
buildah config --workingdir /opt/hello-2.10 $container

buildah run $container ./configure
buildah run $container make
buildah run $container make install
buildah run $container hello -v

# Entrypoint, too, is a “buildah config” command
buildah config --entrypoint /usr/local/bin/hello $container

# Finally saves the running container to an image
buildah commit --format docker $container hello:latest
```

```bash
#!/usr/bin/env bash

set -o errexit

# Create a container
container=$(buildah from fedora:28)
mountpoint=$(buildah mount $container)

buildah config --label maintainer="Chris Collins <collins.christopher@gmail.com>" $container

curl -sSL http://ftpmirror.gnu.org/hello/hello-2.10.tar.gz \
     -o /tmp/hello-2.10.tar.gz
tar xvzf src/hello-2.10.tar.gz -C ${mountpoint}/opt

pushd ${mountpoint}/opt/hello-2.10
./configure
make
make install DESTDIR=${mountpoint}
popd

chroot $mountpoint bash -c "/usr/local/bin/hello -v"

buildah config --entrypoint "/usr/local/bin/hello" $container
buildah commit --format docker $container hello
buildah unmount $container
```

### Building with a Dockerfile

Another significant part of Buildah is the ability to build images using a Dockerfile, and the build-using-dockerfile, or bud command can do just that. Let's take an example Dockerfile as input, and output an OCI image:

```dockerfile
# CoreOS Base
FROM fedora:latest
# Install httpd
RUN echo "Installing httpd"; yum -y install httpd
# Expose the default httpd port 80
EXPOSE 80
# Run httpd
CMD ["/usr/sbin/httpd", "-DFOREGROUND"]
```

Once we save this file as Dockerfile in our local directory, we can use the bud command to build the image:

```
buildah bud -t fedora-httpd
```

To double-check our progress, let's run buildah images and ensure we can see our new fedora-httpd image resting in our localhost repository.

### Build and run Buildah inside a Podman container

```
dnf -y install podman container-selinux --enablerepo updates-testing. 
```

This gave me Podman v1.1.2 and container-selinux version 2.85-1.

Because both the container and the container within the container will be using fuse-overlayfs, they won’t be happy trying to mount their respective directories over each other. So, the first step is to create a directory for the container within the container to use, and I’ve named it /var/lib/mycontainer:

```
# mkdir /var/lib/mycontainer
```

The dockerfile will pull Fedora, set up the GOPATH, install the Buildah dependencies, use git to clone the project in the /root/buildah directory, and finally update /etc/container/storage.conf with sed to uncomment the mount_program:

```dockerfile
# FILE=~/Dockerfile.cinc
# /bin/cat <<EOM >$FILE
FROM fedora:latest
ENV GOPATH=/root/buildah

RUN dnf -y install \
make \
golang \
bats \
btrfs-progs-devel \
device-mapper-devel \
glib2-devel \
gpgme-devel \
libassuan-devel \
libseccomp-devel \
ostree-devel \
git \
bzip2 \
go-md2man \
runc \
fuse-overlayfs \
fuse3 \
containers-common; \
mkdir /root/buildah; \
git clone https://github.com/containers/buildah /root/buildah/src/github.com/containers/buildah

RUN sed -i -e 's|#mount_program = "/usr/bin/fuse-overlayfs"|mount_program = "/usr/bin/fuse-overlayfs"|' /etc/containers/storage.conf
EOM
```

create the image using the Dockerfile

```
# podman build -t buildahimage -f ~/Dockerfile.cinc .
```

create a Podman container in which we’ll do our Buildah development. The following command creates a container named buildahctr, mounts the host’s mycontainer to the container’s containers directory, runs the container detached using the host’s network, turns off label and seccomp confinement in the container, and finally does a little shell hackery to keep the container up and running.

```
# podman run --detach --name=buildahctr --net=host --security-opt label=disable --security-opt seccomp=unconfined --device /dev/fuse:rw -v /var/lib/mycontainer:/var/lib/containers:Z   buildahimage sh -c 'while true ;do wait; done'
```

hop in to the command line inside the container. and compile and install Buildah onto it.

```
# podman exec -it buildahctr /bin/sh
```

```
sh-4.4# cd /root/buildah
export GOPATH=`pwd`
cd /root/buildah/src/github.com/containers/buildah
make
make install

sh-4.4# buildah from alpine
alpine-working-container

sh-4.4# buildah images
REPOSITORY               TAG    IMAGE  ID    CREATED    SIZE
docker.io/library/alpine latest 5cb3aa00f899 9 days ago 5.79 MB
```

So now we’ve compiled, installed, and run Buildah from within a Podman container.

Use vi or your favorite editor to change cmd/buildah/images.go. Search for the outputHeader() function (near line 219) and find the line in it: format := "table {{.Name}}\t{{.Tag}}\t". Remove the word "table" from that line making it format := "{{.Name}}\t{{.Tag}}\t”. Save the file, exit, then make and make install it again.

```
sh-4.4# vi cmd/buildah/images.go
sh-4.4# make
sh-4.4# make install
```

Now if you run buildah images, you should see that we’ve made all output act as if the --quiet option is on, which doesn’t show the table headers.

```
sh-4.4# buildah images
docker.io/library/alpine  latest 5cb3aa00f899 9 days ago   5.79 MB
```

#### Running a buildah container inside of a Podman container

For one last piece of fun, let's see if we can run a Buildah container within this Podman container using our modified Buildah code. We'll just do something simple and list the contents of the / directory.

```
sh-4.4# buildah from --name myalpine alpine
myalpine

sh-4.4# buildah run --isolation=chroot myalpine ls /
bin   dev   etc  home   lib   media   mnt  opt   proc   root run   sbin   srv   sys   tmp   usr   var
```

A portable Buildah development environment

If you’ve made it this far, you’ve built a container using Podman that is capable of doing Buildah development. That container can also build containers and then run those containers.  I used Buildah for this example, but I could have used Podman to build the internal container, too. Regardless of the internal tool chosen, I now have a container that can build and run containers within itself, much like Matryoshka dolls. Best yet, I could commit this container and push it out to Quay.io or another container registry and then pull it down and run it on another Fedora machine or even another Linux platform. I’ve made a totally portable Buildah development environment.

# Podman vs Docker

Podman and Docker share many features in common but have some fundamental differences. These don't make one better than the other but might be decisive to select the most appropriate for a specific project.

## Architecture

Docker uses a daemon, an ongoing program running in the background, to create images and run containers. Podman has a daemon-less architecture which means it can run containers under the user starting the container. Docker has a client-server logic mediated by a daemon; Podman does not need the mediator.

## Root privileges

Podman, since it doesn't have a daemon to manage its activity, also dispenses root privileges for its containers. Docker recently added rootless mode to its daemon configuration, but Podman used this approach first and promoted it as a fundamental feature. And this is because of security

## Security

Is Podman safer than Docker? Podman allows for non-root privileges for containers.Rootless containers are considered safer than containers with root privileges. In Docker, daemons have root privileges, making them the preferred gateway for attackers. Containers in Podman do not have root access by default, adding a natural barrier between root and rootless levels, improving security. Still, Podman can run both root and rootless containers.

## Systemd

Without a daemon, Podman needs another tool to manage services and support running containers in the background. Systemd creates control units for existing containers or to generate new ones. Systemd can also be integrated with Podman allowing it to run containers with systemd enabled by default, without any modification.

By using systemd, vendors can install, run, and manage their applications as containers since most are now exclusively packaged and delivered this way.

## Building images

As a self-sufficient tool, Docker can build container images on its own. Podman requires the assistance of another tool called Buildah, which expresses its specialized nature: it is made for running but not building containers on its own.

## Docker Swarm

Podman does not support Docker Swarm, which may rule it out of the options for projects using this feature since using Docker Swarm commands will generate an error. Podman has recently added support for Docker Compose to make it Swarm compliant, overcoming this limitation. Docker, naturally, works well with Swarm.

## All in one vs modular

And maybe this is the crucial difference in both technologies: Docker is a monolithic, powerful, independent tool with all the benefits and drawbacks implied, handling all of the containerization tasks throughout their entire cycle. Podman has a modular approach, relying on specialized tools for specific duties.

## Podman vs Docker: Can they work together?

Sold as the best and easiest to apply alternative to Docker - users can just alias Docker to Podman (alias docker=podman) without any problems- Podman is a more than capable tool for containerization tasks.

## Is Podman a replacement for Docker?

Podman can be a primary containerization technology option if you are starting a project from scratch. If the project is ongoing and already using Docker, it depends on the specifics, but it might not be worth the effort. As a Linux native application, it demands Linux skills from the developers involved.

Developers can combine both tools by relying on Docker in the development stage and later push the project to Podman in runtime environments, benefitting from the added security it provides. And since they're both OCI-compliant, compatibility shouldn't be a problem.

## Can Docker and Podman coexist?

Yes, and quite well. Many developers have been using Docker and Podman in tandem to create safer, more efficient, agile frameworks. They have a lot in common, making the transition from Docker to Podman or their combination quite seamless.

## Podman and container images

When you first type podman images, you might be surprised that you don’t see any of the Docker images you’ve already pulled down. This is because Podman’s local repository is in /var/lib/containers instead of /var/lib/docker.  This isn’t an arbitrary change; this new storage structure is based on the Open Containers Initiative (OCI) standards.

Since you can run podman without being root, there needs to be a separate place where podman can write images. Podman uses a repository in the user’s home directory: ~/.local/share/containers. This avoids making /var/lib/containers world-writeable or other practices that might lead to potential security problems. This also ensures that every user has separate sets of containers and images and all can use Podman concurrently on the same host without stepping on each other. When users are finished with their work, they can push to a common registry to share their image with others.

Despite the new locations for the local repositories, the images created by Docker or Podman are compatible with the OCI standard. Podman can push to and pull from popular container registries like Quay.io and Docker hub, as well as private registries.

Podman provides capabilities in its command-line push and pull commands to gracefully move images from /var/lib/docker to /var/lib/containers and vice versa.  For example:

$ podman push myfedora docker-daemon:myfedora:latest
Obviously, leaving out the docker-daemon above will default to pushing to the Docker hub.  Using quay.io/myquayid/myfedora will push the image to the Quay.io registry (where myquayid below is your personal Quay.io account):

$ podman push myfedora quay.io/myquayid/myfedora:latest

```
# to create a pod with Podman named my-pod
podman pod create --name my-pod
podman pod list
```


## Podman Cheatsheet

```bash
$ podman --version                    # Check version

$ sudo podman login -u USER_NAME REGISTRY_URL
                                    # Login to Registry
$ sudo podman login -u USER_NAME \
  -p ${TOKEN} \
  REGISTRY_URL  
                                    # Login with token or password
                                    # eg: in OpenShift, token can retrive as
                                    # $ TOKEN=$(oc whoami -t)

$ podman logout quay.io             # Remove login credentials for registry.redhat.io
$ podman logout --all               # Remove login credentials for all registries

$ podman search REGISTRY_URL/IMAGE_NAME
                                    # search for an image in registry

$ sudo podman run --name test -u 1234 \
  -p 8080:8080 -d s2i-sample-app

$ sudo podman run -d --name TEST \
  quay.io/USER_NAME/IMAGE_NAME:VERSION
                                    # Create a container 

$ podman run --privileged quay.io/podman/stable podman run ubi8 echo hello
                                    # The easiest way to run Podman inside of a container is to use the --privileged flag.

$ sudo podman ps                    # List running containers
$ sudo podman stop CONTAINER_NAME   # STOP running containers
$ sudo podman rm CONTAINER_NAME     # remove running containers

# sudo podman rmi IMAGE_NAME        # delete container image
$ sudo podman logs CONTAINER_NAME                    
                                    # check logs of running container

$ sudo podman build -t NAME .       # build container image from Dockerfile and spec
$ sudo podman images                # see available images

podman build -t test -f $PWD/test/Dockerfile .
podman info --debug

# Application Stack with Pods
# create pod and add maridb container in one go
[cloud_user@5ecaa360f91c ~]$ sudo podman run \
> -d --restart=always --pod new:wpapp_pod \
> -e MYSQL_ROOT_PASSWORD="myrootpass" \
> -e MYSQL_DATABASE="wp-db" \
> -e MYSQL_USER="wp-user" \
> -e MYSQL_PASSWORD="w0rdpr3ss" \
> -p 8080:80 \
> --name=wptest-db mariadb

# add wordpress container to pod wpapp_pod
[cloud_user@5ecaa360f91c ~]$ sudo podman run \
> -d --restart=always --pod=wpapp_pod \
> -e WORDPRESS_DB_NAME="wp-db" \
> -e WORDPRESS_DB_USER="wp-user" \
> -e WORDPRESS_DB_PASSWORD="w0rdpr3ss" \
> -e WORDPRESS_DB_HOST="127.0.0.1" \
> --name wptest-web wordpress

# list pods
[cloud_user@5ecaa360f91c ~]$ sudo podman pod list
```