# GitLab high available solution implementation

This repo stores the means to build a high available GitLab solution based on Vagrant and Docker.

## Implementation instructions

### Prerequisites for running Vagrant

* Vagrant, version 1.8.7 or above (tested)

  Previous versions might not work with the Vagrant configuration file.

  Visit https://www.vagrantup.com/downloads.html

* Vagrant Hostmanager

  Visit https://github.com/devopsgroup-io/vagrant-hostmanager
  
  To install hostmanager, with which the connection between vagrant hosts is made, just:

  ```
  vagrant plugin install vagrant-hostmanager
  ```
* Running on MacOS

In MacOS, docker is using VirtualBox in order to emulate linux kernel environment and properly use docker.
Therefore, the standard boot2docker image is being used. However, this image does not have the rsync command installed
which in term is used for the shared folders in this project.

To fix that either use another image of hashicorp/boot2docker or just install the command manually to the virtual machine:
Log in to the boot2docker virtual machine and execute (as user docker):

```
tce-load -w -i rsync.tcz
```

You will have rsync installed now but not properly configured to do the ssh authentication.
Since this is not the purpose of this project you can just change the password of user docker:

```
passwd docker
```

## References

For more details on how a GitLab infrastructure should be build and deployed you can refer to these documentations:
[https://docs.gitlab.com/ee/administration/high_availability/gitlab.html](https://docs.gitlab.com/ee/administration/high_availability/gitlab.html)

[https://gitlab.com/gitlab-org/gitlab-ce/blob/master/doc/install/installation.md](https://gitlab.com/gitlab-org/gitlab-ce/blob/master/doc/install/installation.md)

[https://docs.gitlab.com/omnibus/docker/README.html](https://docs.gitlab.com/omnibus/docker/README.html)
