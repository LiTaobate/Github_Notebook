# 一、关于UR的驱动包的解释
## UR 机器人软件包包括：
 - **ur_description**：该软件包包含了UR-3、UR-5和 UR-10 的机器人描述和 Gazcbo 描述
- **ur_driver**： 该软件包包含了客户端节点，它可与UR-3、UR-5和UR-10 机器人的硬件
控制器通信。
- **ur_bringup**：该软件包由启动文件组成。它用来启动与机器人硬件控制器的通信，以使真实机器人启动。
- **ur_gazebo**:该软件包包含了 UR-3、UR-5和UR-10的 Gazebo 仿真。
- **ur_msgs**:该软件包包含了用于不同 UR 节点之间通信的 ROS 消息。
- **urXX moveit config**: 这些是UR 机器人操作器的 moveit 配置文件。每种类型的机械臂都有一个软件包 (ur3 moveit config 、ur5 moveit config 和 ur10_ moveit_config)。
- **ur kinematics**:该软件包包含了用于 UR-3、UR-5 和UR-10 的运动解算器插件。我们可以在Movelt! 中使用该解算器插件。
安装和编译UR 机器人软件包后，我们就可以用下面的命令启动 Gazebo 中的 UR-10机器人进行仿真
> roslaunch ur_grazebo ur10.launch 
