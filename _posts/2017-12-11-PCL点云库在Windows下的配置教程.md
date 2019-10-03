---
layout: post
title: PCL点云库在Windows下的配置教程
date: 2017-12-11
categories: blog
tags: [PCL点云库,Windows]
description: 
---

#需要下载的东西

**VS2015企业版**
```
ed2k://|file|cn_visual_studio_enterprise_2015_x86_x64_dvd_6846222.iso|4172560384|E558149A422E9FBF7D1D37FB0A2F1F53|/
```
VS2015企业版的激活码可以用这个
```
2XNFG-KFHR8-QV3CP-3W6HT-683CH
```

或者是
**VS2015社区版**
```
ed2k://|file|cn_visual_studio_community_2015_with_update_3_x86_x64_dvd_8923246.iso|7633565696|605D19028916D09E1BFA2DABBD266FE9|/
```




**[PCL 1.8.0 All-in-one Installer MSVC2015 x64](https://1drv.ms/u/s!ApoY_0Ymu57sg5QiqO2sR1k-zcSi_w)**

**[PCL 1.8.0 PDB MSVC2015 x64 (symbol files)](https://1drv.ms/u/s!ApoY_0Ymu57sg5Qhnnvcil_PJO-hyg)**

                       [文件来自Tsukasa Sugiura的博客](http://unanancyowen.com/en/pcl18/)





#下载完之后进行安装，PCL安装在默认位置即可，不然props 文件需要修改

在安装`PCL 一键安装包`时，勾选上`Add PCL to system PATH for all users`。安装过程中，会弹出OpenNI 的安装程序，更改OpenNI 的安装路径为你安装的`PCL`路径下的`3rdParty`文件夹下的`OpenNI2`文件夹中

拷贝与你安装`PCL 版本对应的PDB 压缩包`解压后的`PDB 文件`，到你`PCL`安装路径下的`bin`文件夹，就是里面有`pcl`开头的`dll`的那个文件夹

系统Path 设置添加如下:
```
C:\Program Files\PCL 1.8.0\bin;C:\Program Files\PCL 1.8.0\3rdParty\FLANN\bin;C:\Program Files\PCL 1.8.0\3rdParty\Qhull\bin;C:\Program Files\PCL 1.8.0\3rdParty\OpenNI2\Tools;C:\Program Files\PCL 1.8.0\3rdParty\VTK\bin
```
```
%PCL_ROOT%\bin
```
```
%OPENNI2_REDIST64%
```
然后保存重启电脑





#打开VS 新建一个`win32 控制台程序`






调整为`Debug x64`



在解决方案管理器里找到你新建的项目，右键点属性，弹出属性页窗口


点击配置属性下的调试，右边有个环境项，把值设置为
```
PATH=$(PCL_ROOT)bin;$(PCL_ROOT)3rdPartyFLANNbin;$(PCL_ROOT)3rdPartyVTKbin;$(PCL_ROOT)Qhullbin;$(PCL_ROOT)3rdPartyOpenNI2Tools;$(PATH)
```


在属性管理器里点击项目，展开子项


如果安装的是`64 位`，点击选中开头为`Debug`的子项里右键，点击添加现有属性表，然后找到你下载的文件夹里的`PCLDebug.props`，添加上。点击选中开头为Release 的子项里右键，点击添加现有属性表，然后找到你下载的文件夹里的`PCLRelease.props`，添加上。
注意在这里，64 位的会有2 个开头为`Debug`的子项，都要添加上`PCLDebug.props`，并且2个开头为`Release`的子项，也要添加上`PCLRelease.props`。（如果安装的是`32 位`，点击选中开头为`Debug`的子项里右键，点击添加现有属性表，然后
找到你下载的文件夹里的`PCLDebug.props`，添加上。点击选中开头为`Release`的子项里右
键，点击添加现有属性表，然后找到你下载的文件夹里的`PCLRelease.props`，添加上。）

#测试

```
#include <pcl/visualization/cloud_viewer.h>  
#include <iostream>//标准C++库中的输入输出类相关头文件。  
#include <pcl/io/io.h>  
#include <pcl/io/pcd_io.h>//pcd 读写类相关的头文件。  
#include <pcl/io/ply_io.h>  
#include <pcl/point_types.h> //PCL中支持的点类型头文件。  
int user_data;

void viewerOneOff(pcl::visualization::PCLVisualizer& viewer)
{
    viewer.setBackgroundColor(1.0, 0.5, 1.0);  //设置背景颜色  

}


int main()
{
    pcl::PointCloud<pcl::PointXYZRGBA>::Ptr cloud(new pcl::PointCloud<pcl::PointXYZRGBA>);


    pcl::io::loadPCDFile("test.pcd", *cloud);
    pcl::visualization::CloudViewer viewer("Cloud Viewer");    //创建viewer对象  

    viewer.showCloud(cloud);

    viewer.runOnVisualizationThreadOnce(viewerOneOff);

    system("pause");

    return 0;

}
```

显示如下










#问题排除：


**VS2015无法新建项目错误及解决方法：无法打开“……/VC?VCWizards/default.vcxproj”因为此版本的应用程序不支持其项目类型（.vcxproj）**

解决办法：打开VS2015安装程序，勾选Update，安装


**提示找不到XX.dll等**


解决办法：配置dll有四种方法，第一种，把dll拷贝到system32里去，第二种，把dll拷贝到编译出来的程序的文件中，第三种，在系统的path中添加dll的路径，这种方法设置后需要重启电脑。本文选用第四种方法，直接配置到项目中。具体方法如下：
在“解决方案资源管理器”里选中项目右键点“属性”，然后选“调试”设置环境的变量值，规则如下：
```
PATH=;$(PATH)
```
在等号后添加所有PCL及第三方库的dll路径以英文的“;”隔开，最后加上一个`$(PATH)`即可。例如我设置的如下：
```
PATH=$(PCL_ROOT)\bin;$(PCL_ROOT)\3rdParty\FLANN\bin;$(PCL_ROOT)\3rdParty\VTK\bin;$(PCL_ROOT)\Qhull\bin;$(PCL_ROOT)\3rdParty\OpenNI2\Tools;$(PATH)
```
大家根据自己的安装路径，灵活变通。

**提示缺少xxx.h等头文件**

解决办法：这个问题是因为在设置include，即设置附加包含目录，路径不对或缺少导致的。根据提示，检查下项目的属性，C/C++->常规->附加包含目录里，找到对应错误的库的头文件路径，修改或添加其所在文件夹的路径。

**提示无法解析的外部符号错误**

解决方法：这个问题比较难解决，总结起来有四大原因：安装包版本不对、项目的位数和PCL库的位数不对、缺少对应的lib、函数只有声明，没有定义。第一个找对安装包即可，第二个修改项目位数和PCL库的位数一致即可，第三个要根据经验去添加上lib文件，第四个的话，在预处理器里添加PCL_NO_PRECOMPILE即可，当然上面只是经验之谈，要根据实际情况做出调整。

**提示无法打开xxx.lib**

解决方法：一是检查你PCL安装路径下有没有这个lib，如果没有，那就在附加依赖项里把这个lib删除，如果有，则进行第二个检查。二是检查下项目的属性，链接器->附加包含目录，有没有添加上这个lib所在文件夹的路径。

**出现错误代码为C4996的错误**

解决办法：这个错误一般是通过在项目属性的C/C++->预处理器->预处理定义里，添加对应的定义来解决。这里给出一个“一劳永逸”的方法，在C/C++->常规->SDL检查里的值设置为“否 (/sdl-)”。

**出现无法定位于动态链接库的错误**

解决方法：查看下系统环境变量PATH里是否有多个不同版本PCL及相关库的路径，去掉不使用的路径。

**提示无法打开stdafx.h**

解决办法：默认新建的控制台程序，包含mian函数的cpp里会包含这个头文件，而在拷贝代码时，没注意，把这个包含头文件删除了，只要在包含头文件里重新添加上#include “stdafx.h”就可以了，注意要加在所有include的最上面。

**提示 Worning！ PATH too long installer unable to modify PATH！**
解决办法：修改系统环境变量里的PATH变量，先将PATH变量的值拷贝保存，然后亲空这个变量的值，再安装PCL，安装完成后，在先前保存的变量值拷贝回去。