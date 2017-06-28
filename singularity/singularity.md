![Singularity](singularity.png)

# An introduction to Singularity
Certain container technologies, such as Docker, require privileged access to the underlying operating system, which is often not possible on a university-wide HPC cluster. [Singularity](http://singularity.lbl.gov) is an implementation of a container, and an engine to run those containers without requiring any privileged access (root-access). Singularity containers allow researchers to isolate the software environment needed to produce a result away from the configuration and operating system of the computer that the analysis will be run on. This means that your colleague at University X can run the analysis exactly the same way on their cluster as you are running it on your cluster at University Y, and all it requires is sharing a Git repository (code) and a container image (which includes dependency libraries, software and tools). Singularity is designed for ``Mobility of Compute``, a single-file based container image facilitates distribution, archiving and sharing. Singularity has tighter integration with SLURM, MPI and GPU.

Applications which run in a container run with the same “distance” to the host kernel and hardware as natively running applications. Generally the container daemon is a root owned daemon which will separate out all possible namespaces in order to achieve a fully emulated separation from host and other containers. However, the “Singularity Launcher” is run by the user, loads the Singularity container and executes applications as the user.

![Singularity architecture](architecture.png)
> Singularity architecture

## Access privileges
User contexts are always maintained when a Singularity container is launched i.e., when a container is launched by a particular user, the program inside the container will be running as that user. There are no pathways to escalate privileges from within the container.

Invoking an interactive shell within a Singularity container as a regular user:

```shell
[user@centos7-x64 ~]$ whoami
user
[user@centos7-x64 ~]$ singularity shell /tmp/fedora.img
Singularity.fedora.img> whoami
user
```

Invoking an interactive shell within a Singularity container as a sudo user:

```shell
[user@centos7-x64 ~]$ sudo singularity shell /tmp/fedora.img
Singularity.fedora.img> whoami
root
```

## Workflow

The general workflow moves from development at the user endpoint to a shared computational resource. The user endpoint is typically a laptop, workstation, or server. In this space the user can create, modify, and update a container as needed. This typically requires superuser privileges or root access. Once a container is created with the necessary applications, libraries and data inside, it can be easily shared to other hosts and executed without having root access. Making changes to the container again requires returning to the endpoint system with root, and reuploading the container to the shared resource.

![Singularity workflow](workflow.png)
> Singularity workflow
