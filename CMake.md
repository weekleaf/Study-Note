# CMake

[cmake使用详细教程（日常使用这一篇就足够了）_cmake教程-CSDN博客](https://blog.csdn.net/iuu77/article/details/129229361)

## 一、使用CMake编译

### 一、只有一个源文件的程序

目录下有hello.cpp与CMakeLists.txt，hello.cpp待编译，CMakeLists.txt内容为：

```
# cmake最低版本要求2.8
cmake_minimum_required (VERSION 2.8)

# 项目的工程名
project (learn_cmake)
 
# 第一个参数：生成的可执行文件名，第二个参数：需要的依赖
add_executable(hello hello.cpp)
```

然后在当前目录下执行`cmake .`。

### 二、同一目录下多个源文件

此时目录包含hello.cpp、add.cpp、add.h，只需要在CMakeLists.txt添加所依赖的.cpp文件：

```
cmake_minimum_required (VERSION 2.8)

project (learn_cmake)

add_executable (hello hello.cpp add.cpp)
```

### 三、同一目录有很多源文件

如果同一目录下有无穷多源文件，那么一个一个添加就很慢了。此时可以使用cmake中的`aux_source_directory (dir var)`函数存储这些源文件，它的作用是把**dir**目录中的所有源文件都储存在**var**变量中，然后需要用到源文件的地方用变量**var**来取代：

```
cmake_minimum_required (VERSION 2.8)

project (learn_cmake)

aux_source_directory (. SRC_LIST)

add_executable (hello ${SRC_LIST})
```

### 四、头文件在别的文件夹

对于集中的头文件，CMake提供了一个很方便的函数`include_directories (dir)`，他的作用是自动去**dir**目录下寻找头文件：

```
cmake_minimum_required (VERSION 2.8)

project (learn_cmake)

aux_source_directory (. SRC_LIST)

include_directories (./inc_dir)

add_executable (hello ${SRC_LIST})
```

### 五、头文件源文件分离，有多个文件夹

一般头文件在include文件夹，源文件在src文件夹：

```
cmake_minimum_required (VERSION 2.8)

project (learn_cmake)

aux_source_directory (./src SRC_LIST)

include_directories (./include)

add_executable (hello ${SRC_LIST})
```

### 六、生成动态库和静态库

一般而言，include放头文件，src放源文件，lib放库文件，bin放生成的二进制文件，build放构建项目相关的文件，比如CMakeLists.txt，一般也是在build目录下执行`cmake`和`make`。

```
cmake_minimum_required (VERSION 3.0)
project (learn_cmake)

aux_source_directory (${PROJECT_BINARY_DIR}/../src SRC_LIST)

include_directories (${PROJECT_BINARY_DIR}/../include)

# 生成动态库和静态库，参数一：生成库的名称，参数二：静态或动态，参数三：生成库所需要的源文件
add_library (func_shared SHARED ${SRC_LIST})
add_library (func_static STATIC ${SRC_LIST})

# 最终生成的库的名称，这里把库的名称统一为myfunc；也可以不使用该函数，库名称就与add_library设置的一样
set_target_properties (func_shared PROPERTIES OUTPUT_NAME "myfunc")
set_target_properties (func_static PROPERTIES OUTPUT_NAME "myfunc")

# 此处设定LIBRARY_OUTPUT_PATH为${PROJECT_BINARY_DIR}/../lib路径
set (LIBRARY_OUTPUT_PATH ${PROJECT_BINARY_DIR}/../lib)
```

PROJECT_BINARY_DIR是cmake的系统变量，意思是执行cmake命令的目录，我们计划在**build**目录下执行`cmake`命令，所以这个变量也就等同于build目录。

LIBRARY_OUTPUT_PATH是cmake系统变量，项目生成的库文件都放在这个目录下。这里我指定库生成到lib目录。

开始编译：

在build目录下执行`cmake`和`make`，库文件将生成在lib目录下。

### 七、链接库文件

**lib**目录下存放静态库和动态库，**main_src**目录下存放main函数相关的源文件，**bin**目录存放项目生成的可执行文件：

```
cmake_minimum_required (VERSION 3.0)
project (learn_cmake)

# 整合源文件
aux_source_directory (${PROJECT_BINARY_DIR}/../main_src MAIN_SRC)

# 引入头文件路径
include_directories (${PROJECT_BINARY_DIR}/../include)

# 查找库文件的函数，参数一：用于存储找到的库文件的变量，参数二：要查找的库文件名，参数三：查找路径
find_library (FUNC_LIB myfunc ${PROJECT_BINARY_DIR}/../lib)

# 设置可执行文件生成到哪里
set (EXECUTABLE_OUTPUT_PATH ${PROJECT_BINARY_DIR}/../bin)

add_executable (hello ${MAIN_SRC})

# 可执行文件链接库
target_link_libraries (hello ${FUNC_LIB})
```

EXECUTABLE_OUTPUT_PATH是cmake系统变量，意思是生成的可执行文件的的目录，这里把他改为bin目录，因此生成的可执行性文件会出现在bin目录中。

### 八、添加编译选项

```
add_compile_options (-std=c++11)
```

