---
layout: default
title:  CMake Tricks
date:   2023-07-21
parent: Coding
---

# CMake Tricks


## Release Mode

C++ gets much (much) faster in `Release` mode compared to `Debug` mode. This is usually set by a cli arg when building:
```
cmake -DCMAKE_BUILD_TYPE=Release ..
make
```

but instead, in the `CMakeLists.txt` we can specify that we want to build in `Release` mode by default:
```
if(NOT CMAKE_BUILD_TYPE)
  set(CMAKE_BUILD_TYPE "Release")
  # set(CMAKE_BUILD_TYPE "Debug")
endif()
```

I've seen 10x speed improvement in my code by doing this. 


##  Speed up compilation using Precompiled Headers

In `cmake 3.16` there is a new command: `target_precompile_headers`.  This will speed up compilation, since c++ will not rebuild header files that have been compiled. 

Place some of your includes into a dedicated `precompile.hpp` and then `#include "precompile.hpp"`. Next, in your `CMakeLists.txt` add the following:
```
target_precompile_headers(<target> PRIVATE 
    include/precompile.hpp
)
```

which will tell `cmake` to precompile this header file. Dont forget to set the `cmake_version` to be atleast 3.16. 

Reference: [https://www.youtube.com/watch?v=eSI4wctZUto](https://www.youtube.com/watch?v=eSI4wctZUto)
