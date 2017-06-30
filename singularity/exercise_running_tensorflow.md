# Hands-on #5: Running TensorFlow on Singularity container

> TensorFlow configuration file

```shell
BootStrap: yum
OSVersion: 7
MirrorURL: http://mirror.centos.org/centos-%{OSVERSION}/%{OSVERSION}/os/$basearch/
Include: yum
%post
  yum -y groupinstall "Development Tools"
  yum -y install epel-release which
  yum -y install python-devel python-pip python34-devel python34-pip
  pip install tensorflow
  pip3 install tensorflow
%runscript
  python3 "$@"
```


> TensorFlow GPU configuration file

```shell
BootStrap: yum
OSVersion: 7
MirrorURL: http://mirror.centos.org/centos-%{OSVERSION}/%{OSVERSION}/os/$basearch/
Include: yum
%post
  yum -y groupinstall "Development Tools"
  yum -y install epel-release which
  yum -y install python-devel python-pip python34-devel python34-pip
  pip install tensorflow-gpu
  pip3 install tensorflow-gpu
%runscript
  python3 "$@"
```

Running TensorFlow on Wilkes compute cluster

```shell
module load singularity cuda/8.0 cudnn/5.1_cuda-8.0
singularity run --nv --bind /usr/local/Cluster-Apps tensorflow-gpu.img tflow-gpu-test.py
```
