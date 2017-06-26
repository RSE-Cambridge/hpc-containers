## Building a Singularity image from scratch

The first step in creating a new image is to create the image file. This is a large, single file that will contain the whole filesystem for your container.

The image is created with

    singularity create mycontainer.img

which, by default, creates an image that contain up to 768Mib. To create an image that can hold more, use the `--size` option

    singularity create --size 2048 mycontainer.img

Creating an image requires root access! Therefore we create the image on a machine on which we have root/sudo access - usually your personal workstation or laptop, and in this case you can use the virtual machines at workshop.hpc.cam.ac.uk.

> **Info** Singularity will try to use an image format that does not immediately take up the full allocated size of the container image. If you create a 2048 Mb image then it will (while still empty) actually take up <100Mb and grow dynamically as you add files to the container. This is only possible on some filesystems and is not possible on network filesystems, such as your home directories on the virtual machines or on darwin. Therefore keep the containers in a local filesystem - for example /tmp or /local on the VMs or on darwin.


The newly created image will be empty, and to make it useful we need to bootstrap (again, only possible with root privilege) the image by

    singularity bootstrap mycontainer.img centos.cfg

where `mycontainer.img` is a fresh image and the file `centos.cfg` contains the definitions used to build the image. To build a centos 7 image we use:

    #file centos.cfg
    BootStrap: yum
    OSVersion: 7
    MirrorURL: http://mirror.centos.org/centos-%{OSVERSION}/%{OSVERSION}/os/$basearch/
    Include: yum

This uses the yum package manager (installed as part of the operating system *outside* the container) and builds a centos 7 image. The image is now ready to be explored with

    singularity shell mycontainer.img

which will drop you into a new shell inside the container.

> **Warning** Root access to stuff and network filesystems with `root_squash`.

### The Singularity definition file

The creation of the container can be further specified using the `%setup` etc. blocks.

### Other operating systems

It is possible to build containers based on other linux distributions. For example, we could build an **ubuntu**-flavoured container with a different configuration file

    #file ubuntu.cfg
    BootStrap: debootstrap
    OSVersion: trusty
    MirrorURL: http://us.archive.ubuntu.com/ubuntu/

Bootstrapping each specific linux distribution will requires the correct tool to be available outside the container on the host system. 

| Container Distribution | Bootstraping tool |
| -- | -- |
| centos | yum |
| scientific linux | yum |
| debian | debootstrap |
| ubuntu | debootstrap |
| busybox | busybox |
| arch linux | pacman |

This means that it is easiest to build a container that is similar to your host system. The VMs provided for this workshop are running centos, so we shall be use centos containers for many of our examples. It is of course possible to install (for example) yum on archlinux, allowing you to bootstrap a centos container on an arch linux host. Google is your friend.

