# Hands-on #1: Running a Singularity container
## Accessing the Singularity image
A Singularity image of CentOS should be available in your home directory.
Alternatively, CentOS Singularity container can be pulled
from [SingularityHub](https://singularity-hub.org).
```
    singularity pull --name centos.img shub://singularityhub/centos
```

## Singularity shell
Singularity shell allows you to interact with the container.

```shell
   singularity shell ~/centos.img
   singularity centos.img:~/> uname -a
   # Check user privileges
   singularity centos.img:~/> whoami
   # Create a file in home directory
   singularity centos.img:~/> touch ~/test
```
Singularity auto mounts your home directory, check if a file called `test` is
created in your home directory. If you want to keep the container’s environment
contained, meaning no sharing of host environment, use `--contain`

```shell
   singularity shell --contain ~/centos.img
   # create some files in your home, are they persistent?
   singularity centos.img:~/> touch ~/test
   singularity centos.img:~/> exit
   # Contain but define a new directory to use for your home
   singularity shell --contain --home ~/git ~/centos.img
   singularity centos.img:~/> touch ~/test
```

## Singularity exec
To run commands directly from a container, use `singularity exec`:
```
    singularity exec ~/centos.img echo "Hello Singularity!"
    singularity exec ~/centos.img factor 54321
```

To enable debugging, use `--debug` flag
```
    singularity --debug exec ~/centos.img whoami
```
To launch the container as a separate process use `-p` flag
```
    singularity exec -p ~/centos.img factor 12345
```
