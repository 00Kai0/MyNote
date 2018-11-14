### 1.安装NVIDIA驱动
根据服务器版本来下载对应的驱动程序，
[驱动下载](http://www.nvidia.com/Download/index.aspx?lang=en-us)
查看nvidia相关信息
```bash
lspci | grep -i nvidia
```
通过浏览器下载上传至服务器，或者终端使用wget下载
```bash
wget [download_url]    # wget下载驱动安装文件
sudo bash NVIDIA*.run  # 执行安装
# 根据安装提示完成安装
```

### 2.安装CUDA toolkit
到[官网下载](https://developer.nvidia.com/cuda-downloads)相应的cuda toolkit
，这里推荐使用8.0版本。
cuda安装
```bash
wget [download_url]   # 同样使用wget下载，
# 下载文件名可能不是以.run结尾，需要换名字
sudo bash cuda*.run   # 运行安装程序，一路回车
```
添加环境变量
```bash
sudo vi /etc/profile  # 修改系统环境变量
```
在profile文件最后加上，这里cuda请根据具体下载的版本修改名字
```bash
export PATH=/usr/local/cuda-8.0/bin:$PATH
export LD_LIBRARY_PATH=/usr/local/cuda-8.0/lib64:$LD_LIBRARY_PATH
```
检查安装结果
```bash
source /etc/profile   # 使profile立刻生效
nvcc -V               # 检查CUDA
# 正常显示版本信息则安装完成
```

### 3.安装CUDNN
[官网下载](https://developer.nvidia.com/rdp/cudnn-download)
与CUDA对应版本的CUDNN文件，下载前需要登录或注册新用户，从浏览器下载
上传至服务器
安装CUDNN
```bash
dpkg -i cudnn*.deb   # 这里我下载的是deb文件，使用dpkg安装
```

### 4.安装Anaconda
我使用的镜像已安装Anaconda，请参考[官网](https://www.anaconda.com/)下载安装

### 5.安装PyTorch
##### 5.1 安装流程
使用conda安装pytorch
```bash
conda install pytorch torchvision
```
验证pytorch能否使用GPU
```python
# CUDA TEST
import torch
x = torch.Tensor([1.0])
xx = x.cuda()
print(xx)

# CUDNN TEST
from torch.backends import cudnn
print(cudnn.is_acceptable(xx))
```
无报错就没问题了
##### 5.2可能出现的问题
报错信息
```bash
RuntimeError: cuda runtime error (35) : CUDA driver version is insufficient for CUDA runtime version at /opt/conda/conda-bld/pytorch_1532579805626/work/aten/src/THC/THCGeneral.cpp:74
```
需要重新安装一下驱动，按照安装NVIDIA驱动流程再安装一遍，会更新到正确的驱动

### 6 安装其他库
##### 6.1 nibabel
```bash
conda install -c conda-forge nibabel
```
