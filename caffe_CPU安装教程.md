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

1)CPU_ONLY := 1 或
USE_CUDNN := 1
2)配置一些引用文件（增加部分主要是解决新版本下，HDF5的路径问题）

1)INCLUDE_DIRS := $(PYTHON_INCLUDE) 
 /usr/local/include     
 /usr/lib/x86_64-linux-gnu/hdf5/serial/include    

2)LIBRARY_DIRS := $(PYTHON_LIB) 
 /usr/local/lib 
 /usr/lib /usr/lib/x86_64-linux-gnu/hdf5/serial
3)BLAS := atlas
计算能力 mkl > openlas >atlas

4)# NOTE: this is required only if you will compile the python interface.
# We need to be able to find Python.h and numpy/arrayobject.h.
PYTHON_INCLUDE := /usr/include/python2.7 \
		/usr/local/lib/python2.7/dist-packages/numpy/core/include
5)
/usr/include/boost/property_tree/detail/json_parser_read.hpp: In constructor ‘boost::property_tree::json_parser::json_grammar<Ptree>::definition<Scanner>::definition(const boost::property_tree::json_parser::json_grammar<Ptree>&)’:
/usr/include/boost/property_tree/detail/json_parser_read.hpp:257:264: error: ‘type name’ declared as function returning an array
升级gcc g++
1 sudo add-apt-repository ppa:ubuntu-toolchain-r/test 
2 sudo apt-get update
3 sudo apt-get install gcc-5 g++-5

6)
.build_release/lib/libcaffe.so: undefined reference to `boost::match_results<__gnu_cxx::__normal_iterator<char const*, std::string>, std::allocator<boost::sub_match<__gnu_cxx::__normal_iterator<char const*, std::string> > > >::maybe_assign(boost::match_results<__gnu_cxx::__normal_iterator<char const*, std::string>, std::allocator<boost::sub_match<__gnu_cxx::__normal_iterator<char const*, std::string> > > > const&)'
.build_release/lib/libcaffe.so: undefined reference to `boost::re_detail::perl_matcher<__gnu_cxx::__normal_iterator<char const*, std::string>, std::allocator<boost::sub_match<__gnu_cxx::__normal_iterator<char const*, std::string> > >, boost::regex_traits<char, boost::cpp_regex_traits<char> > >::construct_init(boost::basic_regex<char, boost::regex_traits<char, boost::cpp_regex_traits<char> > > const&, boost::regex_constants::_match_flags)'
collect2: error: ld returned 1 exit status
Makefile:545: recipe for target '.build_release/tools/device_query.bin' failed
make: *** [.build_release/tools/device_query.bin] Error 1
make: *** Waiting for unfinished jobs....

sudo apt-get install libboost-all-dev
安装所有boost库

7)
build/lib/libcaffe.a(image_io.o): In function caffe::ReadVideoToVolumeDatum(char const*, int, int, int, int, int, int, caffe::VolumeDatum*)': image_io.cpp:(.text+0x1905): undefined reference tocv::VideoCapture::VideoCapture()'
image_io.cpp:(.text+0x1abe): undefined reference to cv::VideoCapture::open(cv::String const&)' image_io.cpp:(.text+0x1ace): undefined reference tocv::VideoCapture::isOpened() const'

adding ：
LIBRARIES += glog gflags protobuf leveldb snappy \
lmdb boost_system hdf5_hl hdf5 m \
opencv_core opencv_highgui opencv_imgproc opencv_imgcodecs opencv_videoio 

to LIBRARIES in the Makefile
 
打开之后修改如下内容：
//若使用cudnn，则将# USE_CUDNN := 1 修改成： USE_CUDNN := 1 
//若使用的opencv版本是3的，则将# OPENCV_VERSION := 3 修改为： OPENCV_VERSION := 3 
//若要使用python来编写layer，则需要将# WITH_PYTHON_LAYER := 1 修改为 WITH_PYTHON_LAYER := 1 
//重要的一项 将# Whatever else you find you need goes here.下面的 INCLUDE_DIRS := $(PYTHON_INCLUDE) /usr/local/include LIBRARY_DIRS := $(PYTHON_LIB) /usr/local/lib /usr/lib 
修改为： INCLUDE_DIRS := $(PYTHON_INCLUDE) /usr/local/include /usr/include/hdf5/serial 
      LIBRARY_DIRS := $(PYTHON_LIB) /usr/local/lib /usr/lib /usr/lib/x86_64-linux-gnu /usr/lib/x86_64-linux-gnu/hdf5/serial //这是因为ubuntu16.04的文件包含位置发生了变化，尤其是需要用到的hdf5的位置，所以需要更改这一路径



编辑/usr/local/cuda/include/host_config.h，将其中的第115行注释掉：
将

#error-- unsupported GNU version! gcc versions later than 4.9 are not supported!

改为
//#error-- unsupported GNU version! gcc versions later than 4.9 are not supported!

3.Make Caffe

低配置
make
make test
make runtest

高配置
make -j4
make test -j4
make runtest -j4

若出现：
.build_release/lib/libcaffe.so：对‘cv::imread(cv::String const&, int)’未定义的引用

配置过了opencv的，可以这样查询安装版本：
$ pkg-config --modversion opencv

打开makefile文件，

将
NVCCFLAGS +=-ccbin=$(CXX) -Xcompiler-fPIC $(COMMON_FLAGS)
替换
NVCCFLAGS += -D_FORCE_INLINES -ccbin=$(CXX) -Xcompiler -fPIC $(COMMON_FLAGS)

把opencv需要的lib添加到Makefile文件中，找到LIBRARIES（在PYTHON_LIBRARIES := boost_python python2.7 前一行）并修改为：
LIBRARIES += glog gflags protobuf leveldb snappy lmdb boost_system hdf5_hl hdf5 m opencv_core opencv_highgui opencv_imgproc opencv_imgcodecs

将：
LIBRARIES += glog gflags protobuf boost_system boost_filesystem m hdf5_hl hdf5
改为：
LIBRARIES += glog gflags protobuf boost_system boost_filesystem m hdf5_serial_hl hdf5_serial

缺少caffe.pb.cc, caffe.pb.h
用protoc从caffe/src/caffe/proto/caffe.proto生成caffe.pb.h和caffe.pb.cc
$ protoc src/caffe/proto/caffe.proto --cpp_out=.
$ sudo mkdir include/caffe/proto
$ sudo mv src/caffe/proto/caffe.pb.h include/caffe/proto

如果在编译的过程中出现这样的问题：
nvcc fatal   : Unsupported gpu architecture 'compute_60'
需要手动修改caffe主目录下的Makefile.config文件
CUDA_ARCH := -gencode arch=compute_30,code=sm_30 \
		-gencode arch=compute_35,code=sm_35 \
		-gencode arch=compute_50,code=sm_50 \
		-gencode arch=compute_50,code=compute_50 \
		-gencode arch=compute_52,code=sm_52 \
		#-gencode arch=compute_60,code=sm_60 \
		#-gencode arch=compute_61,code=sm_61
我这里面把下面两行注释掉，再编译，就能通过了，这个可能和自己所用的哪款显卡有关系。
#-gencode arch=compute_60,code=sm_60 \
#-gencode arch=compute_61,code=sm_61

4.编译成功，否则执行 make clean ，再次进行 make

编译python接口
1.Caffe拥有python\C++\shell接口，在Caffe使用python特别方便，在实例中都有接口的说明。

1)确保pip已经安装

sudo apt-get install python-pip

2)新建shell文件, 进入python 文件夹下 并执行安装依赖

for req in $(cat requirements.txt); do sudo pip install $req; done

3)在caffe_master目录下 编译python接口

make pycaffe

make distribute

执行完后修改bashrc文件，
sudo gedit ~/.bashrc
添加

PYTHONPATH=${HOME}/caffe/distribute/python:$PYTHONPATH

LD_LIBRARY_PATH=${HOME}/caffe/build/lib:$LD_LIBRARY_PATH

使得python能够找到caffe的依赖。


当出现下面错误的时候修改

1)fatal error: numpy/arrayobject.h: No such file or directory.


PYTHON_INCLUDE := /usr/include/python2.7 \
/usr/lib/python2.7/dist-packages/numpy/core/include This is where our error is. So by changing this line to:

PYTHON_INCLUDE := /usr/include/python2.7 \
/usr/local/lib/python2.7/dist-packages/numpy/core/include   
# Our problem is gone.

2)libcudart.so.7.5: cannot open shared object file: No such file or directory

解决方法：

[html] view plain copy

    64-bit：sudo ldconfig /usr/local/cuda/lib64 
	
2.运行python结构

import sys
sys.path.append("~/Documents/caffe/python")  
'''
import caffe 

// If the last import caffe doesn't pop out any error, congratulations, now you can use python to play with caffe!
'''
错误1：

File "<stdin>", line 1, in <module>   ImportError: No module named caffe

    1

解决方法：

sudo echo export PYTHONPATH="~/your caffe path/python" >> ~/.bashrc

source ~/.bashrc

错误2：

ImportError: No module named skimage.io


解决方法：

pip install -U scikit-image #若没有安装pip: sudo apt install python-pip

ok，最后一步，配置notebook环境

首先要安装python接口依赖库，在caffe根目录的python文件夹下，有一个requirements.txt的清单文件，上面列出了需要的依赖库，按照这个清单安装就可以了。

在安装scipy库的时候，需要fortran编译器（gfortran)，如果没有这个编译器就会报错，因此，我们可以先安装一下。

首先进入 caffe/python 目录下，执行安装代码：

sudo apt-get install gfortran

for req in $(cat requirements.txt); do sudo pip install $req; done

安装完成以后执行：

sudo pip install -r requirements.txt

就会看到，安装成功的，都会显示Requirement already satisfied, 没有安装成功的，会继续安装。

然后安装 jupyter ：

sudo pip install jupyter

安装完成后运行 notebook :

jupyter notebook

或

ipython notebook
如果成功则说明一切ok，否则检查路径从头再来，甚至需要重新编译python。


