---
layout: default
---

## Roboracer 2nd day
Ubuntu Installation with Virtualbox is done so it is time to install Docker in Ubuntu.

Open Terminal in Ubuntu
1. update system : sudo apt update
2. install docker: sudo apt install docker.io -y
3. append user: sudo usermod -aG docker $USER
4. reboot(for user rights): sudo reboot
5. check docker: docker --version

[moving on to lab 1]
Now we have VirtualBox VM (Ubuntu) and Docker, we will use 2.2 (Docker) for lab1

1. Make working directory: mkdir -p ~/lab1_ws/src
2. Download ROS2 Foxy image and run docker container: docker run -it -v ~/lab1_ws/src:/lab1_ws/src --name f1tenth_lab1 ros:foxy
3. install tmux for multiple terminal inside Docker Container: apt update && apt install tmux

[How to use tmux]
type tmux in terminal to start tmux session.
Ctrl+b, then press next keys:
c: new terminal
n: next terminal
d: detach terminal

[ROS2 basics working fine]
source /opt/ros/foxy/setup.bash
ros2 topic list

/parameter_events
/rosout

Create workspace before creating a package
cd /lab1_ws
colcon build
source install/setup.bash

[Creating a package]
Deliverable 1:

cd /lab1_ws/src
ros2 pkg create lab1_pkg --build-type ament_cmake --dependencies rclcpp rclpy ackermann_msgs


* lab1_pkg - package name
* ament_cmake - supports both C++ and Python
* dependencies - Libraries:
  - rclcpp - C++ ROS2 library
  - rclpy - Python ROS2 library
  - ackermann_msgs - message type for vehicle control

[If declared properly the depencies could be installed using rosdep]
cd /lab1_ws
rosdep install -i --from-path src --rosdistro foxy -y

[Creating nodes with publishers and subscribers]
cd /lab1_ws/src/lab1_pkg
mkdir -p scripts
nano scripts/talker.py

editor pops up, code:
#!/usr/bin/env python3
import rclpy
from rclpy.node import Node
from ackermann_msgs.msg import AckermannDriveStamped

class Talker(Node):
    def __init__(self):
        super().__init__('talker')
        self.declare_parameter('v', 0.0)
        self.declare_parameter('d', 0.0)
        self.publisher = self.create_publisher(AckermannDriveStamped, 'drive', 10)
        self.timer = self.create_timer(0.1, self.timer_callback)
    
    def timer_callback(self):
        msg = AckermannDriveStamped()
        msg.drive.speed = self.get_parameter('v').value
        msg.drive.steering_angle = self.get_parameter('d').value
        self.publisher.publish(msg)

def main(args=None):
    rclpy.init(args=args)
    node = Talker()
    rclpy.spin(node)
    node.destroy_node()
    rclpy.shutdown()

if __name__ == '__main__':
    main()


enter for save
for rights:
chmod +x scripts/talker.py











