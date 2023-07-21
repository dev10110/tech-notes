---
layout: default
title:  CMake Tricks
date:   2023-07-21
parent: Coding
---

# CMake Tricks


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
