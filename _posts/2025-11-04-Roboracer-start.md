---
layout: default
---

## Roboracer 1st day
Since installing Ubuntu is complicated, takes lot of resource, and not compatible with others,
I chose to use Docker as it is recommended in Roboracer Learn Lab1.

The original Docker cannot be installed in my Mac because of version issue (requires Mac OS 14 but my 2016 Mac does not support the version), so I am trying Colima + Docker CLI, which is popular for Mac OS 12.

Everything is going to be done in Mac Terminal, and we are going to install with HomeBrew so I started with installing HomeBrew with next code, admin password was necessary
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

brew install colima

Homebrew helps installing software easilym, but this does not work for old version of Mac OS either so I am going to try MacPorts instead.

[Uninstall Homebrew]
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/uninstall.sh)"

did not uninstall Colima because it will be installed with MacPorts again

[Install MacPorts]
1. download pkg for MacOS Monterey (my version) at macports.org  (click the blue Monterey, it will say downloading package)
2. restart terminal
3. update MacPorts with code: sudo port -v selfupdate

[Install Colima]
5. install colima, that runs Docker in Linux Virtual Machine for macOS: sudo port install colima
6. fix lima building errors
sudo port clean lima
sudo port install lima
sudo port uninstall lima
find the correct lima version

[Install Docker]
