---
layout: default
title:  colcon Build Tricks
date:   2023-09-05
parent: Coding
---

# `colcon build` Tricks

Colcon build is an annoyingly long command when rapidly developing and testing. It usually looks like 
```
colcon build --symlink-install --cmake-args -DCMAKE_BUILD_TYPE=Release
```
and if you want to build a specific package you need to add `--packages-select my_pkg` or `--packages-upto my_pkg`.

This page demonstrates some methods to make life easier. 

Don't forget to `source  install/setup.bash` after running these commands as appropriate. 

## Direct Method

After building the package with the command above, simply 
```
cd build/my_pkg
make
```
which should rebuild `my_pkg`. This may not properly handle `python` packages, so be careful.  It will rerun `cmake` if there are changes in the `CMakeLists.txt` like a normal `make` command. 

In general, this is the fastest method, since it directly invokes `make`. We have seen that for single file changes in particular this can be much faster than `colcon build ...` or the methods listed below. But as I said above it might not be best.

## Using a `Makefile`:

Create the file `colcon_ws/Makefile`:
```make
mode=RelWithDebInfo
.SILENT: build clean test
.PHONY: build clean test

build: 
        if [ -z "$(pkg)" ]; then \
                echo "building all"; \
                colcon build --symlink-install --cmake-args -DCMAKE_BUILD_TYPE=$(mode); \
        else \
                echo "building $(pkg)"; \
                colcon build --symlink-install --cmake-args -DCMAKE_BUILD_TYPE=$(mode) --packages-select $(pkg);\
        fi

clean: 
        if [ -z "$(pkg)" ]; then \
                echo "cleaning all"; \
                rm -r build install; \
        else \
                echo "cleaning $(pkg)"; \
                rm -r build/$(pkg) install/$(pkg); \
        fi

test:  all
        if [ -z "$(pkg)" ]; then \
                echo "specify the pkg to test"; \
        else \
                echo "testing $(pkg)"; \
                colcon test --packages-select $(pkg);\
                colcon test-result --verbose; \
        fi
```
(be careful with leading/trailing tabs/spaces)

To use this, from `colcon_ws`, simply run
```
make
```
to build all pacakges, or
```
pkg=my_pkg make
```
to build only `my_pkg`.


You can also:
```
make clean
```
to remove the build and install folders
or 
```
pkg=my_pkg make clean
```
to remove the build and install folders of `my_pkg`.

To run the tests, (see [unit-testing](https://dev10110.github.io/tech-notes/coding/unit-testing.html)),
```
pkg=my_pkg make test
```



## Using a `bash` script:

Create the file `colcon_ws/build.sh`:
```bash
#!/bin/bash

mode=Release

if [ "$#" -eq 0 ]; then
  colcon build --symlink-install --cmake-args -DCMAKE_BUILD_TYPE=${mode}
else
  colcon build --symlink-install --cmake-args -DCMAKE_BUILD_TYPE=${mode} --packages-select $@
fi
```

To use this, from `colcon_ws`, simply run
```
./build.sh
```
to build all packages, or
```
./build.sh my_pkg
```
to build only `my_pkg`. You can also make it build multiple packages:
```
./build.sh my_pkg1 my_pkg2 my_pkg3
```