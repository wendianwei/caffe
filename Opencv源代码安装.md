第一步：到opencv的官方网站上下载安装包并且解压

第二步：build-essential 软件包，会下载依赖的软件包，安装gcc/g++/gdb/make 等基本编程工具，组成开发环境。还有辅助依赖项，Ubuntu 下可直接打开terminal输入如下四条命令：

    sudo apt-get install build-essential

    sudo apt-get install cmake git libgtk2.0-dev pkg-config libavcodec-dev libavformat-dev libswscale-dev

    sudo apt-get install python-dev python-numpy libtbb2 libtbb-dev libjpeg-dev libpng-dev libtiff-dev libjasper-dev libdc1394-22-dev

    sudo apt-get install pkg-config

至此，安装opencv之前的准备工作，我们已经全部就绪。

第三步：opencv的安装和编译 
笔者在这里推荐采用cMake安装方式进行安装。 
我们将路径cd到有CMakeLists.txt这个文件夹下。我们可以在下载并解压后的opencv包中找到这一文件，图形界面下双击opencv解压后文件夹，就能看到该文件了，我们就cd到这个路径即可。terminal中输入：

    cmake .

就能很快找到该文件，当然网上一些教程中写到在这一步配置参数，笔者建议也如此可以更方便。在terminal中输入：

    cd opencv-2.4.9 
    mkdir release 
    cd release 
    cmake -D CMAKE_BUILD_TYPE=RELEASE -D CMAKE_INSTALL_PREFIX=/usr/local ..

即可完成该步骤。

cMake安装启动！！terminal中输入如下命令：

    make

然后巴拉巴拉之后再在terminal中输入：

    sudo make install

恩，安装开始！会看到屏幕出现一大堆文字巴拉巴拉巴拉……

,terminal中敲入如下命令：

    gedit /etc/ld.so.conf

在弹出的窗口中复制如下一段文字：

    /usr/local/lib

然后使得配置生效：

    sudo ldconfig

然后再terminal中写入：

    sudo gedit /etc/bash.bashrc

之后我们在弹出的窗口中添加：

    PKG_CONFIG_PATH=$PKG_CONFIG_PATH:/usr/local/lib/pkgconfig 
    export PKG_CONFIG_PATH

到此，安装和配置任务完成！此时我们可以欢快地敲代码了。又是熟悉的配方，又是熟悉的味道，我们的opencv老大又回来啦！

第四步：运行与测试 
opencv在linux中可以直接被g++编译，因为我们都装好了。 
那么这时候你可能在好多教程中都看到他们会让你费了半天劲找什么samples/c什么build之类的shell脚本。而实际上，可能是因为笔者笨并没有找到什么之类的脚本。所以我们直接用samples里的c++文件进行测试即可。我们知道这个samples中有好多代码不能直接running而是需要添加参数或者路径之类的，修改代码有一个很快的办法，ubuntu中提供给大家gedit这个东西，很是方便，我们可以直接用cd到代码文件中，然后在terminal输入gedit xxx.cpp，就可以进行修改了。 
笔者这里可以告诉大家samples中有几个文件可以不用修改直接跑。像camshiftdemo.cpp ，edge.cpp之类都可以，随便跑一个吧，笔者这里调用了edge.cpp文件。那么如何调用呢？下面请牢记这条命令，亲测有效，其他版本不保证：

    g++ edge.cpp  -o test `pkg-config --cflags --libs opencv`

编译完之后可以运行：

        ./test

我们可以看到是个canny检测。