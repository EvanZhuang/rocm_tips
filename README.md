# Tips for using AMD GPUs and ROCm
Since I am working at AMD GenAI and are constantly using AMD GPUs, might as well write down my guide for other people's reference. 

The [official guide](https://rocm.docs.amd.com/projects/install-on-linux/en/develop/install/3rd-party/pytorch-install.html) provides useful information. I am going to use [conda](https://docs.conda.io/projects/conda/en/latest/index.html) instead of the docker builds, since my experience with docker tells me it leaves some mess with its sudo access.

## Conda Setup
```
conda create --name [your_env] 
conda activate [your_env] 
```

## Installing Pytorch
Replace the ROCm version with yours, the library will get updated.
```
pip3 install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/rocm6.2
```
Test installation in python
```
Python 3.11.10 (main, Oct  3 2024, 07:29:13) [GCC 11.2.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> import torch
>>> torch.cuda.is_available()
True
```
This should mean success.

## Installing Transformers and the related packages
```
pip install transformers datasets accelerate evaluate
```
So far so good?

## Installing Flash Attention 
```
pip install packaging ninja
```
Move on to [Compostable Kernel](https://github.com/ROCm/composable_kernel)
```
git clone https://github.com/ROCm/composable_kernel.git && \
cd composable_kernel && \
mkdir build && \
cd build
```
Check AMD Arch with `clinfo`, you will find it at "Name=[your arch, gfx...]:sramecc+:xnack-"
Now, time to compile the package. I suggest you open up a screen or tmux, cause the compilation might take some time, and you won't lose your progress.
```
cmake -D CMAKE_PREFIX_PATH="/opt/rocm:$CONDA_PREFIX" -D CMAKE_CXX_COMPILER=/opt/rocm/bin/hipcc -D CMAKE_BUILD_TYPE=Release -D GPU_TARGETS="gfx908;gfx90a" ..
```


