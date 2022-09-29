---
title: "Installing software"
teaching: 15
exercises: 0
questions:
- "How to install software to ManeFrame II?"
objectives:
- software, tensorflow, pytorch
keypoints:

---

In our HPC system M2 and SuperPOD, in order to use Tensorflow and Pytorch with GPU support, we should install these with cuda and cudnn library enabled.

The following instruction shows step by step installation using CLI (Command Line Interface). You can use any terminal from your Windows/MacOS/Linux system.

## Installing Tensorflow-GPU to ManeFrame II
 
Note that some of the previous model of Tensorflow (2.2, 2.4) are not working well with our preexisting cuda library. Therefore I encourage everyone to use the latest version 2.9.1 (the most current at this time of writing, Sep 2022) to avoid missing library.

Here we use 1 node with P100 GPU to do the installation:

### From login node, request a compute node with GPU:

- To request Pascal P100 GPU:

```bash
$ srun -N1 -G1 -c10 -p gpgpu-1 --mem=64G --pty $SHELL
```

- To request Volta V100 GPU:

```bash
$ srun -N1 --gres=gpu:volta:1 -c10 -p v100x8 --mem=64G --pty $SHELL
```

### Load neccesary library

```
$ module load python/3 gcc-9.2
$ module load cuda-11.4.2-gcc-9.2.0-dft6rfn cudnn/8.2.4.15-11.4-tfeuowy
```

### Install Conda environment and tensorflow to your home directory

```
$ conda create --prefix ~/tensorflow_2.9 python=3.8 pip
$ source activate ~/tensorflow_2.9/  
$ pip install tensorflow==2.9.1 --no-cache-dir
```

### Create Jupyter kernel to work in HPC Open OnDemand

```
$ pip install ipykernel
$ python3 -m ipykernel install --user --name tensorflow_2.9 --display-name TensorflowGPU29
```

Once installation done, check if tensorflow can find any GPU?
  
```bash
$  python
>>> import tensorflow as tf
>>> tf.config.list_physical_devices('GPU')
[PhysicalDevice(name='/physical_device:GPU:0', device_type='GPU')]
```
  
**Note**: in order to **enable** GPU run for Tensorflow via Jupyter Notebook, we need to add the following lines to **Custom environment settings** when requesting a node in the **Open OnDemand**:

```bash
module load gcc-9.2
module load cuda-11.4.2-gcc-9.2.0-dft6rfn cudnn/8.2.4.15-11.4-tfeuowy
```
  
  
## Installing Tensorflow-GPU to SuperPOD
  
Here we gonna install Tensorflow GPU to SuperPOD using A100 GPU.
  
It is similar practice to install Tensorflow with GPU support to SuperPOD with the only difference is how you load the module.

### From SuperPOD login node

```
$ srun -N1 -G1 -c10 --mem=64G --pty $SHELL
```

### Load necessary module

```bash
$ module load spack conda 
$ module load cuda-11.4.4-gcc-10.3.0-ctldo35 cudnn-8.2.4.15-11.4-gcc-10.3.0-eluwegp
```

### Install Conda environment and tensorflow to your home directory

```
$ conda create --prefix ~/tensorflow_2.9 python=3.8 pip
$ source activate ~/tensorflow_2.9/  
$ pip install tensorflow==2.9.1 --no-cache-dir
```

**Note**: There is no Open OnDemand installed to SuperPOD so you should run your model in CLI 
