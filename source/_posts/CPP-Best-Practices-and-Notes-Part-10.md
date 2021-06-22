---
title: CPP Best Practices and Notes Part 11, 12
toc: true
date: 2021-06-18 21:17:38
tags:[CPP]
categories: Dev Notes
---
# Part 11: Functions

## passing arguments by value

### When to use pass by value:

- When passing fundamental data type and enumerators, and the function does not need to change the argument.

### When not to use pass by value:

- When passing structs or classes (including std::array, std::vector, and std::string).


## Passing arguments by reference

- avoiding out parameters altogether if possible

- When passing an argument by reference, always use a const reference unless you need to change the value of the argument.

## Passing arguments by Address

- Prefer pass by reference to pass by address whenever applicable.

<!--more-->

## Returning values by reference and address

### Returning values by References
- ```cpp
  int& returnByReference()
  {
      static int x{ 5 }; // static ensures x isn't destroyed when the function ends
      return x;
  }```
  
- When bound to a const reference, the lifetime of the r-value is extended to match the lifetime of the reference

## Inline Functions

- Be aware of inline functions, but modern compilers should inline functions for you as appropriate, so there isn’t a need to use the inline keyword in this context.

## Default Arguments

- If the function has a forward declaration (especially one in a header file), put the default argument there. Otherwise, put the default argument in the function definition.

## Function template instantiation

- Favor the normal function call syntax when using function templates.

- Use function templates to write generic code that can work with a wide variety of types whenever you have the need.

## Function templates with multiple template types

- Feel free to use abbreviated function templates if each auto parameter should be an independent template type (and your language standard is set to C++20 or newer):
    ```cpp
    template <typename T, typename U>
    auto max(T x, U y)
    {
        return (x > y) ? x : y;
    }
    ```

# Part 12: OOP

## Constructor member initializer list

- Use member initializer lists to initialize your class member variables instead of assignment.

## Non-static member initialization

- Favor use of non-static member initialization to give default values for your member variables.

## Static member variables

- static variables keep their values and are not destroyed even after they go out of scope

- Static members are not associated with class objects

## Static member functions

- Static member functions have no *this pointer


