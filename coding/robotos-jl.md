---
layout: default
title:  ROS 1 and Julia
date:   2021-09-07
parent: Coding
---

# Using ROS1 and Julia

There is a package, `RobotOS.jl` that still works for ROS1, using Julia v1.6.2. The setup is quite easy, but a missing step in the documentation threw me off. So here is how I set it up on my computer. 

First, install the necessary packages in Julia:
```
julia
] add RobotOS PyCall
```

Now we need to build PyCall. This requires specifying the path where python is installed. In particular, we must use Python2 since ROS1 only supports python2 by default. 

For me, the easiest way was to go back into the julia REPL and type
```
julia
 
using PyCall

PyCall.python
```
printed something like: `"/home/devansh/.julia/conda/3/bin/python"`

Instead we want to specify that its the python2 version to use:

```
julia

using PyCall

ENV["PYTHON"]="/usr/bin/python2.7"
```
and then we force rebuild:
```
] build PyCall
```
quit Julia, and relaunch it 

```
julia

using PyCall

PyCall.python
```
which now prints `"/usr/bin/python2.7"`

You can check that rospy can be imported by running

```
using PyCall
pyimport("rospy")
```
which for me printed `PyObject <module 'rospy' from '/opt/ros/melodic/lib/python2.7/dist-packages/rospy/__init__.pyc'>`

Now when the example file is run, it worked!