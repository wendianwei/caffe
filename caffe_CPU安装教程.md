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

opencv_videoio 

to LIBRARIES in the Makefile
 
3.Make Caffe

低配置
make all
make test
make runtest

高配置
make all -j4
make test -j4
make runtest -j4

若出现：
.build_release/lib/libcaffe.so：对‘cv::imread(cv::String const&, int)’未定义的引用

配置过了opencv的，可以这样查询安装版本：
$ pkg-config --modversion opencv

把opencv需要的lib添加到Makefile文件中，找到LIBRARIES（在PYTHON_LIBRARIES := boost_python python2.7 前一行）并修改为：
LIBRARIES += glog gflags protobuf leveldb snappy lmdb boost_system hdf5_hl hdf5 m opencv_core opencv_highgui opencv_imgproc opencv_imgcodecs

缺少caffe.pb.c, caffe.pb.h
用protoc从caffe/src/caffe/proto/caffe.proto生成caffe.pb.h和caffe.pb.cc
$ protoc src/caffe/proto/caffe.proto --cpp_out=.
$ sudo mkdir include/caffe/proto
$ sudo mv src/caffe/proto/caffe.pb.h include/caffe/proto

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

