# Hands-on #4: Building containers from Docker

## Singularity and Docker
Singularity is compatible with Docker. You can `shell`, `import`, `run`, and `exec` Docker container in Singularity. 

```shell
singularity exec docker://centos:latest factor 9876543
# 9876543: 3 227 14503
singularity shell docker://centos:latest
```

Create a Singularity container image, and import a docker container.

```shell
singularity create centos.img
singularity import centos.img docker://centos:latest
singularity exec centos.img echo "Hello Dinosaur!"
```

### Running LAMMPS Docker image in Singularity

Run `docker.io/cbgeo/lammps:latest` Docker container directly in Singularity.

```shell
singularity exec docker://cbgeo/lammps:latest /opt/lammps/src/lmp_serial -i /opt/lammps/examples/granregion/in.granregion.mixer
```

[Singularity-Hub](https://singularity-hub.org/tools/converter/dockerfile) converts Docker specification file to a Singularity file. 

> LAMMPS Docker file

```shell
FROM centos:latest
MAINTAINER Krishna Kumar <kks32@cam.ac.uk>

# Update to latest packages and development tools
RUN yum update -y && \
    yum -y groupinstall "Development Tools" && \
    yum install -y wget && \
    yum clean all

# Download and compile LAMMPS
RUN cd /opt && wget http://lammps.sandia.gov/tars/lammps-stable.tar.gz && \
    mkdir -p /opt/lammps && cd /opt/lammps && \
    tar xf ../lammps-stable.tar.gz --strip-components 1 && \
    cd src && \
    make yes-granular |& tee log.make_yes_granular && \
    make -j serial |& tee log.make_serial
```


> LAMMPS Singularity file (`lammps.cfg`)

```shell
bootstrap:docker
From:centos:latest

%post
# Krishna Kumar <kks32@cam.ac.uk>
  yum update -y && \
  yum -y groupinstall "Development Tools" && \
  yum install -y wget && \
  yum clean all

cd /opt && wget http://lammps.sandia.gov/tars/lammps-stable.tar.gz && \
mkdir -p /opt/lammps && cd /opt/lammps && \
tar xf ../lammps-stable.tar.gz --strip-components 1 && \
cd src && \
make yes-granular |& tee log.make_yes_granular && \
make -j serial |& tee log.make_serial
```

Create and import Singularity file.

```shell
singularity create --size 2048 lammps.img
sudo singularity boostrap lammps.img lammps.cfg
```

