# libviso2_windows (Windows 10, Visual Studio 2015)

在Windows Visual Studio 平台上使用libviso2. 
 + 1. 打开 `vo2_test.sln`
 + 2. 修改demo.cpp 中双目图像文件所在路径
 + 3. 运行程序



## LIBVISO2简介
libviso2 (Library for Visual Odometry 2) 是一个快速跨平台C++库用于计算移动单目/双目相机的6DoF运动。

双目版本基于最小化稀疏特征匹配的重投影误差，比较通用（无需运动模型或安装限制，除了输入图像需为校正后（rectified）的并且标定参数已知）。单目版本还是比较实验性的，使用了8点法估计基础矩阵，为估计绝对尺度，进一步假设相机在地面上方一个已知固定高度移动。

libviso2下载地址：http://www.cvlibs.net/software/libviso/


以下为Windows下具体配置说明.

## Windows visual studio 使用libviso2
为了能在windows visual studio下编译运行libviso2，需要进行一定的配置。首先在VS新建空白控制台应用程序。这里我们选择使用x64平台，在工程目录下新建include文件夹，用来存放必要的支持文件。现在下载这些必要的库。

### 必要支持文件下载与build
在libviso2文件夹的readme中指出：需要如下的png库（libpng）和该库的c++封装（png++）。
 - libpng (http://www.libpng.org/pub/png/libpng.html)  libpng下载了1.6.32版本。解压到解压放到工程目录下新建的include文件夹。 
 - png++ (http://www.nongnu.org/pngpp/)  （png++ is a header only, library, which means you don't need to build anything）这里下载了png++ 0.2.9版本。解压放到工程目录下include文件夹。
 - zlib (https://sourceforge.net/projects/libpng/files/zlib/1.2.11/) (libpng需要)  解压放到工程目录下include文件夹。
   
下面对libpng进行build:
 +  文本编辑器打开`include\lpng1632\projects\vstudio\zlib.props`，修改其中的ZLib文件夹名为下载解压的zlib文件名： `<ZLibSrcDir>..\..\..\..\zlib-1.2.11</ZLibSrcDir>`
 + 打开include\lpng1632\projects\vstudio\vstudio.sln. 这里使用了x64平台（与demo测试工程应保持一致）。将解决方案中的libpng工程设为起始工程，build (debug下或release下， 这里用的debug下). 成功后，在`include\lpng1632\projects\vstudio\x64\Debug` 中发现生成了 `libpng16.lib`, `zlib.lib`以及`libpng16.dll`。

### VS2015 配置
 + 包含libviso2->src的头文件和源文件
 + 在VC++ Directories->Include Directories 中添加 `工程根目录\include` 目录
 + 在VC++ Directories->Include Directories 中添加 `工程根目录\include\lpng1632` 目录
 + 在VC++ Directories->Library Directories 中添加 `工程根目录\include\lpng1632\projects\vstudio\x64\Debug` 目录
 + 在Linker->Input->Additional Dependencies中添加 `libpng16.lib`和`zlib.lib` 
 + 将libpng16.dll放到工程目录下。

### 一些错误处理
 + 比如 `error C4996: 'sprintf': This function or variable may be unsafe.` 处理：在demo.cpp 和 matrix.cpp 文件起始位置添加`#define _CRT_SECURE_NO_WARNINGS`.
 + 在error.hpp中错误: identifier "strerror_r" is undefined. 处理：在error.hpp起始部分将 `#ifdef __STDC_LIB_EXT1__` 修改为 `#if defined(__STDC_LIB_EXT1__) || defined(_WIN32)`
