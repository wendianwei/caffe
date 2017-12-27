首先去官网上查看适合你GPU的驱动（http://www.nvidia.com/Download/index.aspx?lang=en-us）
例如，本人的GPU适合的驱动如图：
这里写图片描述

执行如下语句，安装

sudo add-apt-repository ppa:graphics-drivers/ppa
sudo apt-get update
sudo apt-get install nvidia-367
sudo apt-get install mesa-common-dev
sudo apt-get install freeglut3-dev


执行完上述后，重启（reboot）。
重启后输入：

nvidia-smi


如果出现了你的GPU列表，则说明驱动安装成功了。另外也可以通过

nvidia-settings


查看自己机器上详细的GPU信息，本人机器的信息如下：
这里写图片描述
2、安装CUDA

cuda是nvidia的编程语言平台，想使用GPU就必须要使用cuda。
从这里下载cuda的安装文件
https://developer.nvidia.com/cuda-release-candidate-download
这里写图片描述
注意这里下载的是cuda8.0的runfile（local）文件。
这里是nvidia给出的官方安装指南（遇到问题时可以查阅）：
http://docs.nvidia.com/cuda/cuda-installation-guide-linux/#axzz4HIBXnwyt
下载完cuda8.0后，执行如下语句，运行runfile文件：

sudo sh cuda_8.0.27_linux.run


执行后会有一系列提示让你确认，但是注意，有个让你选择是否安装nvidia361驱动时，一定要选择否，因为前面我们已经安装了更加新的nvidia367，所以这里不要选择安装。其余的都直接默认或者选择是即可。
安装成功后会出现如下界面：

===========
= Summary =
===========
Driver: Not Selected
Toolkit: Installed in /usr/local/cuda-8.0
Samples: Installed in /home/textminer
Please make sure that
– PATH includes /usr/local/cuda-8.0/bin
– LD_LIBRARY_PATH includes /usr/local/cuda-8.0/lib64, or, add /usr/local/cuda-8.0/lib64 to /etc/ld.so.conf and run ldconfig as root
To uninstall the CUDA Toolkit, run the uninstall script in /usr/local/cuda-8.0/bin
Please see CUDA_Installation_Guide_Linux.pdf in /usr/local/cuda-8.0/doc/pdf for detailed information on setting up CUDA.
***WARNING: Incomplete installation! This installation did not install the CUDA Driver. A driver of version at least 361.00 is required for CUDA 8.0 functionality to work.
To install the driver using this installer, run the following command, replacing with the name of this run file:
sudo .run -silent -driver
Logfile is /opt/temp//cuda_install_6583.log

安装完毕后，再声明一下环境变量，并将其写入到 ~/.bashrc 的尾部:

export PATH=/usr/local/cuda-8.0/bin${PATH:+:${PATH}}
export LD_LIBRARY_PATH=/usr/local/cuda-8.0/lib64${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}


然后设置环境变量和动态链接库，在命令行输入：

$ sudo gedit /etc/profile


在打开的文件末尾加入：

export PATH=/usr/local/cuda/bin:$PATH

保存之后，创建链接文件：

sudo gedit /etc/ld.so.conf.d/cuda.conf


在打开的文件中添加如下语句：

/usr/local/cuda/lib64

然后执行

sudo ldconfig


使链接立即生效。
3、测试cuda的Samples

cd /usr/local/cuda-7.5/samples/1_Utilities/deviceQuery
make
sudo ./deviceQuery


如果显示的是一些关于GPU的信息，则说明安装成功了。
4、使用cudnn

首先去官网下载你需要的cudnn，下载的时候需要注册账号。选择对应你cuda版本的cudnn下载。这里我下载的是cudnn5.1，是个压缩文件（.tgz）
这里写图片描述
下载完cudnn9.0之后进行解压，cd进入cudnn9.1解压之后的include目录，在命令行进行如下操作：

sudo cp cudnn.h /usr/local/cuda/include/    #复制头文件

再将cd进入lib64目录下的动态文件进行复制和链接：

sudo cp lib* /usr/local/cuda/lib64/    #复制动态链接库
cd /usr/local/cuda/lib64/
sudo rm -rf libcudnn.so libcudnn.so.7    #删除原有动态文件
sudo ln -s libcudnn.so.7.0.5 libcudnn.so.7  #生成软衔接
sudo ln -s libcudnn.so.7 libcudnn.so      #生成软链接
