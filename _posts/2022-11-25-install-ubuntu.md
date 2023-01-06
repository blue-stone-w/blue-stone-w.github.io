---
layout: post
title:  "ubuntu 安装及配置"
date:   2022-11-20 08:30:00 +0800
categories: [Tech]
excerpt: 安装Ubuntu 18和16的系统，然后添加常用工具。
tags:
  - CN
  - Ubuntu 
  - Linux
  - 
---

这篇文章整理了整个安装了Ubuntu 16、18、20及相关软件的过程。软件不是必备的，但提供了很多便利。有些步骤在网上已经有很好的教程，我就不再详写这些步骤，直接放出我认为好用的教程链接，并附加注意事项。建议开始每个步骤前先看注意事项。

## 一、准备工作
准备工作有制作安装盘和为新系统腾出空间两个部分，可参考[Ubuntu18.04安装教程-1.2](https://blog.csdn.net/baidu_36602427/article/details/86548203)

##### 注意事项
 1. 制作安装盘的过程会删除该u盘中原来的所有数据

 2. 腾出空间的操作，指的是删除卷，将空间完全释放出来。新建一个空卷会导致无法安装新系统。

 3. win的磁盘的文件系统类型为NTFS；如果你之前一直使用的win，那么移动硬盘的文件系统类型应该也是NTFS，这一点你可以自行确认；ubuntu的文件格式是ext3和ext4。

    ubuntu系统可以访问NTFS的磁盘，但win不能访问ext的磁盘。意味着你可以将文件放在NTFS磁盘中，使文件可以在两个系统中传递和共用，并且减少ubuntu系统的空间需求。

## 二、安装过程
安装过程可参考[此处](https://blog.csdn.net/baidu_36602427/article/details/86548203)


##### 注意事项

* boot选项异常  <br />
  如果之前曾安装过ubuntu但没有正确删除，例如直接在Windows下格式化了Ubuntu对应的盘。那么在boot选项下仍然会有Ubuntu选项。可以选择忽略原来的Ubuntu，选择自己的U盘。删除原来的Ubuntu boot选项的方法可以参考[“删除Win10 EFI启动分区中的Ubuntu启动引导项 ”](https://blog.csdn.net/Spacegene/article/details/86659349)的 2.2

* 修复18的花屏  <br />
  如果在安装时出现花屏的情况，参照[这个方法](https://zhuanlan.zhihu.com/p/439088148)解决

>提示: /etc/default/grub, nomodeset, sudo update-grub

* when choose option of intallation, I can see "multiple opteration systems". that means that I have installed two OS at least. choose the first option, alongsise with them.

* 安装类型和空间分配  
  如果选择第三项，自己分配空间。对于新手，我的建议是选择第一个，在安装过程中会自行分配各个空间和挂载点。如果你遇到的界面要求选择一个挂载点，选择准备好的空间后把`/`挂在上去。(对于`安装启动器的设备`，可参考[三.10](https://blue-stone-w.github.io/blog/ros-ubuntu-qa) <br /><br />

* 用户名和密码  
  从外界访问时，首先找到按照`计算机名`找到计算机，然后在该计算机内按照`用户名/username`找到用户。第一行的`姓名/name`是让人看的，之后系统会以这个名字称呼你。密码长度自己把握。因为在使用中经常需要输入密码，因此我倾向于使用短密码。在完成安装后也可以按照[这个方法](https://blog.csdn.net/garvie/article/details/55113691)更改为短密码。



## 三、安装常用工具和设置
##### 1. 火狐浏览器
显示书签；缩放zoom设为`1.2~1.5`。可从`html`文件中导入书签。打开的视频默认是静音的，如有需要可打开声音。
###### 2. 显示网速
按照[这个方法](https://www.yisu.com/ask/6880.html)安装和使用sysmonitior工具。也可以添加显示cpu温度的选项，温度过高时及时降功率，延长器件寿命。可以使用如下代码输出网速等信息。
```
Net: $(speed(net.down))   $(speed(net.up))   CPU: $(percent(cpu.inuse))     Mem $(percent(size(mem.user)/size(mem.total)))
```
对于Ubuntu20
###### 3. 汉字
需要安装fcitx，下载[搜狗输入法](https://shurufa.sogou.com/linux)`sogou`，具体安装方法可在[官网](https://shurufa.sogou.com/linux/guide)找到。其中20有个单独的安装介绍。安装中有一个步骤是卸载系统自带的`ibus`，这个卸载不是必要的。
###### 4. gnome
把gnome的`factor`设置为`1.25`。
###### 5. 自动登录
如果是自己的电脑，没必要每次都输入密码，可设置为自动登录。当密码很简短时，也没必要通过频繁输入密码来记住密码。
###### 6. Chrome
缩放zoom设置为`1.25~1.5`。
###### 7. 设置亮度和锁屏睡眠
###### 8. 设置代理(未经验证)
方法一  
输入以下命令
```
gedit ~/.bashrc
```
在文件末尾添加如下语句
```
export http_proxy="socks5://127.0.0.1:1088"
export https_proxy="socks5://127.0.0.1:1088"
export http_proxy="http://localhost:8888"
export https_proxy="http://localhost:8888"
```
方法二  
具体教程点击[此处](https://www.serverlab.ca/tutorials/linux/administration-linux/how-to-set-the-proxy-for-apt-for-ubuntu-18-04/)。
可使用以下命令
```
sudo touch /etc/apt/apt.conf.d/proxy.conf
sudo gedit /etc/apt/apt.conf.d/proxy.conf
```
在文件末尾添加如下语句
```
Acquire::http::proxy "http://127.0.0.1:8888/";
Acquire::ftp::proxy "ftp://127.0.0.1:8888/";
Acquire::https::proxy "https://127.0.0.1:8888/";
```

后面是根据我的实际需要安装的软件。如果开发方向不同，到此就可以结束了。

## 四、安装工作软件
#### 1. 
#### 2. ROS kinetic(16)&melodic(18)
这两个版本的ros安装方法相似，安装方法参考[此处](https://www.guyuehome.com/10082)
```C++
sudo rosdep init //这两个执行错误可以不用管，不是必须执行的<br />
rosdep update
```
**提示事项**<br />
*过程中可能需要装python，按照提示信息安装即可。<br />
*如果提示没有海龟包，自行[下载](https://blog.csdn.net/qqliuzhitong/article/details/114305249)即可。
#### 4. ROS2 
[安装教程](https://zhuanlan.zhihu.com/p/149187701)(其中有一处错误，melodic对应Bionic18.04，而Foxy对应Focal20.04)

#### 5. vscode
* 推荐添加的插件：`C/C++`(选择首个)；`Markdown all in one`；`CMake`；

* 如果安装的是`Ubuntu software`中自带的`vscode`，可能无法输入汉语。卸载并从官网重新下载安装即可。

* 添加垂直标尺：文件–>首选项–>设置->搜索`editor.rulers` -->更改设置`editor.rulers: [80,120]`，或者其他期望的值，即可。

#### 7. qt
qt5.15及之后的版本是在线安装，之前的版本是离线安装。在线安装可点击[此处](https://www.qt.io/download-qt-installer?utm_referrer=https%3A%2F%2Fwww.qt.io%2Fdownload-open-source%3Futm_referrer%3Dhttps%253A%252F%252Fwww.qt.io%252Fdownload)下载`online-installer`。离线安装可点击[此处](https://download.qt.io/)下载安装包。

#### 8. PCL
[Ubuntu18.04下安装PCL1.9.1](https://blog.csdn.net/yingmai7741/article/details/86531850)

[Ubuntu16.04下安装PCL1.7并测试](https://blog.csdn.net/HHT0506/article/details/104439748#commentBox)

#### 10. ceres-solver
18.04[安装依赖和安装检验](https://blog.csdn.net/YMWM_/article/details/101601345)（检验时注意文件夹路径）
 <br />
下载包： https://blog.csdn.net/p942005405/article/details/100654730 <br />
make -j4： https://zhuanlan.zhihu.com/p/110160075 <br />

16.04安装点击[此处](https://www.cnblogs.com/ambition921009/p/10551885.html)
<br />
在ubuntu16.04下安装时.遇到这个错误：
```
could not find a configuration file for package "Eigen3" that is compatible with requested version "3.3".
```
解决方法在[此处](https://zhuanlan.zhihu.com/p/151675712)<br /><br />
检验方法同上, but note the localation and path of problem.txt, should use my problem.txt

#### 11. Eigen
安装教程点击[此处](https://zhuanlan.zhihu.com/p/110160075)
version id 3.2.92, may be old for ceres.

#### 13. opencv
注意cmake是否需要安装。<br />
全部步骤点击[此处](https://zhuanlan.zhihu.com/p/76737748) ；<br />
上述过程1-6以及测试使用[此处](https://zhuanlan.zhihu.com/p/52513112)的方法；<br />
配置和测试使用[此处](https://zhuanlan.zhihu.com/p/110160075)的方法.

#### 14. Docker
18.04[设置存储库及安装检验](https://www.jianshu.com/p/83483c35bfcd)
其中安装步骤的5-6-7使用[此处](https://zhuanlan.zhihu.com/p/57413820)的方法；
安装检验使用[此处](https://www.myfreax.com/how-to-install-and-use-docker-on-ubuntu-18-04)的方法；

16.04的安装参照[这个链接](https://zhuanlan.zhihu.com/p/84000436)

ceres报错：Eigen3版本和ceres版本存在冲突，解决方法在[此处](https://zhuanlan.zhihu.com/p/149775218)。
 (in fact it's about "ceres报错：Eigen3版本和ceres版本冲突问题", it shouldn't be here.)
