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

Downloading simulator source code into src folder
cd /lab1_ws/src
git clone https://github.com/f1tenth/f1tenth_gym_ros.git

ROS2 Foxy is Python 3.8 based, therefore we are going to use pip3 command to install f1tenth simulator engine, in order to do that, let's install pip3 first.
package list update: sudo apt update
pip install: sudo apt install python3-pip
press y for y/n question
