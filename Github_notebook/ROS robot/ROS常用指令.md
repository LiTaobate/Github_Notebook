# 一、ROS常用指令
## 1、rostopic系列
### 1）rostopic list 显示消息列表
```
litao@ubuntu:~/Documents/catkin_ur5$ rostopic list
/cartesian_velocity_controller_sim/command_cart_vel
/clock
/rosout
/rosout_agg
```

### 2）rostopic info [消息名字]; 可以得到Type:消息.
例1:
> rostopic info /odom

得到消息类型：
```
Type: nav_msgs/Odometry`
Publishers: 
 * /mobile_base_nodelet_manager ()

Subscribers: None
```
例2:

> litao@ubuntu:~/Documents/catkin_ur5$ rostopic info /cartesian_velocity_controller_sim/command_cart_vel 

```
litao@ubuntu:~/Documents/catkin_ur5$ rostopic info /cartesian_velocity_controller_sim/command_cart_vel 

Type: geometry_msgs/Twist

Publishers: 
 * /rostopic_3463_1640846039786 (http://ubuntu:44279/)

Subscribers: None
```


### 3）、rosmsg show [消息类型]；可得其数据结构
例：
根据上述操作得到 topic 消息类型为`nav_msgs/Odometry`
使用工具
 > rosmsg show nav_msgs/Odometry  

后可以得到如下结果
```
std_msgs/Header header
uint32 seq
  time stamp
  string frame_id
string child_frame_id
geometry_msgs/PoseWithCovariance pose
  geometry_msgs/Pose pose
    geometry_msgs/Point position
      float64 x
      float64 y
      float64 z
    geometry_msgs/Quaternion orientation
      float64 x
      float64 y
      float64 z
      float64 w
  float64[36] covariance
geometry_msgs/TwistWithCovariance twist
  geometry_msgs/Twist twist
    geometry_msgs/Vector3 linear
      float64 x
      float64 y
      float64 z
    geometry_msgs/Vector3 angular
      float64 x
      float64 y
      float64 z
  float64[36] covariance
```


## 2、基础命令行
ROS常用命令行：
### 1.增
> catkin_create_pkg 自定义包名 依赖包 === 创建新的ROS功能包 
sudo apt install xxx === 安装 ROS功能包
### 2.删
> sudo apt purge xxx ==== 删除某个功能包
### 3.查
>rospack list === 列出所有功能包
rospack find 包名 === 查找某个功能包是否存在，如果存在返回安装路径
roscd 包名 === 进入某个功能包
rosls 包名 === 列出某个包下的文件
apt search xxx === 搜索某个功能包
### 4.改
>rosed 包名 文件名 === 修改功能包文件
需要安装 vim
比如:rosed turtlesim Color.msg
### 5.执行
#### 5.1roscore
roscore === 是 ROS 的系统先决条件节点和程序的集合， 必须运行 roscore 才能使 ROS 节点进行通信。roscore 将启动:
>* ros master
>* ros 参数服务器 
>* rosout 日志节点
用法:
`roscore`
或(指定端口号)
`roscore -p xxxx`
##### 5.2rosrun
rosrun 包名 可执行文件名 === 运行指定的ROS节点
> 比如:rosrun turtlesim turtlesim_node(小海龟测试命令)
##### 5.3roslaunch
roslaunch 包名 launch文件名 === 执行某个包下的 launch 文件
添加可执行权限：
>chmod + x * .py


