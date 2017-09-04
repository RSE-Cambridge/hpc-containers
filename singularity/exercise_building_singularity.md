# Hands-on #2: Building containers from scratch

## Singularity configuration file

We will build a container image for LAMMPS code using CentOS as the base image. A template Singularity configuration file is available in the `/home` directory.

> Create/use the lammps template configuration file `lammps.cfg`

```shell
# Header
BootStrap: 
OSVersion: 
MirrorURL: 
Include:

# Sections
%setup

%post

%runscript
```

### Header

We will BootStrap CentOS version `7` with package manager `yum`. The `MirrorURL` for downloading CentOS is `http://mirror.centos.org/centos-%{OSVERSION}/%{OSVERSION}/os/$basearch/`. `Include: yum`.

### Sections

In this example, we will use `%setup`, `%post` and `%runscript`.

* Download and copy LAMMPS to `/opt`. Use `$SINGULARITY_ROOTFS` to access root file system in the container:

```shell
cd $SINGULARITY_ROOTFS/opt
wget http://lammps.sandia.gov/tars/lammps-stable.tar.gz
```

* Install development tools: `yum -y groupinstall "Development Tools"`

* Configure and compile LAMMPS

```shell
mkdir -p /opt/lammps
cd /opt/lammps
tar xf ../lammps-stable.tar.gz --strip-components 1

cd src
make yes-granular |& tee log.make_yes_granular
make -j serial |& tee log.make_serial
```

* Configure the container to run `lmp_serial` on execution of the container:

```shell
/opt/lammps/src/lmp_serial "$@"
```

## Creating the container image

* Create a container called `lammps` and modify the size to 2048 MiB.

```
singularity create --size 2048 /local/lammps.img`
```

* Bootstrap the lammps configuration file created in the previous step.

```
sudo singularity bootstrap /local/lammps.img lammps.cfg
```
> **Note** Bootstrapping step requires `root` access.

## Running the container image

To execute the container 

```
singularity run /local/lammps.img -i in.granregion.mixer
```
