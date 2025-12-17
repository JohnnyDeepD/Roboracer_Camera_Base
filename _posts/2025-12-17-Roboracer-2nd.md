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


ctrl x, y, enter for save
for rights:
chmod +x scripts/talker.py

explanation:
required Deliverable 2:
talker node creation
AckermannDriveStamped message publishing in drive topic
v parameter → speed field
d parameter → steering_angle field

code:
Node class inheritence - ROS2 node creation
declare_parameter - v, d parameter declaration
create_publisher - publisher creation in drive topic
timer_callback - message per 0.1 second


for clipboard sharing with virtual machine, we are going to install guest additions
first get out of Docker with exit command

then
sudo apt update
sudo apt install virtualbox-guest-utils virtualbox-guest-x11 -y
sudo reboot

then we will set Devices → Shared Clipboard → Bidirectional 

This didn't work because the screen of Ubuntu was cracking
we had to deleted all guestAddition 
sudo /opt/VBoxGuestAdditions-*/uninstall.sh
sudo reboot

and eject the guest additions cd image

[Start the Docker container again (after reboot)]
docker start -i f1tenth_lab1

back here
[Creating nodes with publishers and subscribers]
cd /lab1_ws/src/lab1_pkg
mkdir -p scripts
nano scripts/talker.py

need to install nano first (good for beginners)
apt update && apt install nano -y

back to talker.py
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


ctrl+x, y, enter for save
for rights:
chmod +x scripts/talker.py

explanation:
required Deliverable 2:
talker node creation
AckermannDriveStamped message publishing in drive topic
v parameter → speed field
d parameter → steering_angle field

code:
Node class inheritence - ROS2 node creation
declare_parameter - v, d parameter declaration
create_publisher - publisher creation in drive topic
timer_callback - message per 0.1 second


[creating relay node]
nano scripts/relay.py

#!/usr/bin/env python3
import rclpy
from rclpy.node import Node
from ackermann_msgs.msg import AckermannDriveStamped

class Relay(Node):
    def __init__(self):
        super().__init__('relay')
        self.subscription = self.create_subscription(
            AckermannDriveStamped, 'drive', self.listener_callback, 10)
        self.publisher = self.create_publisher(AckermannDriveStamped, 'drive_relay', 10)
    
    def listener_callback(self, msg):
        new_msg = AckermannDriveStamped()
        new_msg.drive.speed = msg.drive.speed * 3.0
        new_msg.drive.steering_angle = msg.drive.steering_angle * 3.0
        self.publisher.publish(msg)

def main(args=None):
    rclpy.init(args=args)
    node = Relay()
    rclpy.spin(node)
    node.destroy_node()
    rclpy.shutdown()

if __name__ == '__main__':
    main()


ctrl+x, y, then enter for save
rights:
chmod +x scripts/relay.py

[update before build]
(make sure working in lab1_pkg directory with cd command)
nano cMakeLists.txt


add this right above ament_package()

install(PROGRAMS
  scripts/talker.py
  scripts/relay.py
  DESTINATION lib/${PROJECT_NAME}
)

ament_package()


[build]
cd /lab1_ws
colcon build
source install/setup.bash

