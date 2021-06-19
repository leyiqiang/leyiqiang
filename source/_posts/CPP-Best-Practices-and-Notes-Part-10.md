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