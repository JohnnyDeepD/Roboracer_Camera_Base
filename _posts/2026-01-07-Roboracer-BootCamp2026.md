---
layout: default
---

## Roboracer BootCamp 2026 Jan

This page is with both real car and simulation.
First, I made a private repo in github and shared the codes.
I will go through pure pursuit simulation first and then see the difference with the real car.

I will skip the parts about mapping and connnections here.
Pure Pursuit
Lookahead distance: If it is big, it will look for further waypoint, which will make the car to crash in the corners
If Lookahead distance is small, it will wobble because it is looking at short waypoint and it is reacting too much.
Here are the videos with the lookahead distance values:


Lookahead distance is one of the major 4 parameters, the total velocity, velocity according to curve, pid k for raceline following amplification, and the lookahead distance. 


Next part would be follow the gap.

