安装依赖包
1.安装protobuf,leveldb,snappy,opencv,hdf5, protobuf compiler andboost:

sudo apt-get install libprotobuf-dev libleveldb-dev libsnappy-dev libopencv-dev libhdf5-serial-dev protobuf-compiler

sudo apt-get install --no-install-recommends libboost-all-dev


2.安装gflags,glogs ,lmdb andatlas.

sudo apt-get install libgflags-dev libgoogle-glog-dev liblmdb-dev
sudo apt-get install libatlas-base-dev

下载Caffe
使用git直接下载Caffe非常简单，或者去https://github.com/BVLC/caffe下载

git clone git://github.com/BVLC/caffe.git


编译Caffe
1.切换到Caffe所在目录

cp Makefile.config.example Makefile.config


2.配置Makefile.config

1)CPU_ONLY := 1

2)配置一些引用文件（增加部分主要是解决新版本下，HDF5的路径问题）

1)INCLUDE_DIRS := $(PYTHON_INCLUDE) 
 /usr/local/include     
 /usr/lib/x86_64-linux-gnu/hdf5/serial/include    

2)LIBRARY_DIRS := $(PYTHON_LIB) 
 /usr/local/lib 
 /usr/lib /usr/lib/x86_64-linux-gnu/hdf5/serial
3)BLAS := atlas
计算能力 mkl > openlas >atlas


3.Make Caffe

make all -j4
make test -j4
make runtest -j4


4.编译成功，否则执行 make clean ，再次进行 make

编译python接口
1.Caffe拥有python\C++\shell接口，在Caffe使用python特别方便，在实例中都有接口的说明。

1)确保pip已经安装

sudo apt-get install python-pip


2)新建shell文件, 进入python 文件夹下 并执行安装依赖

for req in $(cat requirements.txt); do pip install $req; done


3)在caffe_master目录下 编译python接口

make pycaffe


当出现下面错误的时候修改

fatal error: numpy/arrayobject.h: No such file or directory.


PYTHON_INCLUDE := /usr/include/python2.7 \
/usr/lib/python2.7/dist-packages/numpy/core/include This is where our error is. So by changing this line to:

PYTHON_INCLUDE := /usr/include/python2.7 \
/usr/local/lib/python2.7/dist-packages/numpy/core/include   
# Our problem is gone.

2.运行python结构

import sys
sys.path.append("~/Documents/caffe/python")  
'''
import caffe 

// If the last import caffe doesn't pop out any error, congratulations, now you can use python to play with caffe!
'''

