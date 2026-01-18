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


Lookahead distance is one of the major 4 parameters, the max velocity(threshold), velocity according to curve, pid k for raceline following amplification, and the lookahead distance. 

---------------------------------------------------------------------------------------------------------------------
Next test is follow the gap. first, the car is set to 1.0 velocity for whatever distance_ahead for safe test.
However, it is wobbling too much at first movement. (It is avoiding walls to opposite direction though)
Therefore, I decided to add Moving Average Filter at preprocess_lidar function.

 def preprocess_lidar(self, ranges):
    
        #Moving Average Filter
        window_size = 5   #how many sample for average, bigger value is smoother but slower
        kernel = np.ones(window_size) / window_size    #all add up to 1, window_size if 5 so [0.2, 0.2 ,0.2, 0.2, 0.2]
        ranges = np.convolve(ranges, kernel, mode='same')    #convolution for average value


Not really working so adding steering angle smoothing --> adding did not work, so went back to pure code and change parameters
(commented out the above moving average filter part also)

only changing the bubble_radius showed little difference, increasing it made it to get further from the wall (obstacle)



Next part would be follow the gap.

