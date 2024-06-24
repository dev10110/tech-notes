---
layout: default
title:  unit-testing
date:   2024-06-24
parent: Coding
---

# Unit-testing with `gtest`, `cmake` and `colcon`

It's important to unit test code as you go. Here is how to set it up in `cmake` and `colcon` projects:


## In `cmake` projects:

This guide has a good explanation: http://google.github.io/googletest/quickstart-cmake.html

Essentially, you create a `hello_test.cc` file:

```cpp
#include <gtest/gtest.h>

// Demonstrate some basic assertions.
TEST(HelloTest, BasicAssertions) {
  // Expect two strings not to be equal.
  EXPECT_STRNE("hello", "world");
  // Expect equality.
  EXPECT_EQ(7 * 6 + 1, 42); // THIS SHOULD FAIL
}
```

and then add the following to the bottom of your `CMakeLists.txt`:

```
cmake_minimum_required(VERSION 3.14)
project(my_project)

# GoogleTest requires at least C++14
set(CMAKE_CXX_STANDARD 14)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

include(FetchContent)
FetchContent_Declare(
  googletest
  URL https://github.com/google/googletest/archive/f8d7d77c06936315286eb55f8de22cd23c188571.zip
)
# For Windows: Prevent overriding the parent project's compiler/linker settings
set(gtest_force_shared_crt ON CACHE BOOL "" FORCE)
FetchContent_MakeAvailable(googletest)

# start the actual testing 
enable_testing()

add_executable(
  hello_test
  hello_test.cc
)
target_link_libraries(
  hello_test
  GTest::gtest_main
)

include(GoogleTest)
gtest_discover_tests(hello_test)

```

where the commit-id in the url should be updated to the latest from https://github.com/google/googletest

and then you can build as usual:

```bash
cd build
cmake ..
make
```

and then run the test
```bash
ctest
```


## In `colcon` projects

As is often the case, in `colcon` some "convenient" macros are provided. 

First, install `ament_cmake_gtest`:
```bash
apt-get update
apt-get install ros-${ROS_DISTRO}-ament-cmake-gtest
```

The `hello_test.cc` file is the same as above. 

The `CMakeLists.txt` should look like this instead:
```
include(FetchContent)
FetchContent_Declare(
  googletest
  URL https://github.com/google/googletest/archive/f8d7d77c06936315286eb55f8de22cd23c188571.zip
)
# For Windows: Prevent overriding the parent project's compiler/linker settings
set(gtest_force_shared_crt ON CACHE BOOL "" FORCE)
FetchContent_MakeAvailable(googletest)

if(BUILD_TESTING)
  find_package(ament_cmake_gtest REQUIRED)
  ament_add_gtest(hello_test hello_test.cc)
  target_include_directories(hello_test PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
    $<INSTALL_INTERFACE:include>
  )
  # target_link_libraries(hello_test name_of_local_library)
endif()
```

notice that we still need to use the `FetctContent` to get the googletest library. 

Now, build and test as follows:

```bash
colcon build --symlink-install --packages-select <name of package>
colcon test --packages-select <name of package>
colcon test-result --verbose
```

which will print any errors to the console.

If you use my [colcon-makefile](https://dev10110.github.io/tech-notes/coding/colcon_build_tricks.html), you can also run
```bash
pkg=${name of package} make test
```
which will build and run the tests.