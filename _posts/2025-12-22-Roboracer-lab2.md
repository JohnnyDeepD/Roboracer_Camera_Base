---
layout: default
---

## Roboracer lab2

Start docker container: docker start -i f1tenth_lab1
create lab2 package:
cd lab1_ws/src
ros2 pkg create --build-type ament_python lab2_pkg --dependencies rclpy sensor_msgs nav_msgs ackermann_msgs

review for dependencies:
- rclpy: ROS 2 python communication library
- sensor_msgs: sensor information such as Lidar data
- nav_msgs: vehicle location and velocity information
- ackermann_msgs: vehicle steering and acceleration command

check with ls command if lab2_pkg is successfully made

Download f1tenth_gym simulator source code into src folder (ROS2 interface and Engine itself)
cd /lab1_ws/src
git clone https://github.com/f1tenth/f1tenth_gym_ros.git
git clone https://github.com/f1tenth/f1tenth_gym.git

ROS2 Foxy is Python 3.8 based, therefore we are going to use pip3 command to install f1tenth_gym simulator engine, in order to do that, let's install pip3 first.
package list update: sudo apt update
pip install: sudo apt install python3-pip
press y for y/n question

Install f1tenth_gym simulator engine with pip3
pip3 install git+https://github.com/f1tenth/f1tenth_gym.git

or go to the f1tenth_gym in src folder (check if the folder is there with ls -F command) by cd command and use editable installation command
pip3 install -e.

see if it is successfully installed with: pip3 list | grep f1tenth

simulator package build for ROS2 interface (f1tenth_gym_ros)
cd /lab1_ws                                      #go to workspace root
colcon build --packages-select f1tenth_gym_ros   #build simulator package
source install/setup.bash                        #for ROS2 to acknowledge new package

see if Finished <<< f1tenth_gym_ros line comes up

checking the simulator
ros2 launch f1tenth_gym_ros gym_bridge_launch.py

rviz2 not found error:
<img width="974" height="597" alt="image" src="https://github.com/user-attachments/assets/6e3206e7-8417-4819-bc82-54341344b4d7" />

rviz2 is the GUI, we could only work with data flow without GUI, but this lab2 is with GUI
We do not have nVidia GPU at the moment, so we need to set up something for GUI

VNC is slow, and Rocker needs installation.
Although they help me to use more simple codes, we are going to try X11 at the moment, because in linux (Ubuntu) system it is the basic GUI system.

Start new terminal
docker --version
docker images
ls -F

we are using Ubuntu 22.04 host
image of ros:foxy, the very basic ROS2 image
and our codes are in lab1_ws

for docker to have window pop up rights
xhost +local:root

container creation and execution
docker run -it \
  --name f1tenth_lab2 \
  --net=host \
  --env="DISPLAY" \
  --env="QT_X11_NO_MITSHM=1" \
  --volume="/tmp/.X11-unix:/tmp/.X11-unix:rw" \
  -v $(pwd)/lab1_ws:/lab1_ws \
  ros:foxy

check with ls /lab1_ws
src shows that we are in new container with window pop up right correctly

checking with basic program
package list update: apt-get update
simple program install: apt-get install -y x11-apps
execute the program: xeyes

we are going to install the f1tenth gym again to the docker that has this right to pop up new screen
We do not have to worry about memory space because this is the advantage of Docker, it will only increase slightly with new Docker setup, and the download file itself stays as 1 file.

go to engine source code
cd /lab1_ws/src/f1tenth_gym

package update and pip3 install (need update because we are getting tools from the internet)
apt-get update
apt-get install -y python3-pip

install engine with pip3, with the setup within the folder
pip3 install -e .

go to workspace and build for ROS2 to aknowledge the libraries that we just installed
cd /lab1_ws
colcon build

there are errors because this is new Docker, need to install f1tenth package again from lab1
apt-get update
apt-get install -y ros-foxy-ackermann-msgs
colcon build

Checking for the simulator
Load system setup after build
source install/setup.bash

launch simulator
ros2 launch f1tenth_gym_ros gym_bridge_launch.py

<img width="976" height="615" alt="image" src="https://github.com/user-attachments/assets/d0fe7439-222e-4aa3-9023-3b56b5aa417c" />
rviz2 is not found, because it is not installed!

install rviz2
apt-get update
apt-get install -y ros-foxy-rviz2

launch again
ros2 launch f1tenth_gym_ros gym_bridge_launch.py
<img width="976" height="640" alt="image" src="https://github.com/user-attachments/assets/dc4cc179-d513-44d0-9ac1-8bdcc541b03d" />
looks like we got to install more packages!

installing necessary libraries
pip3 install scipy numba networkx pandas shapely PyYAML

try again
cd /lab1_ws
colcon build
source install/setup.bash
ros2 launch f1tenth_gym_ros gym_bridge_launch.py

More packages and libraries needed!
apt-get update

ROS2 navigation lifecycle and map packages# 2. ROS 2 내비게이션 관련 패키지 설치
apt-get install -y ros-foxy-nav2-lifecycle-manager ros-foxy-nav2-map-server ros-foxy-nav2-bringup

Python math library
pip3 install transforms3d

Try again!
source install/setup.bash
ros2 launch f1tenth_gym_ros gym_bridge_launch.py

still error, it says xacro is needed
apt-get update
apt-get install -y ros-foxy-xacro

try again
source install/setup.bash
ros2 launch f1tenth_gym_ros gym_bridge_launch.py

<img width="1214" height="750" alt="image" src="https://github.com/user-attachments/assets/d5b73c3d-ed1c-45c1-a483-89702da48ad6" />
There we go!
