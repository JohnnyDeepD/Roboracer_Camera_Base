---
layout: default
---

## Roboracer 1st day
Since installing Ubuntu is complicated, takes lot of resource, and not compatible with others,
I chose to use Docker as it is recommended in Roboracer Learn Lab1.

The original Docker cannot be installed in my Mac because of version issue (requires Mac OS 14 but my 2016 Mac does not support the version), so I am 

----------------------------------------------------------------
trying Colima + Docker CLI, which is popular for Mac OS 12.

Things are going to be done in Mac Terminal, and we are going to install with HomeBrew so I started with installing HomeBrew with next code, admin password was necessary
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

brew install colima

Homebrew helps installing software easilym, but this does not work for old version of Mac OS either so I am going to try MacPorts instead.

[Uninstall Homebrew]
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/uninstall.sh)"

did not uninstall Colima because it will be installed with MacPorts again

[Install MacPorts] --> Uninstall
1. download pkg for MacOS Monterey (my version) at macports.org  (click the blue Monterey, it will say downloading package)
2. restart terminal
3. update MacPorts with code: sudo port -v selfupdate

[Install Colima]  --> Lima version does not work so Uninstall
5. install colima, that runs Docker in Linux Virtual Machine for macOS: sudo port install colima
6. fix lima building errors
sudo port clean lima
sudo port install lima
sudo port uninstall lima
find the correct lima version

[Install Docker] --> Uninstall
-----------------------------------------------------------

We are going to use Virtual Box instead. Popular in Roboracer community, and much more compatible.
[Virtual Box]
------------------------------------------------------------

1. Install VirtualBox(0.5GB) https://www.virtualbox.org/wiki/Downloads   : macOS / Intel hosts
2. Download Ubuntu ISO (5GB), install version 22.04 (old version) which is more compatible with F1Tenth Roboracer: https://releases.ubuntu.com/22.04/
3. Open VirtuaBox and create new, select Ubuntu ISO,
4. Do not check proceed with Unattended Installation, and choose OS: Linux, OS Distribution: Ubuntu, OS Version: Ubuntu(64-bit)
5. Allocation (Do not check Use EFI, we are going to use normal BIOS which is more stable)
- memory 8192MB: Ubuntu Desktop, Docker Container, and ROS2+ simulators
- Disk 40GB: Ubuntu installation, Docker images, ROS2 packages, and working files
- CPU 2: dual core Macbook, to run Docker and ROS2 simultaneously
