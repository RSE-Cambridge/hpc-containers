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

### Running LAMMPS Docker image to Singularity
```shell
singularity exec docker://cbgeo/lammps:latest /opt/lammps/src/lmp_serial -i /opt/lammps/examples/granregion/in.granregion.mixer
```


