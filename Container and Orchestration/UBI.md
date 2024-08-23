Getting images from registries

To get images from a remote registry (such as Red Hat’s own Docker registry) and add them to your local system, use the podman pull command:

`podman pull <registry>[:<port>]/[<namespace>/]<name>:<tag>`

The `<registry>` is a host that provides the registry service on TCP `<port>` (default: 5000). Together, `<namespace>` and `<name>` identify a particular image controlled by `<namespace>` at that registry. Some registries also support raw `<name>`; for those, `<namespace>` is optional. When it is included, however, the additional level of hierarchy that `<namespace>` provides is useful to distinguish between images with the same `<name>`.

> The registries that Red Hat supports are registry.redhat.io (requiring authentication) and registry.access.redhat.com (requires no authentication, but is deprecated).

# Red Hat Registries

Red Hat distributes container images through three different container registries:

| Registry                    | Content              | Supports unauthenticated access | Supports Red Hat login | Supports registry tokens |
| :-------------------------- | :------------------- | :------------------------------ | :--------------------- | :----------------------- |
| registry.access.redhat.com  | Red Hat products     | Yes                             | No                     | No                       |
| registry.redhat.io          | Red Hat products     | No                              | Yes                    | Yes                      |
| registry.connect.redhat.com | Third-party products | No                              | Yes                    | Yes                      |

Although both registry.access.redhat.com and registry.redhat.io hold essentially the same container images, some images that require a subscription are only available from registry.redhat.io.

Using Authentication
To retrieve content from an authenticated registry, you will need to log into the registry using either your Customer Portal, Red Hat Developer, or Registry Service Account credentials.

To login to the registry.redhat.io registry, you can use either the podman login, skopeo login, and buildah login commands.

```
# Confirm that you have Podman installed on your local system:

$ podman --version
podman version 3.3.1
$ podman login registry.redhat.io
Username: myusername
Password: ************
Login Succeeded!

```

When you log into the registry, your credentials are stored in your `${XDG_RUNTIME_DIR}/containers/auth.json` file. Those credentials are used automatically the next time you pull from that registry. Here is an example of that file:

```json
$ echo $XDG_RUNTIME_DIR
/run/user/1000
$ cat $XDG_RUNTIME_DIR/containers/auth.json
{
 "auths": {
  "registry.access.redhat.com": {
   "auth": "ZGVsdmVyZGVjazpAciFYbHN0dVNQQTE="
  },
  "registry.connect.redhat.com": {
   "auth": "ZGVsdmVyZGVjazpAciFYbHN0dVNQQTE="
  },
  "registry.redhat.io": {
   "auth": "ZGVsdmVyZGVjazpAciFYbHN0dVNQQTE="
  }
 }
}
```

For other ways to save these credentials, see the config.json description on the docker login page.

For OpenShift nodes you will have an additional step. After you log in, you will need to copy ~/.docker/config.json to /var/lib/origin/.docker/config.json and restart the node.

```
# cp ~/.docker/config.json /var/lib/origin/.docker/config.json; systemctl restart atomic-openshift-node
```

# Registry Service Accounts for Shared Environments

To consume container images from registry.redhat.io in shared environments such as OpenShift, it is recommended for an administrator to use a Registry Service Account, also referred to as authentication tokens, in place of an individual's Customer Portal credentials.

Service Accounts are a mechanism provided to a Customer Portal organization, used exclusively for authenticating to and retrieving content from registry.redhat.io. The use of Service Accounts is encouraged to prevent the need to use Customer Portal credentials on shared systems, in contrast to Customer Portal accounts, Registry Service Accounts are resilient to some security controls applied to Customer Portal accounts, such as mandated password resets.

The management of Service Accounts is available via the Registry Service Account [management application](https://access.redhat.com/terms-based-registry/). You have the freedom to decide how many Service Accounts are created and how they are used on your systems, as a guideline, you may opt to use one Service Account per shared system (such as an OpenShift Container Platform cluster).

Refer

- <https://access.redhat.com/RegistryAuthentication#red-hat-registries-1>
- [Docker Registry HTTP API V2](https://github.com/distribution/distribution/blob/release/2.4/docs/spec/api.md)

# Listing image tags for Red Hat's Container Registry

Currently registry.access.redhat.com accepts most v1 and v2 api calls.

## Registry v1 API

To list information on an image's tags use the following:
`https://registry.access.redhat.com/v1/repositories/(namespace)/(repository)/tags`

```
# curl -s https://registry.access.redhat.com/v1/repositories/rhel7/tags
```

## Registry v2 API

To list information on an image's tags use the following:
`https://registry.access.redhat.com/v2/(namespace)/(repository)/tags/list`

```bash
# curl -sL https://registry.access.redhat.com/v2/rhel7/tags/list`
```

You can list all the image using the following curl command. "/v2/_catalog" API shows just 100 images by default, so you should configure the appropriate count using "?n=xxx".

```bash
# if the target registry is insecure(http), "-k" option is not required.
$ curl -ks https://mirror-private.registry.example.com:5000/v2/_catalog?n=5000 | jq
$ curl -sL https://registry.access.redhat.com/v2/_catalog?n=5000

# Next steps is verifications of the all tags for the target image as follows.
# curl -ks https://mirror-private.registry.example.com:5000/v2/<YOUR IMAGE PATH>/tags/list | jq
```

```bash
# to list all images available in the image repository with tags

# $ podman image search --list-tags <image-repository>
$ podman image search --list-tags docker.io/alpine
$ podman image search --list-tags docker.io/rancher/hyperkube --limit 1000

# To pull the RHEL 7 UBI base image and rsyslog image from the Red Hat registry, type:
$ podman pull registry.access.redhat.com/ubi7/ubi
$ podman pull registry.access.redhat.com/rhel7/rsyslog

# To see the images that resulted from the above podman pull command, along with any other images on your system, type podman images
$ podman images
```

Reference

- [Apache Tomcat versions supported by Red Hat](https://access.redhat.com/solutions/661403)
- [Podman and Buildah for Docker users](https://developers.redhat.com/blog/2019/02/21/podman-and-buildah-for-docker-users#)
- [Red Hat Universal Base Images for Docker users](https://developers.redhat.com/blog/2020/03/24/red-hat-universal-base-images-for-docker-users#)

https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux_atomic_host/7/html-single/getting_started_with_containers/index?extIdCarryOver=true&intcmp=7013a000002CtetAAC&sc_cid=701f2000001OH7EAAW#using_red_hat_universal_base_images_standard_minimal_and_runtimes


https://catalog.redhat.com/software/containers/search?p=1