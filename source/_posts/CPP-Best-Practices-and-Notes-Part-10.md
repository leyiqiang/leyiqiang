---
title: CPP Best Practices and Notes Part 10
toc: true
date: 2021-06-18 21:17:38
tags:[CPP]
categories: Dev Notes
---
## 10 Functions

### passing arguments by value

#### When to use pass by value:

- When passing fundamental data type and enumerators, and the function does not need to change the argument.

#### When not to use pass by value:

- When passing structs or classes (including std::array, std::vector, and std::string).


### Passing arguments by reference

- avoiding out parameters altogether if possible

- When passing an argument by reference, always use a const reference unless you need to change the value of the argument.

### Passing arguments by Address

- Prefer pass by reference to pass by address whenever applicable.

<!--more-->

### Returning values by reference and address

#### Returning values by References
- ```cpp
  int& returnByReference()
  {
      static int x{ 5 }; // static ensures x isn't destroyed when the function ends
      return x;
  }```
  
- When bound to a const reference, the lifetime of the r-value is extended to match the lifetime of the reference

### Inline Functions

- Be aware of inline functions, but modern compilers should inline functions for you as appropriate, so there isn’t a need to use the inline keyword in this context.

### Default Arguments

- If the function has a forward declaration (especially one in a header file), put the default argument there. Otherwise, put the default argument in the function definition.
