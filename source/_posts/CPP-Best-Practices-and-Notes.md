---
title: CPP Best Practices and Notes
date: 2021-06-16 17:02:56
tags:  [CPP]
toc: true
categories: Dev Notes
---
Notes taken from <https://www.learncpp.com/>

## 9.2 Arrays II

- Explicitly initialize arrays, even if they would be initialized without an initializer list.

- When passing arrays to functions, C++ **does not** copy an array. The *actual* array is passed.
    - To ensure a function does not modify the array passed into it, make the array const:
    
      ```cpp
      void functionWithArray(const int arr[5]) {
        std::size(arr); // note that this will cause error!!
      }
      ```
      
- When  you are in the same function that a array is 
  declared in, `sizeof()` will return the total size of the array (array length multiplied by element size)

    - When `sizeof` is used on an array that has been passed to a function, it doesn’t error 
      out like `std::size()` does. Instead, it returns the size of a pointer.

<!--more-->
## 9.6 C Style Strings

- Don't use C-style strings! Use `std::string` or `std::string_view` instead.

## 9.7 std::string_view

- Unlike `std::string`, which keeps its own copy of the string, `std::string_view` provides a view of a string that is defined elsewhere.

- Prefer `std::string_view` over `std::string` for read-only strings, unless you already have a `std::string`.

## 9.8 Introduction to pointers

