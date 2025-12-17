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







