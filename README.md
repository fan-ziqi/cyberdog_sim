# cyberdog_sim

小米铁蛋仿真。

## 环境

* Ubuntu-18.04+ROS-Melodic
* Ubuntu-20.04+ROS-Noetic

## 部署

```bash
mkdir cyberdogsim_ws && cd cyberdogsim_ws
mkdir src && cd src
git clone https://github.com/fan-ziqi/unitree_guide.git
git clone https://github.com/fan-ziqi/unitree_ros.git
cd ..
catkin_make -DCATKIN_WHITELIST_PACKAGES="unitree_guide;unitree_legged_msgs;unitree_move_base;cyberdog_description;unitree_gazebo;unitree_legged_control;"
source ./devel/setup.bash
```

## 使用仿真

### rviz可视化

```bash
roslaunch cyberdog_description display.launch
```

拖动控制条即可可视化关节角度

### gazebo仿真

开启gazebo环境

```bash
roslaunch unitree_guide gazeboSimCyberdog.launch
```

新开一个终端，运行控制程序。首先进入`/cyberdogsim_ws`目录下，再进行下面的操作。

代码使用了实时进程，由于ROS 的限制，并不能直接在命令前面添加sudo，而是需要先切换到root
用户，再运行junior_ctrl

```bash
sudo su
source ./devel/setup.bash
rosrun unitree_guide junior_ctrl
```

或者：直接使用sudo运行可执行文件

```bash
sudo ./devel/lib/unitree_guide/junior_ctrl
```

控制器成功运行后，按下键盘的`2`进入固定站立模式，下述模式均需要在固定站立模式下进入：

* 按`1`进入阻尼模式（机器人关节进入阻尼状态，机器人会趴下）
* 按`3`进入姿态控制模式（此时使用键盘的wasd、ijkl可以改变机器人的姿态）
* 按`4`进入步态模式（此时使用键盘的wasd、jl可以控制机器人运动）
* 按`7`进入后空翻模式

注：步态默认为trot，更多步态需要在`src/unitree_guide/unitree_guide/src/main.cpp`中进行更改

## 控制实物

注意：后空翻是实验功能，请勿于真机测试。步态还有问题，暂时不要测试。

### 在有ROS环境的主机中运行代码

修改`src/unitree_guide/unitree_guide/CMakeLists.txt`中的下述宏定义

```cmake
set(CATKIN_MAKE ON)
set(SIMULATION OFF)
set(REAL_ROBOT ON)
```

单独编译`unitree_guide`包

```bash
catkin_make -DCATKIN_WHITELIST_PACKAGES="unitree_guide”
```

用sudo权限运行`junior_ctrl`，操作与上文**gazebo仿真**中运行`junior_ctrl`的步骤一样，不再赘述。

### 在运控板中编译代码

修改`src/unitree_guide/unitree_guide/CMakeLists.txt`中的下述宏定义

```cmake
set(CATKIN_MAKE OFF)
set(SIMULATION OFF)
set(REAL_ROBOT ON)
```

然后进入`src/unitree_guide/unitree_guide`目录下

```
mkdir build && cd build
cmake ..
make -j8
```

用sudo权限运行`junior_ctrl`，路径为：`build/bin/junior_ctrl`

### 交叉编译

参见铁蛋官方文档
