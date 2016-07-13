title: "build your own android"
date: 2011-10-29
---

这里将简要描述如何建立你本地的工作环境，如何使用Repo来获取Android源码，以及如何在你的机子上编译源码。你需要在Linux或者Mac OS下完成下面操作，Windows暂不支持。完整教程参考：<http://source.android.com/source/index.html>***<br>***

<!-- more --> 

*   ***初始化编译环境***

注意：源码将占用2.6G+空间，你最好有10G以上的剩余磁盘空间。

- 搭建Linux下的源码环境

通常你需要下面这些：

> *   Python2.4–2.7
> *   JDK 6 or 7
> *   Git 1.5.4 or newer
> *   (optional)Valgrind, a tool that will help you find memory leaks,stack corruption,array bounds overflow,etc.

- 安装与配置jdk，不再赘述。

- 安装必要的packages

> $ sudo apt-get install git-core gnupg flex bison gperf build-essential<br>zip curl zlib1g-dev libc6-dev lib32ncurses5-dev ia32-libs<br>x11proto-core-dev libx11-dev lib32readline5-dev lib32z-dev<br>libgl1-mesa-dev g++-multilib mingw32 tofrodos python-markdown<br>libxml2-utils

*   ***下载android源码***

- 安装Repo

按以下步骤安装，初始化配置Repo

在你的home目录下，创建一个bin/目录，同时加入PATH环境变量

> $ mkdir ~/bin<br>$ PATH=~/bin:$PATH

下载repo脚本，修改权限

> $ curl https://dl-ssl.google.com/dl/googlesource/git-repo/repo > ~/bin/repo<br>$ chmod a+x ~/bin/repo

- 初始化一个Repo client

在合适的目录下，创建一个工作目录

> $ mkdir SourceCode<br>$ cd SourceCode

运行repo init以获取最新版本的Repo

> $ repo init -u https://android.googlesource.com/platform/manifest

To check out -u brand other than “master”, specifyit with -b

> $ repo init -u https://android.googlesource.com/platform/manifest -b android-2.3.7_rl

再次过程中，会要求输入你的name和email。初始化完成后的最后一行将提醒你的repo已经被初始化到你的工作目录。这里在SourceCode/目录下会生成一个.repo文件夹（可能是隐藏的，注意查看）会保存你的配置信息。

- 获取android源码

将源码下载到你的工作目录（SourceCode/），运行

> repo sync

获取源码，将花费一两个小时或者更长。2.3的下来共2.8G左右。你可以先去上课，或是午休一下。源码下载完毕后继续。

*   ***编译android源码***

- 初始化

使用envsetup.sh初始化你的系统环境，操作如下：

> $ source build/envsetup.sh

或者

> $ . build/envsetup.sh

- 选择一个Target

> $ lunch full-eng

上面的这个命令指对emulator的一个完整编译，保持调试开启。

如果lunch不带参数的话，会提示从几个选项里选择一个。

- 编译源码<br>GNU make可以使用-jN参数

> $ make -j4

这一步也比较耗时，中间可能会爆出一些错误，而已官网或Google解决。等待编译结束，皆可以下一步了。

- 运行！

你可以把编译结果泡在一个模拟器上。也可以把它刷到一台具体设备上。请确定已经用lunch选择了build target

emulator在编译源码时，已经添加到PATH变量，做一直接运行

> $ emulator

[![emulator][2]][2]

***<br>***

 []: http://czzz.org/wp-content/uploads/2011/10/screenshot81.png
