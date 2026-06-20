# ROS2\-jazzy学习笔记

# ROS2\-jazzy学习笔记

\[TOC\]

## 第零章 jazzy的安装

请使用一键安装进行安装，感谢**小鱼**开源

```Plain Text
wget http://fishros.com/install -O fishros && . fishros

**开源地址：**[**https://github.com/fishros/install**](https://github.com/fishros/install)

在安装时，请注意不要更新其它软件，apt会有锁。
如果有梯子，也就是网络环境较好的时候，可以不换源，软件会更新更全。
国内请不要使用网易源。
```

### 1\.静态IP配置（适用于Jetson/树莓派/虚拟机）

便于SSH远程连接和ROS2多机通信，需要为机器人开发板或电脑设置固定IP。

#### **1\.1准备工作：**

打开终端输入以下命令：

- Windows：`ipconfig`

- Mac / Linux：`ifconfig` 

找到你正在使用的网卡（有线找 `以太网` 或 `eth0`，WiFi找 `WLAN` 或 `wlan0`），看 `IPv4 地址` 这一栏。

例如：`192.168.31.150`

- 你的网关（路由器IP）：通常是 `192.168.31.1`（即把最后一位换成 `.1`）

- 子网掩码：通常是 `255.255.255.0`

- 可用IP范围：`192.168.31.1` \~ `192.168.31.255`

##### **方法一：图形界面（GUI）配置（最直观）**

1\.打开网络设置：点击桌面右上角的网络图标，选择“设置”或“有线设置”

![Image](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=ZWRhZDFmYmM1NjczYjkzNTJiOWY2MDdkYzg3OWZjZTRfMmZhNTkwNDM4N2ZiNTE1NzRhNTRjYjk5NmFjNWM5YzNfSUQ6NzY1MzM5NzcyMTk0Mzk4NTEyMF8xNzgxOTQ4MzczOjE3ODIwMzQ3NzNfVjM)

2\.选择网络接口，切换到“IPv4”标签页，将“方式”从“自动\(DHCP\)”改为“手动”

![Image](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=ODRlYWUyYzljOWJkNTIyYjY2YWY0ZjM1MjA2MDY3MTZfZTg4NjllMzFhNGI0ZDM2MmIwNDU0YzUxOGI1ZTQ3MjJfSUQ6NzY1MzM5OTEyNDg0MjE3MTYwM18xNzgxOTQ4MzczOjE3ODIwMzQ3NzNfVjM)

3\.按照下面填：

![Image](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=MWY2YjA4YWY2ZjFlZjZiOGM3YjMxMzEzNDlmMWUyZmJfM2FkOTU5NTMzYzE0NDA1YTI2NjcyNjg4NzBlMTljOGFfSUQ6NzY1MzQwMzYwMTgwODcyMzE3Nl8xNzgxOTQ4MzczOjE3ODIwMzQ3NzNfVjM)

**注意：**正确示例：电脑 IP 是 `192.168.31.10`，小车是 `192.168.31.100`，路由器是 `192.168.31.1`。

防止填的 IP 和别人冲突导致连不上，先在终端 ping 一下你想填的 IP：

```Plain Text
ping 192.168.31.100
```

- 如果显示 `Destination Host Unreachable` 或 超时：说明这个 IP 无人用。

- 如果显示 有数据返回（bytes=\.\.\.）：说明这个 IP 已经被占用。

## 第一章 jazzy的相关使用介绍

### 一，平台选择

#### \<1\>jetson orin nano super

属于arm架构，请注意后续使用

#### 1\.锁定系统版本号

先备份原始的软件源列表文件：

```Plaintext
sudo cp /etc/apt/sources.list.d/nvidia-l4t-apt-source.list /etc/apt/sources.list.d/nvidia-l4t-apt-source.list.bak
```

然后编辑软件源列表文件

```Plaintext
sudo nano /etc/apt/sources.list.d/nvidia-l4t-apt-source.list
```

将文件中的 main 修改为 main oldrelease，或者直接注释掉所有内容，保存退出。

然后禁用自动更新功能

编辑自动更新配置文件：

```Plaintext
sudo nano /etc/apt/apt.conf.d/20auto-upgrades
```

将文件中的 `APT::Periodic::Update-Package-Lists` 和 `APT::Periodic::Unattended-Upgrade` 都设置为 `0`，保存后退出。

对于关键的 Jetson Linux 软件包，使用 `apt-mark hold` 命令将其锁定在当前版本：

```Plaintext
sudo apt-mark hold nvidia-l4t-boot nvidia-l4t-secboot nvidia-l4t-kernel nvidia-l4t-kernel-dtbs
```

若之后想取消锁定，可使用以下命令：

```Plaintext
sudo apt-mark unhold nvidia-l4t-boot nvidia-l4t-secboot nvidia-l4t-kernel nvidia-l4t-kernel-dtbs
```

修改内核版本：

使用命令 `uname -r` 查看当前内核版本，确认是 36\.4\.3。然后编辑 `/etc/default/grub` 文件，找到 `GRUB_CMDLINE_LINUX_DEFAULT` 这一行，在其值的最后添加 `modules=load`，保存退出后运行以下命令更新 GRUB 配置：

```Plaintext
sudo update-grub
```

#### \<2\>树莓派4B

属于arm架构，请注意后续使用

#### \<3\>虚拟机

属于amd架构，请注意后续使用

### 二，jetson orin nano super

#### 1\.yolov5配置部分

直接在源码中进行更改，需要python基础。

#### 2\.jsoncpp安装时，容易出现的问题:You might want to run 'apt \-\-fix\-broken install' to correct these\. The following packages have unmet dependencies: libcudnn9\-dev\-cuda\-12 : Depends: libcudnn9\-cuda\-12 \(= 9\.10\.2\.21\-1\) but 9\.6\.0\.74\-1 is to be installed

依赖问题,运行`sudo apt --fix-broken install`尝试解决问题

### 三，ros2的相关命令介绍以及使用方式

#### 1\. 编译指令：colcon build

编译指令，通常在工作空间下使用，用于将功能包进行编译，生成可执行文件或命令

##### 1\.1 基本构建命令

###### 1\.1 \.1 构建整个工作空间

```Plaintext
colcon build
```

###### 1\.1 \.2 构建特定包

```Plaintext
colcon build --packages-select <package_name>
```

###### 1\.1\.3 构建多个特定包

```Plaintext
colcon build --packages-select <package1> <package2>
```

###### 1\.1\.4 构建除指定包外的所有包

```Plaintext
colcon build --packages-ignore <package_name>
```

###### 1\.1\.5 跳过特殊功能包

```Plaintext
colcon build --packages-skip nav2_system_tests
nav2_system_tests为功能包名称
```

###### 1\.1\.6 顺序编译模式

```Plaintext
colcon build --executor sequential
```

##### 1\.2 常用选项和参数

###### 1\.2\.1 并行构建（默认使用所有CPU核心）

```Plaintext
colcon build --parallel-workers <number>
colcon build --parallel-workers 4  # 使用4个核心        
```

###### 1\.2\.2 构建时包含符号链接（推荐用于开发）

```Plaintext
colcon build --symlink-install
```

###### 1\.2\.3 合并安装目录（减少文件数量）

```Plaintext
colcon build --merge-install
```

###### 1\.2\.4 指定构建目录

```Plaintext
colcon build --build-base <build_directory>
```

###### 1\.2\.5 指定安装目录

```Plaintext
colcon build --install-base <install_directory>
```

##### 1\.3 高级构建选项

###### 1\.3\.1 仅配置而不构建（检查CMake配置）

```Plaintext
colcon build --cmake-target configure
```

###### 1\.3\.2 强制重新配置CMake

```Plaintext
colcon build --cmake-force-configure
```

###### 1\.3\.3 构建时包含测试

```Plaintext
colcon build --cmake-args -DBUILD_TESTING=ON
```

###### 1\.3\.4 传递CMake参数

```Plaintext
colcon build --cmake-args -DCMAKE_BUILD_TYPE=Debug
```

###### 1\.3\.5 传递其他构建系统参数

```Plaintext
colcon build --ament-cmake-args -DCMAKE_BUILD_TYPE=Release
```

##### 1\.4 包管理相关

###### 1\.4\.1 构建包及其依赖

```Plaintext
colcon build --packages-up-to <package_name>
```

###### 1\.4\.2 跳过依赖检查

```Plaintext
colcon build --packages-select <package_name> --mixin skip-build-deps
```

###### 1\.4\.5 仅构建已更改的包

```Plaintext
colcon build --packages-select <changed_package>
```

##### 1\.5 事件处理器和输出控制

###### 1\.5\.1 显示详细的构建输出

```Plaintext
colcon build --event-handlers console_direct+
```

###### 1\.5\.2 静默构建（仅显示错误）

```Plaintext
colcon build --event-handlers console_cohesion+
```

###### 1\.5\.3 在构建完成后显示总结

```Plaintext
colcon build --event-handlers console_cohesion+ console_package_list+
```

##### 1\.6 常用组合命令

###### 1\.6\.1 开发时常用组合

```Plaintext
colcon build --symlink-install --packages-select <package_name> --event-handlers console_direct+
```

###### 1\.6\.2 完整构建工作空间

```Plaintext
colcon build --symlink-install --merge-install --event-handlers console_cohesion+
```

###### 1\.6\.3 调试构建

```Plaintext
colcon build --cmake-args -DCMAKE_BUILD_TYPE=Debug --event-handlers console_direct+
```

##### 1\.7 清理命令

###### 1\.7\.1 清理构建产物

```Plaintext
colcon build --cmake-clean-first
```

###### 1\.7\.2 删除构建目录

```Plaintext
rm -rf build/ install/ log/
```

###### 1\.7\.3 清理特定包的构建

```Plaintext
colcon build --packages-select <package_name> --cmake-clean-first
```

##### 1\.8 实际使用示例

###### 1\.8\.1 首次构建工作空间

```Plaintext
cd ~/ros2_ws
colcon build --symlink-install
```

###### 1\.8\.2 构建特定包并查看详细输出

```Plaintext
colcon build --packages-select my_robot --symlink-install --event-handlers console_direct+
```

###### 1\.8\.3 调试构建

```Plaintext
colcon build --packages-select my_robot --cmake-args -DCMAKE_BUILD_TYPE=Debug
```

###### 1\.8\.4 构建并运行测试

```Plaintext
colcon build --packages-select my_robot --cmake-args -DBUILD_TESTING=ON
colcon test --packages-select my_robot
```

#### 2\.功能包创建：ros2 pkg create

##### 2\.1 创建工作空间，并进入

```Plaintext
mkdir -p ros2_ws/src && cd ros2_ws/src
```

##### 2\.2 基本语法

```Plaintext
ros2 pkg create <package-name> [options]
```

###### 2\.2\.1 指定构建类型

```Plaintext
--build-type        #这是最关键的选项，它决定了包的编译和安装方式。

--build-type ament_cmake：用于 C++ 项目。这是标准 CMake 项目的变体，集成了 ROS2 的依赖系统。

--build-type ament_python：用于 Python 项目。使用 Python 的 setuptools 进行构建。

--build-type cmake：用于纯 CMake 项目（不推荐，除非有特殊理由）。
注意：如果不指定，默认是 ament_cmake。
```

###### 2\.2\.2 设置依赖

```Plaintext
设置依赖 --dependencies

在创建包时直接声明依赖项，这些依赖会被自动添加到 package.xml 中。

--dependencies [dep1 dep2 ...]：列出所有依赖的 ROS2 包名。

例如：--dependencies rclcpp std_msgs geometry_msgs 会帮你把 rclcpp 等写入 package.xml
```

###### 2\.2\.3 指定节点名

```Plaintext
仅适用于 ament_cmake 和 ament_python， 在创建包的同时，生成一个简单的可执行节点源代码模板。

--node-name <node_name>：指定节点的名字，命令会为你生成一个简单的 Hello World 节点源文件。

对于 ament_cmake：会在 src/ 目录下生成一个 <node_name>.cpp 文件，并在 CMakeLists.txt 中添加相应的可执行目标和安装指令。

对于 ament_python：会在 /<package_name>/ 目录下生成一个 <node_name>.py 文件，并在 setup.py 中注册该节点为控制台脚本。
```

###### 2\.2\.4 库名

```Plaintext
库名 --library-name
（仅适用于 ament_cmake） 指定要创建的 C++ 库的名字。这会在 src/ 目录下生成库的源文件和头文件模板，并配置 CMakeLists.txt 来构建该库。

--library-name <library_name>
```

###### 2\.2\.5 许可证

```Plaintext
许可证 --license
设置包的许可证，默认是 Apache License 2.0。

--license <LICENSE>：例如 --license MIT。
```

###### 2\.2\.6 常用使用示例

###### 2\.2\.6\.1 示例 1：创建一个基本的 C\+\+ 包，并带有一个节点

```Plaintext
ros2 pkg create my_cpp_pkg --build-type ament_cmake --node-name my_cpp_node --dependencies rclcpp std_msgs

生成结果：目录结构
my_cpp_pkg/
├── CMakeLists.txt
├── include/my_cpp_pkg/
├── package.xml
├── src
│   └── my_cpp_node.cpp # 自动生成的节点源文件
└── test/

package.xml 中已包含 <depend>rclcpp</depend> 和 <depend>std_msgs</depend>
CMakeLists.txt 中已包含构建 my_cpp_node 可执行文件的指令。
```

###### 2\.2\.6\.2 示例2：创建一个 Python 包，并带有一个节点

```Plaintext
ros2 pkg create my_py_pkg --build-type ament_python --node-name my_py_node --dependencies rclpy std_msgs

生成结果：目录结构
my_py_pkg/
├── package.xml
├── resource/my_py_pkg
├── setup.cfg
├── setup.py
├── test/
└── my_py_pkg/
    ├── __init__.py
    └── my_py_node.py # 自动生成的节点源文件

package.xml 中已包含依赖。
setup.py 中已注册 console_scripts 入口点：my_py_node = my_py_pkg.my_py_node:main。
```

###### 2\.2\.6\.3 示例3：创建一个空的 C\+\+ 库包

```Plaintext
ros2 pkg create my_cpp_lib --build-type ament_cmake --library-name my_lib

在 src/ 和 include/my_cpp_lib/ 目录下会生成库的源文件和头文件模板。
CMakeLists.txt 中配置了构建 my_lib 库的指令。
```

##### 2\.3 关键注意事项

###### 2\.3\.1 包名有效性：

```Plaintext
包名只能包含小写字母、数字和下划线。
包名必须以字母开头，不能以数字或下划线开头。
遵循这些规则可以避免在编译和依赖解析时出现意外错误。
```

###### 2\.3\.2 构建类型选择：

```Plaintext
C++ 项目用 ament_cmake。
Python 项目用 ament_python。
不要混用。一个包通常只包含一种构建类型的项目。虽然技术上可以混合，但非常不推荐，会增加复杂性。
```

###### 2\.3\.3 依赖管理：

```Plaintext
使用 --dependencies 只是帮你把依赖名写入 package.xml。你仍然需要确保这些依赖包已经安装在你的工作空间中或系统里。
创建包后，务必检查 package.xml，确保依赖关系正确无误。
```

###### 2\.3\.4 创建后的必要步骤：

```Plaintext
ros2 pkg create 只是搭建了骨架。你必须编辑自动生成的文件（如 package.xml 中的描述、作者、邮箱等信息）来实现你的业务逻辑。
对于 ament_cmake 包，如果你手动添加了新的源文件，需要相应地更新 CMakeLists.txt。
对于 ament_python 包，如果你手动添加了新的 Python 模块或节点，需要更新 setup.py 中的 package_dir、packages 和 entry_points 等部分。
```

###### 2\.3\.5 编译测试：

```Plaintext
创建包后，在工作空间的根目录下运行 colcon build 来尝试编译，确保没有基础配置错误。
使用 colcon build --packages-select <your-package-name> 可以只编译你新创建的包，节省时间。
```

###### 2\.3\.6 版本控制：

```Plaintext
建议在 package.xml 中设置正确的 <version> 标签。初始创建时通常是 0.0.0。
```

#### 3\. 话题查看指令：ros2 topic

话题查看指令，通常在加载消息格式的终端下使用，用于查看话题信息

##### 3\.1 查看话题信息

|命令|功能|示例|
|---|---|---|
|`ros2 topic list`|列出当前所有活跃的话题|`ros2 topic list`|
|`ros2 topic list -t`|列出话题及其消息类型|`ros2 topic list -t`|
|`ros2 topic type <topic_name>`|查看指定话题的消息类型|`ros2 topic type /turtle1/cmd_vel`|
|`ros2 topic info <topic_name>`|查看话题的发布者/订阅者数量及类型|`ros2 topic info /turtle1/cmd_vel`|

##### 3\.2 监听话题数据

|命令|功能|示例|
|---|---|---|
|`ros2 topic echo <topic_name>`|实时打印话题消息内容|`ros2 topic echo /turtle1/cmd_vel`|
|`ros2 topic echo <topic_name> --csv`|以 CSV 格式输出|`ros2 topic echo /topic --csv`|
|`ros2 topic echo <topic_name> --field <field>`|只输出指定字段|`ros2 topic echo /topic --field data`|

##### 3\.3 向话题发布数据

|命令|功能|示例|
|---|---|---|
|`ros2 topic pub <topic> <msg_type> '<args>'`|持续发布消息（默认 1Hz）|见下方示例|
|`ros2 topic pub --once <topic> <msg_type> '<args>'`|只发布一次消息|见下方示例|
|`ros2 topic pub --rate <N> <topic> <msg_type> '<args>'`|按指定频率（Hz）发布|见下方示例|
|`ros2 topic pub -1 <topic> <msg_type> '<args>'`|同 `--once`，发布一次后退出|见下方示例|

```Bash
# 发布一次 Twist 消息（让海龟移动）
ros2 topic pub --once /turtle1/cmd_vel geometry_msgs/msg/Twist "{linear: {x: 2.0, y: 0.0, z: 0.0}, angular: {x: 0.0, y: 0.0, z: 1.8}}"

# 以 10Hz 持续发布
ros2 topic pub --rate 10 /turtle1/cmd_vel geometry_msgs/msg/Twist "{linear: {x: 1.0, y: 0.0, z: 0.0}, angular: {x: 0.0, y: 0.0, z: 0.0}}"

# 发布字符串消息
ros2 topic pub /chatter std_msgs/msg/String "{data: 'Hello ROS2'}"
```

##### 3\.4 话题性能统计

|命令|功能|示例|
|---|---|---|
|`ros2 topic hz <topic_name>`|查看话题的发布频率（Hz）|`ros2 topic hz /turtle1/pose`|
|`ros2 topic bw <topic_name>`|查看话题的带宽占用|`ros2 topic bw /turtle1/pose`|

##### 3\.5 查找与接口查看

|命令|功能|示例|
|---|---|---|
|`ros2 topic find <msg_type>`|查找使用指定消息类型的所有话题|`ros2 topic find geometry_msgs/msg/Twist`|
|`ros2 interface show <msg_type>`|查看消息类型的详细结构定义|`ros2 interface show geometry_msgs/msg/Twist`|

##### 3\.6 常用选项速查

|选项|说明|
|---|---|
|`-t`|显示消息类型（配合 `list` 使用）|
|`--once` / `-1`|只发布一次消息|
|`--rate <N>`|设置发布频率为 N Hz|
|`--csv`|以 CSV 格式输出数据|
|`--field <field>`|只输出消息中的指定字段|
|`--qos-profile <profile>`|指定 QoS 策略|
|`--qos-depth <N>`|设置 QoS 队列深度|

##### 3\.7 典型调试流程示例

```Markdown
# 1. 启动海龟模拟器
ros2 run turtlesim turtlesim_node

# 2. 查看当前话题
ros2 topic list

# 3. 查看 /turtle1/cmd_vel 的消息类型
ros2 topic type /turtle1/cmd_vel

# 4. 查看 Twist 消息结构
ros2 interface show geometry_msgs/msg/Twist

# 5. 监听当前控制指令
ros2 topic echo /turtle1/cmd_vel

# 6. 手动发布指令让海龟转圈
ros2 topic pub --rate 1 /turtle1/cmd_vel geometry_msgs/msg/Twist "{linear: {x: 2.0, y: 0.0, z: 0.0}, angular: {x: 0.0, y: 0.0, z: 1.8}}"

# 7. 查看发布频率
ros2 topic hz /turtle1/pose
```

#### 4\.节点信息 ros2 node

##### 4\.1 查看节点列表

```Plain Text
# 列出当前运行的所有节点
ros2 node list

# 显示更详细的信息（包括命名空间）
ros2 node list --all
```

##### 4\.2 查看节点信息

```Plain Text
# 查看某个节点的详细信息（订阅、发布、服务、动作等）
ros2 node info /node_name

# 示例
ros2 node info /turtlesim
```

##### 4\.3 运行节点

```Markdown
# 基本方式
ros2 run <package_name> <executable_name>

# 示例：运行 turtlesim
ros2 run turtlesim turtlesim_node

# 指定节点名称（重映射）
ros2 run turtlesim turtlesim_node --ros-args --remap __node:=my_turtle

# 指定命名空间
ros2 run turtlesim turtlesim_node --ros-args --remap __ns:=/my_ns

# 同时指定名称和命名空间
ros2 run turtlesim turtlesim_node --ros-args --remap __node:=my_turtle --remap __ns:=/my_ns
```

##### 4\.4 使用launch运行多个节点

```Plain Text
# 运行 launch 文件
ros2 launch <package_name> <launch_file>

# 示例
ros2 launch turtlesim multisim.launch.py
```

##### 4\.5 关闭节点，终止节点运行

```Plain Text
# 方式1：在运行节点的终端按 Ctrl+C

# 方式2：通过进程管理（查找并 kill）
ps aux | grep ros2
kill <PID>
```

##### 4\.6 实际操作下的组合使用

```Markdown
# 过滤查看特定命名空间下的节点
ros2 node list | grep /my_ns

# 查看节点信息并分页显示
ros2 node info /turtlesim | less

# 配合 ros2 topic 使用：先找节点，再看它发布/订阅的话题
ros2 node info /turtlesim
ros2 topic echo /turtle1/pose
```

#### 5

#### 6\. 

#### 7\. 查看TF关系

通常情况下，完整版ros是已经安装tf\_tools了，若无法使用tf相关命令，可先安装tf\_tools

```Plaintext
sudo apt install ros-$ROS_DISTRO-tf2-tools
```

##### 3\.1\. 快速看一眼整棵 tf 树

|场景|命令|例子|
|---|---|---|
|生成树状图（PDF）|`ros2 run tf2_tools view_frames`|会在当前目录得到 `frames.pdf`|
|终端打印树（无图）|`ros2 run tf2_tools tf2_tree`|适合远程 SSH|

##### 3\.2\. 查两个坐标系之间的实时变换

|场景|命令|例子|
|---|---|---|
|持续监听|`ros2 run tf2_ros tf2_echo <parent> <child>`|`ros2 run tf2_ros tf2_echo odom base_link`|
|只查最新一次|`ros2 run tf2_ros tf2_echo <parent> <child> --once`|同上，加 `--once`|

##### 3\.3\. 查“此刻”整个系统所有坐标系

|场景|命令|例子|
|---|---|---|
|列出所有 frame|`ros2 run tf2_ros tf2_monitor`|终端实时刷新，带频率、延迟|
|只列名字|`ros2 run tf2_ros tf2_monitor --no-delta`|不刷表格，仅名单|

##### 3\.4\.手动发布一个静态坐标（调试/临时补缺口）

|场景|命令|例子|
|---|---|---|
|列出所有 frame|`ros2 run tf2_ros tf2_monitor`|终端实时刷新，带频率、延迟|
|只列名字|`ros2 run tf2_ros tf2_monitor --no-delta`|不刷表格，仅名单|

##### 3\.5\. 把 tf 录进 bag / 回放

|场景|命令|例子|
|---|---|---|
|录全部 tf|`ros2 bag record /tf /tf_static`|通常再叠加 `/odom` `/scan` 等话题|
|回放并重映射|`ros2 bag play xxx.db3 --remap /tf:=/tf_old`|避免与在线系统冲突|

##### 3\.6\. 检查“tf 过期”故障

|场景|命令|例子|
|---|---|---|
|看时间差|`tf2_echo` 里 `timestamp` 与系统时间对比|差值 \> cache\-time 就会报错 “Lookup would require extrapolation”|
|加大缓存|在节点里 `buffer_.setCacheDuration(rclcpp::Duration(60, 0));`|C\+\+ 例，Python 同理|

##### 3\.7\. 可视化调试（GUI）

|工具|用法|
|---|---|
|rviz2|左侧 Displays → Add → TF，可勾选 show names、arrows，一眼看全树|
|foxglove|打开“3D”面板，同样支持 TF 显示，比 rviz2 轻量，网页即可用|

##### 3\.8\. 自己写代码查 tf（Python 最小模板）

```Plaintext
import rclpy
from tf2_ros import Buffer, TransformListener
rclpy.init()
node = rclpy.create_node('tf_demo')
buf = Buffer()
_ = TransformListener(buf, node)
rclpy.spin_once(node, timeout_sec=1.0)  # 给 buffer 时间接数据
try:
    t = buf.lookup_transform('parent', 'child', rclpy.time.Time())
    print(t.transform.translation)
except Exception as e:
    print('还没收到 tf：', e)
```

#### 8\.调试工具：rqt

##### 4\.1\. 启动方式

```Plain Text
# 方法1：启动rqt主界面
rqt

# 方法2：直接启动特定插件
rqt --standalone <plugin_name>

# 方法3：从命令行启动带特定配置的rqt
rqt --perspective-file <perspective_file>
```

##### 4\.2\. 常用rqt插件及使用方法

###### 4\.2\.1\.  rqt\_graph \- 节点拓扑图

```Plain Text
# 启动
rqt_graph
# 或
ros2 run rqt_graph rqt_graph

功能：
显示节点、话题、服务之间的连接关系
实时更新系统状态
可过滤显示内容
```

###### 4\.2\.2\.  rqt\_console \- 日志查看器

```Plain Text
# 启动
rqt_console
# 或
ros2 run rqt_console rqt_console

功能：
查看ROS2节点的日志消息
按严重级别过滤（Fatal, Error, Warn, Info, Debug）
搜索和过滤特定消息
保存日志到文件
```

###### 4\.2\.3\.  rqt\_plot \- 数据绘图

```Plain Text
# 启动
rqt_plot
# 或
ros2 run rqt_plot rqt_plot

使用方法：
在话题输入框中输入要绘制的话题
支持绘制消息中的特定字段，如：/topic/field

示例：
/turtle1/pose/x
/turtle1/pose/y
/joint_states/position[0]
```

###### 4\.2\.4\.   rqt\_image\_view \- 图像查看

```Plain Text
# 启动
rqt_image_view
# 或
ros2 run rqt_image_view rqt_image_view

功能：
查看相机话题发布的图像
支持保存图像
可切换不同图像话题
```

###### 4\.2\.5\. rqt\_publisher \- 话题发布工具

```Plain Text
# 启动
rqt_publisher
# 或
ros2 run rqt_publisher rqt_publisher

功能：
手动向话题发布消息
配置消息类型和内容
设置发布频率
```

###### 4\.2\.6\. rqt\_service\_caller \- 服务调用工具

```Plain Text
# 启动
rqt_service_caller
# 或
ros2 run rqt_service_caller rqt_service_caller

功能：
调用ROS2服务
填充请求参数
查看服务响应
```

###### 4\.2\.7\. rqt\_reconfigure \- 动态参数配置

```Plain Text
# 启动
rqt_reconfigure
# 或
ros2 run rqt_reconfigure rqt_reconfigure

功能：
动态修改节点的参数
支持整数、浮点数、布尔值、字符串等类型
实时生效
```

###### 4\.2\.8\. rqt\_bag \- 数据记录与回放

```Plain Text
# 启动
rqt_bag
# 或
ros2 run rqt_bag rqt_bag

功能：
录制ROS2话题数据
回放录制的bag文件
可视化消息内容
```

##### 4\.3\. rqt的常用实用技巧

###### 4\.3\.1\. 保存和加载工作区配置

```Plain Text
在rqt中配置好需要的插件布局
点击 Perspectives → Save Current As...
输入配置名称保存
下次可通过 Perspectives → 选择保存的配置快速恢复
```

###### 4\.3\.2\. 插件管理

```Plain Text
通过 Plugins 菜单添加或移除插件
拖拽插件标签页可重新排列布局
双击插件标签页可最大化/恢复
```

###### 4\.3\.3\. 命令行快捷方式

```Plain Text
# 同时启动多个常用插件
rqt --standalone rqt_graph &
rqt --standalone rqt_console &
rqt --standalone rqt_plot &

# 使用屏幕分割工具同时显示多个rqt窗口
terminator -l rqt_layout
```

##### 4\.4\. 故障排除

###### 4\.4\.1\. 插件不显示数据

```Plain Text
检查ROS2环境变量是否正确设置
确认相关节点正在运行
检查话题名称是否正确
```

###### 4\.4\.2\. rqt启动缓慢

```Plain Text
关闭不必要的插件
检查网络连接
```

###### 4\.4\.3\. 特定插件无法启动

```Plain Text
确认插件已安装：ros2 pkg list | grep rqt
重新安装缺失的插件
```

##### 4\.5\. 自定义插件开发

如果需要扩展rqt功能，可以开发自定义插件：

```Python
#!/usr/bin/env python3
import rclpy
from rqt_gui_py.plugin import Plugin
from python_qt_binding import loadUi
from python_qt_binding.QtWidgets import QWidget

class MyCustomPlugin(Plugin):
    def __init__(self, context):
        super(MyCustomPlugin, self).__init__(context)
        self._widget = QWidget()
        # 加载UI文件或手动创建界面
```

#### 9\.留存备用

### 四，关于代码风格

在进行 ROS 2 C\+\+ 开发时，保持一致、清晰的代码风格（Code Style）和良好的架构（Architecture），是确保项目**可读性、可维护性和协作效率**的关键。

#### 1\.ROS 2 C\+\+ 代码风格与规范

ROS 2 官方和主要的维护者（如 Open Robotics）在 C\+\+ 风格上主要遵循 **Google C\+\+ Style Guide**，但在某些方面进行了调整，以适应 ROS 生态和易读性。

##### 1\.文件命名 \(File Naming\)

##### 2\.括号与缩进 \(Braces and Indentation\)

**缩进：** 统一使用 **2 个空格**。

**左花括号 ****`{`****：** 位于函数名或控制语句（`if`, `for`, `while`）的**同一行**，空格分隔。

```Plain Text
**示例：**
void someFunction(int value) {
  if (value > 10) {
    // ... 缩进 2 个空格
  } else {
    // ...
  }
}
```

##### 3\.注释 \(Comments\)

**文件注释：** 在文件开头提供版权、作者、许可和文件功能描述。

**代码注释：** 优先使用 `//` 进行单行注释。

**Doxygen 文档：** 推荐使用 Doxygen 风格的注释来记录类、函数、参数和返回值，以便自动生成文档。

```Java
示例：
/**
 * @brief 计算PID控制输出。
 * @param setpoint 目标值。
 * @param measurement 实际测量值。
 * @return 控制输出量。
 */
double calculate(double setpoint, double measurement);
```

##### 4\.命名空间与头文件

**头文件保护：** 优先使用 `#pragma once`，其次是传统的宏定义。

**命名空间：** 使用有意义的命名空间（如 `namespace my_robot::control {}`）。在 `.cpp` 文件中可以使用 `using namespace`，但在 `.hpp` 文件中**严禁使用** `using namespace`，避免污染全局。

#### 2\.核心架构：算法类与 ROS 节点的解耦

##### 2\.1为什么要解耦？

##### 2\.2职责分离原则 \(Single Responsibility Principle\)

##### 2\.3 代码实现结构 \(示例\)

```C++
// =========================================================
// 1. PIDController Class (无 ROS 依赖的纯 C++ 算法)
// =========================================================
// 依赖: <cmath>, <algorithm>

class PIDController {
    // ... 仅包含算法和状态 ...
};

// =========================================================
// 2. PIDNode Class (ROS 依赖的通信中枢)
// =========================================================
// 依赖: "rclcpp/rclcpp.hpp", "pid_controller.hpp"

class PIDNode : public rclcpp::Node {
public:
    // 构造函数中初始化 ROS 订阅、发布和定时器
    PIDNode() {
        // 实例化算法类
        pid_ = std::make_unique<PIDController>(...);
        // ... 创建订阅、定时器 ...
    }
private:
    // 定时器回调函数，负责 ROS 数据与算法的传递
    void controlLoop() {
        // 从 ROS 变量中获取输入
        double output = pid_->calculate(target_, current_state_, dt_);
        // 将结果发布到 ROS 话题
    }

    // 存储算法实例
    std::unique_ptr<PIDController> pid_;
};

```

##### 2\.4 关键注意事项

###### 2\.4\.1 避免在头文件中使用 `using namespace`

**错误示例 \(在 ****`.hpp`**** 中\)：**

```C++
// pid_controller.hppusing namespace std; // ❌ 污染了包含此头文件的所有其他文件
```

**正确做法：** 在头文件中，要么使用完整的命名空间（`std::vector`），要么只在 `.cpp` 文件中使用 `using namespace std;`。

###### 2\.4\.2 使用智能指针管理资源

使用 `std::unique_ptr` 管理 ROS 节点中的算法对象（如 `pid_`）。

使用 ROS 提供的 `SharedPtr` 管理消息（例如 `const std_msgs::msg::Float64::SharedPtr msg`）。

###### 2\.4\.3 性能优化：定时器与数据锁

**控制频率：** 确保您的定时器回调频率（控制率）与您的 `dt_` 变量一致。这是保证 PID 积分项和微分项准确性的基础。

**实时性：** 对于高性能或实时性要求高的控制，请尽量避免在控制循环（`controlLoop`）中进行内存分配（如频繁创建 `std::string` 或动态容器）。

###### 2\.4\.4 使用 `a_on_set_parameters_callback` 进行动态调参

这是 ROS 2 调参的标准方法。它比读取 YAML 文件更高效，因为它允许在程序运行时，使用 `rqt_reconfigure` 或 `ros2 param set` 实时调整 对应的参数，比如PID调参。



## 第二章 jazzy相关问题

### 1\.功能包的创建（格式测试）

先创建工作空间，并进入

```Plaintext
mkdir -p ros2_ws/src && cd ros2_ws/src #请注意一定要进入到src文件夹下再进行功能包的创建
```

创建对应的功能包

```Plaintext
ros2 pkg create --build-type ament_cmake ping_publisher
```

### 2，E: dpkg was interrupted, you must manually run 'sudo dpkg \-\-configure \-a' to correct the problem\.

包管理器问题，通常是上次使用的时候被中断了，直接运行

`sudo dpkg --configure -a`

等待运行完成即可

### 3\.serial\_driver库的相关问题

默认安装的jazzy是没有安装serial\_driver库文件，需要自己安装，直接通过apt进行安装即可。

```Plaintext
sudo apt update
```

```Plaintext
sudo apt install ros-jazzy-serial-driver
```

需要更新环境后使用。

```Plaintext
source /opt/ros/jazzy/setup.bash
```

### 4\.io\_context问题

serial\_driver依赖于io\_context

io\_context依赖于asio\_cmake\_module

默认安装的jazzy同样没有安装asio\_cmake\_module，需要自己安装。

通常报错信息的关键部分为：

```Plaintext
Could not find a package configuration file provided by "asio_cmake_module"
```

解决方案：

安装缺失的asio\_cmake\_module，直接通过apt进行安装即可。

```Plaintext
sudo apt update
```

```Plaintext
sudo apt install ros-jazzy-asio-cmake-module
```

### 5\.配置文件

在功能包下先创建config文件夹

### 6\.启动文件

在功能包下先创建launch文件夹

### 7\.创建设备端口别名

#### 7\.1 方案1：对插入的USB口进行别名设计，即对物理端口进行别名创建。

1\.先查看对应的端口号

```Plaintext
udevadm info -a -n /dev/ttyUSB0
```

2\.创建并编辑规则文件

```Plaintext
sudo vi /etc/udev/rules.d/my_serial.rules
```

3\.添加内容

```Plaintext
# 1-2.1 端口 → wheeltec_controller
SUBSYSTEM=="tty", KERNELS=="1-2.1", SYMLINK+="wheeltec_controller1", MODE="0666", GROUP="dialout"

# 1-2.2 端口 → wheeltec_controller
SUBSYSTEM=="tty", KERNELS=="1-2.2", SYMLINK+="wheeltec_controller2", MODE="0666", GROUP="dialout"

# 1-2.3 端口 → wheeltec_controller
SUBSYSTEM=="tty", KERNELS=="1-2.3", SYMLINK+="wheeltec_controller3", MODE="0666", GROUP="dialout"

# 1-2.4 端口 → motor_node
SUBSYSTEM=="tty", KERNELS=="1-2.4", SYMLINK+="motor_node", MODE="0666", GROUP="dialout"
```

4\.重载规则文件

```Plaintext
sudo udevadm control --reload-rules
sudo service udev restart
sudo udevadm trigger
```

#### 7\.2 方案2：对不同设备的USB编码名称进行别名设计。

### 8\.开机开始执行任务程序流程

方案1：

1\.创建启动脚本

新建脚本文件，如（ros2\_launch\.sh），内容如下：

```Plaintext
#!/bin/bash
# 加载ROS2的环境
source /opt/ros/jazzy/setup.bash
source ~/driver_ws/install/setup.bash
source ~/ros2_ws/install/setup.bash
source ~/tennis_serial/install/setup.bash

#sleep 100
sleep 60

# 节点检测函数
function wait_for_node() {
    local node_name=$1
    local timeout=$2
    echo "等待节点 $node_name 启动 (最多 $timeout 秒)..."
    
    for ((i=0; i<timeout; i++)); do
        if ros2 node list | grep -wq "$node_name"; then
            echo "节点 $node_name 已检测到"
            return 0
        fi
        sleep 1
    done
    
    echo "超时：未检测到节点 $node_name"
    return 1
}

# 话题检测函数
function wait_for_topic() {
    local topic=$1
    local timeout=$2
    echo "等待话题 $topic 出现 (最多 $timeout 秒)..."

    for ((i=0; i<timeout; i++)); do
        if ros2 topic list | grep -wq "$topic"; then
            echo "话题 $topic 已检测到"
            return 0
        fi
        sleep 1
    done
    
    echo "超时：未检测到话题 $topic"
    return 1
}

echo "===== 开始启动系统 ====="

# 任务1: 串口节点
ros2 run tennis_serial serial_node &
echo "任务1: serial_node 启动完成"
wait_for_node "/serial_node" 5 || echo "警告: 串口节点可能未完全启动"

# 任务2: 发布标志位（在serial_node启动后立即发布）
ros2 topic pub /flag std_msgs/msg/Bool "{data: true}" --once
echo "任务2: /flag 已发布"

# 任务3: 底盘驱动
ros2 run yahboomcar_bringup Mcnamu_driver_X3 &
echo "任务3: Mcnamu_driver_X3 启动完成"
wait_for_node "/driver_node" 5 || echo "警告: 底盘驱动节点可能未完全启动"

# 验证底盘驱动话题
wait_for_topic "/cmd_vel" 3 || echo "警告: 底盘控制话题未检测到"
wait_for_topic "/imu/data_raw" 3 || echo "警告: IMU话题未检测到"

# 任务4: 相机启动
ros2 launch astra_camera astra_pro.launch.py &
echo "任务4: astra_camera 启动完成"

# 等待相机话题出现（相机启动需要时间）
wait_for_topic "/camera/color/image_raw" 10 || echo "警告: 相机彩色图像话题未检测到"
wait_for_topic "/camera/depth/image_raw" 10 || echo "警告: 相机深度图像话题未检测到"
sleep 2  # 额外等待确保相机完全初始化

# 任务5: 目标检测
ros2 launch yolo_detection_node tennis_detector.launch.py &
echo "任务5: tennis_detector 启动完成"
wait_for_node "/yolo_tennis_detector" 8 || echo "警告: 目标检测节点可能未完全启动"
sleep 5  # 额外等待确保相机完全初始化

# 验证目标检测输出话题
wait_for_topic "/new_xy_point" 5 || echo "警告: 检测坐标话题未检测到"
wait_for_topic "/yolo_annotated_image" 5 || echo "警告: 标注图像话题未检测到"

# 任务6: 深度图读取
ros2 launch depth_pixel_reader depth_pixel_reader.launch.py &
echo "任务6: depth_pixel_reader 启动完成"
wait_for_node "/depth_pixel_reader" 5 || echo "警告: 深度读取节点可能未完全启动"

# 验证深度输出话题
wait_for_topic "/detection_depth_info" 5 || echo "警告: 深度信息话题未检测到"

# 任务7: 控制节点
ros2 launch tennis_control tennis_control.launch.py &
echo "任务7: tennis_control 启动完成"
wait_for_node "/tennis_controller" 10 || echo "警告: 控制节点可能未完全启动"

# 验证控制节点话题
wait_for_topic "/scan_status" 5 || echo "警告: 扫描状态话题未检测到"

echo "===== 所有任务已启动! ====="

# 显示当前运行的节点和关键话题
echo -e "\n当前运行的节点:"
ros2 node list

echo -e "\n关键话题列表:"
ros2 topic list | grep -E "flag|cmd_vel|imu|image_raw|new_xy_point|yolo_annotated|detection_depth_info|scan_status"
```

2\.给执行权限

```Plaintext
chmod +x ~/Desktop/tennis_launch.sh
```

3\.创建systemd服务文件

```Plaintext
sudo vim /etc/systemd/system/tennis_auto.service
```

内容如下：

```Plaintext
[Unit]
Description=Tennis Robot Auto Launch Service
After=network.target

[Service]
Type=simple
User=jetson
WorkingDirectory=/home/jetson
ExecStart=/home/jetson/Desktop/tennis_launch.sh
Restart=on-failure
RestartSec=5s
StandardOutput=file:/var/log/tennis_auto.log
StandardError=inherit

[Install]
WantedBy=multi-user.target
```

4\.重载systemd配置

```Plaintext
sudo systemctl daemon-reload
```

5\.启用服务

```Plaintext
sudo systemctl enable tennis_auto.service
```

6\.启动服务

```Plaintext
sudo systemctl start tennis_auto.service
```

7\.验证服务状态

```Plaintext
sudo systemctl status tennis_auto.service
```

8\.停止服务

```Plaintext
sudo systemctl stop tennis_auto.service
```

9\.检查日志输出

查看实时日志

```Plaintext
sudo tail -f /var/log/tennis_auto.log
```

或查看完整日志

```Plaintext
sudo journalctl -u tennis_auto.service -b
```

10\.禁用开机自启程序

```Plaintext
sudo systemctl disable ros2_auto.service
```

### 9\.安装OpenCV

安装依赖项

```Plaintext
sudo apt update
sudo apt install -y build-essential cmake git libgtk2.0-dev pkg-config libavcodec-dev libavformat-dev libswscale-dev
sudo apt install -y libtbb2 libtbb-dev libjpeg-dev libpng-dev libtiff-dev libdc1394-22-dev
```

下载 OpenCV 4\.10\.0

```Plaintext
mkdir ~/opencv_build && cd ~/opencv_build
git clone https://github.com/opencv/opencv.git
cd opencv
git checkout 4.10.0
```

编译安装

```Plaintext
mkdir build && cd build
cmake -D CMAKE_BUILD_TYPE=RELEASE \
      -D CMAKE_INSTALL_PREFIX=/usr/local \
      -D WITH_TBB=ON \
      -D WITH_OPENMP=ON \
      ..
make -j$(nproc)
sudo make install
```

更新库链接

```Plaintext
sudo ldconfig
```

### 10\.主机分组

在ROS2中，希望不让某些节点或主机参与通信，可以通过以下三种方式实现，根据网络环境和需求进行决定

#### 10\.1 使用 **ROS\_DOMAIN\_ID** 隔离主机

ROS2 使用DDS作为中间件，默认通过“多播”发现节点。可以通过设置不同的ROS\_DOMAIN\_ID 来隔离不同主机上的节点。

操作方法：

```Plaintext
在要屏蔽的主机上设置一个与其它主机不同的ROS_DOMAIN_ID

export ROS_DOMAIN_ID=123

在其它主机上设置另一个值，

export ROS_DOMAIN_ID=12
```

这样两个主机上的节点之间就不会互相发现或通讯

#### 10\.2 使用**DDS配置文件** 限制发现范围

如果使用Fast DDS 或者是 Cyclone DDS,可以通过配置XML文件来限制发现范围

包括：禁用多播发现；只允许特定IP地址进行通讯。

示例（Fast DDS）:

```Plaintext
创建一个fastdds_profile.xml文件,内容：

<profiles xmlns="http://www.eprosima.com/XMLSchemas/fastRTPS_Profiles">
  <participant profile_name="disable_multicast">
    <rtps>
      <builtin>
        <discovery_config>
          <simpleEDP>
            <PUBWRITER_SUBREADER>true</PUBWRITER_SUBREADER>
            <PUBREADER_SUBWRITER>true</PUBREADER_SUBWRITER>
          </simpleEDP>
          <initialAnnouncements>
            <count>0</count>
          </initialAnnouncements>
          <multicastLocatorList>
            <!-- 清空多播地址 -->
          </multicastLocatorList>
        </discovery_config>
      </builtin>
    </rtps>
  </participant>
</profiles>

然后设置环境变量

export FASTRTPS_DEFAULT_PROFILES_FILE=fastdds_profile.xml
```

#### 10\.3 使用 **防火墙** 屏蔽 DDS 端口

DDS 默认使用 UDP 端口 `7400~7500`，可以通过 `iptables` 或 `ufw` 屏蔽来自特定主机的这些端口。

示例（Linux）：

```Plaintext
sudo iptables -A INPUT -s 192.168.1.100 -p udp --dport 7400:7500 -j DROP
```

#### 10\.4 使用 ROS 2 安全机制（SROS2）

可以通过**授权控制**，可以使用 SROS2 对节点进行加密和身份验证，未授权的主机将无法参与通信。

### 11\.URDF

### 12\.质量服务策略QoS

### 13\.留存备用

## 第三章 功能包

### 实例（1）电压功能包

需求：订阅电压话题，然后发布启动与停止信号

```Plaintext
订阅话题：/voltage
发布话题：/voltage_flag
发布信息：turn_on/turn_off
```

环境：ROS2 C\+\+

操作：

1，创建功能包

```Plaintext
mkdir -p ros2_ws/src && cd ros2_ws/src
ros2 pkg create voltage_monitor --build-type ament_cmake --dependencies rclcpp std_msgs
```

2,在功能包的src目录下创建voltage\_monitor\.cpp文件

```Plaintext
touch voltage_monitor.cpp
```

添加内容：

```Plaintext
#include "rclcpp/rclcpp.hpp"
#include "std_msgs/msg/float32.hpp"
#include "std_msgs/msg/string.hpp"

class VoltageMonitor : public rclcpp::Node {
public:
    VoltageMonitor() : Node("voltage_monitor") {
        // 创建电压订阅者，订阅"voltage"话题
        voltage_subscriber_ = this->create_subscription<std_msgs::msg::Float32>(
            "voltage", 10, std::bind(&VoltageMonitor::voltage_callback, this, std::placeholders::_1));
        
        // 创建标志位发布者，发布到"turn_on_flag"话题
        flag_publisher_ = this->create_publisher<std_msgs::msg::String>("voltage_flag", 10);
        
        RCLCPP_INFO(this->get_logger(), "电压监控节点已启动");
    }

private:
    void voltage_callback(const std_msgs::msg::Float32::SharedPtr msg) {
        auto flag_msg = std_msgs::msg::String();
        
        // 判断电压值并设置相应的标志位
        if (msg->data >= 11.8) {
            flag_msg.data = "turn_on";
            RCLCPP_INFO(this->get_logger(), "电压 %.2fV >= 11.8V，发布 turn_on", msg->data);
        } else {
            flag_msg.data = "turn_off";
            RCLCPP_INFO(this->get_logger(), "电压 %.2fV < 11.8V，发布 turn_off", msg->data);
        }
        
        // 发布标志位消息
        flag_publisher_->publish(flag_msg);
    }

    rclcpp::Subscription<std_msgs::msg::Float32>::SharedPtr voltage_subscriber_;
    rclcpp::Publisher<std_msgs::msg::String>::SharedPtr flag_publisher_;
};


int main(int argc, char * argv[]) {
    rclcpp::init(argc, argv);
    rclcpp::spin(std::make_shared<VoltageMonitor>());
    rclcpp::shutdown();
    return 0;
}
```

修改CMakeLists\.txt文件

```Plaintext
cmake_minimum_required(VERSION 3.8)
project(voltage_monitor)

if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  add_compile_options(-Wall -Wextra -Wpedantic)
endif()

# 查找依赖
find_package(ament_cmake REQUIRED)
find_package(rclcpp REQUIRED)
find_package(std_msgs REQUIRED)

# 添加可执行文件
add_executable(voltage_monitor src/voltage_monitor.cpp)

# 包含头文件目录
ament_target_dependencies(voltage_monitor
  "rclcpp"
  "std_msgs"
)

# 安装可执行文件
install(TARGETS voltage_monitor
  DESTINATION lib/${PROJECT_NAME})

# ament宏
ament_package()
```

修改 package\.xml文件

```Plaintext
<?xml version="1.0"?>
<?xml-model href="http://download.ros.org/schema/package_format3.xsd" schematypens="http://www.w3.org/2001/XMLSchema"?>
<package format="3">
  <name>voltage_monitor</name>
  <version>0.0.0</version>
  <description>TODO: Package description</description>
  <maintainer email="jazzy@todo.todo">jazzy</maintainer>
  <license>TODO: License declaration</license>

  <buildtool_depend>ament_cmake</buildtool_depend>

  <depend>rclcpp</depend>
  <depend>std_msgs</depend>

  <test_depend>ament_lint_auto</test_depend>
  <test_depend>ament_lint_common</test_depend>

  <export>
    <build_type>ament_cmake</build_type>
  </export>
</package>
```

后续编译即可

```Plaintext
colocn build -packages-select voltage_monitage
```

无误后更新环境

```Plaintext
source install/setup.bash
```

启动节点

```Plaintext
ros2 run 
```

### 实例（2）实例（1）的补充

针对实例（1）进行补充

实例（1）中，当话题/voltage一开始并不存在时，程序会一直等待这个信息，然后导致话题/voltage\_flag无信息发布，从而影响后续程序的执行。

所以需要更改程序思路：通过一个心跳定时器来更新/voltage数据，然后当心跳定时器检测到电压数据处于对应范围内，发布对应的数据信息，在程序的一开始，若无话题/voltage的数据，则循环发布turn\_off数据。

内容更新：

```Plaintext
#include "rclcpp/rclcpp.hpp"
#include "std_msgs/msg/float32.hpp"
#include "std_msgs/msg/string.hpp"
#include <chrono>

class VoltageMonitor : public rclcpp::Node {
public:
    VoltageMonitor() : Node("voltage_monitor"), last_voltage_time_(0), voltage_received_(false), current_state_("turn_off") {
        // 创建电压订阅者，订阅"voltage"话题
        voltage_subscriber_ = this->create_subscription<std_msgs::msg::Float32>(
            "PowerVoltage", 10, std::bind(&VoltageMonitor::voltage_callback, this, std::placeholders::_1));
        
        // 创建标志位发布者，发布到"voltage_flag"话题
        flag_publisher_ = this->create_publisher<std_msgs::msg::String>("voltage_flag", 10);
        
        // 创建定时器，每100ms检查一次电压状态并循环发布
        timer_ = this->create_wall_timer(
            std::chrono::milliseconds(100),
            std::bind(&VoltageMonitor::timer_callback, this));
            
        RCLCPP_INFO(this->get_logger(), "电压监控节点已启动，初始状态：turn_off");
    }

private:
    void voltage_callback(const std_msgs::msg::Float32::SharedPtr msg) {
        last_voltage_time_ = this->now().seconds();
        voltage_received_ = true;
        
        // 判断电压值并设置相应的标志位
        if (msg->data >= 11.8) {
            current_state_ = "turn_on";
            RCLCPP_INFO(this->get_logger(), "电压 %.2fV >= 11.8V，状态更新为 turn_on", msg->data);
        } else {
            current_state_ = "turn_off";
            RCLCPP_INFO(this->get_logger(), "电压 %.2fV < 11.8V，状态更新为 turn_off", msg->data);
        }
    }

    void timer_callback() {
        double current_time = this->now().seconds();
        
        // 如果超过1秒没有收到电压数据，设置为turn_off
        if (voltage_received_ && (current_time - last_voltage_time_ > 1.0)) {
            current_state_ = "turn_off";
            voltage_received_ = false;
            RCLCPP_WARN(this->get_logger(), "超过1秒未收到电压数据，状态更新为 turn_off");
        }
        
        // 循环发布当前状态
        auto flag_msg = std_msgs::msg::String();
        flag_msg.data = current_state_;
        flag_publisher_->publish(flag_msg);
    }

    rclcpp::Subscription<std_msgs::msg::Float32>::SharedPtr voltage_subscriber_;
    rclcpp::Publisher<std_msgs::msg::String>::SharedPtr flag_publisher_;
    rclcpp::TimerBase::SharedPtr timer_;
    
    double last_voltage_time_;
    bool voltage_received_;
    std::string current_state_;  // 保存当前状态
};

int main(int argc, char * argv[]) {
    rclcpp::init(argc, argv);
    rclcpp::spin(std::make_shared<VoltageMonitor>());
    rclcpp::shutdown();
    return 0;
}
```

其余不变，直接编译即可

### 实例（3）发布理想地图

需求：发布一个理想的网球球场地图

地图功能包的创建

```Plaintext
mkdir -p ros2_ws/src && cd ros2_ws/src
ros2 pkg create --build-type ament_cmake tennis_map
```

在tennis\_map功能包中创建maps文件夹，并将tennis\_court\.png和tennis\_court\.yaml放入其中。

tennis\_court\.png

![Image](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=YzI0OTk0MzE4ZjUwODA5ODUwZmNlMTQ2MjgzMDAyNWVfYjU3MTFjNTJjYmYyODQ2YzQwZDhlZmYwYTM1ZDNiYjFfSUQ6NzU3NTA5OTc0MDIxMzQ5Njc3Nl8xNzgxOTQ4MzczOjE3ODIwMzQ3NzNfVjM)

tennis\_court\.yaml

```Plaintext
image: tennis_court.png
resolution: 0.01097
origin: [0.0, 0.0, 0.0]
negate: 0
occupied_thresh: 0.65
free_thresh: 0.196
```

然后在src文件夹下添加map\_publisher\.cpp

```Plaintext
#include "rclcpp/rclcpp.hpp"
#include "nav_msgs/msg/occupancy_grid.hpp"
#include <string>
#include <fstream>
#include <yaml-cpp/yaml.h>
#include <opencv2/opencv.hpp>
#include "tf2/LinearMath/Quaternion.h"

class MapPublisher : public rclcpp::Node
{
public:
    MapPublisher() : Node("map_publisher")
    {
        // 声明参数
        this->declare_parameter("yaml_file", "");
        this->declare_parameter("topic_name", "map");
        this->declare_parameter("publish_frequency", 1.0);

        // 获取参数
        std::string yaml_file = this->get_parameter("yaml_file").as_string();
        std::string topic_name = this->get_parameter("topic_name").as_string();
        double publish_frequency = this->get_parameter("publish_frequency").as_double();

        // 创建发布者
        map_publisher_ = this->create_publisher<nav_msgs::msg::OccupancyGrid>(topic_name, 10);

        // 创建定时器
        timer_ = this->create_wall_timer(
            std::chrono::milliseconds(static_cast<int>(1000.0 / publish_frequency)),
            std::bind(&MapPublisher::publishMap, this));

        // 加载地图
        if (!loadMapFromFile(yaml_file)) {
            RCLCPP_ERROR(this->get_logger(), "Failed to load map from file: %s", yaml_file.c_str());
        }
    }

private:
    void publishMap()
    {
        map_msg_.header.stamp = this->now();
        map_publisher_->publish(map_msg_);
    }

    bool loadMapFromFile(const std::string& yaml_file)
    {
        try {
            // 加载YAML文件
            YAML::Node config = YAML::LoadFile(yaml_file);
            
            // 获取地图文件路径
            std::string image_file = config["image"].as<std::string>();
            
            // 处理相对路径
            if (image_file.find('/') != 0) {
                size_t found = yaml_file.find_last_of("/");
                if (found != std::string::npos) {
                    image_file = yaml_file.substr(0, found + 1) + image_file;
                }
            }
            
            // 获取地图参数
            double resolution = config["resolution"].as<double>();
            std::vector<double> origin = config["origin"].as<std::vector<double>>();
            double negate = config["negate"].as<double>();
            double occupied_thresh = config["occupied_thresh"].as<double>();
            double free_thresh = config["free_thresh"].as<double>();
            
            // 加载图像
            cv::Mat image = cv::imread(image_file, cv::IMREAD_GRAYSCALE);
            if (image.empty()) {
                RCLCPP_ERROR(this->get_logger(), "Failed to load image: %s", image_file.c_str());
                return false;
            }
            
            // 设置地图消息
            map_msg_.header.frame_id = "map";
            map_msg_.info.resolution = resolution;
            map_msg_.info.width = image.cols;
            map_msg_.info.height = image.rows;
            map_msg_.info.origin.position.x = origin[0];
            map_msg_.info.origin.position.y = origin[1];
            map_msg_.info.origin.position.z = 0.0;
            
            // 设置方向（假设地图是平放的）
            tf2::Quaternion q;
            q.setRPY(0, 0, origin[2]);
            map_msg_.info.origin.orientation.x = q.x();
            map_msg_.info.origin.orientation.y = q.y();
            map_msg_.info.origin.orientation.z = q.z();
            map_msg_.info.origin.orientation.w = q.w();
            
            // 调整地图数据大小
            map_msg_.data.resize(map_msg_.info.width * map_msg_.info.height);
            
            // 将图像数据转换为占用网格数据
            for (int y = 0; y < image.rows; y++) {
                for (int x = 0; x < image.cols; x++) {
                    unsigned char value = image.at<unsigned char>(y, x);
                    
                    // 根据阈值确定占用状态
                    double probability = (255.0 - static_cast<double>(value)) / 255.0;
                    if (negate) {
                        probability = 1.0 - probability;
                    }
                    
                    if (probability > occupied_thresh) {
                        map_msg_.data[y * map_msg_.info.width + x] = 100;  // 占用
                    } else if (probability < free_thresh) {
                        map_msg_.data[y * map_msg_.info.width + x] = 0;    // 自由
                    } else {
                        map_msg_.data[y * map_msg_.info.width + x] = -1;   // 未知
                    }
                }
            }
            
            RCLCPP_INFO(this->get_logger(), "Map loaded successfully: %dx%d @ %.3f m/pix", 
                       map_msg_.info.width, map_msg_.info.height, map_msg_.info.resolution);
            return true;
        } catch (const YAML::Exception& e) {
            RCLCPP_ERROR(this->get_logger(), "YAML parsing error: %s", e.what());
            return false;
        } catch (const std::exception& e) {
            RCLCPP_ERROR(this->get_logger(), "Error loading map: %s", e.what());
            return false;
        }
    }

    rclcpp::Publisher<nav_msgs::msg::OccupancyGrid>::SharedPtr map_publisher_;
    rclcpp::TimerBase::SharedPtr timer_;
    nav_msgs::msg::OccupancyGrid map_msg_;
};

int main(int argc, char** argv)
{
    rclcpp::init(argc, argv);
    auto node = std::make_shared<MapPublisher>();
    rclcpp::spin(node);
    rclcpp::shutdown();
    return 0;
}
```

修改CMakeLists\.txt

```Plaintext
cmake_minimum_required(VERSION 3.8)
project(tennis_map)

if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  add_compile_options(-Wall -Wextra -Wpedantic)
endif()

# find dependencies
find_package(ament_cmake REQUIRED)
find_package(rclcpp REQUIRED)
find_package(nav_msgs REQUIRED)
find_package(OpenCV REQUIRED)
find_package(yaml-cpp REQUIRED)
find_package(tf2 REQUIRED)

add_executable(map_publisher src/map_publisher.cpp)
ament_target_dependencies(map_publisher rclcpp nav_msgs)

# 链接OpenCV、yaml-cpp和tf2库
target_link_libraries(map_publisher ${OpenCV_LIBRARIES} yaml-cpp tf2::tf2)

# 安装可执行文件
install(TARGETS map_publisher
  DESTINATION lib/${PROJECT_NAME})

# 安装地图文件
install(DIRECTORY maps
  DESTINATION share/${PROJECT_NAME})

# 安装启动文件
install(DIRECTORY launch
  DESTINATION share/${PROJECT_NAME})

ament_package()
```

修改package\.xml

```Plaintext
<?xml version="1.0"?>
<?xml-model href="http://download.ros.org/schema/package_format3.xsd" schematypens="http://www.w3.org/2001/XMLSchema"?>
<package format="3">
  <name>tennis_map</name>
  <version>0.0.0</version>
  <description>Package for publishing tennis court map</description>
  <maintainer email="your_email@example.com">Your Name</maintainer>
  <license>Apache License 2.0</license>

  <buildtool_depend>ament_cmake</buildtool_depend>

  <depend>rclcpp</depend>
  <depend>nav_msgs</depend>
  <depend>opencv2</depend>
  <depend>yaml-cpp</depend>
  <depend>tf2</depend>

  <test_depend>ament_lint_auto</test_depend>
  <test_depend>ament_lint_common</test_depend>

  <export>
    <build_type>ament_cmake</build_type>
  </export>
</package>
```

创建启动文件launch/map\_publisher\.launch\.py

```Plaintext
from launch import LaunchDescription
from launch_ros.actions import Node
from launch.substitutions import LaunchConfiguration
from ament_index_python.packages import get_package_share_directory
import os

def generate_launch_description():
    package_share_directory = get_package_share_directory('tennis_map')
    yaml_file = os.path.join(package_share_directory, 'maps', 'tennis_court.yaml')
    
    return LaunchDescription([
        Node(
            package='tennis_map',
            executable='map_publisher',
            name='map_publisher',
            output='screen',
            parameters=[{
                'yaml_file': yaml_file,
                'topic_name': 'map',
                'publish_frequency': 1.0
            }]
        )
    ])
```

编译后更新环境

```Plaintext
cd ~/ros2_ws
colcon build --packages-select tennis_map
source ~/ros2_ws/install/setup.bash
ros2 launch tennis_map map_publisher.launch.py
```

### 实例（4）视觉定位初始版

需求：通过视觉方案，确定小车在地图中的位置

视觉定位功能包的创建

```Plaintext
cd ~/ros2_ws/src
ros2 pkg create --build-type ament_cmake visual_localization --dependencies rclcpp sensor_msgs geometry_msgs nav_msgs cv_bridge tf2 tf2_ros tf2_geometry_msgs image_transport
```

然后在include/visual\_localization下创建目录obstacle\_msgs/msg,并创建文件

```Plaintext
mkdir -p include/visual_localization/obstacle_msgs/msg && cd include/visual_localization/obstacle_msgs/msg

touch Obstacle.hpp && touch ObstacleArray.hpp
```

修改Obstacle\.hpp

```Plaintext
#ifndef VISUAL_LOCALIZATION__OBSTACLE_MSGS__MSG__OBSTACLE_HPP_
#define VISUAL_LOCALIZATION__OBSTACLE_MSGS__MSG__OBSTACLE_HPP_

#include <geometry_msgs/msg/pose.hpp>
#include <string>

namespace obstacle_msgs
{
namespace msg
{

class Obstacle
{
public:
  geometry_msgs::msg::Pose pose;
  float size_x;
  float size_y;
  float size_z;
  std::string type;

  Obstacle()
  : size_x(0.0f),
    size_y(0.0f),
    size_z(0.0f)
  {}
};

}  // namespace msg
}  // namespace obstacle_msgs

#endif  // VISUAL_LOCALIZATION__OBSTACLE_MSGS__MSG__OBSTACLE_HPP_
```

修改ObstacleArray\.hpp

```Plaintext
#ifndef VISUAL_LOCALIZATION__OBSTACLE_MSGS__MSG__OBSTACLE_ARRAY_HPP_
#define VISUAL_LOCALIZATION__OBSTACLE_MSGS__MSG__OBSTACLE_ARRAY_HPP_

#include "obstacle_msgs/msg/obstacle.hpp"
#include <std_msgs/msg/header.hpp>
#include <vector>

namespace obstacle_msgs
{
namespace msg
{

class ObstacleArray
{
public:
  std_msgs::msg::Header header;
  std::vector<obstacle_msgs::msg::Obstacle> obstacles;

  ObstacleArray() = default;
};

}  // namespace msg
}  // namespace obstacle_msgs

#endif  // VISUAL_LOCALIZATION__OBSTACLE_MSGS__MSG__OBSTACLE_ARRAY_HPP_
```

在src下创建文件visual\_localization\_node\.cpp

```Plaintext
cd ~/ros2_ws/src/visual_localization/src/visual_localization_node.cpp
```

修改visual\_localization\_node\.cpp

```Plaintext
#include "rclcpp/rclcpp.hpp"
#include "sensor_msgs/msg/image.hpp"
#include "sensor_msgs/msg/camera_info.hpp"
#include "sensor_msgs/msg/point_cloud2.hpp"
#include "geometry_msgs/msg/pose_with_covariance_stamped.hpp"
#include "nav_msgs/msg/occupancy_grid.hpp"
#include "cv_bridge/cv_bridge.h"
#include "tf2_ros/transform_broadcaster.h"
#include "tf2_geometry_msgs/tf2_geometry_msgs.hpp"
#include "image_transport/image_transport.hpp"
#include "obstacle_msgs/msg/obstacle.hpp"
#include "obstacle_msgs/msg/obstacle_array.hpp"
#include <opencv2/opencv.hpp>
#include <opencv2/imgproc/imgproc.hpp>
#include <opencv2/highgui/highgui.hpp>
#include <memory>
#include <string>
#include <vector>
#include <functional>

class VisualLocalizationNode : public rclcpp::Node
{
public:
    VisualLocalizationNode() : Node("visual_localization_node")
    {
        // 声明参数
        this->declare_parameter("map_topic", "map");
        this->declare_parameter("pose_topic", "pose");
        this->declare_parameter("camera_image_topic", "/camera/color/image_raw");
        this->declare_parameter("camera_depth_topic", "/camera/depth/image_raw");
        this->declare_parameter("camera_info_topic", "/camera/color/camera_info");
        this->declare_parameter("base_frame", "base_link");
        this->declare_parameter("map_frame", "map");
        this->declare_parameter("publish_frequency", 10.0);
        
        // 获取参数
        std::string map_topic = this->get_parameter("map_topic").as_string();
        std::string pose_topic = this->get_parameter("pose_topic").as_string();
        std::string camera_image_topic = this->get_parameter("camera_image_topic").as_string();
        std::string camera_depth_topic = this->get_parameter("camera_depth_topic").as_string();
        std::string camera_info_topic = this->get_parameter("camera_info_topic").as_string();
        std::string base_frame = this->get_parameter("base_frame").as_string();
        std::string map_frame = this->get_parameter("map_frame").as_string();
        double publish_frequency = this->get_parameter("publish_frequency").as_double();
        
        // 创建订阅者
        map_sub_ = this->create_subscription<nav_msgs::msg::OccupancyGrid>(
            map_topic, 10, std::bind(&VisualLocalizationNode::mapCallback, this, std::placeholders::_1));
            
        camera_info_sub_ = this->create_subscription<sensor_msgs::msg::CameraInfo>(
            camera_info_topic, 10, std::bind(&VisualLocalizationNode::cameraInfoCallback, this, std::placeholders::_1));
        
        // 创建发布者
        pose_pub_ = this->create_publisher<geometry_msgs::msg::PoseWithCovarianceStamped>(pose_topic, 10);
        obstacle_pub_ = this->create_publisher<obstacle_msgs::msg::ObstacleArray>("detected_obstacles", 10);
        
        // 创建TF广播器
        tf_broadcaster_ = std::make_shared<tf2_ros::TransformBroadcaster>(this);
        
        // 创建定时器，用于延迟初始化image_transport
        timer_init_ = this->create_wall_timer(
            std::chrono::milliseconds(100),
            [this, camera_image_topic, camera_depth_topic]() {
                // 取消定时器
                this->timer_init_->cancel();
                
                // 现在可以安全地使用shared_from_this()
                it_ = std::make_shared<image_transport::ImageTransport>(shared_from_this());
                image_sub_ = it_->subscribe(camera_image_topic, 10, std::bind(&VisualLocalizationNode::imageCallback, this, std::placeholders::_1));
                depth_sub_ = it_->subscribe(camera_depth_topic, 10, std::bind(&VisualLocalizationNode::depthCallback, this, std::placeholders::_1));
                
                RCLCPP_INFO(this->get_logger(), "Image transport initialized");
            });
        
        // 创建定时器，用于处理定位
        timer_ = this->create_wall_timer(
            std::chrono::milliseconds(static_cast<int>(1000.0 / publish_frequency)),
            std::bind(&VisualLocalizationNode::timerCallback, this));
            
        // 初始化变量
        map_received_ = false;
        image_received_ = false;
        depth_received_ = false;
        camera_info_received_ = false;
        
        RCLCPP_INFO(this->get_logger(), "Visual localization node initialized");
    }

private:
    void mapCallback(const nav_msgs::msg::OccupancyGrid::SharedPtr msg)
    {
        map_ = *msg;
        map_received_ = true;
        RCLCPP_INFO(this->get_logger(), "Map received");
    }
    
    void imageCallback(const sensor_msgs::msg::Image::ConstSharedPtr& msg)
    {
        try {
            cv_bridge::CvImageConstPtr cv_ptr;
            cv_ptr = cv_bridge::toCvShare(msg, "bgr8");
            current_image_ = cv_ptr->image.clone();
            image_received_ = true;
        } catch (cv_bridge::Exception& e) {
            RCLCPP_ERROR(this->get_logger(), "cv_bridge exception: %s", e.what());
            return;
        }
    }
    
    void depthCallback(const sensor_msgs::msg::Image::ConstSharedPtr& msg)
    {
        try {
            cv_bridge::CvImageConstPtr cv_ptr;
            cv_ptr = cv_bridge::toCvShare(msg, sensor_msgs::image_encodings::TYPE_16UC1);
            current_depth_ = cv_ptr->image.clone();
            depth_received_ = true;
        } catch (cv_bridge::Exception& e) {
            RCLCPP_ERROR(this->get_logger(), "cv_bridge exception: %s", e.what());
            return;
        }
    }
    
    void cameraInfoCallback(const sensor_msgs::msg::CameraInfo::SharedPtr msg)
    {
        camera_info_ = *msg;
        camera_info_received_ = true;
        
        // 将相机参数转换为OpenCV格式
        camera_matrix_ = cv::Mat(3, 3, CV_64F);
        for (int i = 0; i < 3; i++) {
            for (int j = 0; j < 3; j++) {
                camera_matrix_.at<double>(i, j) = camera_info_.k[i * 3 + j];
            }
        }
        
        distortion_coeffs_ = cv::Mat(camera_info_.d.size(), 1, CV_64F);
        for (size_t i = 0; i < camera_info_.d.size(); i++) {
            distortion_coeffs_.at<double>(i) = camera_info_.d[i];
        }
    }
    
    void timerCallback()
    {
        // 检查是否已接收到所有必要的数据
        if (!map_received_ || !image_received_ || !depth_received_ || !camera_info_received_) {
            return;
        }
        
        // 处理图像以估计位置
        geometry_msgs::msg::PoseWithCovarianceStamped estimated_pose = estimatePosition();
        
        // 发布估计的位置
        pose_pub_->publish(estimated_pose);
        
        // 发布TF变换
        publishTransform(estimated_pose);
        
        // 检测并发布障碍物
        detectAndPublishObstacles();
    }
    
    geometry_msgs::msg::PoseWithCovarianceStamped estimatePosition()
    {
        geometry_msgs::msg::PoseWithCovarianceStamped estimated_pose;
        
        // 这里实现你的视觉定位算法
        // 以下是一个简单的示例，你需要根据实际情况进行修改
        
        // 1. 检测图像中的特征点（例如，使用SIFT、ORB等）
        std::vector<cv::KeyPoint> keypoints;
        cv::Mat descriptors;
        detectFeatures(current_image_, keypoints, descriptors);
        
        // 2. 如果有深度信息，将2D点转换为3D点
        std::vector<cv::Point3f> points_3d;
        if (!current_depth_.empty()) {
            for (const auto& kp : keypoints) {
                cv::Point3f point_3d;
                if (depthTo3D(kp.pt, current_depth_, point_3d)) {
                    points_3d.push_back(point_3d);
                }
            }
        }
        
        // 3. 将3D点与地图中的特征进行匹配，估计位置
        // 这里应该实现你的匹配算法
        // 以下是一个简单的示例，假设我们已经得到了位置估计
        estimated_pose.header.frame_id = this->get_parameter("map_frame").as_string();
        estimated_pose.header.stamp = this->now();
        
        // 假设的位置（需要替换为实际的估计）
        estimated_pose.pose.pose.position.x = 0.0;
        estimated_pose.pose.pose.position.y = 0.0;
        estimated_pose.pose.pose.position.z = 0.0;
        
        estimated_pose.pose.pose.orientation.x = 0.0;
        estimated_pose.pose.pose.orientation.y = 0.0;
        estimated_pose.pose.pose.orientation.z = 0.0;
        estimated_pose.pose.pose.orientation.w = 1.0;
        
        // 设置协方差矩阵（这里使用默认值，实际应根据估计的不确定性进行调整）
        for (int i = 0; i < 36; i++) {
            estimated_pose.pose.covariance[i] = 0.0;
        }
        estimated_pose.pose.covariance[0] = 0.1;  // x
        estimated_pose.pose.covariance[7] = 0.1;  // y
        estimated_pose.pose.covariance[14] = 0.1; // z
        estimated_pose.pose.covariance[21] = 0.1; // roll
        estimated_pose.pose.covariance[28] = 0.1; // pitch
        estimated_pose.pose.covariance[35] = 0.1; // yaw
        
        return estimated_pose;
    }
    
    void detectFeatures(const cv::Mat& image, std::vector<cv::KeyPoint>& keypoints, cv::Mat& descriptors)
    {
        // 使用ORB特征检测器
        cv::Ptr<cv::ORB> orb = cv::ORB::create(1000);  // 检测1000个特征点
        orb->detectAndCompute(image, cv::Mat(), keypoints, descriptors);
        
        // 绘制特征点（调试用）
        cv::Mat output_image;
        cv::drawKeypoints(image, keypoints, output_image);
        
        // 添加边界线检测
        cv::Mat gray;
        cv::cvtColor(image, gray, cv::COLOR_BGR2GRAY);
        cv::Mat edges;
        cv::Canny(gray, edges, 50, 150);
        
        // 查找直线
        std::vector<cv::Vec2f> lines;
        cv::HoughLines(edges, lines, 1, CV_PI/180, 100);
        
        // 绘制检测到的直线
        for( size_t i = 0; i < lines.size(); i++ )
        {
            float rho = lines[i][0], theta = lines[i][1];
            cv::Point pt1, pt2;
            double a = cos(theta), b = sin(theta);
            double x0 = a*rho, y0 = b*rho;
            pt1.x = cvRound(x0 + 1000*(-b));
            pt1.y = cvRound(y0 + 1000*(a));
            pt2.x = cvRound(x0 - 1000*(-b));
            pt2.y = cvRound(y0 - 1000*(a));
            cv::line(output_image, pt1, pt2, cv::Scalar(0,0,255), 3, cv::LINE_AA);
        }
        
        // 显示图像
        cv::imshow("Feature and Line Detection", output_image);
        cv::waitKey(1);
    }
    
    bool depthTo3D(const cv::Point2f& pixel, const cv::Mat& depth_image, cv::Point3f& point_3d)
    {
        // 获取深度值
        uint16_t depth = depth_image.at<uint16_t>(pixel.y, pixel.x);
        
        // 检查深度是否有效
        if (depth == 0) {
            return false;
        }
        
        // 将深度转换为米
        double depth_m = depth / 1000.0;
        
        // 归一化像素坐标
        cv::Mat pixel_normalized(3, 1, CV_64F);
        pixel_normalized.at<double>(0) = pixel.x;
        pixel_normalized.at<double>(1) = pixel.y;
        pixel_normalized.at<double>(2) = 1.0;
        
        // 反投影到3D空间
        cv::Mat point_3d_mat = camera_matrix_.inv() * pixel_normalized * depth_m;
        
        // 转换为Point3f
        point_3d.x = point_3d_mat.at<double>(0);
        point_3d.y = point_3d_mat.at<double>(1);
        point_3d.z = point_3d_mat.at<double>(2);
        
        return true;
    }
    
    void detectAndPublishObstacles()
    {
        if (current_image_.empty() || current_depth_.empty()) {
            return;
        }
        
        obstacle_msgs::msg::ObstacleArray obstacle_array;
        obstacle_array.header.stamp = this->now();
        obstacle_array.header.frame_id = "camera_link";
        
        // 检测边界线
        cv::Mat gray;
        cv::cvtColor(current_image_, gray, cv::COLOR_BGR2GRAY);
        cv::Mat edges;
        cv::Canny(gray, edges, 50, 150);
        
        // 查找直线
        std::vector<cv::Vec2f> lines;
        cv::HoughLines(edges, lines, 1, CV_PI/180, 100);
        
        // 将检测到的直线转换为障碍物
        for( size_t i = 0; i < lines.size(); i++ )
        {
            float rho = lines[i][0], theta = lines[i][1];
            
            // 计算直线上的点
            cv::Point pt1, pt2;
            double a = cos(theta), b = sin(theta);
            double x0 = a*rho, y0 = b*rho;
            pt1.x = cvRound(x0 + 1000*(-b));
            pt1.y = cvRound(y0 + 1000*(a));
            pt2.x = cvRound(x0 - 1000*(-b));
            pt2.y = cvRound(y0 - 1000*(a));
            
            // 获取直线上的深度信息
            std::vector<cv::Point3f> line_points_3d;
            for (int j = 0; j < 10; j++) {
                cv::Point2f pixel;
                pixel.x = pt1.x + (pt2.x - pt1.x) * j / 10;
                pixel.y = pt1.y + (pt2.y - pt1.y) * j / 10;
                
                cv::Point3f point_3d;
                if (depthTo3D(pixel, current_depth_, point_3d)) {
                    line_points_3d.push_back(point_3d);
                }
            }
            
            // 如果有足够的3D点，创建障碍物
            if (line_points_3d.size() >= 2) {
                obstacle_msgs::msg::Obstacle obstacle;
                
                // 计算障碍物的中心位置
                cv::Point3f center(0, 0, 0);
                for (const auto& point : line_points_3d) {
                    center += point;
                }
                center /= static_cast<float>(line_points_3d.size());
                
                obstacle.pose.position.x = center.x;
                obstacle.pose.position.y = center.y;
                obstacle.pose.position.z = center.z;
                
                // 计算障碍物的方向
                cv::Point3f direction = line_points_3d.back() - line_points_3d.front();
                double length = cv::norm(direction);
                direction /= length;
                
                // 将方向向量转换为四元数
                double yaw = atan2(direction.y, direction.x);
                tf2::Quaternion q;
                q.setRPY(0, 0, yaw);
                obstacle.pose.orientation.x = q.x();
                obstacle.pose.orientation.y = q.y();
                obstacle.pose.orientation.z = q.z();
                obstacle.pose.orientation.w = q.w();
                
                // 设置障碍物的大小
                obstacle.size_x = length;
                obstacle.size_y = 0.05;  // 线的宽度
                obstacle.size_z = 0.05;  // 线的高度
                
                // 设置障碍物类型
                obstacle.type = "line";
                
                // 添加到障碍物数组
                obstacle_array.obstacles.push_back(obstacle);
            }
        }
        
        // 发布障碍物数组
        obstacle_pub_->publish(obstacle_array);
    }
    
    void publishTransform(const geometry_msgs::msg::PoseWithCovarianceStamped& pose)
    {
        geometry_msgs::msg::TransformStamped transform_stamped;
        
        // 设置时间戳和帧ID
        transform_stamped.header.stamp = pose.header.stamp;
        transform_stamped.header.frame_id = pose.header.frame_id;
        transform_stamped.child_frame_id = this->get_parameter("base_frame").as_string();
        
        // 设置位置
        transform_stamped.transform.translation.x = pose.pose.pose.position.x;
        transform_stamped.transform.translation.y = pose.pose.pose.position.y;
        transform_stamped.transform.translation.z = pose.pose.pose.position.z;
        
        // 设置方向
        transform_stamped.transform.rotation = pose.pose.pose.orientation;
        
        // 发布变换
        tf_broadcaster_->sendTransform(transform_stamped);
    }
    
    // 订阅者
    rclcpp::Subscription<nav_msgs::msg::OccupancyGrid>::SharedPtr map_sub_;
    std::shared_ptr<image_transport::ImageTransport> it_;
    image_transport::Subscriber image_sub_;
    image_transport::Subscriber depth_sub_;
    rclcpp::Subscription<sensor_msgs::msg::CameraInfo>::SharedPtr camera_info_sub_;
    
    // 发布者
    rclcpp::Publisher<geometry_msgs::msg::PoseWithCovarianceStamped>::SharedPtr pose_pub_;
    rclcpp::Publisher<obstacle_msgs::msg::ObstacleArray>::SharedPtr obstacle_pub_;
    
    // TF广播器
    std::shared_ptr<tf2_ros::TransformBroadcaster> tf_broadcaster_;
    
    // 定时器
    rclcpp::TimerBase::SharedPtr timer_;
    rclcpp::TimerBase::SharedPtr timer_init_;  // 用于延迟初始化image_transport
    
    // 存储接收到的数据
    nav_msgs::msg::OccupancyGrid map_;
    cv::Mat current_image_;
    cv::Mat current_depth_;
    sensor_msgs::msg::CameraInfo camera_info_;
    cv::Mat camera_matrix_;
    cv::Mat distortion_coeffs_;
    
    // 标志变量
    bool map_received_;
    bool image_received_;
    bool depth_received_;
    bool camera_info_received_;

};

int main(int argc, char** argv)
{
    rclcpp::init(argc, argv);
    auto node = std::make_shared<VisualLocalizationNode>();
    rclcpp::spin(node);
    rclcpp::shutdown();
    return 0;
}
```

修改CMakeLists\.txt

```Plaintext
cmake_minimum_required(VERSION 3.8)
project(visual_localization)

if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  add_compile_options(-Wall -Wextra -Wpedantic)
endif()

# find dependencies
find_package(ament_cmake REQUIRED)
find_package(rclcpp REQUIRED)
find_package(sensor_msgs REQUIRED)
find_package(geometry_msgs REQUIRED)
find_package(nav_msgs REQUIRED)
find_package(cv_bridge REQUIRED)
find_package(tf2 REQUIRED)
find_package(tf2_ros REQUIRED)
find_package(tf2_geometry_msgs REQUIRED)
find_package(image_transport REQUIRED)
find_package(OpenCV REQUIRED)
find_package(obstacle_msgs REQUIRED)  # 确保这一行存在

add_executable(visual_localization_node src/visual_localization_node.cpp)
ament_target_dependencies(visual_localization_node
  rclcpp
  sensor_msgs
  geometry_msgs
  nav_msgs
  cv_bridge
  tf2
  tf2_ros
  tf2_geometry_msgs
  image_transport
  obstacle_msgs  # 确保这一行存在
)

# 链接OpenCV库
target_link_libraries(visual_localization_node ${OpenCV_LIBRARIES})

# 安装可执行文件
install(TARGETS visual_localization_node
  DESTINATION lib/${PROJECT_NAME})

# 安装启动文件
install(DIRECTORY launch
  DESTINATION share/${PROJECT_NAME})

ament_package()
```

修改package\.xml

```Plaintext
<?xml version="1.0"?>
<?xml-model href="http://download.ros.org/schema/package_format3.xsd" schematypens="http://www.w3.org/2001/XMLSchema"?>
<package format="3">
  <name>visual_localization</name>
  <version>0.0.0</version>
  <description>Visual localization package for estimating robot position using camera data</description>
  <maintainer email="your_email@example.com">Your Name</maintainer>
  <license>Apache License 2.0</license>

  <buildtool_depend>ament_cmake</buildtool_depend>

  <depend>rclcpp</depend>
  <depend>sensor_msgs</depend>
  <depend>geometry_msgs</depend>
  <depend>nav_msgs</depend>
  <depend>cv_bridge</depend>
  <depend>tf2</depend>
  <depend>tf2_ros</depend>
  <depend>tf2_geometry_msgs</depend>
  <depend>image_transport</depend>
  <depend>OpenCV</depend>
  <depend>obstacle_msgs</depend>  <!-- 确保这一行存在 -->

  <test_depend>ament_lint_auto</test_depend>
  <test_depend>ament_lint_common</test_depend>

  <export>
    <build_type>ament_cmake</build_type>
  </export>
</package>
```

添加visual\_localization/launch文件夹并创建启动文件viaual\_localization\.launch\.py

```Plaintext
touch visual_localization.launch.py
```

修改visual\_localization\.launch\.py

```Plaintext
from launch import LaunchDescription
from launch_ros.actions import Node
from launch.substitutions import LaunchConfiguration

def generate_launch_description():
    return LaunchDescription([
        Node(
            package='visual_localization',
            executable='visual_localization_node',
            name='visual_localization_node',
            output='screen',
            parameters=[{
                'map_topic': 'map',
                'pose_topic': 'visual_pose',
                'camera_image_topic': '/camera/color/image_raw',
                'camera_depth_topic': '/camera/depth/image_raw',
                'camera_info_topic': '/camera/color/camera_info',
                'base_frame': 'base_link',
                'map_frame': 'map',
                'publish_frequency': 10.0
            }]
        )
    ])
```

然后回到\~/ros2\_ws下进行编译即可

```Plaintext
cd ~/ros2_ws
colcon build --packages-select visual_localization
source install/setup.bash
```

启动文件

```Plaintext
ros2 launch visual_localization visual_localization.launch.py
```

### 实例（5）Ctrl\+C停止任务的时候，让设备安全停止

需求：当任务执行时，通过Ctrl\+C中断任务的时候，让设备安全停止。

```Plaintext
1.基本原理
Linux信号机制：Ctrl+C会向前台进程组发送SIGINT信号（中断信号）。ROS默认会捕获此信号并调用ros::shutdown()关闭节点

2.ROS关闭机制：
ros::shutdown()会将ros::ok()设为false，使ros::spin()和ros::spinOnce()退出循环,关闭所有话题订阅/发布、服务客户端/服务器;
但不会自动停止用户创建的线程或释放自定义资源。

3.多线程风险：
若使用多线程，SIGINT可能被某个工作线程捕获，主线程无法收到，导致程序无法退出，残留进程。
```

标准安全停止流程

```Plaintext
核心步骤：
1.禁用ROS默认信号处理
2.注册自定义信号处理函数
3.在回调中执行清理逻辑
4.等待所有线程结束
5.调用ros::shutdown()
关键点：必须在创建第一个NodeHandle之后注册信号处理函数，否则可能被覆盖。一定要注意在ros进程关闭之前进行停止动作的操作，当ros关闭之后，通讯会被切断，此时再执行停止动作则是无效的。
```

代码实现

```Plaintext
#include <rclcpp/rclcpp.hpp>
#include <signal.h>
#include <atomic>

std::atomic<bool> g_keep_running(true);

void SignalHandler(int signal)
{
    RCLCPP_INFO(rclcpp::get_logger("safe_stop_node"), "收到中断信号...");
    g_keep_running = false;
    rclcpp::shutdown();  // ROS2关闭函数
}

int main(int argc, char** argv)
{
    rclcpp::init(argc, argv);
    
    auto node = std::make_shared<rclcpp::Node>("safe_stop_node");
    rclcpp::Rate rate(30);
    
    // 注册信号处理
    signal(SIGINT, SignalHandler);
    signal(SIGTERM, SignalHandler);
    
    // 使用执行器spin
    rclcpp::executors::SingleThreadedExecutor executor;
    executor.add_node(node);
    
    // 单独线程执行spin
    std::thread spin_thread([&executor]() {
        executor.spin();
    });
    
    // 主循环
    while (rclcpp::ok() && g_keep_running)
    {
        // 小车控制逻辑
        rate.sleep();
    }
    
    // 等待线程结束
    executor.cancel();
    spin_thread.join();
    
    rclcpp::shutdown();
    return 0;
}
```

最佳实践

```Plaintext
1.始终使用ros::ok()或rclcpp::ok()作为循环条件：确保能响应外部关闭请求
2.避免在信号处理函数中执行复杂操作：只做标志位设置和ros::shutdown()，具体清理放主线程
3.对小车子系统专门处理：
        3.1向底盘驱动节点发布stop消息
        3.2保存当前状态到文件
        3.3等待传感器数据刷新完毕
4.测试验证：使用rosnode kill或kill -SIGINT <pid>模拟信号，观察是否优雅退出
```

特别注意

```Plaintext
当你在 main 函数中等待 rclcpp::spin(node) 返回时（即按下 Ctrl+C 后），ROS 2 的默认信号处理器已经被触发了。它会将 ROS 上下文（Context）标记为“无效/正在关闭”。虽然代码执行到了Stop（）函数，但由于上下文已经处于“关闭中”状态，底层的 DDS 通信中间件可能已经停止发送新数据，或者直接丢弃了你的停止指令。导致小车根本没收到“速度为0”的命令，而是继续执行最后接收到的速度指令，知道下位机触发超时保护。
解决方法：
我们需要接管 Ctrl+C 信号，在 ROS 2 杀死自己之前，优先执行停车逻辑。
```

代码补充

```Plaintext
std::shared_ptr<WaypointCruiser> g_node = nullptr;

void MySigintHandler(int sig)
{
    // 1. 收到 Ctrl+C，先打印
    // 使用底层打印防止 ROS 日志系统已被关闭
    std::cout << "\n[MySigintHandler] Caught signal " << sig << ". Stopping robot..." << std::endl;

    // 2. 只要节点还存在，就强制执行停车
    if (g_node) {
        // 调用类内部的急停函数 (包含多次发送和延时)
        g_node->forceStop();
    }

    // 3. 发送完停车指令后，再优雅关闭 ROS
    std::cout << "[MySigintHandler] Robot stopped. Shutting down ROS..." << std::endl;
    rclcpp::shutdown();
    
    // 4. 退出程序
    exit(0);
}

// ==========================================
// 【修改】Main 函数
// ==========================================
int main(int argc, char** argv) {
    // 1. 初始化 ROS
    rclcpp::init(argc, argv);
    
    // 2. 创建节点
    g_node = std::make_shared<WaypointCruiser>();
    
    RCLCPP_INFO(g_node->get_logger(), "Node started. Press Ctrl+C to stop.");

    // 3. 【关键】注册自定义信号处理器
    // 这会覆盖 ROS 2 默认的处理器，确保先停车，再关闭
    signal(SIGINT, MySigintHandler);

    // 4. 开始循环
    // 这里不需要 try-catch，因为信号会被上面的函数拦截
    rclcpp::spin(g_node);

    // 正常退出（一般不会执行到这里，除非节点内部调用了 shutdown）
    rclcpp::shutdown();
    return 0;
}
```

### 实例（6）通过uwb定位功能代替amcl算法

需求：通过UWB模块进行定位功能。

分析：现在需要使用nav2功能包进行导航任务，但是设备并没有能输出相关的观测数据，然后现在需要一个复合的全图定位，但是需要精度符合使用要求。


### 实例（7）ROS 2 节点实现PID控制器的动态调参

这个节点支持**动态参数调整**，可以在运行中修改 PID 参数。

`pid_node.cpp` \(源文件\)

```C++
#include <memory>
#include "rclcpp/rclcpp.hpp"
#include "std_msgs/msg/float64.hpp"
#include "pid_controller.hpp"

class PIDNode : public rclcpp::Node {
public:
    PIDNode() : Node("robust_pid_node") {
        // --- 1. 声明参数 (带默认值) ---
        this->declare_parameter("kp", 2.0);
        this->declare_parameter("ki", 0.1);
        this->declare_parameter("kd", 0.5);
        this->declare_parameter("alpha", 0.3); // 滤波系数
        this->declare_parameter("max_output", 10.0); // 假设输出是电压 +/- 10V

        // 读取初始参数
        double kp = this->get_parameter("kp").as_double();
        double ki = this->get_parameter("ki").as_double();
        double kd = this->get_parameter("kd").as_double();
        double alpha = this->get_parameter("alpha").as_double();
        double max_out = this->get_parameter("max_output").as_double();

        // --- 2. 初始化控制器 ---
        pid_controller_ = std::make_unique<PIDController>(kp, ki, kd, max_out, -max_out, alpha);

        // --- 3. 设置通信接口 ---
        // 订阅目标值 (Setpoint)
        sub_setpoint_ = this->create_subscription<std_msgs::msg::Float64>(
            "setpoint", 10, [this](const std_msgs::msg::Float64::SharedPtr msg) {
                target_ = msg->data;
            });

        // 订阅实际值 (Measurement)
        sub_state_ = this->create_subscription<std_msgs::msg::Float64>(
            "state", 10, [this](const std_msgs::msg::Float64::SharedPtr msg) {
                current_state_ = msg->data;
            });

        // 发布控制量 (Control Output)
        pub_control_ = this->create_publisher<std_msgs::msg::Float64>("control_output", 10);

        // --- 4. 定时控制循环 (例如 50Hz) ---
        // 注意：控制频率应与实际硬件需求匹配
        double loop_rate = 50.0;
        dt_ = 1.0 / loop_rate;
        timer_ = this->create_wall_timer(
            std::chrono::duration<double>(dt_), 
            std::bind(&PIDNode::controlLoop, this));

        // --- 5. 启用动态参数回调 ---
        param_callback_handle_ = this->add_on_set_parameters_callback(
            std::bind(&PIDNode::parametersCallback, this, std::placeholders::_1));

        RCLCPP_INFO(this->get_logger(), "PID Node started. Kp:%.2f Ki:%.2f Kd:%.2f", kp, ki, kd);
    }

private:
    // PID 逻辑对象
    std::unique_ptr<PIDController> pid_controller_;

    // ROS 通信对象
    rclcpp::Subscription<std_msgs::msg::Float64>::SharedPtr sub_setpoint_;
    rclcpp::Subscription<std_msgs::msg::Float64>::SharedPtr sub_state_;
    rclcpp::Publisher<std_msgs::msg::Float64>::SharedPtr pub_control_;
    rclcpp::TimerBase::SharedPtr timer_;
    OnSetParametersCallbackHandle::SharedPtr param_callback_handle_;

    // 变量
    double target_ = 0.0;
    double current_state_ = 0.0;
    double dt_ = 0.02;

    // 控制循环
    void controlLoop() {
        // 计算
        double output = pid_controller_->calculate(target_, current_state_, dt_);

        // 发布
        std_msgs::msg::Float64 msg;
        msg.data = output;
        pub_control_->publish(msg);
    }

    // 动态参数回调 (运行时修改 PID)
    rcl_interfaces::msg::SetParametersResult parametersCallback(
        const std::vector<rclcpp::Parameter> &parameters) 
    {
        rcl_interfaces::msg::SetParametersResult result;
        result.successful = true;
        result.reason = "success";

        // 读取当前 PID 参数以备更新
        double kp = this->get_parameter("kp").as_double();
        double ki = this->get_parameter("ki").as_double();
        double kd = this->get_parameter("kd").as_double();
        double alpha = this->get_parameter("alpha").as_double();

        for (const auto &param : parameters) {
            if (param.get_name() == "kp") kp = param.as_double();
            if (param.get_name() == "ki") ki = param.as_double();
            if (param.get_name() == "kd") kd = param.as_double();
            if (param.get_name() == "alpha") alpha = param.as_double();
        }

        // 更新控制器
        pid_controller_->setGains(kp, ki, kd);
        pid_controller_->setFilter(alpha);

        RCLCPP_INFO(this->get_logger(), "Parameters updated: P:%.2f I:%.2f D:%.2f Alpha:%.2f", kp, ki, kd, alpha);
        return result;
    }
};

int main(int argc, char **argv) {
    rclcpp::init(argc, argv);
    rclcpp::spin(std::make_shared<PIDNode>());
    rclcpp::shutdown();
    return 0;
}
```

`pid_controller.hpp` \(头文件\)

```Java
#ifndef PID_CONTROLLER_HPP_
#define PID_CONTROLLER_HPP_

#include <algorithm>
#include <cmath>

class PIDController {
public:
    /**
     * @brief 构造函数
     * @param kp 比例增益
     * @param ki 积分增益
     * @param kd 微分增益
     * @param max_out 输出最大值 (如电机最大电压)
     * @param min_out 输出最小值
     * @param alpha 微分滤波系数 (0.0 ~ 1.0, 1.0=不滤波, 推荐 0.3~0.5)
     */
    PIDController(double kp, double ki, double kd, 
                  double max_out, double min_out, 
                  double alpha = 0.5)
        : kp_(kp), ki_(ki), kd_(kd), 
          max_out_(max_out), min_out_(min_out), 
          alpha_(alpha) {}

    /**
     * @brief 计算控制量
     * @param setpoint 目标值
     * @param measurement 实际测量值
     * @param dt 采样时间 (秒)
     */
    double calculate(double setpoint, double measurement, double dt);

    // 重置控制器状态
    void reset();

    // 动态设置参数的接口
    void setGains(double kp, double ki, double kd) { kp_ = kp; ki_ = ki; kd_ = kd; }
    void setFilter(double alpha) { alpha_ = std::clamp(alpha, 0.0, 1.0); }

private:
    // 参数
    double kp_, ki_, kd_;
    double max_out_, min_out_;
    double alpha_; // 滤波系数

    // 状态变量
    double integral_sum_ = 0.0;
    double prev_error_ = 0.0;
    double prev_derivative_ = 0.0; // 记录上一次滤波后的微分项
};

#endif // PID_CONTROLLER_HPP_
```

应用流程

第一步：编译与运行

```Plain Text
假设你的包名叫 my_pid_pkg：
colcon build --packages-select my_pid_pkg
ros2 run my_pid_pkg pid_node
```

第二步：数据流连接

```Plain Text
你需要外部的节点配合：
**发布目标**：例如发布 1.0 到 /setpoint (表示想要 1m/s)。
**发布反馈**：你的电机驱动节点必须读取编码器，发布当前速度到 /state。
**接收控制**：你的电机驱动节点必须订阅 /control_output，并将其作为 PWM 或电压给电机。
```

第三步：实战调参 \(Tuning\)

打开终端，使用命令行动态调参：

```Plain Text
**第一步：仅调节 P (Start with P):**
ros2 param set /robust_pid_node kp 0.5
ros2 param set /robust_pid_node ki 0.0
ros2 param set /robust_pid_node kd 0.0

*现象*：电机动起来了，但可能达不到目标（有稳态误差），或者有点晃动。
```

```Plain Text
**第二步：调节 D (Add D for Stability):**
ros2 param set /robust_pid_node kd 0.05
ros2 param set /robust_pid_node alpha 0.3

*现象*：晃动减少，运动变平滑。如果电机啸叫，减小 alpha (更强滤波) 或减小 kd。
```

```Plain Text
**第三步：调节 I (Add I for Accuracy):**
ros2 param set /robust_pid_node ki 0.1

*现象*：之前达不到目标的那个小差距被慢慢消除了。
```

第四步：数据可视化

```Plain Text
使用 rqt_plot 同时观察 /setpoint (目标) 和 /state (实际)。

现象：应该出现红线和蓝线重合
```

## 第四章 相关算法解析

### 1\.AMCL滤波算法

AMCL 是一个粒子滤波算法，它需要以下三种核心数据才能工作：

**观测数据（必须）：****`sensor_msgs/msg/LaserScan`**

```Plain Text
这是 AMCL 的“眼睛”。
**作用：** 它提供了机器人周围障碍物的实时距离信息。
**原理：** AMCL 会把激光雷达扫描到的轮廓（比如一面墙的距离），去和已知的静态地图进行匹配。如果匹配度高，粒子权重就大；匹配度低，粒子就会消失。
**没有它的后果：** 粒子无法收敛，机器人不知道自己在地图的哪里。
```

**静态地图（必须）：****`nav_msgs/msg/OccupancyGrid`**

```Plain Text
这是 AMCL 的“参考书”。
通常由 map_server 发布。AMCL 需要在这个地图上撒粒子。
```

**里程计信息（必须）：****`nav_msgs/msg/Odometry`**** 和 TF 变换**

```Plain Text
这是 AMCL 的“直觉”（通常来自轮子编码器或 IMU）。
**作用：** 当机器人移动时，里程计告诉 AMCL “我大概走了 1 米”。
**原理：** AMCL 利用里程计数据进行**预测（Predict）**（推算粒子群的移动），利用激光雷达数据进行**更新（Update）**（修正粒子群的位置）。
```

请注意：如果你没有激光雷达（LiDAR）也没有深度相机（无法转换出激光数据），那么你无法使用 AMCL。

### 2\.PID算法

#### 2\.1核心理论回顾

PID 控制的核心目标是根据**误差**（设定值 \- 实际值）来计算**控制量**，使系统稳定、快速地达到目标。

在实际工程中，我们使用的不仅仅是教科书上的公式，而是“鲁棒 PID”：

$u(t) = \underbrace{K_p e(t)}_{\text{P: 响应现状}} + \underbrace{\text{Clamp}(K_i \int e(\tau) d\tau)}_{\text{I: 消除偏差 (带抗饱和)}} + \underbrace{K_d \cdot \text{LPF}(\frac{de(t)}{dt})}_{\text{D: 抑制震荡 (带滤波)}}$

```Plain Text
关键组件说明：
**P (比例)**：决定响应速度。越大越快，太大会震荡。
**I (积分)**：消除稳态误差。**抗饱和 (Anti-windup)** 是必须的，防止误差积累导致超调。
**D (微分)**：提供阻尼，抑制震荡。**低通滤波 (LPF)** 是必须的，防止噪声导致电机啸叫。
```

#### 2\.2 C\+\+ 代码实现 \(Library\)

为了保持代码整洁，我们将 PID 算法封装为一个独立的类，不依赖 ROS，这样你可以单独进行单元测试。

##### 2\.2\.1 `pid_controller.hpp` \(头文件\)

```Java
#ifndef PID_CONTROLLER_HPP_
#define PID_CONTROLLER_HPP_

#include <algorithm>
#include <cmath>

class PIDController {
public:
    /**
     * @brief 构造函数
     * @param kp 比例增益
     * @param ki 积分增益
     * @param kd 微分增益
     * @param max_out 输出最大值 (如电机最大电压)
     * @param min_out 输出最小值
     * @param alpha 微分滤波系数 (0.0 ~ 1.0, 1.0=不滤波, 推荐 0.3~0.5)
     */
    PIDController(double kp, double ki, double kd, 
                  double max_out, double min_out, 
                  double alpha = 0.5)
        : kp_(kp), ki_(ki), kd_(kd), 
          max_out_(max_out), min_out_(min_out), 
          alpha_(alpha) {}

    /**
     * @brief 计算控制量
     * @param setpoint 目标值
     * @param measurement 实际测量值
     * @param dt 采样时间 (秒)
     */
    double calculate(double setpoint, double measurement, double dt);

    // 重置控制器状态
    void reset();

    // 动态设置参数的接口
    void setGains(double kp, double ki, double kd) { kp_ = kp; ki_ = ki; kd_ = kd; }
    void setFilter(double alpha) { alpha_ = std::clamp(alpha, 0.0, 1.0); }

private:
    // 参数
    double kp_, ki_, kd_;
    double max_out_, min_out_;
    double alpha_; // 滤波系数

    // 状态变量
    double integral_sum_ = 0.0;
    double prev_error_ = 0.0;
    double prev_derivative_ = 0.0; // 记录上一次滤波后的微分项
};

#endif // PID_CONTROLLER_HPP_
```

##### 2\.2\.2 `pid_controller.cpp` \(源文件\)

这里包含了**积分限幅**和**微分滤波**的具体实现。

```Java
#include "pid_controller.hpp"

void PIDController::reset() {
    integral_sum_ = 0.0;
    prev_error_ = 0.0;
    prev_derivative_ = 0.0;
}

double PIDController::calculate(double setpoint, double measurement, double dt) {
    if (dt <= 0.0) return 0.0; // 保护机制

    // 1. 计算误差
    double error = setpoint - measurement;

    // 2. 比例项 (P)
    double p_term = kp_ * error;

    // 3. 积分项 (I) - 带抗饱和 (Clamping)
    integral_sum_ += error * dt;
    
    // 计算积分项输出
    double i_term = ki_ * integral_sum_;

    // 抗饱和核心：如果积分项过大，限制它
    // 策略：通常限制积分项不超过总输出的 30%~50% 或直接限制在 [min_out, max_out]
    double i_limit = max_out_ * 0.5; 
    if (i_term > i_limit) {
        i_term = i_limit;
        integral_sum_ = i_limit / ki_; // 反向更新累积值
    } else if (i_term < -i_limit) {
        i_term = -i_limit;
        integral_sum_ = -i_limit / ki_;
    }

    // 4. 微分项 (D) - 带低通滤波 (LPF)
    // 原始微分
    double derivative_raw = (error - prev_error_) / dt;
    
    // 滤波公式：Output = alpha * New + (1-alpha) * Old
    double derivative_filtered = alpha_ * derivative_raw + (1.0 - alpha_) * prev_derivative_;
    prev_derivative_ = derivative_filtered; // 更新历史值

    double d_term = kd_ * derivative_filtered;

    // 5. 总输出 + 限幅
    double output = p_term + i_term + d_term;
    output = std::clamp(output, min_out_, max_out_);

    // 6. 更新状态
    prev_error_ = error;

    return output;
}
```

#### 2\.3 ROS 2 节点实现 \(Node\)

详见：实例（7）ROS 2 节点实现PID控制器的动态调参[ROS2\-humble学习笔记](https://xcne49dnyrws.feishu.cn/docx/B3spdRmoSoGZZBxUkJ7cKaRZnNf#share-GrJEdqMxooaA7Vxp4wAc5yrFnPg)





## 第五章 实际操作

### 操作（1）寻找good first issue

需求：熟悉git操作，接入世界发展

```Plaintext
方法：
    在github上进行查找：
    查找简单的一些新手问题。
    is:issue is:open label:"good first issue" "ros" OR "ros2" no:assignee

作用：熟悉git全流程操作，熟悉多人协作流程。了解多人项目的开发过程，以及由相关问题引出的知识点部分。
```

### 操作（2）寻找高质量的C\+\+练习

需求：提高C\+\+学习水平

```Plaintext
方法：
    在github上进行查找：
    is:issue is:open label:"good first issue" language:c++ "ros2" stars:>200 no:assignee

作用：纯粹的 C++ 工程代码修复，加上语言限制，并过滤掉一些没有关注度的小项目：

    org:ros2 is:issue is:open label:bug -label:"good first issue" language:c++ no:assignee
    去 ROS 2 官方核心组织（如 rclcpp 客户端库、rcutils 底层工具、ros2cli 工具链等）寻找那些涉及代码逻辑的真实 Bug。这些任务能逼着深入到 ROS 2 的源码框架里去。
    
    is:issue is:open "ros2" (performance OR refactor OR deadlock OR "memory leak") language:c++ stars:>300 no:assignee
    很多优秀的机器人开源项目会挂出需要性能优化、代码重构或者多线程安全性升级的任务。搜这些关键词能让你学到如何写出高性能、高鲁棒性的机器人代码。
    这里我们锁定了 performance（性能）、refactor（重构）、deadlock（死锁，深入研究 Executor 和 CallbackGroup 的好机会）以及 memory leak（内存泄漏）等硬核技术词。
```

### 操作（3）进大厂

需求：进大厂，提高个人水平

```Plaintext
方法：
    在github上进行查找：
    org:ros2 is:issue is:open label:"good first issue" no:assignee
    
作用：ROS 2 的官方核心组件（如 rclcpp、rmw、ros2cli 等）都集中在 ros2 这个组织下。这里的项目代码极其规范，非常适合去学习大厂的架构和 Review 流程。
```





### QoS 策略、话题重映射（remapping）





# 书末页，便于评论和进行内容补充，请勿修改等级



