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



