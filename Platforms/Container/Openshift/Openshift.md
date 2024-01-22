e---
layout: default
title:  "Openshift"
---

# Openshift

- [Openshift](#openshift)
- [Application "Self Healing"](#application-self-healing)
- [Routes](#routes)
- [Logging](#logging)
- [Permissions](#permissions)
- [Understanding Deployment and DeploymentConfig objects](#understanding-deployment-and-deploymentconfig-objects)
  - [Comparing Deployment and DeploymentConfig objects](#comparing-deployment-and-deploymentconfig-objects)
  - [Design](#design)
  - [Building blocks of a deployment](#building-blocks-of-a-deployment)
  - [Replication controllers](#replication-controllers)
- [Adding Applications to a Project](#adding-applications-to-a-project)
  - [The Role of a Project](#the-role-of-a-project)
    - [Creating a Project](#creating-a-project)
    - [Deploying Applications](#deploying-applications)
      - [Templates](#templates)
      - [Catalog](#catalog)
    - [Deploying an Image](#deploying-an-image)





Services provide a convenient abstraction layer inside OpenShift to find a group of similar Pods. They also act as an internal proxy/load balancer between those Pods and anything else that needs to access them from inside the OpenShift environment. For example, if you needed more parksmap instances to handle the load, you could spin up more Pods. OpenShift automatically maps them as endpoints to the Service, and the incoming requests would not notice anything different except that the Service was now doing a better job handling the requests.

When you asked OpenShift to run the image, it automatically created a Service for you. Remember that services are an internal construct. They are not available to the "outside world", or anything that is outside the OpenShift environment. That’s okay, as you will learn later.

The way that a Service maps to a set of Pods is via a system of Labels and Selectors. Services are assigned a fixed IP address and many ports and protocols can be mapped.

```
 oc get services 
```

each Service receives a unique IP address upon creation. Service IPs are fixed and never change for the life of the Service.

Any Pod in this Project that has a Label that matches the Selector will be associated with the Service. To see this in action, issue the following command:

```
oc describe service parksmap
```

Background: Deployments and ReplicaSets

While Services provide routing and load balancing for Pods, which may go in and out of existence, ReplicaSet (RS) and ReplicationController (RC) are used to specify and then ensure the desired number of Pods (replicas) are in existence. For example, if you always want your application server to be scaled to 3 Pods (instances), a ReplicaSet is needed. Without an RS, any Pods that are killed or somehow die/exit are not automatically restarted. ReplicaSets and ReplicationController are how OpenShift "self heals" and while Deployments control ReplicaSets, ReplicationController here are controlled by DeploymentConfigs.

Similar to a replication controller, a ReplicaSet is a native Kubernetes API object that ensures a specified number of pod replicas are running at any given time. The difference between a replica set and a replication controller is that a replica set supports set-based selector requirements whereas a replication controller only supports equality-based selector requirements.

In Kubernetes, a Deployment (D) defines how something should be deployed. In almost all cases, you will end up using the Pod, Service, ReplicaSet and Deployment resources together. And, in almost all of those cases, OpenShift will create all of them for you.

There are some edge cases where you might want some Pods and an RS without a D or a Service, and others

```
oc get deployment 
oc get rs 
```

OpenShift’s HorizontalPodAutoscaler effectively monitors the CPU usage of a set of instances and then manipulates the RCs accordingly.

```
oc scale --replicas=2 deployment/parksmap 
```

Another way to look at a Service's endpoints is with the following

```
oc get endpoints parksmap 
```

Application scaling can happen extremely quickly because OpenShift is just launching new instances of an existing image, especially if that image is already cached on the node.

# Application "Self Healing"

Because OpenShift’s RSs are constantly monitoring to see that the desired number of Pods actually are running, you might also expect that OpenShift will "fix" the situation if it is ever not right. You would be correct!

Additionally, OpenShift provides rudimentary capabilities around checking the liveness and/or readiness of application instances. If the basic checks are insufficient, OpenShift also allows you to run a command inside the container in order to perform the check. That command could be a complicated script that uses any installed language.

# Routes

While Services provide internal abstraction and load balancing within an OpenShift environment, sometimes clients (users, systems, devices, etc.) outside of OpenShift need to access an application. The way that external clients are able to access applications running in OpenShift is through the OpenShift routing layer. And the data object behind that is a Route.

The default OpenShift router (HAProxy) uses the HTTP header of the incoming request to determine where to proxy the connection. You can optionally define security, such as TLS, for the Route. If you want your Services, and, by extension, your Pods, to be accessible from the outside world, you need to create a Route.

The TLS certificate for cluster Apps domains is used by default, so you don’t need to add any certificate. In case you would like a custom domain resolving to OpenShift Nodes hosting Routers, you can add certificates also on a per-route basis.

When creating a Route, some other options can be provided, like the hostname and path for the Route or the other TLS configurations.

```
oc create route edge parksmap --service=parksmap 
```

# Logging

OpenShift provides some convenient mechanisms for viewing application logs. First and foremost is the ability to examine a Pod's logs directly from the web console or via the command line.

# Permissions

Almost every interaction with an OpenShift environment that you can think of requires going through the OpenShift’s control plane API. All API interactions are both authenticated (AuthN - who are you?) and authorized (AuthZ - are you allowed to do what you are asking?).

As OpenShift is a declarative platform, some actions will be performed by the platform and not by the end user (when he or she issues a command). These actions are performed using a Service Account which is a special type of user that the platform will use internally.

OpenShift automatically creates a few special service accounts in every project. The default service account is the one taking the responsibility of running the pods, and OpenShift uses and injects this service account into every pod that is launched. By changing the permissions for that service account, we can do interesting things.

You can view current permissions in the web console, go to the Topology view in the Developer Perspective, click the parksmap entry, go to the Details tab, and then click the Namespace. Then, select Role Bindings tab and filter by Namespace Role Binding so see all permissions for selected project.

```

2022-01-09 17:15:07.112 ERROR 1 --- [ main] c.o.e.r.rest.AbstractResourceWatcher : Error initialiting application. Probably you need the appropriate permissions to view this namespace delverdeck-dev. Failure executing: GET at: [https://172.30.0.1/apis/route.openshift.io/v1/namespaces/delverdeck-dev/routes?labelSelector=type%3Dparksmap-backend](https://172.30.0.1/apis/route.openshift.io/v1/namespaces/delverdeck-dev/routes?labelSelector=type%3Dparksmap-backend). Message: Forbidden!Configured service account doesn't have access. Service account may have been revoked. routes.route.openshift.io is forbidden: User "system:serviceaccount:delverdeck-dev:default" cannot list resource "routes" in API group "route.openshift.io" in the namespace "delverdeck-dev". 

2022-01-09 17:15:07.372 ERROR 1 --- [ main] c.o.e.r.rest.AbstractResourceWatcher : Error initialiting application. Probably you need the appropriate permissions to view this namespace delverdeck-dev. Failure executing: GET at: [https://172.30.0.1/api/v1/namespaces/delverdeck-dev/services?labelSelector=type%3Dparksmap-backend](https://172.30.0.1/api/v1/namespaces/delverdeck-dev/services?labelSelector=type%3Dparksmap-backend). Message: Forbidden!Configured service account doesn't have access. Service account may have been revoked. services is forbidden: User "system:serviceaccount:delverdeck-dev:default" cannot list resource "services" in API group "" in the namespace "delverdeck-dev".

```

The parksmap application wants to talk to the OpenShift API to learn about other Pods, Services, and resources within the Project. The parksmap application runs as a Service Account in our project on the cluster: namely the default Service Account. It is to this Service Account that we can grant the necessary view`access that will allow it to query the API to see what resources are within the Project. This also has the added benefit of addressing the cause of the error message we witnessed previously in the log

```
$ oc project 
Using project "delverdeck-dev" on server "https://api.sandbox-m2.ll9k.p1.openshiftapps.com:6443". 
  
$ oc policy add-role-to-user view -z default 
```

-z, --serviceaccount=[]: service account in the current namespace to use as a user

The -z syntax is a special one that saves us from having to type out the entire string, which, in this case, is system:serviceaccount:%PROJECT%:default. It’s a nifty shortcut. The -z flag will only work for service accounts in the current project. If you’re referring to a service account in a different project, use the -n &lt;project>switch.

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
 name: view
 namespace: delverdeck-dev
 uid: ca107c57-3d8e-49de-85b4-38eca96e3d93
 resourceVersion: '521688491'
 creationTimestamp: '2022-01-09T19:55:44Z'
 managedFields:
   - manager: Mozilla
     operation: Update
     apiVersion: rbac.authorization.k8s.io/v1
     time: '2022-01-09T19:55:44Z'
     fieldsType: FieldsV1
     fieldsV1:
       'f:roleRef': {}
       'f:subjects': {}
subjects:
 - kind: ServiceAccount
   name: default
   namespace: delverdeck-dev
roleRef:
 apiGroup: rbac.authorization.k8s.io
 kind: ClusterRole
 name: view

```

Connecting to a Containers

Containers are treated as immutable infrastructure and therefore it is generally not recommended to modify the content of a container through SSH or running custom commands inside the container. Nevertheless, in some use-cases, such as debugging an application, it might be beneficial to get into a container and inspect the application.

Remote Shell Session to a Container Using the CLI

OpenShift allows establishing remote shell sessions to a container without the need to run an SSH service inside each container. In order to establish an interactive session inside a container, you can use the oc rsh command

The default shell used by oc rsh is /bin/sh. If the deployed container does not have sh installed and uses another shell, (e.g. A Shell) the shell command can be specified after the pod name in the issued command.

```
oc rsh parksmap-74489f47bc-847ts /bin/sh 
```

The OpenShift Web Console also provides a convenient way to access a terminal session on the container without having to use the CLI.

In order to access a pod’s terminal via the Web Console, go to the Topology view in the Developer Perspective, click the parksmap entry, and then click on the Pod.

In addition to remote shell, it is also possible to run a command remotely in an already running container using the oc exec command. This does not require that a shell is installed, but only that the desired command is present and in the executable path.

```
$ oc exec parksmap-74489f47bc-847ts -- ls -l /parksmap.jar 
-rw-r--r--. 1 root root 39295028 Feb 1 2021 /parksmap.jar 
```

It is important to understand that, for security reasons, OpenShift does not run containers as the user specified in the Dockerfile by default. In fact, when OpenShift launches a container its user is actually randomized.

If you want or need to allow OpenShift users to deploy container images that do expect to run as root (or any specific user), a small configuration change is needed. You can learn more about the container image guidelines for OpenShift.

```
oc rsh parksmap-65c4f8b676-fxcrq whoami 
```

[https://redhat-scholars.github.io/openshift-starter-guides/rhs-openshift-starter-guides/4.7/nationalparks-java.html](https://redhat-scholars.github.io/openshift-starter-guides/rhs-openshift-starter-guides/4.7/nationalparks-java.html)

[Source-to-Image (S2I)](https://github.com/openshift/source-to-image) is a open source project sponsored by Red Hat that has the following goal:

Source-to-image (S2I) is a tool for building reproducible container images. S2I produces ready-to-run images by injecting source code into a container image and assembling a new container image which incorporates the builder image and built source. The result is then ready to use with docker run. S2I supports incremental builds which re-use previously downloaded dependencies, previously built artifacts, etc.

OpenShift is S2I-enabled and can use S2I as one of its build mechanisms (in addition to building container images from Dockerfiles, and "custom" builds).

OpenShift runs the S2I process inside a special Pod, called a Build Pod, and thus builds are subject to quotas, limits, resource scheduling, and other aspects of OpenShift.

template - a preconfigured set of resources that includes parameters that can be customized.  

```
# Console : https://console-openshift-console.apps.sandbox-m2.ll9k.p1.openshiftapps.com/
# API : https://api.sandbox-m2.ll9k.p1.openshiftapps.com:6443 
# You must obtain an API token by visiting https://oauth-openshift.apps.sandbox-m2.ll9k.p1.openshiftapps.com/oauth/token/request
TOKN=sha256~dDoKyCCtX18PjaL6Es8nl3DqqL9tQyevoqC4fdUtzWU

oc login --token=${TOKEN} --server=https://api.sandbox-m2.ll9k.p1.openshiftapps.com:6443  
oc create project <project_name> 
oc project <project_name>           # Change to the project 
 
 
oc create -f <file-name>.yaml       # Create the object using the object definition using file or dir path 
oc describe pod <pod name> 
 
 
oc get is  
oc get service 
oc get endpoints 
oc get pods 
oc get deployment 
oc get rs 
 
oc get deployment 
oc scale --replicas=2 deployment/parksmap 
oc get dc image-registry 
oc autoscale dc/image-registry --min 3 --max 7 --cpu-percent=75 
 
oc get routes 
oc create route edge parksmap --service=parksmap 
 
oc rsh <podname> 
oc rsh <podname> sh 
oc exec <pod> -- <command> 

oc adm top pods                 #  
oc get secrets 
oc get priorityclasses          # Pod priority classes 
oc get events 
oc logs -f <pod_name> -c <container_name> 
oc get sa                       # get service account 
oc rollout restart deployment/parksmap 
oc policy add-role-to-user view -z default

oc get useroauthaccesstokens        # Client : console, openshift-browser-client
oc get useroauthaccesstokens --field-selector=clientName="console"
oc describe useroauthaccesstokens <token_name>
oc delete useroauthaccesstokens <token_name>


export ENDPOINT=myose01:8443
https://oauth-openshift.apps.sandbox-m2.ll9k.p1.openshiftapps.com/oauth/token/request
export TOKEN=$(curl -u user1:test@123 -kI "https://$ENDPOINT/oauth/authorize?clientid=openshift-challenging-client&response_type=token" | grep -oP "access_token=\K[^&]*")
curl -k \
    -H "Authorization: Bearer $TOKEN" \
    -H 'Accept: application/json' \
    https://$ENDPOINT/oapi/v1/projects

```

Restarting Pods

You can scale deployments down (to zero) and then up again:

```
oc get deployments -n <your project> -o wide 
 
oc get pods -n <your project> -o wide 
 
oc scale --replicas=0 deployment/<your deployment> -n <your project> 
 
oc scale --replicas=1 deployment/<your deployment> -n <your project> 
 
# watch  
oc get pods -n <your project>  
 
# wait until your deployment is up again
```

# Understanding Deployment and DeploymentConfig objects

The Deployment and DeploymentConfig API objects in OpenShift Container Platform provide two similar but different methods for fine-grained management over common user applications. They are composed of the following separate API objects:

- A DeploymentConfig or Deployment object, either of which describes the desired state of a particular component of the application as a pod template.

- DeploymentConfig objects involve one or more **replication controllers**, which contain a point-in-time record of the state of a deployment as a pod template. Similarly, Deployment objects involve one or more **replica sets**, a successor of replication controllers.

- One or more pods, which represent an instance of a particular version of an application.

## Comparing Deployment and DeploymentConfig objects

Both Kubernetes Deployment objects and OpenShift Container Platform-provided DeploymentConfig objects are supported in OpenShift Container Platform; however, it is recommended to use Deployment objects unless you need a specific feature or behavior provided by DeploymentConfig objects.

The following sections go into more detail on the differences between the two object types to further help you decide which type to use.

## Design

___One important difference between Deployment and DeploymentConfig objects is the properties of the [CAP theorem](https://en.wikipedia.org/wiki/CAP_theorem) that each design has chosen for the rollout process. DeploymentConfig objects prefer consistency, whereas Deployments objects take availability over consistency.___

For DeploymentConfig objects, if a node running a deployer pod goes down, it will not get replaced. The process waits until the node comes back online or is manually deleted. Manually deleting the node also deletes the corresponding pod. This means that you can not delete the pod to unstick the rollout, as the kubelet is responsible for deleting the associated pod.

However, deployment rollouts are driven from a controller manager. The controller manager runs in high availability mode on masters and uses leader election algorithms to value availability over consistency. During a failure it is possible for other masters to act on the same deployment at the same time, but this issue will be reconciled shortly after the failure occurs.

## Building blocks of a deployment

Deployments and deployment configs are enabled by the use of native Kubernetes API objects ReplicaSet and ReplicationController, respectively, as their building blocks.

Users do not have to manipulate replication controllers, replica sets, or pods owned by DeploymentConfig objects or deployments. The deployment systems ensure changes are propagated appropriately.

> If the existing deployment strategies are not suited for your use case and you must run manual steps during the lifecycle of your deployment, then you should consider creating a custom deployment strategy

## Replication controllers

A replication controller ensures that a specified number of replicas of a pod are running at all times. If pods exit or are deleted, the replication controller acts to instantiate more up to the defined number. Likewise, if there are more running than desired, it deletes as many as necessary to match the defined amount.

Replica sets
Similar to a replication controller, a ReplicaSet is a native Kubernetes API object that ensures a specified number of pod replicas are running at any given time. The difference between a replica set and a replication controller is that a replica set supports set-based selector requirements whereas a replication controller only supports equality-based selector requirements.

> Only use replica sets if you require custom update orchestration or do not require updates at all. Otherwise, use deployments. Replica sets can be used independently, but are used by deployments to orchestrate pod creation, deletion, and updates. Deployments manage their replica sets automatically, provide declarative updates to pods, and do not have to manually manage the replica sets that they create.

# Adding Applications to a Project

Applications can be deployed from an existing container image that you have built
outside the OpenShift cluster, or one that is supplied by a third party.
Or, if you have the source code for the application, you can have OpenShift build the
image for you. OpenShift can build an image from instructions provided by a Docker
file, or the source code can be run through a Source-to-Image (S2I) builder to pro‐
duce the container image.
OpenShift also includes ready-to-run container images for popular database products
such as PostgreSQL, MySQL, MongoDB, and Redis.
Before you can deploy any application, though, you first need to create a project in
the OpenShift cluster to contain your applications

## The Role of a Project

Whenever you work with OpenShift, you will work within the context of a project. This is a walled namespace used to hold everything related to a set of applications.

When you create a project, it is owned by you and you are the administrator for that project. Any application you deploy within the project is only visible to other applications running in the same project, unless you choose to make it public outside the OpenShift cluster.

You can deploy more than one application into a single project. You would usually do this if they have tight coupling. Or, you could instead choose to always create a separate project for each application and selectively set up access between projects if they needed to communicate with each other.

### Creating a Project

You can create a Project via webconsole or via command line . When you specify a name for a project, it will need to satisfy a couple of requirements.

  1. name you choose must be unique across the whole OpenShift cluster. This means you cannot use a project name that is already in use by another user.
  2. name can only include lowercase letters, numbers, and the dash character. This is necessary as the project name is used as a component in the hostname assigned to an application when it is made visible outside the OpenShift cluster.

```openshift
# To create a project 
$ oc new-project myproject --display-name 'My Project'
Already on project "myproject" on server "https://localhost:8443".

# To list all projects
$ oc projects

# To get the name of the current project
$ oc project

# to set the current project
$ oc project myproject

# to run a single command against a different project, you can pass the name of the project using the --namespace option
$ oc get templates --namespace openshift


```

> The openshift project is a special project that acts as a repository for images and templates available for use by everyone in the OpenShift cluster. Although it doesn’t appear in your own project list, you can still query it for certain information.

When adding a user to the project, they can be added in one of three primary roles:

- admin
A project manager. The user will have rights to view any resource in the project and modify any resource in the project except for quotas. A user with this role for a project will be able to delete the project.
- edit
A user that can modify most objects in a project, but does not have the power to view or modify roles or bindings. A user with this role can create and delete applications in the project.
- view
A user who cannot make any modifications, but can see most objects in a project.


```
$ oc whami

# To add another user with edit role to the project, so they can create and delete applications, you need to use the oc adm policy command. You must be in the project
when you run this command:

$ oc adm policy add-role-to-user edit <collaborator>

# Replace <collaborator> with the name of the user as displayed by the oc whoami command when run by that user.

# To remove a user from a project, run:
$ oc adm policy remove-role-from-user edit <collaborator>

# To get a list of the users who have access to a project and their roles, a project manager can run the oc get rolebindings command.
$ oc get rolebindings

```

### Deploying Applications
The main methods for deploying an application are:
1. From an existing container image hosted on an image registry located outside the OpenShift cluster.
2. From an existing container image that has been imported into the image registry running inside the OpenShift cluster.
3. From application source code in a Git repository hosting service. The application source code would be built into an image inside OpenShift, using an S2I builder.
4. From image source code in a Git repository hosting service. The image source code would be built into an image inside OpenShift using instructions provided in a Dockerfile.
5. From application source code pushed into OpenShift from a local filesystem using the command-line oc client. The application source code would be built into an image inside OpenShift using an S2I builder.
6. From image source code pushed into OpenShift from a local filesystem using the command-line oc client. The image source code would be built into an image inside OpenShift using instructions provided in a Dockerfile.


#### Templates
To simplify deployment of applications that have multiple component parts, or that require configuration to be provided when creating the application, OpenShift pro‐ vides a mechanism for defining templates. A **template** can be used to set up deploy‐ ment of one or more applications at the same time using any of the methods listed. Parameters can be provided to a template when creating the applications, with values being used to fill out any configuration in resource objects that are defined by the template.

For maximum configurability and control, an application deployment can also be directly described using a list of the resource objects to be created. These lists can be provided as YAML or JSON.

#### Catalog
Via Web you have catalog of templates and S2I builders. The catalog is constructed automatically from a number of sources. The list of S2I builders you can use is derived by looking for images in the current project and the openshift project.

The openshift project acts as a global repository for builder images and templates. If an administrator wants to make available a builder image or application template to the whole OpenShift cluster, this is where they should add them.

Because what is included in the openshift project is controlled by the administrator of the OpenShift cluster, what you find listed in the catalog may differ between Open‐ Shift clusters. What appears can also differ based on whether OpenShift Origin or the Red Hat OpenShift Container Platform product is used.

```
# to get list of application templates
$ oc get templates

# To list the templates available in the openshift project
$ oc get templates --namespace openshift

# To get list of imagestreams 
$ oc get imagestreams

# to list combining application templates and builder images for both the current project and the openshift project
$ oc new-app -L 

# To search templates, image streams, and container images that match the arguments provided, use:

  oc new-app -S php
  oc new-app -S --template=rails
  oc new-app -S --image-stream=mysql
  oc new-app -S --image=registry.access.redhat.com/ubi8/python-38

```
> Not all images listed may correspond to a builder image. This is because an image is also constructed for the application image created by running an S2I builder


### Deploying an Image

You will be
able to see only projects that you are the owner of, other projects that you have been
explicitly granted access to, and the openshift project.