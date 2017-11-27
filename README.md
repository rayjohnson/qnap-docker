
## Docker on QNAP

The QNAP storage server comes with a system called container-station
which is really just a fancy UI over docker.

In this repository I have some of the docker compose files I have created
to get the most out of my QNAP server.

First you will want to set up your machine to point to the docker instance
running on QNAP.  Here are some instructions: [DockerSetup.md](DockerSetup.md)

### Applications

Gogs - Container-Station has a way to load GitLab which is a Git
repository.  Useful for local development and code you do not want
to exist on GitHub.  However, Gitlab is bloated!  It consumed 25%
of the memory on QNAP server when I installed it.  Gogs is much lighter
weight and by using sqllite as a database it only requires 1 container 
to run.  The result is that it only used 1% of QNAPs memory.  It may not
have some of the features of GitLab but it is perfect for home needs.


