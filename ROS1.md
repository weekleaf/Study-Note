# ROS1

[cn/ROS/Tutorials - ROS Wiki](https://wiki.ros.org/cn/ROS/Tutorials)

## 一、初级教程

### 1.配置ROS环境

#### 管理环境

ROS依赖于使用shell环境组合空间的概念，这使得针对不同版本的ROS或不同的软件包集开发变得更加容易。

在每次打开终端时你都需要先运行下面这条后才能访问ROS相关的命令，为了避免这一繁琐过程，你可以事先在`.bashrc`文件中添加这条命令。这样做也可以方便你在同一台计算机上安装并随时切换到不同版本的ROS。

```
$ source /opt/ros/<distro>/setup.bash
```

#### 创建ROS工作空间

```
$ mkdir -p ~/catkin_ws/src
$ cd ~/catkin_ws/
$ catkin_make
```

当前目录应该能看到`build`和`devel`这两个目录。在`devel`文件夹里面你可以看到几个`setup.*sh`文件。`source`这些文件中的任何一个都可以将当前工作空间设置在环境的最顶层。

```
$ source devel/setup.bash
```

设置该空间在环境最顶层，这样会优先查找该工作空间内的功能包。

确认工作区被安装脚本正确覆盖，echo一下：

```
$ echo $ROS_PACKAGE_PATH
/home/<username>/catkin_ws/src:/opt/ros/<distro>/share
```

### 2.ROS文件系统导览

#### 文件系统概念简介

- **软件包（Packages）：**包是ROS代码的软件组织单元，每个软件包都可以包含程序库、可执行文件、脚本或其他构件。
- **Manifests ([package.xml](https://wiki.ros.org/catkin/package.xml))：** 清单（Manifest）是对软件包的描述。它用于定义软件包之间的依赖关系，并记录有关软件包的元信息，如版本、维护者、许可证等。

#### 文件系统工具

使用Linux内置命令行工具`ls`和`cd`等可能会繁琐，ros提供了专门的命令行工具。

##### rospack

只涉及到`find`参数选项，该选项可以返回软件包的所在路径。

```
$ rospack find [package_name]
```

##### roscd

它允许你直接切换目录（[cd](http://ss64.com/bash/cd.html)）到某个软件包或者软件包集当中。

```
$ roscd [locationname[/subdir]]
```

比如：

```
$ roscd roscpp
```

注意，就像ROS中的其它工具一样，[roscd](https://wiki.ros.org/roscd)只能切换到那些路径已经包含在[ROS_PACKAGE_PATH](https://wiki.ros.org/ROS/EnvironmentVariables#ROS_PACKAGE_PATH)环境变量中的软件包。

roscd也可以切换到一个软件包或软件包集的子目录中：

```
$ roscd roscpp/cmake
```

##### roscd log

`roscd log`将带您进入存储ROS日志文件的目录。需要注意的是，如果你没有执行过任何ROS程序，系统会报错说该目录不存在。

##### rosls

它允许你直接按软件包的名称执行 [ls](http://ss64.com/bash/ls.html) 命令（而不必输入绝对路径）。

```
$ rosls [locationname[/subdir]]
```

```
$ rosls roscpp_tutorials
```

##### TAB补全

如果功能包名相当长，可以输入一部分，然后使用tab补全。

### 3.创建ROS软件包

#### catkin软件包由什么组成

* [package.xml](https://wiki.ros.org/catkin/package.xml)，该文件提供有关软件包的元信息
* [catkin版本的CMakeLists.txt](https://wiki.ros.org/catkin/CMakeLists.txt)
* 软件包必须有自己的目录，意味着在同一个目录下不能有嵌套的或者多个软件包存在

#### 工作空间中的软件包

一个catkin工作空间的文件结构：

```
workspace_folder/        -- WORKSPACE
  src/                   -- SOURCE SPACE
    CMakeLists.txt       -- 'Toplevel' CMake file, provided by catkin
    package_1/
      CMakeLists.txt     -- CMakeLists.txt file for package_1
      package.xml        -- Package manifest for package_1
    ...
    package_n/
      CMakeLists.txt     -- CMakeLists.txt file for package_n
      package.xml        -- Package manifest for package_n
```

#### 创建catkin软件包

```
# This is an example, do not try to run this
# catkin_create_pkg <package_name> [depend1] [depend2] [depend3]
```

cd到catkin工作空间中的源文件空间目录下，使用`catkin_create_pkg`命令创建一个名为`beginner_tutorials`的新软件包，这个软件包依赖于`std_msgs`、`roscpp`和`rospy`：

```
$ catkin_create_pkg beginner_tutorials std_msgs rospy roscpp
```

#### 生效配置文件

工作空间构建（`catkin_make`）完成后，在`devel`子目录下创建了一个与你通常在`/opt/ros/$ROSDISTRO_NAME`下看到的目录结构类似的结构。

要将这个工作空间添加到ROS环境中，你需要`source`一下生成的配置文件：

```
$ . ~/catkin_ws/devel/setup.bash
```

#### 软件包依赖关系

##### 一级依赖

使用`rospack`命令工具来查看软件包`beginner_tutorials`的**一级**依赖包：

```
$ rospack depends1 beginner_tutorials 
```

##### 间接依赖

一个软件包可以有相当多间接的依赖关系，比如`beginner_tutorials`依赖`rospy`，`rospy`又依赖其他包，好在使用`rospack`可以递归检测出所有嵌套的依赖包。

```
$ rospack depends beginner_tutorials
```

#### 自定义package.xml

##### 描述标签

```en
<description>The beginner_tutorials package</description>
```

将描述信息修改为任何你喜欢的内容。

##### 维护者标签

```en
<maintainer email="you@yourdomain.tld">Your Name</maintainer>
```

它能够让其他人联系到软件包的相关人员。

##### 许可证标签

```en
<license>BSD</license>
```

##### 依赖项标签

描述了软件包的依赖关系，这些依赖项分为`build_depend`、`buildtool_depend`、`run_depend`、`test_depend`。有关这些标记的详细说明，请参考[Catkin Dependencies](https://wiki.ros.org/catkin/package.xml#Build.2C_Run.2C_and_Test_Dependencies)相关的文档。

除了catkin中默认提供的`buildtool_depend`，所有我们列出的依赖包都已经被添加到`build_depend`标签中。因为**在编译和运行时都需要用到**所有指定的依赖包，因此还要将每一个依赖包分别添加到`run_depend`标签中：

```en
<buildtool_depend>catkin</buildtool_depend>

<build_depend>roscpp</build_depend>
<build_depend>rospy</build_depend>
<build_depend>std_msgs</build_depend>

<exec_depend>roscpp</exec_depend>
<exec_depend>rospy</exec_depend>
<exec_depend>std_msgs</exec_depend>
```

#### 自定义CMakeLists.txt

见后文。

### 4.构建ROS软件包

使用[catkin_make](https://wiki.ros.org/catkin/commands/catkin_make) 构建软件包，你可以认为catkin_make是在标准CMake工作流程中依次调用了`cmake`和`make`。

```
# 在catkin工作空间下
$ catkin_make [make_targets] [-DCMAKE_VARIABLES=...]
```

上面的步骤可以认为为：

```
# 在CMake工作空间下
$ mkdir build
$ cd build
$ cmake ..
$ make
$ make install  # （可选）
```

每个CMake项目都要单独进行这样的步骤，相反，多个catkin项目可以放在工作空间中一起构建，上述命令会构建`src`目录下的所有catkin项目，注意把软件包放在src目录下。

catkin_make后会生成`build`和`devel`文件夹，`build` 目录是[构建空间](https://wiki.ros.org/catkin/workspaces#Build_Space)的默认位置，同时`cmake`和`make`也是在这里被调用来配置和构建你的软件包。而`devel`目录是[开发空间](https://wiki.ros.org/catkin/workspaces#Development_.28Devel.29_Space)的默认位置, 在安装软件包之前，这里可以存放可执行文件和库。

### 5.ROS节点

#### 图概念速览

[计算图（Computation Graph）](https://wiki.ros.org/cn/ROS/Concepts#ROS.2Bi6F7l1b.2BXEJrIQ-)是一个由ROS进程组成的点对点网络，它们能够共同处理数据。ROS的基本计算图概念有节点（Nodes）、主节点（Master）、参数服务器（Parameter Server）、消息（Messages）、服务（Services）、话题（Topics）和袋（Bags），它们都以不同的方式向图（Graph）提供数据。

[节点（Nodes）](https://wiki.ros.org/Nodes)：节点是一个可执行文件，它可以通过ROS来与其他节点进行通信。

[消息（Messages）](https://wiki.ros.org/Messages)：订阅或发布话题时所使用的ROS数据类型。

[话题（Topics）](https://wiki.ros.org/Topics)：节点可以将消息*发布*到话题，或通过*订阅*话题来接收消息。

[主节点（Master）](https://wiki.ros.org/Master)：ROS的命名服务，例如帮助节点发现彼此。

[rosout](https://wiki.ros.org/rosout)：在ROS中相当于`stdout/stderr（标准输出/标准错误）`。rosout是一个节点。

[roscore](https://wiki.ros.org/roscore)：主节点 + rosout + [参数服务器](https://wiki.ros.org/Parameter Server)（会在以后介绍）。

#### 节点

是ROS软件包中的一个可执行文件。ROS节点使用ROS[客户端库](https://wiki.ros.org/cn/Client Libraries)与其他节点通信。节点可以发布或订阅话题，也可以提供或使用[服务](https://wiki.ros.org/Services)。

#### 客户端库

ROS客户端库可以让用不同编程语言编写的节点进行相互通信：

rospy = Python客户端库

roscpp = C++客户端库

#### roscore

`roscore`是你在运行所有ROS程序前首先要运行的命令。

#### rosnode

`rosnode`显示当前正在运行的ROS节点信息。`rosnode list`命令会列出这些活动的节点：

```
$ rosnode list
```

`rosnode info`命令返回的是某个指定节点的信息：

```
$ rosnode info /rosout
```

输出打印：

```
------------------------------------------------------------------------
Node [/rosout]
Publications:
 * /rosout_agg [rosgraph_msgs/Log]

Subscriptions:
 * /rosout [unknown type]

Services:
 * /rosout/get_loggers
 * /rosout/set_logger_level

contacting node http://machine_name:54614/ ...
Pid: 5092
```

#### rosrun

`rosrun`可以让你用包名直接运行软件包内的节点（而不需要知道包的路径）。

```
$ rosrun [package_name] [node_name]
```

### 6.ROS话题

节点之间通过话题来互相通信。

#### rqt_graph

```
$ rosrun rqt_graph rqt_graph
```

该命令用窗口显示目前节点与节点间的话题关系。

#### rostopic

显示在某个话题上发布的数据：

```
rostopic echo [topic]
```

列出当前已被订阅和发布的所有话题：

```
$ rostopic list
```

查看所发布话题的消息类型：

```
rostopic type [topic]
```

把数据发布到当前某个正在广播的话题上：

```
rostopic pub [topic] [msg_type] [args]
```

例子：

```
$ rostopic pub -1 /turtle1/command_velocity turtlesim/Velocity  -- 2.0  1.8
```

`-1`选项会让rostopic只发布一条消息，然后退出，可以使用`-r`选项下来发布源源不断的命令，选项`--`（两个破折号）用来告诉选项解析器，表明之后的参数都不是选项。

报告数据发布的速率：

```
rostopic hz [topic]
```

#### rqt_plot

`rqt_plot`命令可以在滚动时间图上显示发布到某个话题上的数据。

```
$ rosrun rqt_plot rqt_plot
```

### 7. ROS服务与参数

[服务（Services）](https://wiki.ros.org/Services)是节点之间通讯的另一种方式。服务允许节点发送一个**请求（request）**并获得一个**响应（response）**。

#### rosservice

列出服务：

```
$ rosservice list
```

查看服务类型：

```
rosservice type [service]
```

用给定的参数调用服务：

```
rosservice call [service] [args]
```

#### rosparam

`rosparam`能让我们在ROS[参数服务器（Parameter Server）](https://wiki.ros.org/Parameter Server)上存储和操作数据。

列出参数服务器上的参数：

```
$ rosparam list
```

改变参数值：

```
rosparam set [param_name]
rosparam get [param_name]
```

改变参数后需要调用`/clear`服务使得参数修改生效：

```
$ rosservice call /clear
```

显示参数服务器上的所有内容：

```
$ rosparam get /
```

输出类似：

```
rosdistro: 'noetic

  '
roslaunch:
  uris:
    host_nxt__43407: http://nxt:43407/
rosversion: '1.15.5

  '
run_id: 7ef687d8-9ab7-11ea-b692-fcaa1494dbf9
turtlesim:
  background_b: 255
  background_g: 86
  background_r: 69
```

你可以将参数服务器的所有内容存储在一个文件内，以便下次可以重新加载：

```
rosparam dump [file_name] [namespace]
```

加载参数：

```
rosparam load [file_name] [namespace]
```

比如，将所有的参数写入`params.yaml`文件：

```
$ rosparam dump params.yaml
```

将yaml文件重载入新的命名空间，例如`copy_turtle`（这里参数二可以为空，仅加载参数）：

```
$ rosparam load params.yaml copy_turtle
```

### 8.rqt_console与roslaunch

`rqt_console`连接到了ROS的日志框架，以显示节点的输出信息。`rqt_logger_level`允许我们在节点运行时改变输出信息的详细级别，包括`Debug`、`Info`、`Warn`和`Error`。

```
$ rosrun rqt_console rqt_console
```

```
$ rosrun rqt_logger_level rqt_logger_level
```

日志级别的优先级按以下顺序排列：

```
Fatal （致命）
Error （错误）
Warn  （警告）
Info  （信息）
Debug （调试）
```

在`rqt_logger_level`中设置日志级别，你可以在`rqt_console`获得所有优先级级别，或只是更高级别的消息。比如，将日志级别设为`Warn`时，你会得到`Warn`、`Error`和`Fatal`这三个等级的日志消息。

#### 使用roslaunch

`roslaunch`可以用来启动定义在`launch（启动）`文件中的节点。

```
$ roslaunch [package] [filename.launch]
```

推荐将launch文件写在软件包内的launch目录下，这种推荐的标准做法被认为是“最佳实践”。

#### launch解析

标签`<launch></launch>`表明这是个launch文件。

`<group ns = ""></group>`创建分组，并以命名空间（namespace）标签来区分，一般在需要跑多个同名节点的时候用上，比如：

```en
<group ns="turtlesim1">
	<node pkg="turtlesim" name="sim" type="turtlesim_node"/>
</group>
<group ns="turtlesim2">
	<node pkg="turtlesim" name="sim" type="turtlesim_node"/>
</group>
```

这样可以让我们同时启动两个turtlesim模拟器，而不会产生命名冲突。

### 9.ROS中编辑文件

`rosed`是[rosbash](https://wiki.ros.org/rosbash)套件的一部分。利用它可以直接通过软件包名编辑包中的文件，而无需键入完整路径。

```
$ rosed [package_name] [filename]
```

### 10.创建ROS消息和服务

[msg](https://wiki.ros.org/msg)（消息）：msg文件就是文本文件，用于描述ROS消息的字段。它们用于为不同编程语言编写的消息生成源代码。

[srv](https://wiki.ros.org/srv)（服务）：一个srv文件描述一个服务。它由两部分组成：请求（request）和响应（response）。

msg文件存放在软件包的`msg`目录下，srv文件则存放在`srv`目录下。

ROS中还有一个特殊的数据类型：`Header`，它含有时间戳和ROS中广泛使用的坐标帧信息。

msg文件示例：

```
Header header
string child_frame_id
geometry_msgs/PoseWithCovariance pose
geometry_msgs/TwistWithCovariance twist
```

srv文件示例，包含两个部分：请求和响应。这两部分用一条`---`线隔开。：

```
int64 A
int64 B
---
int64 Sum
```

#### 创建并使用msg

假设在软件包下存在msg/Num.msg文件，已经按示例写好消息的数据类型，现在需要确保msg文件能被转换为C++、Python和其他语言的源代码。

打开`package.xml`, 确保它包含以下两行且没有被注释。如果没有，添加进去：

```
<build_depend>message_generation</build_depend>
<exec_depend>message_runtime</exec_depend>
```

在构建时，其实只需要`message_generation`，而在运行时，我们只需要`message_runtime`。

打开`CMakeLists.txt`文件，为已经存在里面的`find_package`调用添加`message_generation`依赖项，这样就能生成消息了。直接将`message_generation`添加到`COMPONENTS`列表中即可，如下所示：

```
find_package(catkin REQUIRED COMPONENTS
   roscpp
   rospy
   std_msgs
   message_generation
)
```

确保导出消息的运行时依赖关系：

```
catkin_package(
  ...
  CATKIN_DEPENDS message_runtime ...
  ...)
```

找到`add_message_files()`函数，取消注释并将示例文件名改为你需要的msg文件，改完之后像这样：

```
add_message_files(
  FILES
  Num.msg
)
```

然后确保`generate_messages()`函数被调用，取消函数原有的注释：

```
generate_messages(
  DEPENDENCIES
  std_msgs
)
```

构建完成后可以使用`rosmsg show`命令查看：

```
$ rosmsg show [message type]
```

```
$ rosmsg show beginner_tutorials/Num
```

#### 创建并使用srv

假设在软件包下存在srv/AddTwoInts.msg文件，已经按示例写好消息的数据类型，现在需要确保msg文件能被转换为C++、Python和其他语言的源代码。

`package.xml`操作同上。

打开`CMakeLists.txt`文件，为已经存在里面的`find_package`调用添加`message_generation`依赖项，这样就能生成消息了。直接将`message_generation`添加到`COMPONENTS`列表中即可，如下所示：

```
find_package(catkin REQUIRED COMPONENTS
   roscpp
   rospy
   std_msgs
   message_generation
)
```

找到`add_service_files()`函数，取消注释并将示例文件名改为你需要的srv文件，改完之后像这样：

```
add_service_files(
  FILES
  AddTwoInts.srv
)
```

构建完成后可以使用`rosmsg show`命令查看：

```
$ rossrv show <service type>
```

```
$ rossrv show beginner_tutorials/AddTwoInts
```

C++消息的头文件将生成在`~/catkin_ws/devel/include/beginner_tutorials/`。Python脚本将创建在`~/catkin_ws/devel/lib/python2.7/dist-packages/beginner_tutorials/msg`。而Lisp文件则出现在`~/catkin_ws/devel/share/common-lisp/ros/beginner_tutorials/msg/`。

`srv`目录中的任何`.srv`文件都将生成支持语言的代码。对于C++，头文件将生成在消息的头文件的同一目录中。对于Python和Lisp，会在`msg`目录旁边的`srv`目录中。

---

>其他的就翻开头的文档吧。
>
>[cn/ROS/Tutorials/NavigatingTheWiki - ROS Wiki](https://wiki.ros.org/cn/ROS/Tutorials/NavigatingTheWiki)
>
>[Introduction · Autolabor-ROS机器人入门课程《ROS理论与实践》零基础教程](http://www.autolabor.com.cn/book/ROSTutorials/)
