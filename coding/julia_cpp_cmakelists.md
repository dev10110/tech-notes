---
layout: default
title:  Embeingging Julia in C++ using CMakeLists
date:   2022-08-17
parent: Coding
---

# Embedding Julia in C++ using CMakeLists

While the documentation is available [here](https://docs.julialang.org/en/v1/manual/embedding/), it does not show how one can include the julia libraries in C++ projects using CMakeLists.txt

Here is my solution. 

## Files:

Suppose we have the following Julia module we want to use

``` 
## /project/my_module.jl
module myMath
export square_array
function square_array(x)
  x .^= 2
end
end
```

and the following C++ file:



