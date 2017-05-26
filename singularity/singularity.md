# What is Singularity
Certain container technologies, such as Docker, require privileged access to the underlying operating system, which is often not possible on a university-wide HPC cluster. [Singularity](http://singularity.lbl.gov) is an implementation of a container, and an engine to run those containers without requiring any privileged access (root-access). Singularity containers allow researchers to isolate the software environment needed to produce a result away from the configuration and operating system of the computer that the analysis will be run on. This means that your colleague at University X can run the analysis exactly the same way on their cluster as you are running it on your cluster at University Y, and all it requires is sharing a Git repository (code) and a container image (which includes dependency libraries, software and tools).



