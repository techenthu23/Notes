
Vagrant is distributed as a binary package for all supported platforms and architectures. You can also compile Vagrant from source, which is covered in the README.

# Initialize a Project Directory

he first step in configuring any Vagrant project is to create a Vagrantfile. The Vagrantfile allows you to:

Mark the root directory of your project. Many of the configuration options in Vagrant are relative to this root directory.

Describe the kind of machine and resources you need to run your project, as well as what software to install and how you want to access it.

```
$ vagrant init rhel83
A `Vagrantfile` has been placed in this directory. You are now
ready to `vagrant up` your first virtual environment! Please read
the comments in the Vagrantfile as well as documentation on
`vagrantup.com` for more information on using Vagrant.
```

# Install and Specify a Box

Instead of building a virtual machine from scratch, which would be a slow and tedious process, Vagrant uses a base image to quickly clone a virtual machine. These base images are known as "boxes" in Vagrant, and specifying the box to use for your Vagrant environment is always the first step after creating a new Vagrantfile.
