
# Installing Google Chrome Stable version using toolbox

Toolbox
As an immutable host, Silverblue is an excellent platform for container-based development and, for working with containers, buildah and podman are recommended.

Silverblue also comes with the toolbox utility, which uses containers to provide an environment where development tools and libraries can be installed and used.

<https://docs.fedoraproject.org/en-US/fedora-silverblue/toolbox/>
<https://ostechnix.com/getting-started-with-toolbox-on-fedora-silverblue/>
<https://cloud.google.com/container-optimized-os/docs/how-to/toolbox>

```bash
toolbox create –container google-chrome 
sudo dnf install fedora-workstation-repositories 
sudo dnf config-manager --set-enabled google-chrome 
sudo dnf install google-chrome-stable 
exit 
toolbox list –containers 
toolbox list –images 
toolbox list 
toolbox run -c google-chrome /usr/bin/google-chrome-stable --no-sandbox 
```

```bash
podman container stop google-chrome  
podman ps –a 
podman images 
podman start google-chrome 
podman stats 
podman stop google-chrome 
podman inspect google-chrome 
podman rm google-chrome 
podman rmi <image-name> 
podman --help 
```

In Fedora Silverblue, toolbox is used to provide a mutable working environment on top of a (mostly) immutable operating system such as Silverblue or CoreOS
The difference with toolbox is that it overlays this environment on top of your profile, carries your shell settings and helps users resolve just like in the host. So you wouldn't need to worry about things like mounting a 9p filesystem or sync'ing files and adjusting ownership, etc.

So when you enter the toolbox environment, you feel like you're in your regular environment, but things you change beyond your profile are kept to the container.

The role of podman
As I mentioned earlier, toolbox is a script. A 2.5K+ SLOC script with close to 60 mentions to podman

Container orchestration automates the deployment, management, scaling, and networking of containers. Enterprises that need to deploy and manage hundreds or thousands of Linux® containers and hosts can benefit from container orchestration.

Container orchestration can be used in any environment where you use containers. It can help you to deploy the same application across different environments without needing to redesign it. And microservices in containers make it easier to orchestrate services, including storage, networking, and security.

Containers give your microservice-based apps an ideal application deployment unit and self-contained execution environment. They make it possible to run multiple parts of an app independently in microservices, on the same hardware, with much greater control over individual pieces and life cycles.

Managing the lifecycle of containers with orchestration also supports DevOps teams who integrate it into CI/CD workflows. Along with application programming interfaces (APIs) and DevOps teams, containerized microservices are the foundation for cloud-native applications.
