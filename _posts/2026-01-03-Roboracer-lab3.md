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
