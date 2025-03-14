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
* 必须有自己的目录，意味着在同一个目录下不能有嵌套的或者多个软件包存在

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