# Docker

- [Docker](#docker)
- [Docker Cheatsheet](#docker-cheatsheet)
- [Get environment variable value in Dockerfile](#get-environment-variable-value-in-dockerfile)
- [Best Practices for Writing a Dockerfile](#best-practices-for-writing-a-dockerfile)
- [Docker Topics](#docker-topics)
  - [Dockerfile](#dockerfile)
    - [Parser directives](#parser-directives)
  - [Docker Layers](#docker-layers)
  - [Docker Images](#docker-images)
  - [Docker --tag, --name and --label clarification](#docker---tag---name-and---label-clarification)
  - [docker run](#docker-run)

# Docker Cheatsheet

```
# Building containers

# Build an image based on the current directory
docker build .

# Build an image based on the current directory with a tag
docker build -t your-tag .


# Running Containers

# Check the image storage
docker images

# Start a container based on an image ID. Get the ID from docker images.
# control-c will stop the container for all of these docker run commands.
docker run -it <your-image-id>

# Start an image based on a tag
docker run -it <image tag>

# Start hello-world server
docker run -it quay.io/practicalopenshift/hello-world

# Start the Hello World app with port forwarding
docker run -it -p 8080:8080 quay.io/practicalopenshift/hello-world


# Stopping Containers

# Get running images
docker ps

# Stop a running image. The container ID will be in the docker ps output.
docker kill <Container ID>
```

# Get environment variable value in Dockerfile

You should use the ARG directive in your Dockerfile which is meant for this purpose.

    The ARG instruction defines a variable that users can pass at build-time to the builder with the docker build command using the --build-arg <varname>=<value> flag.

So your Dockerfile will have this line:

```
ARG request_domain
```

or if you'd prefer a default value:

```
ARG request_domain=127.0.0.1
```

Now you can reference this variable inside your Dockerfile:

ENV request_domain=$request_domain

then you will build your container like so:

```
docker build --build-arg request_domain=mydomain Dockerfile
```

- Note 1: Your image will not build if you have referenced an ARG in your Dockerfile but excluded it in --build-arg.

- Note 2: If a user specifies a build argument that was not defined in the Dockerfile, the build outputs a warning:

```
    [Warning] One or more build-args [foo] were not consumed.
```

# Best Practices for Writing a Dockerfile

A Dockerfile allows you to mention a sequence of instructions that are executed step by step and each execution creates an intermediate image layer on top of the base image. After executing the last instruction, you get your final Docker image. It helps you automate the entire process and helps to keep track of all the changes that you make. Basically, it’s a blueprint of your image.

The performance of the Docker Container can vary depending upon the sequence of steps you have specified in your dockerfile. Thus, it’s very important that you take the utmost care of the steps that you include inside your dockerfile. In this article, we are going to discuss the best practices that you can adopt in order to make sure that your final Docker Image builds and runs efficiently with low resource consumption.

1. Avoid installing unnecessary packages.

    If you install unnecessary packages in your dockerfile, it will increase the build time and the size of the image. Also, each time you make changes in the dockerfile, you will have to go through all the steps to build that same large image again and again. This creates a cascading downward effect on the performance. To avoid this, it’s always advised that only include those packages that are of utmost importance and try avoiding installing the same packages again and again.

    You can use a requirements file to install all the packages you require. Use the command below to do so.

    ```
    RUN pip3 install -r requirements.txt
    ```

2. Chain all RUN commands

    Each RUN command creates a cacheable unit and builds a new intermediate image layer every time. You can avoid this by chaining all your RUN commands into a single RUN command. Also, try to avoid chaining too much cacheable RUN commands because it would then lead to the creation of a large cache and would ultimately lead to cache burst.

    ```
    RUN apt-get -y install firefox
    RUN apt-get -y install vim
    RUN apt-get -y update
    ```

    The above commands can be chained into a single RUN command.

    ```
    RUN apt-get -y install firefox \
    && apt-get -y install vim \
    && apt-get -y update
    ```

3. Use a `.dockerignore` file

    Similar to `.gitignore` file, you can specify files and directories inside .dockerignore file which you would like to exclude from your Docker build context. This would result in removing unnecessary files from your Docker Container, reduce the size of the Docker Image, and boost up the build performance.

4. Use the best order of statements

    Include the most frequently changing statements at the end of your dockerfile. The reason behind this is that when you change a statement in your dockerfile, its cache gets invalidated and all the subsequent statements cache will also break. For example, include RUN commands to the top and COPY commands to the bottom. Include the CMD, ENTRYPOINT commands at the end of the dockerfile.

5. Avoid installing unnecessary package dependencies

    You can use the `–no-install-recommends` flag while building the image. It will tell the apt package manager to not install redundant dependencies. Installing unnecessary packages only increases the build time and size of the image which would lead to degraded performance.

To conclude, how not choosing the proper order while writing your dockerfile can increase the build time, size of the image, and decrease the performance of the whole process. We also discussed some of the top tips you can follow to improve the overall build performance, reduce the number of caches that builds an intermediate image layer.

# Docker Topics

## Dockerfile

A Dockerfile is a text document that contains all the commands a user could call on the command line to assemble an image.

The docker build command builds an image from a `Dockerfile` and a *context*. The build’s context is the set of files at a specified location PATH or URL. The build is run by the Docker daemon, not by the CLI. The first thing a build process does is send the entire context (recursively) to the daemon.

> _Do not use your root directory, /, as the PATH for your build context, as it causes the build to transfer the entire contents of your hard drive to the Docker daemon. it’s best to start with an empty directory as context and keep your Dockerfile in that directory. Add only the files needed for building the Dockerfile. To increase the build’s performance, exclude files and directories by adding a `.dockerignore` file to the context directory_

Traditionally, the Dockerfile is called `Dockerfile` and located in the root of the context. You use the `-f` flag with docker build to point to a Dockerfile anywhere in your file system.

`docker build -f /path/to/a/Dockerfile .`

You can specify a repository and tag at which to save the new image if the build succeeds:

`docker build -t shykes/myapp .`

To tag the image into multiple repositories after the build, add multiple -t parameters when you run the build command:

`docker build -t shykes/myapp:1.0.2 -t shykes/myapp:latest .`

Docker runs instructions in a `Dockerfile` in order. A `Dockerfile` must begin with a `FROM` instruction. This may be after *parser directives, comments, and globally scoped ARGs .*

Docker treats lines that begin with # as a comment, unless the line is a valid *parser directive.*

### Parser directives

Parser directives are optional, and affect the way in which subsequent lines in a Dockerfile are handled. Parser directives do not add layers to the build, and will not be shown as a build step. Parser directives are written as a special type of comment in the form # directive=value. A single directive may only be used once.

Once a comment, empty line or builder instruction has been processed, Docker no longer looks for parser directives. Instead it treats anything formatted as a parser directive as a comment and does not attempt to validate if it might be a parser directive. Therefore, all parser directives must be at the very top of a Dockerfile.

Parser directives are not case-sensitive. However, convention is for them to be lowercase. Convention is also to include a blank line following any parser directives. Line continuation characters are not supported in parser directives.

__Specifying Dockerfile__

```sh

# Commit used: myrepo.git#mybranch:myfolder and Build Context Used : /myfolder
docker build "https://github.com/docker/myrepo.git#mybranch:myfolder"

# This will use a file called Dockerfile.debug for the build instructions instead of Dockerfile.
docker build -f dockerfiles/Dockerfile.debug -t myapp_debug .

# THIS will use the current directory as the build context and read a Dockerfile from stdin.
curl example.com/remote/Dockerfile | docker build -f - .

# This will build the current build context (as specified by the .) twice, once using a debug version of a Dockerfile and once using a production version. Both use the contents of the debug file instead of looking for a Dockerfile and will use /home/me/myapp as the root of the build context.
cd /home/me/myapp/some/dir/really/deep
docker build -f /home/me/myapp/dockerfiles/debug /home/me/myapp
docker build -f ../../../../dockerfiles/debug /home/me/myap
```

## Docker Layers

- A Docker image consists of several layers.
- Layers are there, to save on computational effort when building images, and bandwidth when distributing (aka pulling and pushing) them.
- Each layer corresponds to certain instructions in your Dockerfile. The following instructions create a layer: `RUN`, `COPY`, `ADD`. The other instructions will create intermediate layers and do not influence the size of your image.
- Each layer is an image itself, just one without a human-assigned tag. They have auto-generated IDs though.
- Each layer stores the changes compared to the image it’s based on.
- An image can consist of a single layer (that’s often the case when the squash command was used).
- Each instruction in a Dockerfile results in a layer. (Except for multi-stage builds, where usually only the layers in the final image are pushed, or when an image is squashed to a single layer).
- Layers are used to avoid transferring redundant information and skip build steps which have not changed (according to the Docker cache).

In this example

```
FROM openjdk:10-jdk
VOLUME /tmp

RUN useradd -d /home/appuser -m -s /bin/bash appuser
USER appuser

HEALTHCHECK --interval=5m --timeout=3s CMD curl -f http://localhost:8080/actuator/health/ || exit 1

ARG JAR_FILE
COPY ${JAR_FILE} app.jar
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-jar","/app.jar"]
```

You will notice that layers are created and most of them are removed (removing intermediate container). So why does it say removing intermediate container and not removing intermediate layer? That’s because a build step is executed in an intermediate container. When the build step is finished executing, the intermediate container can be removed. Besides that, a layer is read-only. A layer contains the differences between the preceding layer and the current layer. On top of the layers, there is a writable layer (the current one) which is called the container layer. As mentioned before, only specific instructions will create a new layer.

To check at the history of the docker image : `docker history <imageid>`

If you add something in one layer, and delete in the next, the add operation is still there in the first layer and can be looked up if you share the image.

When you want to reduce a chain of images to a single image, you can use squashing. It takes all changes, and sums them up into a single image.

The squash flag is an experimental feature. It allows you to merge the new layers into one layer during the build time. To use it just add the flag to the build command: `docker build --squash -t <image> .`

This was a handy approach to remove unnecessary temporary files, or to make sure no secrets made it into Docker images.

__Multi-stage builds__ are another way to use layers during a build, which are not shared with the final image. They are great for many use cases and are a step up from simply squashing your images, as you can still make use of layers for speed and convenience!

With multi-stage builds, you use multiple FROM statements in your Dockerfile. Each FROM instruction can use a different base image, and each of them begins a new stage of the build. You can selectively copy artifacts from one stage to another, leaving behind everything you don’t want in the final image.

When using multi-stage builds, you are not limited to copying from stages you created earlier in your Dockerfile. You can use the COPY --from instruction to copy from a separate image, either using the local image name, a tag available locally or on a Docker registry, or a tag ID. The Docker client pulls the image if necessary and copies the artifact from there.

Reference: <https://docs.docker.com/develop/develop-images/multistage-build/#use-an-external-image-as-a-stage>

```
FROM golang:1.11-alpine3.7 AS builder

WORKDIR /app
COPY main.go .
ARG CGO_ENABLED=0
RUN go build -o server .

FROM scratch

WORKDIR /app
COPY --from=builder /app .

ENTRYPOINT ["./server"]
```

There are many things to consider, below are the key points:

- Think about your application’s needs;
- Develop the Dockerfile in logically separated blocks but compact it in the final version;
- Don’t install a package if you can avoid it;
- Don’t add files to the build context if you don’t need them;
- Don’t create files if you don’t have to, use streams and pipes as much as possible;
- Remove unnecessary files, such as packages cache files;
- If you have to add a file, install a dependency or extend an image, use the smallest one possible.

## Docker Images

A docker image is a read-only template for creating containers, and provides a filesystem based on an ordered union of multiple layers of files and directories, which can be shared with other images and containers. Sharing of image layers is a fundamental component of the Docker platform, and is possible through the implementation of a copy-on-write (COW) mechanism. During its lifetime, if a container needs to change a file from the read-only image that provides its filesystem, it copies the file up to its own private read-write layer before making the change.

A layer or 'diff' is created during the Docker image build process, and results when commands are run in a container, which produce new or modified files and directories. These new or modified files and directories are 'committed' as a new layer. The output of the `docker history` command  shows that

Historically (pre Docker v1.10), each time a new layer was created as a result of a commit action, Docker also created a corresponding image, which was identified by a randomly generated 256-bit UUID, usually referred to as an image ID (presented in the UI as either a short 12-digit hex string, or a long 64-digit hex string). Docker stored the layer contents in a directory with a name synonymous with the image ID. Internally, the image consisted of a configuration object, which held the characteristics of the image, including its ID, and the ID of the image's parent image. In this way, Docker was able to construct a filesystem for a container, with each image in turn referencing its parent and the corresponding layer content, until the base image was reached which had no parent. Optionally, each image could also be tagged with a meaningful name (e.g. my_image:1.0), but this was usually reserved for the leaf image.

Since Docker v1.10, generally, images and layers are no longer synonymous. Instead, an image directly references one or more layers that eventually contribute to a derived container's filesystem.

Layers are now identified by a digest, which takes the form `algorithm:hex`. The hex element is calculated by applying the algorithm (SHA256) to a layer's content. If the content changes, then the computed digest will also change, meaning that Docker can check the retrieved contents of a layer with its published digest in order to verify its content. Layers have no notion of an image or of belonging to an image, they are merely collections of files and directories.

A Docker image now consists of a configuration object, which (amongst other things) contains an ordered list of layer digests, which enables the Docker Engine to assemble a container's filesystem with reference to layer digests rather than parent images. The image ID is also a digest, and is a computed SHA256 hash of the image configuration object, which contains the digests of the layers that contribute to the image's filesystem definition.

The diff directory for storing the layer content, is now named after a randomly generated 'cache ID', and the Docker Engine maintains the link between the layer and its cache ID, so that it knows where to locate the layer's content on disk.

So, when a Docker image is pulled from a registry, and the docker history command provides detail about the image and the layers it is composed of. The `<missing>` value in the `IMAGE` field for all but one of the layers of the image, is misleading and a little unfortunate. It conveys the suggestion of an error, but there is no error as layers are no longer synonymous with a corresponding image and ID. I think it would have been more appropriate to have left the field blank. Also, the image ID appears to be associated with the uppermost layer, but in fact, the image ID doesn't 'belong' to any of the layers. Rather, the layers collectively belong to the image, and provide its filesystem definition.

## Docker --tag, --name and --label clarification

You can think of Docker image as a blueprint for starting a container. On the other hand there are containers which are running instances that are based on an image.

When you docker `build -t tagname .` you are creating an image and tag it with a `"name:tag"` format usually. So for example, you are building your image as

`docker build -t myimage:1.0 .`

which creates a new image that is named `myimage` with a version of `1.0`. This is what you will see when you run docker images.

The `--name` parameter is then used when you create and start a new container based of your image. So for example, you run a new container using the following command:

`docker run -it --name mycontainerinstance myimage`

This creates a new container based of your image `myimage`. This container instance is named `mycontainerinstance`. You can see this when you run `docker ps -a` which will list the container with its container name `mycontainerinstance`.

You can assign multiple tags to images, e.g.

`docker build --tag=tomcat-admin --tag=tomcat-admin:1.0 .`

If you list images you get one line per tag, but they are related to the same image id:

```
docker images |grep tomcat

tomcat-admin                                    1.0                 955395353827        11 minutes ago      188 MB
tomcat-admin                                    latest              955395353827        11 minutes ago      188 MB
```

You can tag images also a second time, not just when you build them, so you can keep different image versions.

Obviously you get different attributes by inspecting images and containers. I think it's more clear if you use different name for image tag and container name, e.g.

```
docker build --tag=tomcat-admin .
docker run -d -ti --name=tomcat-admin-container tomcat-admin

docker inspect tomcat-admin              ==> You inspect the image
docker inspect tomcat-admin-container    ==> You inspect the container
```

Labels are added at the time you create an object. They cannot be modified after the initial creation; you need to delete the object and replace it with a new version if changes are necessary.

The LABEL instruction adds metadata to an image. A LABEL is a key-value pair. To include spaces within a LABEL value, use quotes and backslashes as you would in command-line parsing. A few usage examples:

## docker run

`docker run [OPTIONS] IMAGE[:TAG|@DIGEST] [COMMAND] [ARG...]`

The docker run command must specify an IMAGE to derive the container from. An image developer can define image defaults related to:

- detached or foreground running
- container identification
- network settings
- runtime constraints on CPU and memory

__Foreground Mode (Default) vs Background/Detached Mode__

Before starting a Docker container, you must, first of all, decide if you want to run it in the default foreground mode or in the background in a detached mode.

In the foreground mode, Docker can start the process in the container and attach the console to the process’s standard input, standard output, and standard error.

There are also command line options to configure it more such as `-t` to allocate a pseudo-tty to the process, and `-i` to keep STDIN open even if not attached. You can also attach it to one or more file descriptors (STDIN, STDOUT and/or STDERR) using the `-a=[value here]` flag.

Importantly, the `--rm` option tells Docker to automatically remove the container when it exits.

The disadvantage of running a container in the foreground is that you can not access the command prompt anymore,

To run a Docker container in the background, use the use `-d=true` or just `-d` option. By design, containers started in detached mode exit when the root process used to run the container exits, unless you also specify the `--rm` option. If you use -d with --rm, the container is removed when it exits or when the daemon exits, whichever happens first.

```sh
#Foregroound Mode
docker run --rm -ti -p 8000:80 -p 8443:443 --name pandorafms pandorafms/pandorafms:latest

# Bachround Mode
docker run -d --rm -p 8000:80 -p 8443:443 --name pandorafms pandorafms/pandorafms:latest
```

To do input/output with a detached container use network connections or shared volumes. These are required because the container is no longer listening to the command line where docker run was run.

To reattach to a detached container, use `docker attach` command.
