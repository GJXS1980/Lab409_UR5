# universal_robot
#### 1.ur_description的测试
```
roslaunch ur_description ur5_upload.launch
```
新开终端
```
roslaunch ur_description test.launch
```

#### 2.ur_modern_driver
>说明:来自Universal Robots的UR3 UR5 UR10机器人手臂的新驱动程序,它旨在取代旧驱动程序，同时解决一些问题，提高可用性并实现ros_control的兼容性。

用法
```
roslaunch ur_modern_driver urXX_bringup.launch robot_ip:=IP_OF_THE_ROBOT [reverse_port:=REVERSE_PORT]
```
其中IP_OF_THE_ROBOT是您的UR手臂的IP，XX取决于您的机器人是'5'还是'10'。上述启动文件会将roscore和启动文件调用到urXX_description，以便ROS的参数服务器获得有关机器人手臂的信息

运行MoveIt来计划和执行手臂上的操作
```
roslaunch ur5_moveit_config ur5_moveit_planning_execution.launch
roslaunch ur5_moveit_config moveit_rviz.launch config:=true
```
如果您想使用基于ros_control的方法，请使用启动文件`urXX_ros_control.launch`，其中根据您的机器人，XX为“5”或“10”。

注意：如果您使用的是基于ros_control的方法，则需要2个可在ur_driver软件包中找到的软件包。如果您的工作区中没有ur_driver软件包，只需将这些软件包复制到工作区/src文件夹中即可：
>
urXX_moveit_config
ur_description

要将ros_control与MoveIt一起使用，请务必将所需的控制器添加到`urXX_controllers.yaml urXX_moveit_config/ config`文件夹中。添加以下内容
```
controller_list:
 - name: /vel_based_pos_traj_controller #or /pos_based_pos_traj_controller
   action_ns: follow_joint_trajectory
   type: FollowJointTrajectory
   default: true
   joints:
      - shoulder_pan_joint
      - shoulder_lift_joint
      - elbow_joint
      - wrist_1_joint
      - wrist_2_joint
      - wrist_3_joint
```

>使用tool0_controller框架
默认的框架名称是：base和tool0_controller。

该驱动程序目前支持两个位置轨迹控制器; 基于位置和基于速度。它们都通过启动文件加载，但其中只有一个可以同时运行。默认情况下，基于速度的控制器启动。您可以通过调用相应的服务来切换控制器：
```
rosservice call /universal_robot/controller_manager/switch_controller "start_controllers:
- 'vel_based_pos_traj_controller'
stop_controllers:
- 'pos_based_pos_traj_controller'
strictness: 1"
```
或者
```
rosservice call /universal_robot/controller_manager/switch_controller '{start_controllers: {'vel_based_pos_traj_controller'}, stop_controllers:: {'pos_based_pos_traj_controller'},strictness: 1}'
```
在启动新控制器之前，请确保在当前正在运行的控制器之前或之后停止，否则将失败。


#### 3.ur_gazebo(可选)
(1)安装要模拟UR5并在模拟中对其进行控制，请首先安装必要的软件包：
```
$ sudo apt-get install \
  ros-kinetic-ur-gazebo \
  ros-kinetic-ur5-moveit-config \
  ros-kinetic-ur-kinematics
$ sudo apt-get install ros-kinetic-gazebo-ros-pkgs ros-kinetic-gazebo-ros-control
```

(2)启动
```
$ roslaunch ur_gazebo ur5.launch
```
打开新终端
```
$ roslaunch ur5_moveit_config ur5_moveit_planning_execution.launch sim:=true
```
打开新终端
```
$ roslaunch ur5_moveit_config moveit_rviz.launch config:=true
```


#### 4.ROS-Industrial与Universal Robot控制器通讯
(1)配置机器人ip,然后测试是否已经成功
```
ping IP_OF_THE_ROBOT
```
(2)与UR v1.5.x联系至v1.8.x
要调出真实的机器人，请在用实际硬件地址替换IP_OF_THE_ROBOT后运行以下命令：
```
roslaunch ur_bringup ur5_bringup.launch robot_ip:=IP_OF_THE_ROBOT [reverse_port:=REVERSE_PORT]
```
在新的终端中，可以执行一个简单的测试脚本，将机器人移动到预定义的位置如下所示：
```
rosrun ur_driver test_move.py
```
(3)与UR v3.x联系
要调出真实的机器人，请在用实际硬件地址替换IP_OF_THE_ROBOT后运行以下命令：
```
roslaunch ur_modern_driver ur5_bringup.launch robot_ip:=IP_OF_THE_ROBOT [reverse_port:=REVERSE_PORT]
```
使用MoveIt！与硬件
为了设置MoveIt！允许运动计划运行的节点
```
roslaunch ur5_moveit_config ur5_moveit_planning_execution.launch
```
用于启动包含MoveIt！的配置的RViz！运动规划插件运行：
```
roslaunch ur5_moveit_config moveit_rviz.launch config:=true
```
或者要调出真实的机器人也有下面的方式:
```
roslaunch ur_bringup ur5_bringup.launch limited:=true robot_ip:=IP_OF_THE_ROBOT [reverse_port:=REVERSE_PORT]
roslaunch ur5_moveit_config ur5_moveit_planning_execution.launch limited:=true
roslaunch ur5_moveit_config moveit_rviz.launch config:=true
```



