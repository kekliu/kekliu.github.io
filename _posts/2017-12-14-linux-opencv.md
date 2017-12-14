---
layout:     post
title:      "Linux 编译安装 OpenCV + python3"
subtitle:   ""
date:       2017-12-14 11:00
author:     "kekliu"
header-img: ""
catalog:    true
tags:
    - Linux
    - OpenCV
---


### 1. 为什么使用源码安装
Q: 为什么不使用直接官方的二进制分发包？<br>
A: 对于C++，使用apt-get的二进制分发包最新版本仅为2.4.9；对于python，使用pip安装的opencv没有对视频解析的支持。总之，二进制分发包功能有限/版本不够新，这就是我们使用源码编译的原因。

### 2. 编译安装
#### 2.1 环境介绍
下面为安装环境，其它Linux发行版的安装步骤与此类似。
> OS:                 Ubuntu 16.04<br>
> kernel:             4.10.0-42-generic<br>
> hardware platform:  x86_64

#### 2.2 安装步骤
##### a. 下载源码
我们直接从github上下载源码：
> git clone -b 3.3.1 --depth 1 https://github.com/opencv/opencv<br>
> git clone -b 3.3.1 --depth 1 https://github.com/opencv/opencv_contrib

'-b 3.3.1' 表示下载 3.3.1 版本的 opencv 和相应的 opencv_contrib, '--depth 1' 表示只下载 3.3.1 一个版本的库而非克隆整个库

##### b. 编译和安装
``` shell
# 安装必备库
sudo apt-get install build-essential cmake
sudo apt-get install python3-dev python3-numpy
sudo apt-get install libgtk2.0-dev pkg-config libavcodec-dev libavformat-dev libswscale-dev
# 安装可选包
sudo apt-get install libjpeg-dev libpng-dev libtiff-dev libjasper-dev libdc1394-22-dev

# 进入opencv源码目录
cd <opencv-source-dir>/
mkdir build
cd build
# 运行cmake
cmake \
-DCMAKE_BUILD_TYPE=Release \
-DPYTHON_DEFAULT_EXECUTABLE=/usr/bin/python3 \
-DBUILD_opencv_python3=ON \
-DBUILD_opencv_python2=OFF \
-DPYTHON3_EXECUTABLE=/usr/bin/python3 \
-DPYTHON_INCLUDE_DIR=/usr/include/python3.5 \
-DPYTHON_INCLUDE_DIR2=/usr/include/x86_64-linux-gnu/python3.5m \
-DPYTHON_LIBRARY=/usr/lib/x86_64-linux-gnu/libpython3.5m.so \
-DPYTHON3_NUMPY_INCLUDE_DIRS=/usr/lib/python3/dist-packages/numpy/core/include \
-DOPENCV_EXTRA_MODULES_PATH=../../opencv_contrib/modules \
-DCMAKE_INSTALL_PREFIX=/usr/local \
-DBUILD_EXAMPLES=OFF \
..

# 编译和安装
sudo make install -j4
```
##### c. 注意
<b>如果要在virtualenv/anaconda等python环境中安装opencv，请将上述PYTHON_INCLUDE_DIR,PYTHON3_NUMPY_INCLUDE_DIRS等更改到相应的目录</b>
