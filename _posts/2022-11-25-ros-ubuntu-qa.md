---
layout: post
title:  "ROS和Ubuntu使用中的QA"
date:   2022-11-20 08:30:00 +0800
categories: [Tech]
excerpt: 这篇文章整理了ROS和Ubuntu使用过程中遇到的问题以及对应的解决方案。
tags:
  - CN
  - Ubuntu 
  - ROS
  - QA
  - C++
---

这篇文章整理了ROS和Ubuntu使用过程中遇到的问题以及对应的解决方案。这些方案并不总是能解决问题，只是提供一个排查问题的方向。

## 一、ROS1相关

覆盖范围包括ROS生态内的软件，比如 rqt，gazebo，rviz，yaml等等。

##### 1

运行以下命令时：

```C++
rosdep update
```

出现错误提示：

```C++
ERROR: unable to process source [https://raw.githubusercontent.com/ros/rosdistro/master/rosdep/osx-homebrew.yaml]:<urlopen error [Errno -2] Name or service not known> (https://raw.githubusercontent.com/ros/rosdistro/master/rosdep/osx-homebrew.yaml)
```

可以尝试以下命令：

```C++
sudo apt-get update && sudo apt-get install python-rosdep
```

然后再重复之前的输入——rosdep update——即可。最好不要以root用户运行”rosdep update“。

##### 2

运行以下命令  

```C++
rqt_plot 
```

错误提示

```C++
qt_gui_main() found no plugin matching "rqt_plot.plot.Plot" 
```

输入以下命令解决

```C++
rqt_plot --force-discover 
```

##### 3

错误提示如下

```C++
Error: package 'rqt_graph' not found
```

输入以下命令解决

```C++
sudo apt-get install ros-melodic-rqt-graph
```

##### 4

错误提示如下

```C++
/usr/bin/rosrun: line 56: rospack: command not found 
```

输入以下命令获取root权限后解决

```C++
sudo su 
```

对于部分需要root权限的命令，使用非root时会提示命令不存在。如果该命令本该以非root用户安装，但却以root用户安装，可能出现该问题。

##### 5

运行以下命令  

```C++
roslaunch mws turtlemimic.launch  
```

错误提示如下

```C++
RLException: [turtlemimic.launch] is neither a launch file in package [mws] nor is [mws] a launch file name. The traceback for the exception was written to the log file 
```

原因分析：编译后没有执行`source`，或者包名文件名拼写错误

输入以下命令解决

```C++
source devel/setup.bash
```

##### 6

运行以下命令  

```C++
rosed roscpp Logger.msg
```

错误提示如下

```C++
rosed: command not found
```

尝试输入以下命令之一来解决  

```C++
sudo apt-get install ros-melodic-rosbash
```

或者

```C++
source /opt/ros/melodic/setup.bash
```

##### 7

错误提示如下

```C++
[ERROR] [1619511862.358210519]: [registerPublisher] Failed to contact master at [localhost:11311].  Retrying...
```

原因分析：必须先启动roscore，然后才能运行其它ros节点

输入以下命令解决

```C++
roscore
```

##### 8

运行以下命令

```C++
rosservice call /optimize_map
```

错误提示如下

```C++
ERROR: Unable to load type [lidar_localization/optimizeMap]. Have you typed 'make' in [lidar_localization]?
```

输入以下命令解决

```C++
source ./devel/setup.bash
```

##### 9. exit code 134

gazebo 在运行时闪退，错误提示如下

```C++
[gazebo-2] process has died [pid 2074, exit code 134, cmd /opt/ros/kinetic/lib/gazebo_ros/gzserver -e ode /opt/ros/kinetic/share/turtlebot_gazebo/worlds/playground.world __name:=gazebo __log:=/home/kim/.ros/log/fa7d7a88-3b3a-11ea-8115-54bf64a35529/gazebo-2.log]
```

查看发现gazebo的版本是 7.0.0。更新gazebo的版本后，问题解决

##### 10

错误程序如下

```C++
ros::init(argc, argv, " listener");
```

编译错误提示如下: 当前的首字母为无效字符

```C++
terminate called after throwing an instance of 'ros::InvalidNameException'
  what():  Character [ ] is not valid as the first character in Graph Resource Name [ listener].  Valid characters are a-z, A-Z, / and in some cases ~.
Aborted (core dumped)
```

解决方法：改为以下程序

```C++
ros::init(argc, argv, "listener");// first character in "" can't be space(空格)
```

##### 11

移动工作空间后，执行catkin_make

```C++
catkin_make
```

编译错误提示如下

```C++
The source directory "/home/nnnn/Documents/mr/03/src" does not exist.
Specify --help for usage, or press the help button on the CMake GUI.
Makefile:1438: recipe for target 'cmake_check_build_system' failed
make: *** [cmake_check_build_system] Error 1
```

尝试输入以下方法之一解决

```C++
catkin_make -DCATKIN_WHITELIST_PACKAGES="package1;package2" //in the root of your workspace
```

或者

```C++
catkin_make -DCATKIN_WHITELIST_PACKAGES=""
```

或者

```C++
cmake ../src -DCMAKE_INSTALL_PREFIX=../install -DCATKIN_DEVEL_PREFIX=../devel //指定src文件夹，命令在bulid文件夹中
make
```

或者

```C++
delete folder devel and build and re-compile；// 删除编译产生的所有文件并重新编译
```

##### 12

运行以下命令

```C++
catkin_make
```

错误提示如下

```C++
Error in cmake code at /home/nnnn/Documents/my/li/src/frame/CMakeLists.txt:239: Parse error.  Function missing ending ")". End of file reached.
```

解决方案：在文件中检查一下括号是否成对。虽然提示出错的地方在文件末尾，但实际错误处可能在cmakelists文件的任何地方。

##### 13

错误程序如下

```C++
ros::init(argc, argv, "scanRegistration");
```

编译错误提示如下

```C++
argc’ was not declared in this scope
```

原因分析：ros::init的参数应与main函数一致。

##### 14

运行以下命令

```C++
catkin_make
```

错误提示如下

```C++
/usr/local/include/ceres/internal/integer_sequence_algorithm.h:64:16: error: ‘integer_sequence’ is not a member of ‘std’
 struct SumImpl<std::integer_sequence<T, N, Ns...>> 
```

解决方案如下

```C++
replace " set(CMAKE_CXX_FLAGS "-std=c++11") " with " set(CMAKE_CXX_STANDARD 14) " % In the new version of cmake, the latter method is used to set C + + standard %
```

##### 15

注意区分 `ROS::ok()` 和 `ROS::OK()`

##### 16

程序运行中崩溃，错误提示如下

```C++
terminate called after throwing an instance of 'YAML::InvalidNode'
```

原因分析：yaml文件中的变量名和程序中的变量名不一致

##### 17

if remove cmake, when command roscore, there will be something wrong with poython-roslaunch. you can also meet it when you make ros but didn't source it.

##### 18

异常代码如下：

```C++
while(ros::ok()) { return;}  // ros只循环了一次，没有继续循环调用
```

解决方案如下:

```C++
while(ros::ok()) { continue;}
```

##### 19

提示如下：

```C++
Warning: TF_OLD_DATA ignoring data from the past for frame base_link at time 0 according to authority unknown_publisher Possible reasons are list at
```

原因分析：TF没有设置更新频率，使得tf一直没有更新

##### 20

异常情况：运行ros节点，只开启了部分线程

原因分析：`.join()` 函数应该在

```C++
ros::MultiThreadSpinner spinner(9); spinner.spin(); 
```

之后,因为`.join()`函数表示,在该线程结束后再继续执行该函数后的语句。如果前面的线程是一个订阅和回调的循环，那么就无法进入后面的线程

##### 21

异常代码如下：

```C++
tf::TransformBroadcaster br; br.sendTransform(trans_map_to_truck);
```

错误提示如下

```C++
For frame [map_link]: No transform to fixed frame [base_link].  TF error: [Lookup would require extrapolation at time 1652258510.336753588, but only time 1652258489.731681437 is in the buffer, when looking up transform from frame [map_link] to frame [base_link]]
```

解决方案如下:

```C++
static tf::TransformBroadcaster br; br.sendTransform(trans_map_to_truck);
```

##### 22
安装ros时出现如下错误
```C++
Failed to connect to raw.githubusercontent.com
```
可参照[这个方案](https://www.guyuehome.com/37844)解决。

## 二、ROS2相关

##### 1.
出现如下错误
```C++
unknown command line flags: -r // 需要确认
unknown command line flags: -params-files
unknown command line flags: -r 
```
在搜索时发现，结果几乎都是`tensorflow`和`cartographer`相关的。经过高人指点，发现出错的代码是下面这段
```C++
google::ParseCommandLineFlags(&argc, &argv, true);
```
解决方案就是把这段代码注释掉，即
```C++
// google::ParseCommandLineFlags(&argc, &argv, true);
```

##### 2 
我在开启代理的情况下执行命令`curl http://repo.ros2.org/repos.key | sudo apt-key add -`，出现了如下错误
![pic1](/assets/images/posts/ros-ubuntu-qa/02-01.png)
```C++
url http://repo.ros2.org/repos.key | sudo apt-key add -
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
curl: (5) Could not resolve proxy: http
gpg: no valid OpenPGP data found.
```
解决方法是，关闭当前终端和代理，然后重新开启终端执行如下命令。
```C++
sudo apt update && sudo apt install curl gnupg2 lsb-release
curl http://repo.ros2.org/repos.key | sudo apt-key add -
```

## 三、C++相关

覆盖范围包括编译、CMake、某些库使用中的问题，比如Eigen，PCL等等。

##### 1.exit code -6

原因分析：内存访问错误，可能的原因有访问不存在的变量。

##### 2

```C++
collect2: error: ld returned 1 exit status
undefined reference to symbol '_ZN3tbb8internal25deallocate_via_handler_v3EPv'
```

在代码的前面部分出现了错误，导致此处引用错误，错误状态为1;导入库出错也可能导致这一结果。

##### 3.exit code -11

具体的错误提示如下

```C++
[loop_closing_node-5] process has died [pid 29437, exit code -11, cmd /home/nnnn/Documents/my/li/devel/lib/frame/loop_closing_node __name:=loop_closing_node __log:=/home/nnnn/.ros/log/af90aa56-dcc2-11eb-9108-024258b94a05/loop_closing_node-5.log].
```

原因分析：新建向量变量，没有指定大小，直接用下标引用; 指针没有分配空间; 访问opencv的矩阵时越界

解决方案：使用向量前指定向量大小；使用更大的矩阵或者不要越界访问。

##### 4

`std::bad_alloc`是 `operator new`不能满足内存分配请求时抛出的异常类型

##### 5

编译时出现如下错误

```C++
error: stray '\357' in program
```

原因分析：猜测，错误的原理是出现了不能识别的字符，例如了一个汉语的分号代替了英语的分号时

##### 6

编译时出现如下错误

```C++
error: cannot resolve overloaded function ‘getFitnessScore’ based on conversion to type ‘float’
```

原因分析：函数A调用函数B后，B的返回值为一个仅在函数B中有效的参数C，B为float型

##### 7

编译时出现如下错误

```C++
error: no match for call to ‘(Eigen::Matrix4f {aka Eigen::Matrix<float, 4, 4>}) ()’
```

原因分析：对于类型为Eigen::Matrix4f的东西，没有相匹配的可调用的东西。 <font color=Blue>？？？???</font>

##### 8

编译时出现如下错误

```C++
error: invalid new-expression of abstract class type ‘frame::G2oGraphOptimizer’
```

原因分析：类中存在需要实现的成员函数；基类中的虚函数，在子类中需要重载，但没有重载。

##### 9

编译时出现如下错误

```C++
error: “marked ‘override’, but does not override”
```

原因分析：重载虚函数时，函数的参数类型和名称需要相同，否则会出现上述错误

##### 10

编译时出现如下错误

```C++
uses undefined class
```

原因分析：main中引用了头文件A和B，B中引用了A。，会出现上述错误。

解决方案：删除main中A的引用

##### 11

编译时出现如下错误

```C++
error: usleep is not declared in this scope 
```

解决方案：添加如下代码到出错的文件

```C++
#include <unistd.h>
#include <stdio.h>
#include <stdlib.h>
```

##### 12. ??

编译时出现如下错误

```C++
cannot open shared object file: no such file or directory
```

解决方案：设置下环境变量可能会解决问题

##### 13

错误程序如下

```C++
Eigen::Vector3f pos(3,0);
```

编译时出现如下错误

```C++
expected identifier before numeric constant、
```

原因分析：重复定义了向量的维度

##### 14

编译时出现如下错误

```C++
expected unqualified-id before ‘if’ ; expected a declaration ; this declaration has no storage class or type specifier
```

原因分析：`if`语句应该放在函数内

##### 15

编译时出现如下警告

```C++
warning: ignoring #pragma omp parallel
```

原因分析：没有正确配置OpenMP(例如创建了相应的cmake文件来编译，但没有在CMakeLists文件中include该cmake文件)，直接在程序中使用了omp

##### 16

编译时出现如下错误

```C++
error: static assertion failed: YOU_MIXED_MATRICES_OF_DIFFERENT_SIZES
     #define EIGEN_STATIC_ASSERT(X,MSG) static_assert(X,#MSG)
```

原因分析： eigen定义的变量类型为matrix4d作为函数实参，在调用函数时，使用的形参为matrix3d

解决方案：使实参和形参的数据类型保持一致

##### 17

编译时出现如下错误

```C++
error: static assertion failed: YOU_MIXED_DIFFERENT_NUMERIC_TYPES__YOU_NEED_TO_USE_THE_CAST_METHOD_OF_MATRIXBASE_TO_CAST_NUMERIC_TYPES_EXPLICITLY
     #define EIGEN_STATIC_ASSERT(X,MSG) static_assert(X,#MSG);
```

原因分析：eigen变量赋值时，左右的数据类型不一致

解决方案：更改变量的数据类型，或者，使用强制转换

##### 18

编译时出现如下错误

```C++
/usr/include/boost/smart_ptr/shared_ptr.hpp:648: typename boost::detail::sp_member_access<T>::type boost::shared_ptr<T>::operator->() const [with T = pcl::PointCloud<pcl::PointXYZ>; typename boost::detail::sp_member_access<T>::type = pcl::PointCloud<pcl::PointXYZ>*]: Assertion `px != 0' failed.
```

原因分析如下  

```C++
pcl::PointCloud<pcl::PointXYZ>::Ptr keyPoses3D; // 在使用前没有new
```

解决方案如下

```C++
keyPoses3D.reset(new pcl::PointCloud<pcl::PointXYZ>());
```

##### 19

编译时出现如下错误

```C++
DSO missing from command line
```

原因分析：cmakelists其它正常，但 未加入需要的库(没有add target)

解决方案: 查看是否填写了库的路径以及库的路径是否正确

##### 20

编译时出现如下错误

```C++
invalid use of function
```

原因分析: 在默认构造函数完成后再执行自建的构造函数

解决方案: 除了构造以外的地方，不要调用构造函数

##### 21

```C++
lidarLocatorVec.push_back(LidarLocator(lidarsID[i]));
```

编译错误如下：

```C++
调用拷贝构造函数时提示use of deleted function错误：use of deleted function ‘LidarLocator::LidarLocator(LidarLocator&&)’
```

原因分析：声明移动构造函数证明你的类需要深拷贝，对于包含指针的类，编译器简单的浅拷贝可能导致反复释放或野指针问题。推广至Copy constructor、Move constructor、Copy assignment operator、Move assignment operator、Destructor这五个函数，定义了任何一个都会导致编译器认为你在主动管理资源，原本默认生成的函数可能无法满足需求甚至是错误的故被转换为delete强制用户手动实现，也就是`rule of five`规则。

##### 22

编译时出现如下错误

```C++
main_node.cpp:8: undefined reference to `WheelOdom<double>::WheelOdom()'
```

原因分析: 编译nodecpp时没有指定要链接定义WheelOdom的cpp文件; 模板类的成员函数的声明和定义分开了, 使模板类在实例化时不能使用类的模板，而是使用定义时的模板，这会产生构造和析构函数未定义的效果;

##### 23

错误提示如下:

```C++
空函数体的构造函数提示，赋值时两侧矩阵的行列不相同
```

原因分析：在声明函数时就对变量初始化, 初始化时赋值出现错误

##### 24

编译时出现如下错误

```C++
undefined reference to `LidarLocator::rtk2PointPose(rtk_msgs::Rtk_<std::allocator<void> >)'
```

原因分析: without "classname::" when define a member function

##### 25

编译时出现如下错误

```C++
‘rpy2Quat’ was not declared in this scope
```

原因分析: 编译有顺序, 被调用的代码必须在调用代码之前

##### 26

编译时出现如下错误

```C++
(特殊)大量编译错误; many errors when compilation
```

原因分析: 链接文件时, 如果前一段.o文件有错误, 就会导致大量错误

##### 27

运行崩溃的程序如下

```C++
Eigen::VectorXd odom_edge_noise; odom_edge_noise(0) = 0.1;
```

错误提示如下

```C++
/home/nnnn/Documents/my/li/src/frame/third_party/eigen3/Eigen/src/Core/DenseCoeffsBase.h:413: Eigen::DenseCoeffsBase<Derived, 1>::Scalar& Eigen::DenseCoeffsBase<Derived, 1>::operator()(Eigen::Index) [with Derived = Eigen::Matrix<double, -1, 1>; Eigen::DenseCoeffsBase<Derived, 1>::Scalar = double; Eigen::Index = long int]: Assertion `index >= 0 && index < size()' failed.
```

原因分析：在类的public中定义了一个动态向量，但未定义大小，然后在赋值时出现错误

解决方案如下

```C++
// (在类的构造函数中定义该动态向量的大小)
Eigen::VectorXd odom_edge_noise; odom_edge_noise.resize(6); odom_edge_noise(0) = 0.1;
```

##### 28

运行崩溃的程序如下

```C++
kdtreeLocalMapCorner->nearestKSearch(pointInMap, 5, searchedPointInd, searchedPointDis);
```

错误提示如下

```C++
lio_sam_fusedOptimization: /build/pcl-0nnAvv/pcl-1.7.2/kdtree/include/pcl/kdtree/impl/kdtree_flann.hpp:136: int pcl::KdTreeFLANN<PointT, Dist>::nearestKSearch(const PointT&, int, std::vector<int>&, std::vector<float>&) const [with PointT = pcl::PointXYZ; Dist = flann::L2_Simple<float>]: Assertion `point_representation_->isValid (point) && "Invalid (NaN, Inf) point coordinates given to nearestKSearch!"' failed.
```

原因分析：any of the x,y,z fields of a point are infinite; pointInMap 是个nan点;当存在无效点云的NaNs值作为算法的输入

##### 29

```C++
downSizeFilterSurf.setInputCloud(inLaserCloudSurf); // many points in this cloud
downSizeFilterSurf.filter(outLaserCloudSurfLast);   // only 1 or 2 points left
```

原因分析如下: points are nan

## 四、Linux相关

覆盖范围包括常用命令和功能包的使用，比如vim等等。

##### 1

错误提示如下

```C++
E:The package xterm needs to be reinstalled, but I can't find an archive for it.
```

输入以下命令解决

```C++
sudo dpkg --remove --force-all xterm
```

##### 2

无法用robotstudio打开工作空间，可能是因为工作空间需要root权限。可以通过更改文件夹权限的方式解决。

```C++
chmod -R 777 /home/nnnn/Documents/mws
```

运行以上命令会把文件夹mws的权限更改为“任何人可执行任何操作”，因此请确定真的需要更改权限时再使用这一命令。

##### 3

运行以下命令

```C++
nautilus /media/nnnn/software/
```

错误提示如下

```C++
Nautilus-Share-Message: Called “net usershare info” but it failed
```

输入以下命令解决

```C++
sudo apt install samba” and "$ sudo mkdir -p /var/lib/samba/usershares/"
```

##### 4. vim 突然不响应了

`CTRL+S`表示停止向终端停止输出; `CTRL+Q`恢复向终端输出流。期间的输入会缓存在流中，恢复输出流后一次性输出至终端。

##### 5

运行以下命令

```C++
sudo apt-get update; // 或更新源时
```

错误提示如下

```C++
problems: E:Failed to fetch https://download.docker.com/linux/ubuntu/dists/xenial/InRelease  Unable to find expected entry 'stable/source/Sources' in Release file (Wrong sources.list entry or malformed file), E:Some index files failed to download. They have been ignored, or old ones used instead.
```

原因分析：通常是因为 Great Wall

##### 6

运行以下命令

```C++
sudo apt-get install libreadline7 libreadline-dev
```

错误提示如下

```C++
E: Unable to locate package libreadline7
```

原因分析：安装的为版本7，安装版本6即可

##### 7

错误提示如下  

```C++
E: Unable to correct problems, you have held broken packages 
```

原因分析：你想安装的软件所需要的依赖项你的系统已经安装了，但是系统已经安装的依赖项版本与它想要的版本不一致导致。可以选择卸载已经安装的版本并重新安装。

##### 8
运行以下命令

```C++
apt install progress
```

错误提示如下

```C++
E: Could not open lock file /var/lib/dpkg/lock-frontend - open (13: Permission denied)
E: Unable to acquire the dpkg frontend lock (/var/lib/dpkg/lock-frontend), are you root?
```
原因分析：没有root权限，使用`sudo`获取root权限即可

##### 9 ubuntu 20 找不到设置 cannot find setting
这是因为有个包丢失了，重新安装即可。可使用如下命令
```C++
sudo apt install gnome-control-center
```

##### 10
问题如下：
我先把 Windows10 和 Ubuntu 18 装在了第一个硬盘，然后又在第二个硬盘安装了 Ubuntu20。结果win10和u18都能正常进入，但无法进入u20，尽管这个系统已经正确安装。

在安装Ubuntu20时，我把它装在了第二个硬盘。对于`安装启动器的设备`，我尝试了装u20的分区和装u20的硬盘，安装后在grub界面有`Ubuntu`，但都无法打开。最后我把该选项选为第一个盘，即win10所在的盘。

##### 11 搜狗输入法不能汉字

已经按照官网的步骤安装、重启、配置，但是调为搜狗 输入法时任然不能输入汉字。可能是忘记安装相关的依赖项。在终端执行以下命令安装依赖项后即可使用。

```
sudo apt install libqt5qml5 libqt5quick5 libqt5quickwidgets5 qml-module-qtquick2

sudo apt install libgsettings-qt1
```

