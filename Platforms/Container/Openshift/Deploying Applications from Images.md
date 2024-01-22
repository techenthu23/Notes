
OpenShift supports deployment of container images hosted on any image registry that can be accessed from the OpenShift cluster.

When you leave off the hostname for the image registry, OpenShift will default to first looking for the image on any global image registries that a cluster admin has specified in the cluster configuration. It is typical to have the Docker Hub image registry included in that list. A company image registry or the Red Hat Container Registry might also be included.

To deploy the container image use the oc new-app command, providing it with the location of the image. The --name option is to set the name for the deployed application. If a name is not supplied, it will default to the last part of the image name.

```
oc new-app openshiftkatacoda/blog-django-py --name blog
```

When you create the deployment, a series of resource objects will be created that tell OpenShift what it needs to do. In this case, an imagestream, deploymentconfig, and service were created.

The imagestream is a record of the image you want deployed. The deploymentconfig captures the details of how the deployment should be done. The service maintains a mapping to instances of your application so it can be accessed.

When a container image is deployed from the command line using oc new-app, the running container will not be visible outside the OpenShift cluster. In the case of a web application, you can make it visible by exposing the service. This is done using the oc expose command:

```
oc expose service/blog
```

This will create a resource object called a route. With the application deployed and visible, you can check on the status of the overall project using the oc status command:

To get a list of the instances of the application that were deployed you can use the command oc get pods:

To get a list of the resource objects created for the application you can use the oc get all command:

```
oc get all -o name --selector app=blog
```

A `replicationcontroller` is created from the deploymentconfig. It records a count of how many instances of your application should be running at any point in time. OpenShift will create as many pods as are needed to match the required count.

# Scaling Up the Application

When oc new-app is used to deploy an application from a container image, only one instance of the application will be started. If you need to run more than once instance in order to handle the expected traffic, you can scale up the number of instances by running the oc scale command against the deployment configuration:

```
$ oc scale --replicas=3 dc/blog
deploymentconfig "blog" scaled
```

When the web application is scaled up, OpenShift will automatically reconfigure the router through which it is exposed to the public to load-balance between all instances of the application.

Instead of manually scaling the number of instances, you can enable automatic scal ing based on metrics collected for an application. For further information, check out the OpenShift documentation on pod autoscaling.

# Runtime Configuration

Configuration for an application can be supplied by setting environment variables in the container or by mounting configuration files into the container. You set required environment variables by using the --env option when running the oc new-app command:

```
$ oc new-app openshiftkatacoda/blog-django-py --name blog \
--env BLOG_BANNER_COLOR=green
```

Optional environment variables can be set later by running the oc set env com mand against the deployment configuration:

```
$ oc set env dc/blog BLOG_BANNER_COLOR=green
deploymentconfig "blog" updated
```

When the environment variables are updated using oc set env, the application will be redeployed automatically with the new configuration. If you want to see what envi ronment variables will be set in the container, you can use oc set env with the -- list option:

```
$ oc set env dc/blog --list
# deploymentconfigs blog, container blog
BLOG_BANNER_COLOR=green
```

# Deleting the Application

When you no longer need the application, you can delete it using the oc delete command.

When doing this, you need to be selective about which resource objects you delete to ensure you delete only those for that particular application. This can be achieved by using the label that was applied by the oc new-app and oc expose commands to the resource objects created:

```
$ oc delete all --selector app=blog
imagestream "blog" deleted
deploymentconfig "blog" deleted
route "blog" deleted
service "blog" deleted
```

The pod and replicationcontroller may not be listed in the output from running oc delete, but they will be deleted. This is because they are deleted as a side effect of deleting the deploymentconfig for the application.

## Importing an Image

When you deploy an application from an existing container image hosted on an
external image registry, a copy of the image is downloaded and stored into an image
registry internal to OpenShift. The image is then copied from there to each node in a
cluster where the application is run.
In order to track the image that has been downloaded, an image stream definition is
created. To see the list of the image stream definitions, run oc get is:

```
oc get is
```

The `<IP>:<PORT>` shown under DOCKER REPO is the address of the internal image registry.

Because the image is being used as part of an application deployment, it is labeled with the app label for the application. If you delete the application using the label, this will also delete the image stream and image. 

If you need to deploy multiple separate applications from one image, you should import the image into OpenShift first using oc import-image:

```
$ oc import-image openshiftkatacoda/blog-django-py --confirm
The import completed successfully.
Name:
...
blog-django-py
You can then deploy the applications from the imported image:
```

```
$ oc new-app blog-django-py --name blog
```

In the web console, instead of the Image Name option on the Deploy Image page, you would use the Image Stream Tag option. 

Because the image is imported prior to deploying an application, it is not tagged with the app label of a specific deployment. When you delete any of the applications using the label, you will not delete the image stream, leaving it in place for the other applications that depend on it.

Pushing to the Registry
The methods described thus far for deploying from an image relied on being able to pull the image from an external image registry. If you are using tools on your own local computer to build an image you can bypass the need to first push the image to an external image registry and instead push directly to the internal image registry of OpenShift.
To use the internal image registry, you need to know the address for accessing it.

For OpenShift Online, the internal image registry is accessible using an address of the form:
`registry.<cluster-name>.openshift.com:443`

To log in with the docker command, run:
```
$ docker login -u `oc whoami` -p `oc whoami --show-token` \
registry.pro-us-east-1.openshift.com:443
Login Succeeded
```

Before you can push an image, you need to create an empty image stream for it using oc create imagestream.

```
oc create imagestream blog-django-py
```

Next, tag the local image you wish to push with the details of the image registry, your project in OpenShift, and the name of the image stream and image version tag:

```
$ docker tag blog-django-py \
registry.pro-us-east-1.openshift.com:443/book/blog-django-py:latest
```

You are then ready to push the image to the OpenShift internal image registry:

```

docker push registry.pro-us-east-1.openshift.com:443/book/blog-django-py

```

The application can then be deployed using the image stream name.
