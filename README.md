# GitLab high available solution implementation

This repo stores the means to build a high available GitLab solution based on Vagrant and Docker.

## Implementation instructions

### Prerequisites for running Vagrant

**!Note** that this setup is tested on Ubuntu 17.4 and MacOS Sierra. 
  On MacOS, docker uses docker-machine and runs on virtualbox so it is not the optimal environment.

* Vagrant, version 1.8.7 or above (tested)

  Previous versions might not work with the Vagrant configuration file.

  Visit https://www.vagrantup.com/downloads.html

* Vagrant Hostmanager

  Visit https://github.com/devopsgroup-io/vagrant-hostmanager
  
  To install hostmanager, with which the connection between vagrant hosts is made, just:

  ```
  vagrant plugin install vagrant-hostmanager
  ```
* Docker Engine

  Tested on Docker version 17.04
  
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

## How to run

To start the environment simply execute the following command from the home folder of the project:

```
sudo vagrant up
```

This will bring up each of the docker containers and will configure them.
If you want to do it one by one just execute the following commands in that order:

```
sudo vagrant up database-primary
sudo vagrant up redis
sudo vagrant up gitlab-1
sudo vagrant up gitlab-2
sudo vagrant up haproxy
```

At the end you can enter the newly setup GitLab environment on localhost from: http://localhost:10080/
SSH is also available on the non-standard port: 10022.

## About the Environment

This high available GitLab environment is consisted of the following set of containers:

 * PostgreSQL 9.6 container
 * Redis 4 container
 * Multiple GitLab Community Edition containers in a clustered environment with shared storage
 * HAProxy container
 
### Database container

[TBD] setup high availability with repmgr and primary-standby postgresql cluster.

This container is build on top of the standard docker postgres:9.6 image.
In order to use it in a fully functional vagrant environment a SSHD daemon has been added to the environment
so that `vagrant ssh` command can be used.

For provisioning and creating the GitLab databases and user, an ansible script is used.

For more information on the setup, please have a look at these files:

```
./postgres/Dockerfile
./scripts/database-primary.yml
```

### Redis container

This container is build on top of the standard docker redis:4.0 image.
In order to use it in a fully functional vagrant environment a SSHD daemon has been added to the environment
so that `vagrant ssh` command can be used.

For more information on the setup, please have a look at these files:

```
./redis/Dockerfile
```

### GitLab containers

Currently, we have two containers working together in the GitLab application tier.
This is implemented using the default docker GitLab Community Edition image `gitlab/gitlab-ce`,
adding the vagrant sshd configuration so that `vagrant ssh` command can be executed and
adding a shared storage between the environments.

The "shared storage" in our proof-of-concept environment is just a set of shared folders which
are being mounted from the git project itself (the folder 'mountdirs').

The setup of the GitLab environment is performed in the usual way with omnibus.
The default all-in-one setup is not used so we are not installing redis or postgresql 
on this container but using our separate dedicated containers for that.

The configuration file which is being added at startup and applied to the gitlab reconfigure process is:

```
./gitlab-ce/gitlab.rb
```

**!Note** that vagrant hostmanager is used in the environment so the other containers are resolved by the container name.

The shared folders are shared between hosts and both containers are listening on 80 and 22 and for incoming connections.

### HAProxy container

To have a fully redundant GitLab environment, HAProxy is used to loadbalance and act as a front facing interface.

The HAProxy node is build on top of the standard docker image `haproxy:1.7`.
A custom configuration file is applied `haproxy.cfg` and an SSHD daemon is set up on a non-trivial port (not to interact with gitlab ssh).

HAProxy loadbalances the two gitlab nodes based on their docker network ips and exposes the following ports to the local environment:
* 10080 - The default HTTP port for the GitLab UI: http://localhost:10080
* 10022 - SSH port for the gitlab environment
* 18080 - Management interface of HAProxy: http://localhost:18080

For more information on how the setup was made, please check:

```
./haproxy/Dockerfile
./haproxy/haproxy.cfg
./haproxy/wrapper
```

## References

For more details on how a GitLab infrastructure should be build and deployed you can refer to these documentations:
[https://docs.gitlab.com/ee/administration/high_availability/gitlab.html](https://docs.gitlab.com/ee/administration/high_availability/gitlab.html)

[https://gitlab.com/gitlab-org/gitlab-ce/blob/master/doc/install/installation.md](https://gitlab.com/gitlab-org/gitlab-ce/blob/master/doc/install/installation.md)

[https://docs.gitlab.com/omnibus/docker/README.html](https://docs.gitlab.com/omnibus/docker/README.html)
