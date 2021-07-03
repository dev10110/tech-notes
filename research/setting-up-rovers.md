---
layout: default
title:  Setting up the rovers
date:   2021-06-27
parent: Research
categories: experimental
---


# Setting up the Rover

This page makes some notes on how I got the ground rover in lab up and running. This page is mostly for my own reference, but feel free to get in touch if youre having similar issues. 

The rover is very simple. It contains:
- 4 wheels, powered by DC brushed motors
- Roboclaw 2x15amp brushed motor controller (so both motors on either side of the rover are connected to the same channel) These motors also have encoders on them.
- Jetson TX2
- Pixhawk 2 (black cube)
- a sheet metal frame to hold everything in

Pretty simple. 

The set up was also quite easy. 

1. I used the NVIDIA SDK to load the latest JetPack onto the TX2, currently Jetpack 4.5.1. This gave me ubuntu 18.04 on the tx2.
2. I had to calibrate the roboclaw drivers:
  - Connect the motor controller by USB to a windows machine. 
  - Download, install and run the "BasicMicro Motion Studio" application [(here)](https://www.basicmicro.com/downloads)
  - Calibrate the motors and enoders following the steps [(here)](https://resources.basicmicro.com/auto-tuning-with-motion-studio/) - I stopped at the velocity calibration. Dont forget to save the settings by going to Device > Write Settings from the top bar of the window.
3. Set up SSH and ROS on the TX2
4. I used the [roboclaw-ros library](https://github.com/sonyccd/roboclaw_ros), and so the roboclaw motor controller is connected by USB to the TX2. At time of writing, there is a typo in the "ticks_per_meter" - see [this issue](https://github.com/sonyccd/roboclaw_ros/pull/22/commits/7d4e2b35de97e0ccadcba2e46aa0c86d7a1f7392).  
5. This library allows us to publish messages to `/cmd_vel`, and the motors will start spinning. There is an internal check, where if it doesnt receive a new message in one second, it automatically sends a command to stop the rover.  I also modified my version of the library so it doesnt try to read the encoder values. Ill be using an external sensor suite to do odometry, so I dont need to know the raw encoder values. I'll make my version of the repository public soon.
6. Calibrate the ros node, by tuning `ticks_per_meter` and `base_width` until the robot runs at the expected linear and angular speeds.

For autuomous control, I'll be using position feedback from a vicon set up, although in the future I might plug in a pixhawk and use other (onboard) sensors for state estimation. 

Finally, as a safety precaution, I set up an external radio to act as a radio, and as a remote kill switch.

I used a Spektrum DXe radio, connected to a computer using a usb cable. these cables are often used to train flying quads in simulators, but the linux system can just recognise the radio, and so we can use it within ros. I just followed [this tutorial](http://wiki.ros.org/joy/Tutorials/ConfiguringALinuxJoystick), but the steps are repeated here:

1. Plug in the radio to the ground station computer, and check that Linux detects it: 
```
ls /dev/input
```
should contain a joystick, something like `js0`. We can see the raw values by:
```
sudo jtest /dev/input/js0
```

2. Give the user permissions to access this input device 
```
sudo chmod a+rw /dev/input/js0
```
or for something that will last across reboots
```
sudo adduser $USER input
```
(i think, not sure)


3. ROS interface check:
- Install joy: `sudo apt-get install ros-melodic-joy`
- Terminal 1: `roscore`
- Terminal 2: `rosrun joy joy_node joy_node/dev:="dev/input/js0"`
- Terminal 3: `rostopic echo joy`

wiggle the joysticks and you should see the numbers change

4. Determine which axes and buttons you care about. For me I identified:
- axes[0]: (Ignore)
- axes[1]: Forward/Back Speed
- axes[2]: (Ignore)
- axes[3]: Left/Right
- buttons[0]: If 0: set to autonous mode. If 1: set to manual mode
- buttons[1]: (Ignore)
- buttons[2]: If 0: stop motors. If 1: allow motors to spin.
- buttons[3]: (Ignore)

Note: realistically, on `buttons[2]` I would have been able to set them to kill power, but instead I am just going to publish a 0-speed.


5. Write a node to take the joystick values and map to roboclaw's `cmd_vel`. 

6. Finally, we now need to operate ros across multiple machines: the TX2 inside the rover, and the ubuntu ground station outside. As such, we need to add the following to `~/.bashrc`:

```
export ROS_MASTER_URI: xxxx
export ROS_IP: yyyy
```

where `xxxx` is the ip address of the machine that runs ros master
and `yyyy` is the ip address of the machine that you are modifying the `bashrc` of

