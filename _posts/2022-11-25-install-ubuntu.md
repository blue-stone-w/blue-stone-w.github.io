---
layout: post
title:  "ubuntu 18 安装及配置"
date:   2022-11-20 08:30:00 +0800
categories: [Tech]
excerpt: 安装Ubuntu 18的系统，然后添加常用工具。
tags:
  - CN
  - Ubuntu 
  - Linux
  - 
---

这篇文章整理了整个安装了Ubuntu 18及相关软件的过程。软件不是必备的，但提供了很多便利。有些步骤在网上已经有很好的教程，我就不再详写这些步骤，直接放出我认为好用的教程链接，并附加注意事项。建议开始每个步骤前先看注意事项。

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

1.boot选项异常  <br />
如果之前曾安装过ubuntu但没有正确删除，例如直接在Windows下格式化了Ubuntu对应的盘。那么在boot选项下仍然会有Ubuntu选项。可以选择忽略原来的Ubuntu，选择自己的U盘。删除原来的Ubuntu boot选项的方法可以参考[“删除Win10 EFI启动分区中的Ubuntu启动引导项 ”](https://blog.csdn.net/Spacegene/article/details/86659349)的 2.2

2.修复花屏  <br />
如果在安装时出现花屏的情况，参照[这个方法](https://zhuanlan.zhihu.com/p/439088148)解决

3.安装类型  
如果是新手的话，我的建议是选择第一个，在安装过程中会自行分配各个空间和挂载点。<br /><br />


4.用户名和密码  
从外界访问时，首先找到按照`计算机名`找到计算机，然后在该计算机内按照`用户名/username`找到用户。第一行的`姓名/name`是让人看的，之后系统会以这个名字称呼你。密码长度自己把握。因为在使用中经常需要输入密码，因此我倾向于使用短密码。在完成安装后也可以按照[这个方法](https://blog.csdn.net/garvie/article/details/55113691)更改为短密码。



## 三、安装常用工具
#### 1. 系统工具
###### (1) 显示网速
按照[这个方法](https://www.yisu.com/ask/6880.html)安装和使用sysmonitior工具。也可以添加显示cpu温度的选项，温度过高时及时降功率，延长器件寿命。
###### (2)

后面是根据我的实际需要安装的软件。如果开发方向不同，到此就可以结束了。

## 四、安装工作软件
#### 1. 安装chrome
#### 2. 安装ROS
安装方法参考[此处](https://www.guyuehome.com/10082)
#### 3. 安装vscode
可以添加的插件：
#### 4. 安装qt
qt5.15及之后的版本是在线安装，之前的版本是离线安装。在线安装可点击[此处](https://www.qt.io/download-qt-installer?utm_referrer=https%3A%2F%2Fwww.qt.io%2Fdownload-open-source%3Futm_referrer%3Dhttps%253A%252F%252Fwww.qt.io%252Fdownload)下载`online-installer`。离线安装可点击[此处](https://download.qt.io/)下载安装包。
