# 《ROS机器人开发实践》胡春旭  源码报错问题及解决
## 《ROS机器人开发实践》源码复现报错
 在ROS学习过程中，古月居老师的ROS系列教学仿佛是苦海明灯，很适合入门，我最近也在跟着这个教材学习。
话不多说，首先先附上胡春旭版教程的源码获取和使用方式：
在home目录下先新建工作空间：
> mkdir -p ~/catkin_ws/src
cd ~/catkin_ws/src

然后再将老师的源代码clone到你的ros工作空间的src目录下
> $ git clone https://github.com/huchunxu/ros_exploring.git

下边是我在跟着《ROS机器人开发实践》一书，复现案例时候遇到的一些问题，也提供了一下解决方法。（代码中还是有很多坑需要填的）
## 1、源码编译catkin_make失败
可参考大神文章（[https://blog.csdn.net/YiKangJ/article/details/82024809](https://blog.csdn.net/YiKangJ/article/details/82024809)）我只是在此基础上汇总了一些
原文说到：如果将源码下载到ROS的工作空间中，则需要将其中的ros2文件夹移出到工作空间之外（或者将其放置到ROS 2的工作空间中），否则会导致编译失败。
  但是就算如此还会有问题：
#### 报错1

```py
Could not find the required component ‘ecto’. The following CMake error indicates that you either need to install the package with the same name or change your environment so that it can be found.
CMake Error at /opt/ros/kinetic/share/catkin/cmake/catkinConfig.cmake:83 (find_package):
Could not find a package configuration file provided by “ecto” with any of
the following names:
ectoConfig.cmake
ecto-config.cmake

Add the installation prefix of “ecto” to CMAKE_PREFIX_PATH or set
“ecto_DIR” to a directory containing one of the above files. If “ecto”
provides a separate development package or SDK, be sure it has been
installed.

Call Stack (most recent call first):
ros_exploring-master/robot_perception/ork_tutorials/CMakeLists.txt:4 (find_package)
– Configuring incomplete, errors occurred!
See also “/home/qinpeiwang/catkin_ws/build/CMakeFiles/CMakeOutput.log”.
See also “/home/qinpeiwang/catkin_ws/build/CMakeFiles/CMakeError.log”.
Invoking “cmake” failed
```

#### 解决
缺少ecto这个包，直接安装就行
> $ sudo apt-get install ros-kinetic-ecto
#### 报错2
缺少 manipulation-msgs 包，直接安装（注意不是“_”而是“-”，尽量直接复制）
#### 解决
> $ sudo apt install ros-kinetic-manipulation-msgs
#### 报错3
缺少gazebo_ros_control 包
#### 解决
> $ sudo apt install ros-kinetic-gazebo-ros-control
#### 报错3
缺少链接库报错：
/usr/bin/ld: cannot find -lmsc
collect2: error: ld returned 1 exit status
#### 解决
参考书中207页说明，将科大讯飞的SDK库文件拷贝到系统目录下：
注：先进入库文件所在文件夹 ros_exploring/robot_perception/robot_voice/libs/x64（32位系统最后选择x86文件夹） 再执行如下命令
> $ sudo cp libmsc.so /usr/lib/libmsc.so


#### 报错4
权限错误：
```
/home/jyk/catkin_ws/build/ros_exploring/ros_advanced/dynamic_tutorials/setup_custom_pythonpath.sh: 5:exec: /home/jyk/catkin_ws/src/ros_exploring/ros_advanced/dynamic_tutorials/cfg/Tutorials.cfg: Permission denied
ros_exploring/ros_advanced/dynamic_tutorials/CMakeFiles/dynamic_tutorials_gencfg.dir/build.make:63: recipe for target ‘/home/jyk/catkin_ws/devel/include/dynamic_tutorials/TutorialsConfig.h’ failed
```
或者是
> Makefile:138: recipe for target 'all' failed
make: *** [all] Error 2
Invoking "make -j2 -l2" failed

#### 解决
进入 ```ros_exploring/ros_advanced/dynamic_tutorials/cfg ```文件夹，执行如下命令：

> chmod +x Tutorials.cfg


#### 报错5
pocketsphinx 语音包安装报错：
```ImportError: No module named pygst```
####解决
错误原因为缺少 python-gst 依赖，执行如下命令即可解决问题：

  > sudo apt install python-gst0.10
#### 报错6
```
Could not find the required component 'moveit_ros_perception'. 
The following CMake error indicates that you either need to install the package with the same name or change your environment so that it can be found.
CMake Error at /opt/ros/kinetic/share/catkin/cmake/catkinConfig.cmake:83 (find_package):   
Could not find a package configuration file provided by   "moveit_ros_perception" with any of the following names:
```
#### 解决
这个部分的话是运行到Moveit时候才有的问题，但是要复现代码，可以直接先安装Moveit。因为必然要安装。
> sudo apt-get install ros-kinetic-moveit

到这一步一般就可以正常编译了,但是进入终端后可以先source一下，再编译
> cd ~/catkin_ws
source devel/setup.bash
catkin_make 

假如不想每次开终端都需要source的话，你可以直接这样设置：
终端输入：
> 终端输入：`gedit ~/.bashrc`

文件打开后直接翻到最后面,在底部添加以下代码，保存退出，每次开新的终端都可以直接source了。
> source ~/catkin_ws/devel/setup.bash
## 2、其他常见错误
#### 报错7
```
-- Configuring incomplete, errors occurred!
See also "/home/litao/catkin_ws/build/CMakeFiles/CMakeOutput.log". 
See also "/home/litao/catkin_ws/build/CMakeFiles/CMakeError.log". Invoking "cmake" failed
```
不要直接去更改CMakeList.txt,只需要清理构建空间就可以
> rm -r build devel
catkin_make
#### 报错8
```p
ERROR: cannot launch node of type [arbotix_python/arbotix_driver]: arbotix_python
ROS path [0]=/opt/ros/kinetic/share/ros
ROS path [1]=/home/xcy/catkin_lml/src
ROS path [2]=/home/xcy/catkin_syl/src
ROS path [3]=/home/xcy/catkin_ws/src
ROS path [4]=/opt/ros/kinetic/share
```
#### 解决
检查是否安装 `arbotix_[python](https://so.csdn.net/so/search?from=pc_blog_highlight&q=python) package`

> roscd arbotix_python

安装 arbotix_python

> sudo apt-get install ros-kinetic-arbotix
#### 报错9
```p
Invoking :make -j2" fail and undefined reference to 'libusb
```
这个问题，我花了很长时间，一直没找到合适的解决方式，但有人用下边的方式解决了：
> cd ~/catkin_ws/src
git clone https://github.com/uos/sick_tim.git
cd ~/catkin_ws/
catkin_make

#### 报错10
  ``` 
[WARN] [1588564677.171522]: The ‘use_gui’ parameter was specified, which is deprecated. We’ll attempt to find and run the GUI, but if this fails you should install the ‘joint_state_publisher_gui’ package instead and run that. This backwards compatibility option will be removed in Noetic.
[ERROR] [1581780877.646970]: Could not find the GUI, install the ‘joint_state_publisher_gui’ package
```
#### 解决
安装好GUI的包就可以
> sudo apt-get install ros-kinetic-joint-state-publisher-gui 

  后续问题持续更新












   
