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

crtl+c for closing RViz2
exit for container exit

[ start docker again ] 
xhost +local:root
docker start -i f1tenth_lab2

go to workspace
cd /lab1_ws

open RViz again
source install/setup.bash
ros2 launch f1tenth_gym_ros gym_bridge_launch.py

[making safety_node.py]
go to directory: cd /lab1_ws/src/lab2_pkg
make scripts folder like lab1 (general ROS2 way of making python files): mkdir -p scripts
make file: touch scripts/safety_node.py

to edit python files, install nano again to this new Docker
apt-get update
apt-get install -y nano

open setup.py and edit
nano /lab1_ws/src/lab2_pkg/setup.py

entry_points={
        'console_scripts': [
            'safety_node = lab2_pkg.scripts.safety_node:main',
        ],
    },

ctrl+o and enter for save, then ctrl+X to exit

go to scripts folder and edit safety_node.py with nano

#import libraries
import rclpy                              # for ROS2
import math                        # for math.cos() calculation at Time to Collision
from rclpy.node import Node

from sensor_msgs.msg import LaserScan     # for lidar sensor data
from nav_msgs.msg import Odometry         # for vehicle velocity
from ackermann_msgs.msg import AckermannDriveStamped   # for vehicle control

class SafetyNode(Node):                    # node class inheritance

    # constructor
    def __init__(self):                    
        super().__init__('safety_node')    # give a name

        # initialize
        self.speed = 0.0

        #subscriber (lidar)
        self.scan_subscription = self.create_subscription(  # subscribe data
            LaserScan,                                 # receive lidar data
            'scan',                                    # topic name in ROS2
            self.scan_callback,            # a function that runs for every new data
            10)                 # queue size to keep in the buffer before processing
                
        #publisher (vehicle control)
        self.publisher = self.create_publisher(
            AckermannDriveStamped,                    # transmit drive command
            'drive',                                  # topic name in ROS2
            10)                 # queue size to keep in the buffer before processing          
        #subscriber (velocity)
        self.odom_subscription = self.create_subscription(  # subscribe data
            Odometry,                                  # receive velocity
            'odom',                                    # topic name in ROS2
            self.odom_callback,            # a function that runs for every new data
            10)                 # queue size to keep in the buffer before processing
                
        def odom_callback(self, msg):                # msg object as an argument
            self.speed = msg.twist.twist.linear.x    # velocity is in this path of msg
            
        def scan_callback (self, msg):               # msg object as an argument
            for i,r in enumerate(msg.ranges):        # get both index and range
                theta = msg.angle_min + msg.angle_increment * i    # theta calc
                v_rel = self.speed * math.cos(theta) #relative velocity calc

                if v_rel > 0:         # if v_rel is valid
                    ttc = r/v_rel     # calculate time to collision

                    if ttc<0.5:       # if there is collision within 0.5 sec
                        drive_msg = AckermannDriveStamped() # create message object
                        drive_msg.drive.speed = 0.0         # initialize speed to 0
                        self.publisher.publish(drive_msg)   # publish message
                        self.get_logger().warn("AEB Active! Braking...") # leave log
                        
