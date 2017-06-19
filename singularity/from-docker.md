## Docker to Singularity

### Running Docker images in Singularity

Singularity can shell, import, run and exec a Docker image directly.

> **Info** Docker is not required for running Docker images in Singularity.

```
singularity shell docker://ubuntu:latest
singularity run docker://ubuntu:latest
singularity exec docker://ubuntu:latest echo "Hello Cambridge!"
```

### Converting a Docker image to a Singularity image

To import a docker image to a singularity image:

```
singularity create --size 4000 tensorflow.img
singularity import tensorflow.img docker://tensorflow/tensorflow:latest
```

The `singularity create` command by default will create an image of size 768MB, you can modify this by using `--size` or `-s` argument. 

### Converting a Docker specification file to a Singularity specification file

Just like Docker has the Dockerfile, Singularity has a file called Singularity. It is recommended to build Singularity images from the specification file.

[Singularity-Hub](https://singularity-hub.org/tools/converter/dockerfile) converts Docker specification file to a Singularity file. 

> **Warning** Singularity does not currently support metadata, and so tags like `MAINTAINER` and `LABEL` are converted to comments.

Sample Docker file

```
FROM fedora:latest
MAINTAINER Krishna Kumar <kks32@cam.ac.uk>

# Update to latest packages, remove vim-minimal & Install Git, GCC, Clang, Autotools and VIM
RUN dnf update -y && \
    dnf remove -y vim-minimal python sqlite && \
    dnf install -y clang cmake cppcheck eigen3-devel findutils gcc gcc-c++ git make perl-Digest-MD5 ruby tar valgrind vim wget && \
    dnf clean all

# Coverage 
RUN wget http://ftp.de.debian.org/debian/pool/main/l/lcov/lcov_1.11.orig.tar.gz && tar xf lcov_1.11.orig.tar.gz && make -C lcov-1.11/ install

# Create a user cbgeo
RUN useradd cbgeo
USER cbgeo

# Create a research directory and clone git repo of felib code
RUN mkdir -p /home/cbgeo/research && \
    cd /home/cbgeo/research && \
    git clone https://github.com/cb-geo/felib.git

# Done
WORKDIR /home/cbgeo/research/felib
```

Converted Singularity specification file
```
bootstrap:docker
From:fedora:latest

%post
# Krishna Kumar <kks32@cam.ac.uk>
dnf update -y && \
    dnf remove -y vim-minimal python sqlite && \
    dnf install -y clang cmake cppcheck eigen3-devel\
    findutils gcc gcc-c++ git make perl-Digest-MD5 ruby tar valgrind vim wget && \
    dnf clean all

wget http://ftp.de.debian.org/debian/pool/main/l/lcov/lcov_1.11.orig.tar.gz && tar xf lcov_1.11.orig.tar.gz && make -C lcov-1.11/ install

useradd cbgeo
USER cbgeo

mkdir -p /home/cbgeo/research && \
    cd /home/cbgeo/research && \
    git clone https://github.com/cb-geo/felib.git
```
