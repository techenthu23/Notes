# Nodes, Pods and Containers

# Node

A node is a virtual or bare-metal machine in a Kubernetes cluster. Worker nodes host your application containers, grouped as pods. The control plane nodes run services that are required to control the Kubernetes cluster. In OpenShift Container Platform, the control plane nodes contain more than just the Kubernetes services for managing the OpenShift Container Platform cluster.

```
oc get nodes
oc get nodes -o wide
oc get node <node>
oc describe node <node>

# To list all or selected pods on selected nodes:
oc describe --selector=<node_selector>      
oc describe -l=<pod_selector>

# To list all pods on a specific node, including terminated pods:
oc get pod --all-namespaces --field-selector=spec.nodeName=<nodename>

# To view the usage statistics:
oc adm top nodes

```

# Containers and Pods
Your application, when deployed, is run within a container. The container isolates your application from other applications. From within the container, your application can see only processes that are a part of the same deployed application. It cannot see the processes of applications running in other containers.

When containers are run directly on a host, although applications are isolated from each other, all the containers will normally share the same IP address and port name‐ space. 

This means that if you want to run multiple instances of the same web application, all wanting to use the same listener port for accepting web requests, they will conflict with each other. For this reason, when your application is run within OpenShift, the container is further encapsulated in what is called a pod.

A pod is a group of containers with shared storage and network resources. The containers in a pod are always co-located and co-scheduled, and run in a shared context. Containers within a pod share an IP address and port namespace, and can find each other via localhost. They can also communicate with each other using local inter-process communication (IPC) mechanisms like SystemV semaphores or POSIX shared memory. Containers in different pods have distinct IP addresses and cannot communicate using local IPC mechanisms.

Each pod having its own IP address and port namespace means that multiple instances of an application, all wanting to use the same listener port for accepting web requests, can be run on the same host, without your needing to override what port each is using. A pod in that respect behaves as if it were its own host.
To see a list of all pods within a project, you can run the `oc get pods` command. If you wish to see more information on a pod, including the IP address of the pod and details of each container running in the pod, run `oc describe pod`, passing the name of the pod as the argument.

It is not possible from outside a container to see what processes are running in it. In order to see what is running in a container, you can use oc rsh to start an interactive terminal session within the container. Provided the application image bundles the required Unix commands, you can interact with the processes from the terminal session, in the same way as you would if they were running on your own host. You can use Unix commands such as ps or top to list the processes that are running.


# Containers

The basic units of OpenShift Container Platform applications are called containers. Linux container technologies are lightweight mechanisms for isolating running processes so that they are limited to interacting with only their designated resources.

Many application instances can be running in containers on a single host without visibility into each others' processes, files, network, and so on. Typically, each container provides a single service (often called a "micro-service"), such as a web server or a database, though containers can be used for arbitrary workloads.

Containers are treated as immutable infrastructure and therefore it is generally not recommended to modify the content of a container through SSH or running custom commands inside the container. Nevertheless, in some use-cases, such as debugging an application, it might be beneficial to get into a container and inspect the application.

```
oc rsh <podname>
oc rsh <podname> <command>
```
> The default shell used by oc rsh is /bin/sh. If the deployed container does not have sh installed and uses another shell, (e.g. A Shell) the shell command can be specified after the pod name in the issued command.

> It is important to understand that, for security reasons, OpenShift does not run containers as the user specified in the Dockerfile by default. In fact, when OpenShift launches a container its user is actually randomized.
> If you want or need to allow OpenShift users to deploy container images that do expect to run as root (or any specific user), a small configuration change is needed.

In addition to remote shell, it is also possible to run a command remotely in an already running container using the oc exec command. This does not require that a shell is installed, but only that the desired command is present and in the executable path.

```
oc exec <podname> -- <command>
```

> The -- syntax in the oc exec command delineates where exec’s options end and where the actual command to execute begins. 



## Init Containers

An init container is a container in a pod that is started before the pod app containers are started. Init containers can share volumes, perform network operations, and perform computations before the remaining containers start. Init containers can also block or delay the startup of application containers until some precondition is met.

When a pod starts, after the network and volumes are initialized, the init containers are started in order. Each init container must exit successfully before the next is invoked. If an init container fails to start (due to the runtime) or exits with failure, it is retried according to the pod restart policy.

A pod cannot be ready until all init containers have succeeded.

# Pods

OpenShift Container Platform leverages the Kubernetes concept of a pod, which is one or more containers deployed together on one host, and the smallest compute unit that can be defined, deployed, and managed.

Pods are the rough equivalent of a machine instance (physical or virtual) to a container. Each pod is allocated its own internal IP address, therefore owning its entire port space, and containers within pods can share their local storage and networking.

Pods have a lifecycle; they are defined, then they are assigned to run on a node, then they run until their container(s) exit or they are removed for some other reason. Pods, depending on policy and exit code, may be removed after exiting, or may be retained in order to enable access to the logs of their containers.

OpenShift Container Platform treats pods as largely immutable; changes cannot be made to a pod definition while it is running. OpenShift Container Platform implements changes by terminating an existing pod and recreating it with modified configuration, base image(s), or both. Pods are also treated as expendable, and do not maintain state when recreated. Therefore pods should usually be managed by higher-level controllers, rather than directly by users.

-  Pods can be "tagged" with one or more labels, which can then be used to select and manage groups of pods in a single operation. The labels are stored in key/value format in the metadata hash
- Pods must have a unique name within their namespace. A pod definition may specify the basis of a name with the generateName attribute, and random characters will be added automatically to generate a unique name.
- The container can bind to ports which will be made available on the pod’s IP.
-  OpenShift Container Platform defines a security context for containers which specifies whether they are allowed to run as privileged containers, run as a user of their choice, and more. The default context is very restrictive but administrators can modify this as needed.
- The pod restart policy with possible values Always, OnFailure, and Never. The default value is Always.
- Pods making requests against the OpenShift Container Platform API is a common enough pattern that there is a serviceAccount field for specifying which service account user the pod should authenticate as when making the requests. This enables fine-grained access control for custom infrastructure components.

```yaml
# oc get pod -o yaml
apiVersion: v1
items:
- apiVersion: v1
  kind: Pod
  metadata:
    annotations:
      k8s.v1.cni.cncf.io/network-status: |-
        [{
            "name": "openshift-sdn",
            "interface": "eth0",
            "ips": [
                "10.128.6.99"
            ],
            "default": true,
            "dns": {}
        }]
      k8s.v1.cni.cncf.io/networks-status: |-
        [{
            "name": "openshift-sdn",
            "interface": "eth0",
            "ips": [
                "10.128.6.99"
            ],
            "default": true,
            "dns": {}
        }]
      kubernetes.io/limit-ranger: 'LimitRanger plugin set: cpu, memory request for
        container parksmap; cpu, memory limit for container parksmap'
      openshift.io/generated-by: OpenShiftWebConsole
      openshift.io/scc: restricted
    creationTimestamp: "2022-01-22T16:28:54Z"
    generateName: parksmap-75cb86d8fd-
    labels:
      app: parksmap
      component: parksmap
      deploymentconfig: parksmap
      pod-template-hash: 75cb86d8fd
      role: fronted
    name: parksmap-75cb86d8fd-f96ds
    namespace: delverdeck-dev
    ownerReferences:
    - apiVersion: apps/v1
      blockOwnerDeletion: true
      controller: true
      kind: ReplicaSet
      name: parksmap-75cb86d8fd
      uid: 4c7837de-67dd-4913-91e6-149312f3cc5d
    resourceVersion: "728850353"
    uid: 141e7a4d-3c9a-4127-a02d-f4a9434105c7
  spec:
    containers:
    - image: image-registry.openshift-image-registry.svc:5000/delverdeck-dev/parksmap@sha256:89d1e324846cb431df9039e1a7fd0ed2ba0c51aafbae73f2abd70a83d5fa173b
      imagePullPolicy: Always
      name: parksmap
      ports:
      - containerPort: 8080
        protocol: TCP
      resources:
        limits:
          cpu: "1"
          memory: 750Mi
        requests:
          cpu: 10m
          memory: 64Mi
      securityContext:
        capabilities:
          drop:
          - KILL
          - MKNOD
          - SETGID
          - SETUID
        runAsUser: 1020580000
      terminationMessagePath: /dev/termination-log
      terminationMessagePolicy: File
      volumeMounts:
      - mountPath: /var/run/secrets/kubernetes.io/serviceaccount
        name: kube-api-access-7r2mj
        readOnly: true
    dnsPolicy: ClusterFirst
    enableServiceLinks: true
    imagePullSecrets:
    - name: default-dockercfg-87fkg
    nodeName: ip-10-0-155-136.ec2.internal
    preemptionPolicy: PreemptLowerPriority
    priority: -3
    priorityClassName: sandbox-users-pods
    restartPolicy: Always
    schedulerName: default-scheduler
    securityContext:
      fsGroup: 1020580000
      seLinuxOptions:
        level: s0:c143,c137
    serviceAccount: default
    serviceAccountName: default
    terminationGracePeriodSeconds: 30
    tolerations:
    - effect: NoExecute
      key: node.kubernetes.io/not-ready
      operator: Exists
      tolerationSeconds: 300
    - effect: NoExecute
      key: node.kubernetes.io/unreachable
      operator: Exists
      tolerationSeconds: 300
    - effect: NoSchedule
      key: node.kubernetes.io/memory-pressure
      operator: Exists
    volumes:
    - name: kube-api-access-7r2mj
      projected:
        defaultMode: 420
        sources:
        - serviceAccountToken:
            expirationSeconds: 3607
            path: token
        - configMap:
            items:
            - key: ca.crt
              path: ca.crt
            name: kube-root-ca.crt
        - downwardAPI:
            items:
            - fieldRef:
                apiVersion: v1
                fieldPath: metadata.namespace
              path: namespace
        - configMap:
            items:
            - key: service-ca.crt
              path: service-ca.crt
            name: openshift-service-ca.crt
  status:
    conditions:
    - lastProbeTime: null
      lastTransitionTime: "2022-01-22T16:28:54Z"
      status: "True"
      type: Initialized
    - lastProbeTime: null
      lastTransitionTime: "2022-01-22T16:28:57Z"
      status: "True"
      type: Ready
    - lastProbeTime: null
      lastTransitionTime: "2022-01-22T16:28:57Z"
      status: "True"
      type: ContainersReady
    - lastProbeTime: null
      lastTransitionTime: "2022-01-22T16:28:54Z"
      status: "True"
      type: PodScheduled
    containerStatuses:
    - containerID: cri-o://b66ddeba72ead6172a3a68e1b143514e3e98c5ad2af5d62b7db9b19dbca6d854
      image: image-registry.openshift-image-registry.svc:5000/delverdeck-dev/parksmap@sha256:89d1e324846cb431df9039e1a7fd0ed2ba0c51aafbae73f2abd70a83d5fa173b
      imageID: image-registry.openshift-image-registry.svc:5000/alegr99-dev/parksmap@sha256:89d1e324846cb431df9039e1a7fd0ed2ba0c51aafbae73f2abd70a83d5fa173b
      lastState: {}
      name: parksmap
      ready: true
      restartCount: 0
      started: true
      state:
        running:
          startedAt: "2022-01-22T16:28:57Z"
    hostIP: 10.0.155.136
    phase: Running
    podIP: 10.128.6.99
    podIPs:
    - ip: 10.128.6.99
    qosClass: Burstable
    startTime: "2022-01-22T16:28:54Z"
kind: List
metadata:
  resourceVersion: ""
  selfLink: ""

```

