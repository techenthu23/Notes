# Image Streams

An image stream is just a set of references (tags) which point to Docker images. An image stream acts as a pointer to a set of images. An image stream doesn’t contain the Docker image itself. It’s a signpost to images, which can be in any registry – either OpenShift’s internal registry, or an external one.

## Why would you use them?

An image stream is a single pointer to a set of related images.

One image stream can contain many different tags (latest, develop, 1.0, etc), each of which points to an image in a registry.

Whenever you want to deploy an application in a project, instead of having to hardcode the registry URL and tag, you can just refer to the image stream tag.

If the source image changes location in future, you just need to update the image stream definition, instead of updating all your deployments individually!

An image stream and its associated tags provide an abstraction for referencing container images from within OpenShift Container Platform. The image stream and its tags allow you to see what images are available and ensure that you are using the specific image you need even if the image in the repository changes.

Image streams do not contain actual image data, but present a single virtual view of related images, similar to an image repository.

Image streams can also be used as triggers. This means that you can set an image stream as a source for a Build or a Deployment. When the image changes, this can trigger a new Build, or trigger a redeployment of your app.

## Where are image streams stored in OpenShift?

The metadata is stored in the etcd instance along with other cluster info.

## Access control for images

You can lock away your images in a private registry and control access to them through image streams.

Because image streams are like other objects in OpenShift, they can be restricted, if you like. So you can restrict images to only be used by specific service accounts.

## Create an image stream

Here’s how I would create an image stream using the command line.

Create an image stream using the oc import-image command. Give the image stream a name and then the image you want to import.

```
oc import-image approved-apache:2.4 \
    --from=bitnami/apache:2.4 \
    --confirm
```

This creates an image stream in your project, called approved-apache. It has one tag, 2.4, which points to the tag 2.4 on the image bitnami/apache.

But wait, you didn’t specify a registry?

That’s right. I don’t specify a registry here, because Docker Hub is configured as one of OpenShift’s default search registries. So it’ll search in the Red Hat registry and Docker Hub, which is where it will find the image.

Then the image will be pulled into OpenShift’s internal registry.

If you’re a cluster-admin, you can see the image in the OpenShift registry, by typing oc get images:

```
$ oc get images | grep bitnami
sha256:aa742bfa021b81f8d...   bitnami/apache@sha256:aa742bfa021b81f8d...
```

The image stream image consists of the image stream name and image ID from the repository, delimited by an @ sign: `<image-stream-name>@<image-id>`

## Declaring it with YAML

Once you’ve learned how to create image streams using the oc command, you probably want to progress on to creating objects declaratively. This is a much better way to do it, because you can store all of your configuration as files in Git, rather than a bunch of commands.

To do this, you create an ImageStream object in YAML. The YAML for an ImageStream looks something like this:

```yaml
apiVersion: image.openshift.io/v1
kind: ImageStream
metadata:
  name: approved-apache
spec:
  lookupPolicy:
    local: false
  tags:
  - name: "2.4"
    from:
      kind: DockerImage
      name: bitnami/apache:2.4
    referencePolicy:
      type: Source
```

Then apply this using `oc apply <filename>`.

## How do you use an image stream?

The way I use image streams is as sources for Builds and Deployments.

## Deploying an image stream

Once you’ve created an image stream, you can run `oc new-app --image-stream=<name>` to easily create a Deployment and a Service from it.

OpenShift will inspect the image to see which ports to expose, and then create a Deployment and a Service for your app.

Let’s import a sample image that I made:

```
oc import-image my-python \
    --from=quay.io/tdonohue/python-hello-world:latest \
    --confirm
```

Now I create a Deployment and a Service to deploy it in my project:

```
oc new-app --image-stream=my-python
```

### Using an image stream as a source image in a build

Or, you can use an image stream as a source for a build.

Here’s an example of a BuildConfig which uses an image stream as a source:

```yaml
kind: BuildConfig
apiVersion: build.openshift.io/v1
metadata:
  name: example-app
spec:
  source:
    type: Git
    git:
      uri: 'https://github.com/sclorg/nodejs-ex.git'
  strategy:
    type: Source
    sourceStrategy:
      from:
        kind: ImageStreamTag
        name: 'nodejs-8-centos7:latest'
  output:
    to:
      kind: ImageStreamTag
      name: 'example-app:latest'
  triggers:
    - type: ConfigChange
    - type: ImageChange
```

## What if your image is in a private registry?

Yep, you can still use image streams if your image is in a private registry.

Firstly, if the registry is password-protected, make sure you create a Secret with your credentials for the external registry, so OpenShift can pull the image.

Here I’m connecting to the registry on the Mars rover (well, maybe one day Mars rovers will run OpenShift):

```
oc create secret docker-registry my-mars-secret \
  --docker-server=registry.marsrover.space \
  --docker-username="login@example.com" \
  --docker-password=thepasswordishere
```

Then link the secret to your builder and default service accounts:

```
oc get sa                       # Get Service Account
                                # oc describe sa <acct>

# to allow a secret to be used as an image pull secret by a service account
# oc secrets link --for=pull <serviceaccount-name> <secret-name>

oc secrets link builder my-mars-secret
oc secrets link default my-mars-secret --for=pull   # 
```

Now we’re good to import the image, using the oc import-image command:

```
oc import-image marsview \
    --from=registry.marsrover.space/engine/marsview \
    --confirm
```

Congrats! You’ve created an image stream to import an image from a private registry, with authentication.

# What about triggering when the source image changes?
If your image stream points to an image in OpenShift’s internal registry (perhaps, like an image you’ve built inside OpenShift), then OpenShift gets notified automatically when your image changes.

But if you want to tell OpenShift to keep checking an external image for changes, you’ll need to use the `--scheduled` tag when you use `oc import-image`.

```
oc import-image marsview \
    --from=registry.marsrover.space/engine/marsview \
    --scheduled --confirm
```

When you use this option, you’ll see this in the output from oc import-image. Note how it says “updates automatically”:

```
Name:   marsview
Namespace:  toms-project
Created:  12 minutes ago
Labels:   <none>
Annotations:  openshift.io/image.dockerRepositoryCheck=2021-01-13T18:54:19Z
Image Repository: image-registry.openshift-image-registry.svc:5000/toms-project/marsview
Image Lookup:  local=false
Unique Images:  1
Tags:   1

latest
  updates automatically from registry registry.marsrover.space/engine/marsview:latest

* registry.marsrover.space/engine/marsview@sha256:a7ca9b84c7a642ac14294679ba6fb9c80918ea210f3c63154d23231912d41394
      About a minute ago

```

## When does the image update?

OpenShift will keep checking the image, usually around every 15 minutes (see below for how to change this.)

Wait 15 minutes. Put the kettle on. Whatever it takes to run down the clock.

If a new image is pushed, you’ll see something like this, when you run oc describe is/marsview:

```
latest
  updates automatically from registry registry.marsrover.space/engine/marsview:latest

* registry.marsrover.space/engine/marsview@sha256:77dc2d9740a2450dec03cef69d4c42ad3ca900b6b0d93dcbf0c7f46b598f8d9c
      About a minute ago
    registry.marsrover.space/engine/marsview@sha256:a7ca9b84c7a642ac14294679ba6fb9c80918ea210f3c63154d23231912d41394
      19 minutes ago
```

Bingo, the image updated about a minute ago!

Then, as long as your DeploymentConfig is configured with a Trigger of “ImageChange”, it will automatically trigger a redeployment.

Here’s what a DeploymentConfig for this app would look like, with a trigger:

```yaml
kind: DeploymentConfig
apiVersion: apps.openshift.io/v1
metadata:
  name: marsview
spec:
  selector:
    deploymentconfig: marsview
  replicas: 1
  template:
    metadata:
      labels:
        deploymentconfig: marsview
    spec:
      containers:
        - name: main
          image: marsview    # OpenShift will replace this with the 'real' image URL
          ports:
            - containerPort: 8080
              protocol: TCP
          imagePullPolicy: Always
      restartPolicy: Always
  triggers:
    - type: ConfigChange
    - type: ImageChange
      imageChangeParams:
        automatic: true       # Set automatic=true to trigger a redeployment on image change
        containerNames:
          - marsview
        from:
          kind: ImageStreamTag
          namespace: toms-project
          name: 'marsview:latest'
```

This allows you to implement basic continuous deployment. The moment that a new image is pushed to your latest tag, OpenShift will automatically trigger an update of your app. Fab.

# How often are images imported?

How often images are imported, is a cluster-wide setting. Look for these settings in /etc/origin/master/master-config.yaml:

```
imagePolicyConfig:
    MaxScheduledImageImportsPerMinute: 10
    ScheduledImageImportMinimumIntervalSeconds: 1800
    disableScheduledImport: false
    maxImagesBulkImportedPerRepository: 3
```

# Adding additional tags to the image stream

To add additional tags to an image stream, you can use oc tag.

The new tag doesn’t need to be in the same registry, it could be somewhere else entirely.

Here I’m adding a develop tag to my image stream, pointing to a different registry (registry.spacedev.com):

```
oc tag registry.spacedev.com/engine/marsview:develop \
    marsview:develop \
    --scheduled
which will say this:
```

Tag marsview:develop set to import registry.marsrover.space/engine/marsview:develop periodically.

Job done. Now I can reference the develop tag from my development registry.

TL;DR. Do I really need them?
No, you don’t really need them. But they are useful for:

Creating one place to pull images from external registries, so you don’t have to keep track of everywhere you’re using an image

Creating triggers for your builds and deployments

Understanding how the built-in Red Hat images are installed in the cluster (you’ll see them in the openshift project)

And here are the key terms.

| TERM               | WHAT IT IS                                                                                                                                           |
| ------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------- |
| Image stream       | A set of pointers (tags). Each tag points to a Docker image.                                                                                         |
| Image stream tag   | A tag within an image stream. The tag points to an individual Docker image in a registry somewhere, either the internal registry or an external one. |
| Image stream image | A pointer from within an image stream, to a particular image ID. Basically lets you get all the details (metadata) of the actual image itself.       |

# How to delete references in ImageStream for deleted image

Deleting the image alone will not immediately remove the reference to the image from the imagestreamtag.

We need to run oc adm prune to clean up the references as well.

This is an improvement in pruning images as pruning will now remove the reference.

Steps

```
# Reference 3 images to one imagestreamTag, delete 2 images:
oc delete image sha256:6a448ad9ac44348523cd13fef635ed1ac220a7a0f95b2a5f200f2b42a910d1d6 sha256:c2e5ffc909fc598628caebe56571849cbcbeea6c99f885a9870cb8c5967d5111

# Prune image from imagestreamTag:
oadm prune images --keep-tag-revisions=1 --keep-younger-than=0 --certificate-authority=ca.crt --registry-url=docker-registry-default.com --confirm

# Only one image references to imagestreamTag.
```
