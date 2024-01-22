# Services and Endpoints

Each pod has a distinct IP address. From any application running in the same project, you can connect to another pod using its IP address on the port the application that is running in that pod is using.

> In order to be able to accept connections on a port from outside the pod, an application should bind to the network address 0.0.0.0 and not 127.0.0.1 or localhost.

Using the direct IP address of a pod when connecting to it is not recommended. This is because the IP addresses are not permanent and can change if a pod is killed and replaced with an instance of the application running in a new pod.

If you have multiple instances of an application, they will each run in a separate pod. These may be on the same node, or a different node in the OpenShift cluster. Each instance will have a separate IP address.

In order to have a single permanent IP address that can be used to connect to any instance of the application and to load-balance connections between the instances of the application, OpenShift provides a service abstraction. 

If you use oc new-app or the web console to deploy an application from a pre-existing image, or one built from your source code, a service resource object will be created for you automatically.

To see a list of both the pods and services for an application, you can run `oc get pods,services` and provide a label selector which matches that used by the application:

```
$ oc get pods,services --selector app=blog
NAME            READY STATUS  RESTARTS AGE
po/blog-2-sxbpd 1/1   Running 0         1m
po/blog-2-ww5ck 1/1   Running 0         1m

NAME      CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE
svc/blog  172.30.229.46   <none>        8080/TCP  2m
```

The entry for the service will display the permanent IP address that can be used to connect to the application. When there are multiple instances of the application, the
instance you connect to will be random.

To see a list of the pod IP addresses associated with a service, you can use the `oc get endpoints` command:
```
$ oc get endpoints blog
NAME  ENDPOINTS                           AGE
blog  172.17.0.10:8080,172.17.0.8:8080    2m
```

Although the IP address of the service remains the same for the life of the application, you cannot determine what IP address will be used in advance.

If you were deploying an application directly to a host, to avoid users of the application having to know an IP address, you would register the IP address against a hostname within a DNS server. When you use OpenShift and a service is created, this will be done for you, with the IP address being registered in a DNS server internal to the OpenShift cluster and the hostname used matching the name of the service.

In this example, rather than a client application running in the same project needing to use the IP address, it would be able to use the hostname blog to connect to the application.

The registration of the IP address in the DNS server means that client applications can be coded to use a fixed hostname, and the deployed backend application or database need only use the same name when it is created.

Alternatively, you could pass the hostname for the backend application or database to the client application using an environment variable, or in a configuration file mounted into the container for the client application using a config map or secret.



Services provide a convenient abstraction layer inside OpenShift to find a group of similar Pods. They also act as an internal proxy/load balancer between those Pods and anything else that needs to access them from inside the OpenShift environment. For example, if you needed more application  instances to handle the load, you could spin up more Pods. OpenShift automatically maps them as endpoints to the Service, and the incoming requests would not notice anything different except that the Service was now doing a better job handling the requests.

Backing pods can be added to or removed from a service arbitrarily while the service remains consistently available, enabling anything that depends on the service to refer to it at a consistent address. The default service clusterIP addresses are from the OpenShift Container Platform internal network and they are used to permit pods to access each other.

When you asked OpenShift to run the image, it automatically created a Service for you. Remember that services are an internal construct. They are not available to the "outside world", or anything that is outside the OpenShift environment.

Like pods, services are REST objects

```
# This may be for OSE3

To permit external access to the service, additional externalIP and ingressIP addresses that are external to the cluster can be assigned to the service. These externalIP addresses can also be virtual IP addresses that provide highly available access to the service.

Services are assigned an IP address and port pair that, when accessed, proxy to an appropriate backing pod. A service uses a label selector to find all the containers running that provide a certain network service on a certain port.

To permit external access to the service, additional externalIP and ingressIP addresses that are external to the cluster can be assigned to the service. These externalIP addresses can also be virtual IP addresses that provide highly available access to the service.

Services are assigned an IP address and port pair that, when accessed, proxy to an appropriate backing pod. A service uses a label selector to find all the containers running that provide a certain network service on a certain port.
```



```yaml
oc get service parksmap -o yaml
apiVersion: v1
kind: Service
metadata:
  annotations:
    openshift.io/generated-by: OpenShiftWebConsole
  creationTimestamp: "2022-01-22T16:28:54Z"
  labels:
    app: workshop
    app.kubernetes.io/component: parksmap
    app.kubernetes.io/instance: parksmap
    app.kubernetes.io/name: parksmap
    app.kubernetes.io/part-of: workshop
    app.openshift.io/runtime-version: latest
    component: parksmap
    role: fronted
  name: parksmap
  namespace: delverdeck-dev
  resourceVersion: "728850158"
  uid: 2a7d3e16-e06e-44ba-8b1c-1e2db03a17f8
spec:
  clusterIP: 172.30.52.197
  clusterIPs:
  - 172.30.52.197
  internalTrafficPolicy: Cluster
  ipFamilies:
  - IPv4
  ipFamilyPolicy: SingleStack
  ports:
  - name: 8080-tcp
    port: 8080
    protocol: TCP
    targetPort: 8080
  selector:
    app: parksmap
    deploymentconfig: parksmap
  sessionAffinity: None
  type: ClusterIP
status:
  loadBalancer: {}
```

## Service externalIPs
In addition to the cluster’s internal IP addresses, the user can configure IP addresses that are external to the cluster. The administrator is responsible for ensuring that traffic arrives at a node with this IP.

The externalIPs must be selected by the cluster administrators from the `externalIPNetworkCIDRs` range configured in `/etc/origin/master/master-config.yaml` file. When `master-config.yaml` is changed, the master services must be restarted.


## Service ingressIPs
In non-cloud clusters, externalIP addresses can be automatically assigned from a pool of addresses. This eliminates the need for the administrator manually assigning them.

The pool is configured in /etc/origin/master/master-config.yaml file by setting `ingressIPNetworkCIDR`


## Service NodePort
Setting the service `type=NodePort` will allocate a port from a flag-configured range (default: 30000-32767), and each node will proxy that port (the same port number on every node) into your service.

The selected port will be reported in the service configuration, under `spec.ports[*].nodePort`.

To specify a custom port just place the port number in the `nodePort` field. The custom port number must be in the configured range for `servicesNodePortRange`. When 'master-config.yaml' is changed the master services must be restarted.

## Service Proxy Mode
OpenShift Container Platform has two different implementations of the service-routing infrastructure. The default implementation is entirely iptables-based, and uses probabilistic iptables rewriting rules to distribute incoming service connections between the endpoint pods. The older implementation uses a user space process to accept incoming connections and then proxy traffic between the client and one of the endpoint pods.

The iptables-based implementation is much more efficient, but it requires that all endpoints are always able to accept connections; the user space implementation is slower, but can try multiple endpoints in turn until it finds one that works. If you have good readiness checks (or generally reliable nodes and pods), then the iptables-based service proxy is the best choice. Otherwise, you can enable the user space-based proxy when installing, or after deploying the cluster by editing the node configuration file.


Refer https://docs.openshift.com/container-platform/3.11/architecture/core_concepts/pods_and_services.html