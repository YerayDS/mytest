---
permalink: /exercises/Drones/drone_gymkhana
title: "Drone Gymkhana"

sidebar:
  nav: "docs"

toc: true
toc_label: "TOC Drone Gymkhana"
toc_icon: "cog"

gallery:
  - url: /assets/images/exercises/drone_gymkhana/drone_gymkhana_complete.png
    image_path: /assets/images/exercises/drone_gymkhana/drone_gymkhana_complete.png
    alt: "Drone Gymkhana"
    title: "Drone Gymkhana"
  - url: /assets/images/exercises/drone_gymkhana/drone_detail.png
    image_path: /assets/images/exercises/drone_gymkhana/drone_detail.png
    alt: "Drone detail"
    title: "Drone detail"

drone_teleop:
  - url: /assets/images/exercises/drone_gymkhana/drone_teleop_vel.png
    image_path: /assets/images/exercises/drone_gymkhana/drone_teleop_vel.png
    alt: "Drone Teleoperator"
    title: "Drone Teleoperator"

youtubeId1: Guq3nPIQmdM
youtubeId2: 68GSXnOTUNs
---
## Goal

The goal of this exercise is to learn how to control a drone to complete a gymkhana course, composed of several waypoints that you'll have to navigate through. 

{% include gallery caption="Gymkhana course. 3DR Iris drone it its launch pad" %}

You will have contact with the drone infrastructure in our Robotics Academy for the first time, learning how to access the drone sensors, actuators and cameras via an *Application Programing Interface* (API), which is a key point to master the rest of the proposed exercises.

## Instructions
This is the preferred way for running the exercise.

### Installing and Launching
1. Download [Docker](https://docs.docker.com/get-docker/). Windows users should choose WSL 2 backend Docker installation if possible, as it has better performance than Hyper-V.

2. Pull the current distribution of Robotics Academy Docker Image:

	```bash
  docker pull jderobot/robotics-academy:latest
  ```

- In order to obtain optimal performance, Docker should be using multiple CPU cores. In case of Docker for Mac or Docker for Windows, the VM should be assigned a greater number of cores.

- It is recommended to use the latest image. However, older distributions of RADI (Robotics-Academy Docker Image) can be found [here](https://hub.docker.com/r/jderobot/robotics-academy/tags).

### How to perform the exercises?
- Start a new docker container of the image and keep it running in the background:

	```bash
  docker run --rm -it -p 7164:7164 -p 2303:2303 -p 1905:1905 -p 8765:8765 -p 6080:6080 -p 1108:1108 -p 7163:7163 jderobot/robotics-academy
  ```

- On the local machine navigate to 127.0.0.1:7164/ in the browser and choose the desired exercise.

- Wait for the Connect button to turn green and display "Connected". Click on the "Launch" button and wait for some time until an alert appears with the message `Connection Established` and button displays "Ready". 

- The exercise can be used after the alert.

### Enable GPU Acceleration
- Follow the advanced launching instructions from [here](https://jderobot.github.io/RoboticsAcademy/user_guide/#enable-gpu-acceleration).

### Optional: Store terminal output
- To store the terminal output of manager.py and launch.py to a file execute the following docker run command and keep it running in the background:
```bash
docker run -it --rm -v $HOME/.roboticsacademy/log/:/root/.roboticsacademy/log/ --device /dev/dri -p 7164:7164 -p 2303:2303 -p 1905:1905 -p 8765:8765 -p 6080:6080 -p 1108:1108 -p 2304:2304 -p 1904:1904 jderobot/robotics-academy --logs
```

- The log files will be stored inside `$HOME/.roboticsacademy/{year-month-date-hours-mins}/`. After the session, use `more` to view the logs, for example:
```bash
more $HOME/.roboticsacademy/log/2021-11-06-14-45/manager.log
```

### Where to insert the code?

In the launched webpage, type your code in the text editor,

```python
from GUI import GUI
from HAL import HAL
# Enter sequential code!

while True:
    # Enter iterative code!
```

### Using the Interface

* **Control Buttons**: The control buttons enable the control of the interface. Play button sends the code written by User to the Robot. Stop button stops the code that is currently running on the Robot. Save button saves the code on the local machine. Load button loads the code from the local machine. Reset button resets the simulation(primarily, the position of the robot).

* **Brain and GUI Frequency**: This input shows the running frequency of the iterative part of the code (under the `while True:`). A smaller value implies the code runs less number of times. A higher value implies the code runs a large number of times. The numerator is the one set as the Measured Frequency who is the one measured by the computer (a frequency of execution the computer is able to maintain despite the commanded one) and the input (denominator) is the Target Frequency which is the desired frequency by the student. The student should adjust the Target Frequency according to the Measured Frequency.

* **RTF (Real Time Factor)**: The RTF defines how much real time passes with each step of simulation time. A RTF of 1 implies that simulation time is passing at the same speed as real time. The lower the value the slower the simulation will run, which will vary depending on the computer. 

* **Pseudo Console**: This shows the error messages related to the student's code that is sent. In order to print certain debugging information on this console. The student can use the `print()` command in the Editor. 

## Robot API

* `from HAL import HAL` - to import the HAL(Hardware Abstraction Layer) library class. This class contains the functions that sends and receives information to and from the Hardware(Gazebo).
* `from GUI import GUI` - to import the GUI(Graphical User Interface) library class. This class contains the functions used to view the debugging information, like image widgets.

### Sensors and drone state

* `HAL.get_position()` - Returns the actual position of the drone as a numpy array [x, y, z], in m.
* `HAL.get_velocity()` - Returns the actual velocities of the drone as a numpy array [vx, vy, vz], in m/s
* `HAL.get_yaw_rate()` - Returns the actual yaw rate of the drone, in rad/s.
* `HAL.get_orientation()` - Returns the actual roll, pitch and yaw of the drone as a numpy array [roll, pitch, yaw], in rad. 
* `HAL.get_roll()` - Returns the roll angle of the drone, in rad
* `HAL.get_pitch()` - Returns the pitch angle of the drone, in rad.
* `HAL.get_yaw()` - Returns the yaw angle of the drone, in rad. 
* `HAL.get_landed_state()` -  Returns 1 if the drone is on the ground (landed), 2 if the drone is in the air and 4 if the drone is landing. 0 could be also returned if the drone landed state is unknown. 

### Actuators and drone control

The three following drone control functions are *non-blocking*, i.e. each time you send a new command to the aircraft it immediately discards the previous control command. 

#### 1. Position control

* `HAL.set_cmd_pos(x, y, z, az)` - Commands the *position* (x,y,z) of the drone, in m and the *yaw angle (az)* (in rad) taking as reference the first takeoff point (map frame)

#### 2. Velocity control

* `HAL.set_cmd_vel(vx, vy, vz, az)` - Commands the *linear velocity* of the drone in the x, y and z directions (in m/s) and the *yaw rate (az)* (rad/s) in its body fixed frame

#### 3. Mixed control

* `HAL.set_cmd_mix(vx, vy, z, az)` - Commands the *linear velocity* of the drone in the x, y directions (in m/s), the *height* (z) related to the takeoff point and the *yaw rate (az)* (in rad/s) 

### Drone takeoff and land

Besides using the buttons at the drone teleoperator GUI, taking off and landing can also be controlled from the following commands in your code:

* `HAL.takeoff(height)` - Takeoff at the current location, to the given height (in m)
* `HAL.land()` - Land at the current location. 

### Drone cameras

* `HAL.get_frontal_image()` - Returns the latest image from the frontal camera as a OpenCV cv2_image
* `HAL.get_ventral_image()` - Returns the latest image from the ventral camera as a OpenCV cv2_image

### GUI

* `GUI.showImage(cv2_image)` - Shows a image of the camera  in the GUI
* `GUI.showLeftImage(cv2_image)` - Shows another image of the camera in the GUI

## Hints

### How can I build a blocking position control?

There are several ways of solving this exercise, that differ in the approach and complexity when building the blocking position control function:

1. **Time-based open loop control:** The easiest alternative, recommended just for testing purposes. If you command the drone using the non-blocking API function `drone.set_cmd_pos(x, y, z, yaw)` and wait long enough (using, for example, `rospy.sleep(time_in_sec)`), the drone will surely reach its destination. However, this is not recommended, as we don't get confirmation of arrival at the given target location.
2. **Closed loop position control**: Based on getting the current drone position, comparing it with the target position, and executing the position or velocity API control functions till the geometric distance between current and target 3D positions is within certain *tolerance*  (which can also be passed to the blocking as input parameter)

### How do I get the waypoint coordinates?

To give you some initial references, each leg in the squared course is 16 m long, and the position of the numbered cubes at the corners are:

1. **CUBE 1:** Located at (x, y) = (8, -8) m
2. **CUBE 2:** Located at (x, y) = (-8, -8) m
3. **CUBE 3:** Located at (x, y) = (-8, 8) m
4. **CUBE 4:** Located at (x, y) = (8, 8) m

The takeoff pad is located at (x, y) = (0, 0) m. The dimensions of all cubes are 1 x 1 x 1 m.

### Directional control. How should drone yaw be handled? 

If you don't take care of the drone yaw angle or yaw_rate in your code (keeping them always equal to zero), you will fly in what's generally called **Heads Free Mode**. The drone will always face towards its initial orientation, and it will fly sideways or even backwards when commanded towards a target destination. Multi-rotors can easily do that, but what's not the best way of flying a drone.

In this exercise, we want you to try programming your drone to travel towards each waypoint similarly to how a fixed-wing aircraft would do, namely **nose forward**.  Then, you'll have to implement by yourself some kind of directional control, to rotate the nose of your drone left or right using yaw angle, or yaw_rate. 

If you know your current position and your target one, you can easily compute the direction (yaw angle) the drone must be turned to by applying some elementary geometry. Probably both `math.sqrt()` and `math.atan2()` Python functions will be very useful for you here. 

### Do I need to know when the drone is in the air?

No, you can solve this exercise without taking care of the **land state** of the drone. However, it could be a great enhancement to your blocking position control function if you make it only work when the drone is actually flying, not on the ground.

## Videos

{% include youtubePlayer.html id=page.youtubeId2 %}

### Demonstrative video of the solution

{% include youtubePlayer.html id=page.youtubeId1 %}

---------

## Contributors

- Contributors: [Pedro Arias](https://github.com/pariaspe), [Diego Martín](https://github.com/diegomrt), [JoseMaria Cañas](https://github.com/jmplaza) and [Arkajyoti Basak](https://github.com/iamarkaj).
- Maintained by [Pedro Arias](https://github.com/pariaspe), [Diego Martín](https://github.com/diegomrt) and [Arkajyoti Basak](https://github.com/iamarkaj).
