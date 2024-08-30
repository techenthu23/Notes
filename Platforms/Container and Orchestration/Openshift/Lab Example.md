```bash
# Connect to open shift cluster
TOKEN=sha256~r9qExuWNOAsFunITFsfzMYIvBpOI7VBJ31q0tuULuxA
oc login --token=${TOKEN} --server=https://api.sandbox.x8i5.p1.openshiftapps.com:6443
oc whoami

# Connect to the project 
oc get projects
oc get project
oc project delverdeck-dev

# Application Name : workshop
# Name : parksmap
# Labels : app=workshop component=parksmap  role=frontend
# Image Name : quay.io/openshiftroadshow/parksmap:latest

# Deploy an existing Image from an Image Stream or Image registry.
# To deploy an Image from a private repository, you must  with your Image registry credentials.

oc import-image parksmap:latest \
    --from=quay.io/openshiftroadshow/parksmap:latest \
    --confirm

oc import-image parksmap:latest --from=quay.io/openshiftroadshow/parksmap:latest 
> 
Error from server (Forbidden): imagestreams.image.openshift.io "parksmap" is forbidden: User "system:serviceaccount:delverdeck-dev:default" cannot get resource "imagestreams" in API group "image.openshift.io" in the namespace "delverdeck-dev"

```