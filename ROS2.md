# ROS2

[教程 — ROS 2 documentation 文档](http://dev.ros2.fishros.com/doc/Tutorials.html)

## 初学者：CLI工具

### 配置ROS2环境

您需要在打开的每个新shell终端上运行此命令才能访问ROS 2命令：

```
source /opt/ros/foxy/setup.bash
```

将source命令添加到您的shell启动脚本：

```
echo "source /opt/ros/foxy/setup.bash" >> ~/.bashrc
```

如果您在查找或使用ROS 2包时遇到问题，请确保使用以下命令正确设置了环境：

```
printenv | grep -i ROS
```

### 了解ROS2节点

ROS图是ROS 2元素同时处理数据的网络。它包含所有可执行文件以及它们之间的连接。

ROS中的每个节点应负责单个模块目的，每个节点可以通过话题、服务、动作或参数向其他节点发送和接收数据。

在ROS 2中，单个可执行文件 (cprogram程序、Python程序等) 可以包含一个或多个节点。

#### 1 ros2运行

```
ros2 run <package_name> <executable_name>
```

#### 2 ros2节点列表

```
ros2 node list
```

##### 2.1 重新映射

remap允许您将默认节点属性 (如节点名称、话题名称、服务名称等) 重新分配给自定义值。

#### 3 ros2节点消息

```
ros2 node info <node_name>
```

返回与该节点交互的订阅者、发布者、服务和动作 (ROS图连接) 的列表。

### 理解ROS2话题

#### 2 rqt_graph

可视化变化的节点和话题，以及它们之间的联系：

```
rqt_graph
```

#### 3 ros2话题列表

```
ros2 topic list
```

返回系统中当前活动的所有话题的列表。

#### 4 ros2话题echo

查看正在发布的关于某个话题的数据：

```
ros2 topic echo <topic_name>
```

#### 5 ros2话题信息

话题不一定只是点对点通讯; 它可以是一对多、多对一或多对多。

```
ros2 topic info <topic_name>
```

#### 7 ros2话题pub

有了消息结构，可以使用以下命令直接从命令行将数据发布到话题上：

```
ros2 topic pub <topic_name> <msg_type> '<args>'
```

参数需要以YAML语法输入，例子：

```
ros2 topic pub --once /turtle1/cmd_vel geometry_msgs/msg/Twist "{linear: {x: 2.0, y: 0.0, z: 0.0}, angular: {x: 0.0, y: 0.0, z: 1.8}}"
```

`--once` 选项是发送一条消息后退出，`--rate 1` 选项是以1hz发送消息。