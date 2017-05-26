Containers for High Performance Computing
=========================================
> An introduction to Singularity and containers.

> Research Software Engineering, University of Cambridge

# What are containers?
Containers wrap up a piece of software in a complete file system that contains everything it needs to run: code, runtime, system tools, system libraries – anything you can install on a server. This guarantees that a container will always run the same, regardless of the environment it is deployed. Containers, especially Docker, has been promoted  as a critical tool for reproducible research. In the past few years, there has been an increased adoption of container technologies such as [Docker](https://www.docker.com/), [Kubernetes](https://kubernetes.io) and [Singularity](http://singularity.lbl.gov).


# Containers for research reproducibility 
Reproducible research has been receiving an increasing level of attention throughout universities and research laboratories. There has been a gradual adoption of open source tools and open standards in research, which facilitates research collaboration and enables reproducibility. However, as the complexity of research tools increases, the development environment and dependencies are critical for reproducibility.

Traditionally, research communities conduct large-scale simulations and data analysis on HPC clusters. These clusters are typically set-up on slightly different configuration of hardware, operating system and software. Trying to recreate an environment to re-run code exactly as it was executed on another cluster is beyond the abilities, free time, and account privileges of most researchers who may want to try to reproduce the results. Often times it is also impossible for HPC managers to install new libraries, and support an array of research tools and libraries required by an ever increasing diverse research communities. Ability to run containers on HPC clusters will enable researchers and HPC administrators alike to facilitate reproducible research on large-scale clusters, which was never been possible in the past.
