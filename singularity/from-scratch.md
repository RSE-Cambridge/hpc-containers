## Building a Singularity image from scratch

The first step in creating a new image is to create the image file. This is a large, single file that will contain the whole filesystem for your container.

The image is created with

    singularity create mycontainer.img

which, by default, creates an image that contain up to 768Mib. To create an image that can hold more, use the `--size` option

    singularity create --size 2048 mycontainer.img

Creating an image requires root access! Therefore we create the image on a machine on which we have root/sudo access - usually your personal workstation or laptop, and in this case you can use the virtual machines at workshop.hpc.cam.ac.uk.


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


## The Bootstrap Definition File
There are multiple sections of the Singularity bootstrap definition file:

* `Header`: The Header describes the core operating system to bootstrap within the container. 

* `Sections`: The rest of the definition is in sections. Each section is defined by a `%` character followed by the name of the particular section. All sections are optional.

### Header

The header is at the top of the file, and tells Singularity the kind of bootstrap, and from where. For example a Bootstrap that uses a mirror to install Centos-7 might look like this:

```
BootStrap: yum
OSVersion: 7
MirrorURL: http://mirror.centos.org/centos-%{OSVERSION}/%{OSVERSION}/os/$basearch/
Include: yum
```

A very minimal Docker bootstrap might look like this:

```
Bootstrap: docker
From: ubuntu:latest
```

### Sections

The main content of the bootstrap file is broken into sections.

`%setup`: Setup is where you might perform actions on the host before we move into the container.

`%files`: If you want to copy content into the container, you should do so using the `%files` section, where each is a pair of <source> and <destination>, where the file or expression to be copied is a path on your host, and the destination is a path in the container. 

`%labels`: To store metadata with your container.

`%post`: This is the configuration which will be run from inside the container. This is where the guts of your setup will live, including making directories, and installing software and libraries.

```shell
%post
    yum -y groupinstall "Development Tools"

    git clone https://github.com/open-mpi/ompi.git

    # Compile
    cd ompi
    ./autogen.pl
    ./configure --prefix=/usr/local
    make
    make install
```

`%runscript`:  does not get executed during bootstrapping. Instead it gets persisted within the container to a file called `/singularity` which is the execution driver when the container image is run (either via the singularity run command or via executing the container directly).

`%test`: at the end of boostrapping process to validate the container. If you want to bootstrap without running tests, you can do so with the --notest argument.


### Best Practices for Bootstrapping
When bootstrapping a container, it is best to consider the following:

* Install packages, programs, data, and files into operating system locations (e.g. not `/home`, `/tmp`, or any other directories that might get commonly binded on).
* A good runscript will spit out usage, variables, and tell the user how to interact with the container.
* If you require any special environment variables to be defined, add them the `%environment` section of the bootstrap recipe.
* Files should never be owned by actual users, they should always be owned by a system account (UID < 500).
* Ensure that the container's `/etc/passwd`, `/etc/group`, `/etc/shadow`, and no other sensitive files have anything but the bare essentials within them.
* Do all of your bootstrapping via a definition file instead of manipulating the containers by hand (with the `--writable` options), this ensures greatest possibility of reproducibility and mitigates the black box effect.
