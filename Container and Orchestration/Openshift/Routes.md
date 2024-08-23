# Routes

## Connecting Between Projects

When using the unqualified name of the service for an application as the hostname, the name will only be able to be resolved from within the project where the applicaion is deployed. If you need to be able to access a backend application or database from a different project, you will need to use a qualified hostname that incorporates the name of the project.

The format for the fully qualified hostname is:
`<service-name>.<project-name>.svc.cluster.local`

For example, if the service name is blog and it’s deployed in the project myproject, the fully qualified hostname will be blog.myproject.svc.cluster.local.

As an installation of OpenShift is by default provisioned as multitenant, and applications in one project cannot see any applications deployed in different projects, before you can attempt to make connections directly from one project to another you will need to enable access. To open up access between projects you can use the `oc adm pod-network` command.

If you are using Minishift or oc cluster up, these do not come with the multitenant network overlay enabled. This means you will actually be able to make connections across projects without needing to do anything.

If you require this ability, check out the OpenShift documentation on managing networks.

While Services provide internal abstraction and load balancing within an OpenShift environment, sometimes clients (users, systems, devices, etc.) outside of OpenShift need to access an application. The way that external clients are able to access applications running in OpenShift
The default OpenShift router (HAProxy) uses the HTTP header of the incoming request to determine where to proxy the connection. You can optionally define security, such as TLS, for the Route. If you want your Services, and, by extension, your Pods, to be accessible from the outside world, you need to create a Route.

The TLS certificate for cluster Apps domains is used by default, so you don’t need to add any certificate. In case you would like a custom domain resolving to OpenShift Nodes hosting Routers, you can add certificates also on a per-route basis.

## External Routes

Neither the direct IP address of a pod or service nor the hostname for a service can be used to access an application from outside the OpenShift cluster. In order for a web application to be public and accessible from outside the OpenShift cluster is through the OpenShift routing layer. And the data object behind that is a Route which we will need to create it

To expose a service for a web application so it can be accessed externally by a user, you can run the `oc expose service` command, passing the name of the service as an additional argument:

```
$ oc expose service/blog
route "blog" exposed
```

When a route is created, OpenShift will by default assign your application a unique hostname by which it can be accessed from outside the OpenShift cluster. You can see the details of the route created by running oc describe on the route:

```
$ oc describe route/blog
Name:               blog
Namespace:          myproject
Created:            5 minutes ago
Labels:             app=blog
Annotations:        openshift.io/host.generated=true
Requested Host:     blog-myproject.b9ad.pro-us-east-1.openshiftapps.com
                    exposed on router router 5 minutes ago
Path:               <none>
TLS Termination:    <none>
Insecure Policy:    <none>
Endpoint Port:      8080-tcp
Service:            blog
Weight:             100 (100%)
Endpoints:          172.17.0.4:8080
```

If you have your own custom hostname, you can use it rather than relying on the assigned hostname. This can be done by passing the hostname to oc expose using the --hostname option.

When using a custom hostname, you will need to have control over the DNS servers for the domain name. You will then need to create a CNAME record in the DNS server configuration for the hostname and point it at the hostname of the inbound router of the OpenShift cluster.

If you can’t determine the hostname of the inbound router, you can usually use as the target of the CNAME any hostname value that would fall within the wildcard subdomain used by the OpenShift cluster for generated hostnames.

For the preceding example, where the assigned hostname was: `blog-myproject.b9ad.pro-us-east-1.openshiftapps.com`

you could, as the target of the CNAME record, use:  `myproject.b9ad.pro-us-east-1.openshiftapps.com`

Where you have multiple instances of an application and a route is created, OpenShift will automatically configure routing so that traffic is distributed between the instances.

> Sticky sessions will be used so that traffic from the same client will be preferentially routed back to the same instance

## Using Secure Connections

The route created using `oc expose` only supports requests using the HTTP protocol. It cannot be used to expose a database service using a non-HTTP protocol.
In the case of HTTP traffic, if you want clients to use a secure connection, you will need to create the route using `oc create route` instead of using oc expose.

Three types of secured routes are supported:

1. edge

    The secure connection is terminated by the router, with the router proxying the connection to the application using an insecure connection over the internal cluster network. If not supplying your own hostname and SSL certificate, the SSL certificate of the OpenShift cluster will be used. Although an insecure connection is used internally, the traffic is not visible to other users of the cluster.

2. passthrough

    The router will proxy the secure connection directly through to the application. The application must be able to accept a secure connection and be configured with the appropriate SSL certificate. Provided that the client supports Server Name Identification (SNI) over a Transport Layer Security (TLS) connection, this can be used to allow access to an application using non-HTTP protocols.

3. reencrypt

    The secure connection is terminated by the router, with the router re-encrypting traffic when proxying the connection to the application. If not supplying your own hostname and SSL certificate, the SSL certificate of the OpenShift cluster will be used for the initial inbound connection. For the connection from the router to the application, the application must be able to accept a secure connecion and be configured with the appropriate SSL certificate used for the reencrypted connection.

To create a secure connection using edge termination, where the hostname assigned by OpenShift is used, run the command:

```
oc create route edge blog-secure --service blog
```

> If you have already created a route using `oc expose` for the insecure connection, the name supplied for the route must be different.

In this example the name of the route was given as blog-secure.

Rather than creating separate routes for the insecure and secure connections, you can create a single route that covers both cases by specifying how the insecure connection should be handled.

If you want to allow a web application to accept HTTP requests via both insecure and secure connections, run:

```
oc create route edge blog --service blog --insecure-policy Allow
```

If you want users who attempt to use an insecure connection to be redirected so that a secure connection is used, run:

```
oc create route edge blog --service blog --insecure-policy Redirect
```

If using your own custom hostname, you can supply it using the `--hostname` option. You will need to supply the SSL certificate files using the `--key`, `--cert`, and `--ca-cert` options

# Internal and External Ports

When hosting an application using OpenShift, the user ID that a container runs as will be assigned based on which project it is running in. Containers are not allowed to run as the root user by default. Because containers do not run as the root user, they will not be able to use privileged port numbers below 1024. A web application would not, for example, be able to use the standard port 80 used for HTTP.

Images designed not to require running as root will use a higher port number. For web applications it is typical to use port 8080 instead of port 80. You can see what ports a service is advertised as using by running oc get services. From within the OpenShift cluster, when you access a pod directly via the IP address, or via the service
using the IP address or hostname, you would use this port number.

If you expose a service externally using a route, an external user would instead use the standard port 80 for HTTP requests, or port 443 for HTTP requests over a secure connection. The routing layer will always accept requests via the standard ports and, when proxying the requests to the application, will map those to the internal port numbers.

The port used when routing an HTTP request is dictated by what port the image advertises itself as using and which was added to the service. If there is more than one port advertised by the image, the first is used. If this is the wrong port, you can overide what port will be used by passing the --port option to oc expose service or oc create route.

## Exposing Non-HTTP Services

Exposing a service via a route is only useful if you are running an HTTP-based serice or a service that can terminate a secure connection, with the client supporting SNI over a secure connection using TLS.

If you wish to expose a different type of service, you have two choices. First, you could dedicate a new public IP address for the service and configure netork routing to pass connections to any port on that address through to the IP address of the internal service. If you have multiple services using the same port, you
would need to dedicate a separate public IP address for each.

Alternatively, a dedicated port on the gateway host for the cluster could be assigned to the service. Any connections on this port would be routed through to a port on the
internal service. Assigning a specific port to the service like this would result in the port being reserved on each node in the cluster, even though it would only be in use
on one node at a time. If it were a standard port, you would not be able to use it for any other services.

Both these methods require additional setup to be performed by the cluster admin. For further information, check out the OpenShift documentation on getting traffic into a cluster on non standard ports.

## Local Port Forwarding

If the reason you want to expose a non-HTTP–based service is to allow temporary access to permit debugging of an application, loading of data, or administration, a service can be temporarily exposed to a local machine using port forwarding.
To use port forwarding, you need to identify a specific pod that you want to commuicate with. You cannot use port forwarding to expose an application via the service. Use oc get pods with an appropriate label selector to identify the pod for the appliation: $ oc get pods --selector name=postgresql

```
$ oc get pods --selector name=postgresql
NAME                READY   STATUS      RESTARTS    AGE
postgresql-1-8cng2  1/1     Running     0           5m
```

You can then run oc port-forward with the name of the pod and the port on the container you wish to connect to:

```
$ oc port-forward postgresql-1-8cng2 5432
Forwarding from 127.0.0.1:5432 -> 5432
Forwarding from [::1]:5432 -> 5432
```

The remote port will be exposed locally using the same port number. If the port number is already in use on the local machine, you can specify a different local port to use:

$ oc port-forward postgresql-1-8cng2 15432:5432
Forwarding from 127.0.0.1:15432 -> 5432
Forwarding from [::1]:15432 -> 5432

You can also have oc port-forward select a local port for you:

```
$ oc port-forward postgresql-1-8cng2 :5432
Forwarding from 127.0.0.1:48888 -> 5432
Forwarding from [::1]:48888 -> 5432
```

The oc port-forward command will run in the foreground until it is killed or the connection is lost. While the connection is active you can run a client program
locally, connecting to the forwarded port on 127.0.0.1, with the connection being
proxied through to the remote application:

```
$ psql sampledb username --host=127.0.0.1 --port=48888
Handling connection for 5432
psql (9.2.18, server 9.5.4)
Type "help" for help.
sampledb=>
```

Summary
When an application is deployed to OpenShift, by default, it will be accessible only within the OpenShift cluster. You can access the application from within the cluster using an internal hostname derived from the service name of the application. When there are multiple instances of the application, connecting to the application will result in the connection being routed through to one of the pods that is running the application.

If you need to make an HTTP web service available to users outside the OpenShift cluster, you can expose it by creating a route. Creation of the route will result in OpenShift automatically reconfiguring the routing layer for you. You can expose a web application via the HTTP protocol or as HTTPS over a secure connection. OpenShift can provide an external hostname for you, or you can use your own custom hostname.

Services can also be exposed on a dedicated IP address or port, or temporarily to your own local system using port forwarding.