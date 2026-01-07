---
layout: default
---

## Roboracer Lab3

Before starting lab3, I would like to change the name of the docker and workspace. Because we are using the name lab2 and lab1 for the docker and workspace folder. 

docker rename f1tenth_lab2 robo_lab

#change directory name
cd ~
mv lab1_ws robo_ws

<img width="844" height="395" alt="image" src="https://github.com/user-attachments/assets/1a86e08a-f3c6-43bf-8fdb-02ce5538fff0" />
this does not work because lab1_ws is a Mount folder, which means that it is connecting the docker to our computer. 
For simplicity, I will just create a shortcut.

cd ~ 
ln -s /lab1_ws robo_ws

check with ls -l (need blue color for proper connection)
<img width="934" height="663" alt="image" src="https://github.com/user-attachments/assets/e24a2dab-0644-4fef-aeb7-c821b362111b" />

#now remove old built folders and make new for our new address robo_ws command
cd ~/robo_ws
rm -rf build install log
colcon build

<img width="1020" height="663" alt="image" src="https://github.com/user-attachments/assets/b3a6c8da-9e81-424d-9e8d-978d120552ce" />
build successful. (ignore python version warning)

let's make lab3_pkg in the VirtualBox Docker, in robo_ws
<img width="718" height="504" alt="image" src="https://github.com/user-attachments/assets/0693fd8f-ed8c-44ef-b54c-7bbfaa03f698" />

#right after restarting the VirtualBox and Ubuntu terminal
xhost +local:root
docker start -i robo_lab
cd ~/robo_ws/src

#for all the sensor_msgs and ackermann_msgs dependencies
ros2 pkg create --build-type ament_python lab3_pkg --dependencies rclpy sensor_msgs nav_msgs ackermann_msgs geometry_msgs

#check with ls, lab3_pkg folder is there right?

#go back to workspace and build for the package (for only lab3_pkg!)
cd ~/robo_ws
colcon build --packages-select lab3_pkg
source install/setup.bash

#see if Summary: 1 package finished message is there
<img width="718" height="563" alt="image" src="https://github.com/user-attachments/assets/2d584441-f6cd-4214-bd5a-c4dbc4609606" />

#now let's create wall_follower python file

#go to directory
cd ~/robo_ws/src/lab3_pkg/lab3_pkg
#make python file
touch wall_follower.py
#for execution rights
chmod +x wall_follower.py
#open the file
nano wall_follower.py

#the code for wall_folower.py
import rclpy
from rclpy.node import Node
from sensor_msgs.msg import LaserScan
from ackermann_msgs.msg import AckermannDriveStamped, AckermannDrive

class WallFollower(Node):
    def __init__(self):
        super().__init__('wall_follower')
        
        # 1. subscribe lidar sensor data
        # receive /scan topic and run listener_callback function
        self.subscription = self.create_subscription(
            LaserScan,
            '/scan',
            self.lidar_callback,
            10)
            
        # 2. publish Drive command
        # send command to /drive topic to move the car
        self.publisher_ = self.create_publisher(
            AckermannDriveStamped,
            '/drive',
            10)

        # PID control variables (will be tuned later)
        self.kp = 0.5
        self.kd = 0.0
        self.ki = 0.0
        self.prev_error = 0.0
        self.integral = 0.0

    def lidar_callback(self, msg):
        # place for the main logic
        # 1. get distance data from lidar data(msg)
        # 2. calculate distance difference(error) from the wall
        # 3. calculate PID and make decision for steering angle
        
        # temporary test: If a wall is in front stop, or proceed (will be deleted later)
        drive_msg = AckermannDriveStamped()
        drive_msg.drive.speed = 1.0
        drive_msg.drive.steering_angle = 0.0
        self.publisher_.publish(drive_msg)

def main(args=None):
    rclpy.init(args=args)
    wall_follower = WallFollower()
    rclpy.spin(wall_follower)
    wall_follower.destroy_node()
    rclpy.shutdown()

if __name__ == '__main__':
    main()
