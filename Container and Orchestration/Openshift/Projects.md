# Projects

Projects are a top level concept to help you organize your deployments. An OpenShift project allows a community of users (or a user) to organize and manage their content in isolation from other communities. Each project has its own resources, policies (who can or cannot perform actions), and constraints (quotas and limits on resources, etc). Projects act as a "wrapper" around all the application services and endpoints you (or your teams) are using for your work.

oc status

```
oc status -v                  # get oc cluster status
oc types                      # to list all concepts and types
```

Managing Projects

```
oc get projects               # list Existing Projects
oc get project                # Display current project
oc project myproject          # switch to a project
oc new-project testlab --display-name='testlab' --description='testlab'        
                              # create a new project
oc adm new-project testlab --node-selector='project101=testlab'
                              # create a new project with node-selector. 
                              # Project pods will be created only those nodes with a label "project101=testlab"
oc delete project testlab     # delete a project
oc delete all --all           # delete all from a project
oc delete all -l app=web      # delete all where label app=web
```
