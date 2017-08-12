# GitLab high available solution implementation

This repo stores the means to build a high available GitLab solution based on Vagrant and Docker.

## The task
Your task is to provision a highly available wordpress cluster. The entry point of the cluster should be a host and port 443.

## The solution
In your solution please enphasize on readability, maintanability and DevOps methodologies. We expect a clear way to recreate your setup.

Use git. We like seing history of small improvements instead of big pushes with unclear goals.

If you wish, you can use a configuration management tool (We use ansible). The infrastructure provider is up to you (We use AWS, Vagrant and Docker).

It is not required to implement all the features you can think of. A clean bare minimum working infrastructure is prefered than a full blown solution peaced together with scissors, rope and duct tape. Do not skip security considerations.
=======
## References

For more details on how a GitLab infrastructure should be build and deployed you can refer to these documentations:
[https://docs.gitlab.com/ee/administration/high_availability/gitlab.html](https://docs.gitlab.com/ee/administration/high_availability/gitlab.html)

[https://gitlab.com/gitlab-org/gitlab-ce/blob/master/doc/install/installation.md](https://gitlab.com/gitlab-org/gitlab-ce/blob/master/doc/install/installation.md)

[https://docs.gitlab.com/omnibus/docker/README.html](https://docs.gitlab.com/omnibus/docker/README.html)
