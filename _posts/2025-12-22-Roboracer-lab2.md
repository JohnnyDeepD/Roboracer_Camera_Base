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

    #methods
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
                    break          # no more loop after the danger

def main(args=None):
    rclpy.init(args=args)       # start the communication
    node = SafetyNode()    # create object with SafetyNode class
    
    try:
        rclpy.spin(node)        # run node for callback functions
    except KeyboardInterrupt:   # Ctrl+C for quit
        pass
    
    # exit
    node.destroy_node()      
    rclpy.shutdown()        

if __name__ == "__main__":
  main()

<img width="815" height="578" alt="image" src="https://github.com/user-attachments/assets/5c3d3fed-8246-4f69-8508-014a681ce865" />
This is where we saved the python file

need tmux for this docker again, one terminal for rviz and another for running the node we made

[install tmux]
apt-get update
apt-get install -y tmux

start tmux with tmux command and make terminals with ctr+b,c and move with crt+b, n
exit with exit

[open rviz in 1 terminal]
source install/setup.bash
ros2 launch f1tenth_gym_ros gym_bridge_launch.py

[runs node in another terminal]
cd /lab1_ws
source install/setup.bash
ros2 run lab2_pkg safety_node

no executable found, got to update settings
<img width="918" height="641" alt="image" src="https://github.com/user-attachments/assets/0ebe92bb-9701-4f38-beb9-3e673499ee52" />

colcon build --packages-select lab2_pkg
<img width="918" height="641" alt="image" src="https://github.com/user-attachments/assets/b68b103f-12d0-4cc2-bca2-70eac3c13bf4" />
did it in wrong folder, remove build, install, log folder for clearance with
rm -rf build install log
(we have to do this in the right folder because the path might get confused when packaging)

#go back to workspace
cd /lab1_ws
colcon build --packages-select lab2_pkg

#run again
ros2 run lab2_pkg safety_node
<img width="918" height="641" alt="image" src="https://github.com/user-attachments/assets/3bc875e8-305e-44d3-9688-c79b4709e797" />
It cannot find safety_node.py in scripts folder, so let's move it to main package folder for simplicity.

#move file to python pkg folder
mv src/lab2_pkg/scripts/safety_node.py src/lab2_pkg/lab2_pkg/

#delete scripts folder
rm -rf src/lab2_pkg/scripts

#fix the entry_points in setup.py
nano src/lab2_pkg/setup.py

#delete .scripts
'safety_node = lab2_pkg.safety_node:main',
(crt+o, enter, ctr+x)

#build again and run
cd /lab1_ws
colcon build --packages-select lab2_pkg
source install/setup.bash
ros2 run lab2_pkg safety_node

now it looks like it is waiting for the car to collide
<img width="918" height="641" alt="image" src="https://github.com/user-attachments/assets/78460f0a-3cc4-4286-9b8f-180717d95bf7" />

#try to move the car at another tmux terminal
source install/setup.bash
ros2 topic pub /drive ackermann_msgs/msg/AckermannDriveStamped "{drive: {speed: 2.0}}"

car not moving
<img width="1036" height="489" alt="image" src="https://github.com/user-attachments/assets/22e8d703-e286-4f18-bf3c-7ec1bfdd0e47" />

found that the map and car is not found in the simulator
<img width="796" height="561" alt="image" src="https://github.com/user-attachments/assets/855e0614-7fee-4627-8c8c-a73eec19fd00" />

[scroll tmux]
crt+b, [, arrows, 
q for quit

levine.yaml map error with rviz
<img width="813" height="561" alt="image" src="https://github.com/user-attachments/assets/4e748bc3-bc5a-4635-92c3-5ad5bf5b573b" />

let's debug
#find levine.yaml
find / -name "levine.yaml"

the file is in /lab1_ws
/lab1_ws/src/f1tenth_gym/gym/f110_gym/envs/maps/levine.yaml
/lab1_ws/src/f1tenth_gym_ros/maps/levine.yaml

But the system is looking for it in /sim_ws

#fine anything with sim_ws
grep -r "sim_ws" /lab1_ws/src

got to change this part:
/lab1_ws/src/f1tenth_gym_ros/config/sim.yaml: map_path: '/sim_ws/src/f1tenth_gym_ros/maps/levine'

#open the file and change
nano /lab1_ws/src/f1tenth_gym_ros/config/sim.yaml

before: map_path: '/sim_ws/src/f1tenth_gym_ros/maps/levine'
after: map_path: '/lab1_ws/src/f1tenth_gym_ros/maps/levine'

lets, build and source and launch again
cd /lab1_ws
colcon build --packages-select f1tenth_gym_ros
source install/setup.bash
ros2 launch f1tenth_gym_ros gym_bridge_launch.py
<img width="944" height="599" alt="image" src="https://github.com/user-attachments/assets/8a3b6c1d-952f-440b-a965-2574d14af597" />
now there is the levine hall!

but some errors are there, let's debug 
<img width="944" height="599" alt="image" src="https://github.com/user-attachments/assets/26959786-4119-4537-951d-8554554f9f64" />

Map coordinates seems to be out of connection
<img width="1215" height="706" alt="image" src="https://github.com/user-attachments/assets/e7166375-13bf-4499-bd70-64ec17e23d5d" />

There is no map frame, 
<img width="1215" height="752" alt="image" src="https://github.com/user-attachments/assets/a980dc29-0293-458c-ad38-9881761a7911" />

rviz shows this error, 
<img width="1215" height="752" alt="image" src="https://github.com/user-attachments/assets/6ad0da6d-efea-44cd-b9d7-fa1aa86eed0e" />

[gym_bridge-2] Traceback ... pkg_resources ...
this error indicates that gym_bridge dies, so let's check if python library version synch can fix this problem.

pip3 install setuptools==58.2.0

after updating python version there is still the same error, so I scrolled up to find the exact error message
<img width="1215" height="752" alt="image" src="https://github.com/user-attachments/assets/b26be47a-e5e6-40e2-b267-339bdaa634f9" />

we are missing a library!
pip3 install transforms3d

launch again
still error, 
hard to find in terminal so I tried using log file

ros2 launch f1tenth_gym_ros gym_bridge_launch.py > debug.txt 2>&1
nano debug.txt

with ctrl w (where is) I can find the text I want, and alt+w for next
<img width="1215" height="752" alt="image" src="https://github.com/user-attachments/assets/eb43da67-b424-4355-97ba-9e8d49d36152" />

The error shows compatibility error of llvmlite and AttributeError: 'GEPInstr'...
so compatible versions shall be installed
pip3 install numba==0.53.1 llvmlite==0.36.0

#numpy old version for numba old version 
pip3 install "numpy<1.20"    

lauch still error
<img width="1215" height="752" alt="image" src="https://github.com/user-attachments/assets/51ebc072-ca81-43ec-874d-fc8aa2bc9808" />
we are in dependency hell now.

[I thought of moving on with C++, but the whole gym is made of python for ai system algorithm practice, so I will continue with python, and make it C++ later for real world implementation, the time and memory efficiency in real driving environment.]

#try with medium version
pip3 install numba==0.56.4 llvmlite==0.39.1
pip3 install numpy==1.21.6

<img width="1215" height="752" alt="image" src="https://github.com/user-attachments/assets/b04c8652-165e-461b-b2f0-ddf577535c58" />
It is working! No error!

alright now let's check the movement of the car with Ackermann drive command
ros2 topic pub /drive ackermann_msgs/msg/AckermannDriveStamped "{drive: {speed: 2.0, steering_angle: 0.5}}"

https://github.com/user-attachments/assets/34a3a63c-c31e-4531-a577-43ba06ac3a0f

<video width="100%" controls>
  <source src="https://github.com/user-attachments/assets/34a3a63c-c31e-4531-a577-43ba06ac3a0f" type="video/mp4">
  Your browser does not support the video tag.
</video>

It is working! Also this is our first video!

