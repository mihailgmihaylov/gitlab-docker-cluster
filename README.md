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



## References

For more details on how a GitLab infrastructure should be build and deployed you can refer to these documentations:
[https://docs.gitlab.com/ee/administration/high_availability/gitlab.html](https://docs.gitlab.com/ee/administration/high_availability/gitlab.html)

[https://gitlab.com/gitlab-org/gitlab-ce/blob/master/doc/install/installation.md](https://gitlab.com/gitlab-org/gitlab-ce/blob/master/doc/install/installation.md)

[https://docs.gitlab.com/omnibus/docker/README.html](https://docs.gitlab.com/omnibus/docker/README.html)
