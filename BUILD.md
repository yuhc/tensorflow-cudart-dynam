The following steps have been tested on Ubuntu 18.04 with Python 3.6.9.

## Setup toolchain

Upgrade python (to 3.6.9 on Ubuntu 18.04) and install bazel (0.24.1 is used as example; 0.26.1 is recommended):

```
$ sudo apt update
$ sudo apt upgrade
$ sudo apt install python3 python3-dev
$ wget https://github.com/bazelbuild/bazel/releases/download/0.24.1/bazel-0.24.1-installer-linux-x86_64.sh
$ chmod +x bazel-0.24.1-installer-linux-x86_64.sh
$ sudo ./bazel-0.24.1-installer-linux-x86_64.sh
```

Create virtualenv for compiling TensorFlow:

```
$ virtualenv -p python3 ./venv-ava
$ source venv-ava/bin/activate
$ pip install keras_preprocessing
```

## Configure TensorFlow

`./configure` will ask where your python is. Answer the real python location and do not fill in a soft link.
In the example, it uses `/project/venv-ava/bin/python3` instead of `/project/venv-ava/bin/python`.

```
$ cd tensorflow-cudart-dynam/
$ ./configure
```

The output will look like this (make sure that compute capability and CUDA version match your hardware):

```
WARNING: --batch mode is deprecated. Please instead explicitly shut down your Bazel server using the command "bazel shutdown".
You have bazel 0.24.1 installed.
Please specify the location of python. [Default is /project/venv-ava/bin/python]:
/project/venv-ava/bin/python3

Traceback (most recent call last):
  File "<string>", line 1, in <module>
AttributeError: module 'site' has no attribute 'getsitepackages'
Found possible Python library paths:
  /project/venv-ava/lib/python3.6/site-packages
Please input the desired Python library path to use.  Default is [/project/venv-ava/lib/python3.6/site-packages]

Do you wish to build TensorFlow with XLA JIT support? [Y/n]:
XLA JIT support will be enabled for TensorFlow.

Do you wish to build TensorFlow with OpenCL SYCL support? [y/N]:
No OpenCL SYCL support will be enabled for TensorFlow.

Do you wish to build TensorFlow with ROCm support? [y/N]:
No ROCm support will be enabled for TensorFlow.

Do you wish to build TensorFlow with CUDA support? [y/N]: y
CUDA support will be enabled for TensorFlow.

Do you wish to build TensorFlow with TensorRT support? [y/N]:
No TensorRT support will be enabled for TensorFlow.

Could not find any cudnn.h matching version '' in any subdirectory:
        ''
        'include'
        'include/cuda'
        'include/*-linux-gnu'
        'extras/CUPTI/include'
        'include/cuda/CUPTI'
of:
        '/lib'
        '/lib/i386-linux-gnu'
        '/lib/x86_64-linux-gnu'
        '/lib32'
        '/usr'
        '/usr/lib'
        '/usr/lib/i386-linux-gnu'
        '/usr/lib/x86_64-linux-gnu'
        '/usr/lib/x86_64-linux-gnu/libfakeroot'
        '/usr/lib32'
        '/usr/local/cuda'
        '/usr/local/cuda/lib64'
Asking for detailed CUDA configuration...

Please specify the CUDA SDK version you want to use. [Leave empty to default to CUDA 10]: 10.0


Please specify the cuDNN version you want to use. [Leave empty to default to cuDNN 7]: 7


Please specify the locally installed NCCL version you want to use. [Leave empty to use http://github.com/nvidia/nccl]:


Please specify the comma-separated list of base paths to look for CUDA libraries and headers. [Leave empty to use the default]:


Found CUDA 10.0 in:
    /usr/local/cuda-10.0/lib64
    /usr/local/cuda-10.0/include
Found cuDNN 7 in:
    /usr/local/cuda-10.0/lib64
    /usr/local/cuda-10.0/include


Please specify a list of comma-separated CUDA compute capabilities you want to build with.
You can find the compute capability of your device at: https://developer.nvidia.com/cuda-gpus.
Please note that each additional compute capability significantly increases your build time and binary size, and that TensorFlow only supports compute capabilities >= 3.5 [Default is: 5.0]:


Do you want to use clang as CUDA compiler? [y/N]:
nvcc will be used as CUDA compiler.

Please specify which gcc should be used by nvcc as the host compiler. [Default is /usr/bin/gcc]:


Do you wish to build TensorFlow with MPI support? [y/N]:
No MPI support will be enabled for TensorFlow.

Please specify optimization flags to use during compilation when bazel option "--config=opt" is specified [Default is -march=native -Wno-sign-compare]:


Would you like to interactively configure ./WORKSPACE for Android builds? [y/N]:
Not configuring the WORKSPACE for Android builds.

Preconfigured Bazel build configs. You can use any of the below by adding "--config=<>" to your build command. See .bazelrc for more details.
	--config=mkl         	# Build with MKL support.
	--config=monolithic  	# Config for mostly static monolithic build.
	--config=gdr         	# Build with GDR support.
	--config=verbs       	# Build with libverbs support.
	--config=ngraph      	# Build with Intel nGraph support.
	--config=numa        	# Build with NUMA support.
	--config=dynamic_kernels	# (Experimental) Build kernels into separate shared objects.
Preconfigured Bazel build configs to DISABLE default on features:
	--config=noaws       	# Disable AWS S3 filesystem support.
	--config=nogcp       	# Disable GCP support.
	--config=nohdfs      	# Disable HDFS support.
	--config=noignite    	# Disable Apache Ignite support.
	--config=nokafka     	# Disable Apache Kafka support.
	--config=nonccl      	# Disable NVIDIA NCCL support.
Configuration finished
```

## Build TensorFlow

Bazel will build a python package under `/tmp/tensorflow_pkg`.

```
$ bazel build --config=cuda //tensorflow/tools/pip_package:build_pip_package
$ ./bazel-bin/tensorflow/tools/pip_package/build_pip_package /tmp/tensorflow_pkg
```
