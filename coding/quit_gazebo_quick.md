---
layout: default
title:  Quit Gazebo Quickly
date:   2021-04-18
parent: Coding
---

# Quiting Gazebo Quickly

When developing in Ros/Gazebo, quiting Gazebo takes a horribly long time. 

There is a simple setting you can change to make it much quicker, at least for Gazebo 9, running with ROS melodic, on Ubuntu 18.04

Edit the following file:
```
/opt/ros/melodic/lib/python2.7/dist-packages/roslaunch/nodeprocess.py
```
and change the line:
```
_TIMEOUT_SIGINT = 15.0 # seconds
_TIMEOUT_SIGTERM = 2.0 # seconds
```
to whatever time you want. For instance
```
_TIMEOUT_SIGINT = 0.5 # seconds
_TIMEOUT_SIGTERM = 0.5 # seconds
```

Relaunch gazebo as normal, and now it should quit much more quickly.

[Source](https://answers.ros.org/question/304910/why-do-ros-programs-often-have-difficulty-stopping-cleanly/)

