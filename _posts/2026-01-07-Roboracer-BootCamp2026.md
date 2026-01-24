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


Not really getting better so adding steering angle smoothing --> adding did not work, so went back to pure code and change parameters
(commented out the above moving average filter part also)

only changing the bubble_radius showed little difference, increasing it made it to get further from the wall (obstacle)


I could not solve wobble problem, because adding something only made the car to crash.
But testing with bubble radius and moving average filter still made it not to crash to obstacles, and it was hallway (Levine Hall) so bubble radius did not really matter that much. 
I tried to make it fast by relating the velocity to distance ahead but it only made the car to crash because when the car was turning at curve when there was nothing ahead it speeded up and it crashed. Therefore at this hallway situation slow speed and not so much of bubble radius would be good idea not to crash. I am still wondering how to make it fast. Maybe adding speed limit only when it gets too fast after a curve. 


Next part would be MPC, the Model Predictive Control.
I am planning to spend most of the time here, to push NVidia Jetson Orin Nano to its extreme efficiency. 
I will first test with normal python mpc_node.py
Then, I will work with mpc_jax.py which is library with enhanced python calculation for GPU.
Then I will also test with C++ to consider real world situations. 
MPC fixes, Python node, and new waypoints settings took a long time in the first place but it worked. 

A Problem:
Python code has Latency, so there is a mismatch between sensor data processing and control cycle, which causes localization instability. 

[Trying Jax library for faster python calculation acceleration]

Setting up mpc_jax.py is not easy because first, reading waypoint file cannot be done with jax numpy (jnp) GPU, so it has to be changed to using normal numpy with CPU. Furthermore, numba accelerator calculations could not use GPU for nearest point calculation also so we changed it to normal CPU numpy with changing all the jnp matrix to normal matrix, and matched float32 type as error showed up.
(imported numpy and made loading waypoint to use numpy, not jax)
<img width="1099" height="717" alt="image" src="https://github.com/user-attachments/assets/63a1390f-02c4-46f3-9165-272c0b24eddf" />

sol:
_, _, _, idx = nearest_point(np.array([px,py], dtype=np.float32), np.array(wp_xy, dtype=np.float32))

and also changed some configuration for jax decorator errors (should not use static variables)
sol for newest jax:
# cfg (index 3) variable is static
@jit(static_argnums=(3,))
def linearize_dynamics(v, yaw, steering, cfg: MPCConfig):

sol for old jax:
def linearize_dynamics(v, yaw, steering, cfg: MPCConfig):
    # ... (codes) ...
    return A, B  # <--- 함수 끝

# out of the function
linearize_dynamics = jax.jit(linearize_dynamics, static_argnums=(3,))

