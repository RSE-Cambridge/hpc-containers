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
# Now try running as root
sudo singularity shell ~/centos.img
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

### Environments
Singularity passes the Host environment to the container.

```shell
# How is the shell environment transposed into the container?
singularity exec ~/centos.img env
singularity exec ~/centos.img env | wc -l
env -i singularity exec ~/centos.img env | wc -l
env -i FOO=BAR singularity exec ~/centos.img en
```

## Directory access
By default Singularity tries to create a seamless user experience between the host and the container. To do this, Singularity makes various locations accessible within the container automatically. For example, the user’s home directory is always bound into the container as is `/tmp` and `/var/tmp`. Additionally your current working directory (`cwd/pwd`) is also bound into the container iff it is not an operating system directory or already accessible via another mount. For almost all cases, this will work flawlessly as follows:

```shell
[z301@training-z301 ~]$ pwd
/home/z301
[z301@training-z301 ~]$ singularity shell /local/centos.img 
#Singularity: Invoking an interactive shell within container...

Singularity centos.img:~> pwd
#/home/z301
Singularity centos.img:~> uname -a
#Linux training-z301 3.10.0-229.el7.x86_64 #1 SMP Fri Mar 6 11:36:42 UTC 2015 x86_64 x86_64 x86_64 GNU/Linux
Singularity centos.img:~> exit
```

> **Warning**
For directory binds to function properly, there must be an existing target endpoint within the container (just like a mount point). This means that if your home directory exists in a non-standard base directory like `/foobar/username` then the base directory `/foobar` must already exist within the container.
Singularity will not create these base directories! You must enter the container with the `--writable` option being set, and create the directory manually.


Files on the host can be reachable from within the container

```shell
echo "Hello World" > $HOME/hello.txt
singularity exec ~/centos.img cat $HOME/hello.txt
Hello World
```

On a research cluster, you probably want to access locations with big datasets, and then write results too. For this, you will want to bind a folder to the container. Here, we are binding our `/lammps` `/lammps` in the image, and listing the contents to show it worked. We use the command `-B` or `--bind` to do this.

```shell
# On Host
sudo mkdir /lammps
sudo wget -O /lammps/in.granregion.mixer https://goo.gl/axtEQ7
# Launch container
singularity shell --bind /lammps:/lammps ~/centos.img
Singularity centos.img:~> ls -alh /lammps
#in.granregion.mixer
```

## Running LAMMPS code
LAMMPS is a popular Molecular Dynamics code, a Singularity image for LAMMPS code is available in the home directory. To execute the `lammps` directory code:

```shell
wget -O ./in.granregion.mixer https://goo.gl/axtEQ7
singularity run /local/lammps.img -i $HOME/in.granregion.mixer 
# or to run the example problem from the Host /lammp directory
singularity run --bind /lammps:/lammps /local/lammps.img -i /lammps/in.granregion.mixer
```

## Writing in the container

While it is discouraged to make tweaks on the fly to containers (you should properly define all edits to the container in a boostrap specification file, shown later) you can add `--writable` to any command to write inside the container. Assuming we have our `centos.img` on our local resource with `sudo`, let’s 
try to make a `/data` directory:

```shell
sudo singularity shell --writable centos7.img
Singularity centos.img:~> mkdir /data
Singularity centos.img:~> touch /data/foo.txt
Singularity centos.img:~> exit
# We made data & foo! But after we exit, is the file still there?
singularity exec ~/centos.img ls /data
#foo.txt
```
> **Info** We would ideally have done this action with bootstrap, discussed next.

