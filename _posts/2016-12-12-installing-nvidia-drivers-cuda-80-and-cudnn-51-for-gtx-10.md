---
inFeed: true
description: >-
  Restart the computer. Screen should has the right resolution now, Nvidia
  drivers should be installed in /usr/local/cuda.
dateModified: '2017-03-09T03:06:25.719Z'
datePublished: '2017-03-09T03:06:26.388Z'
title: 'Installing Nvidia drivers, CUDA 8.0 and cuDNN 5.1 for GTX-1060 on Ubuntu 16.04'
author: []
publisher: {}
via: {}
hasPage: true
sourcePath: _posts/2016-12-12-installing-nvidia-drivers-cuda-80-and-cudnn-51-for-gtx-10.md
datePublishedOriginal: '2017-03-09T03:06:26.388Z'
starred: false
url: installing-nvidia-drivers-cuda-80-and-cudnn-51-for-gtx-10/index.html
_type: Article

---
# Installing Nvidia drivers, CUDA 8.0 and cuDNN 5.1 for GTX-1060 on Ubuntu 16.04

## Install Nvidia drives and CUDA 8.0

    wget http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/cuda-repo-ubuntu1604_8.0.44-1_amd64.deb
    
    sudo dpkg -i cuda-repo-ubuntu1604_8.0.44-1_amd64.deb
    
    sudo apt-get update sudo apt-get install cuda

Restart the computer. Screen should has the right resolution now, Nvidia drivers should be installed in _/usr/local/cuda_.

Edit your _~/.bashrc_ and source it:

    export PATH=/usr/local/cuda-8.0/bin${PATH:+:${PATH}}
    export LD_LIBRARY_PATH=/usr/local/cuda-8.0/lib64${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}

## Install cuDNN

First signup or login into Nvidia developer program and download it: [https://developer.nvidia.com/cudnn][0]

You should download the following package:

_Download cuDNN v5.1 (August 10, 2016), for CUDA 8.0 cuDNN v5.1 Library for Linux_

Uncompress and copy cuDNN to CUDA directory:

    tar xvzf cudnn-8.0-linux-x64-v5.1.tgz
    sudo cp cuda/include/cudnn.h /usr/local/cuda/include
    sudo cp cuda/lib64/libcudnn* /usr/local/cuda/lib64
    sudo chmod a+r /usr/local/cuda/include/cudnn.h /usr/local/cuda/lib64/libcudnn*

Now you can already check if CUDA/cuDNN are properly installed:

    nvidia-smi
    
        Sat Oct 15 22:46:05 2016 
        +-----------------------------------------------------------------------------+
        | NVIDIA-SMI 367.48 Driver Version: 367.48                                    | 
        |-------------------------------+----------------------+----------------------+ 
        | GPU Name Persistence-M        | Bus-Id Disp.A        | Volatile Uncorr. ECC | 
        | Fan Temp Perf Pwr:Usage/Cap   | Memory-Usage         | GPU-Util Compute M.  | 
        |===============================+======================+======================| 
        | 0 GeForce GTX 106... Off      | 0000:01:00.0 On      | N/A                  |
        | 0% 35C P8 9W / 120W           | 324MiB / 6071MiB     | 1% Default           | 
        +-------------------------------+----------------------+----------------------+ 
        +-----------------------------------------------------------------------------+ 
        | Processes: GPU Memory              | | GPU PID Type Process name Usage      | 
        |=============================================================================| 
        | 0 2802 G /usr/lib/xorg/Xorg 167MiB | | 0 3497 G compiz 155MiB               | 
        +-----------------------------------------------------------------------------+

Check if building CUDA samples works:

    cd /usr/local/cuda/samples/5_Simulations/oceanFFT/
    make
    ./oceanFFT

## Install TensorFlow

TensorFlow binaries do not support CUDA 8.0 neither cuDNN 5.1 so we have to build it from source. We're going to build TF as a PIP wheel, so we need PIP installed:

    sudo apt-get install python-pip python-dev

Then setup the builder, Bazel, this guy will build the PIP wheel:

    sudo add-apt-repository ppa:webupd8team/java
    sudo apt-get update
    sudo apt-get install oracle-java8-installer
    echo "deb [arch=amd64] http://storage.googleapis.com/bazel-apt stable jdk1.8" | sudo tee /etc/apt/sources.list.d/bazel.list
    curl https://storage.googleapis.com/bazel-apt/doc/apt-key.pub.gpg | sudo apt-key add -
    sudo apt-get update
    sudo apt-get install bazel
    sudo apt-get upgrade bazel

Install other dependencies:

    sudo apt-get install python-numpy swig python-dev python-wheel

Clone TF and configure it:

    git clone https://github.com/tensorflow/tensorflow
    cd tensorflow
    ./configure # Defaults generally work

Use Bazel to create the PIP package and install it (with GPU support):

    bazel build -c opt --config=cuda //tensorflow/tools/pip_package:build_pip_package bazel-bin/tensorflow/tools/pip_package/build_pip_package /tmp/tensorflow_pkg
    sudo pip install /tmp/tensorflow_pkg/tensorflow-0.11.0rc0-py2-none-any.whl

And that's it! Check if your build is working:

    cd tensorflow/models/image/mnist
    python convolutional.py



[0]: https://developer.nvidia.com/cudnn