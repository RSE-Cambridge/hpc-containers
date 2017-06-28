## Running a Singularity container

A container can be run with

    singularity run mycontainer.img

which executes a pre-specified command in the container.

To get more flexibility, running

    singularity shell mycontainer.img

will spawn a shell inside the containerised environment.

### Sharing containers

Sharing a container with a collegue is as simple as giving them access to the container. This could mean copying the image to them or in a cluster environment, setting the permisions on the image file to let that user read the file.

### Changing the contents of a container

By default, the container will be mounted as read-only. If you need to make changes to the container (e.g. install new software) then this can be acheived with

    singularity shell --writeable mycontainer.img

which, as long as your user has permission to edit the `mycontainer.img` will let you make changes to the container.

> **Hint** In general, it will make most sense to construct your workflows so that the container image is not changed frequently.

A containerised application can read and write from files outside the container.

### Accessing data outside the container

Data from outside the container can be accessed by *mounting* it inside the container with the `--bind` option. Running

    singularity shell --bind ~/mydata:/scratch mycontainer.img

will make the directory `~/mydata` *outside* the container appear *inside* the container at `/scratch`.
