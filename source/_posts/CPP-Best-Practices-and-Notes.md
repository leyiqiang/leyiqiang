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

- The address-of operator `&` allows us to see what memory address is assigned to a variable.

- The indirection operator `*` allows us to access the value at a particular address:
    ```cpp
    int main() {
        int x{5};
        std::cout << *(&x) << '\n';
    }
    ```

- When declaring a pointer variable, put the asterisk next to the type to make it easier to distinguish it from an indirection.
    ```cpp
    int* ptr{ &v }; // initialize ptr with address of variable v
    int* ptr{ &value }; // ptr points to value
    std::cout << ptr; // prints address held in ptr, which is &value
    std::cout << *ptr; // Indirection through ptr (get the value that ptr is pointing to)
    ```
  
-  The address-of `&` operator returns a pointer

## 9.9 Null Pointers
- Initialize your pointers to a null value if you’re not giving them another value.

- Because NULL is a preprocessor macro with an implementation defined value, avoid using NULL.

- When use `NULL`, the compiler can’t tell whether we mean a null pointer or the integer 0.
  So use `nullptr` to initialize your pointers to a null value.
  
## 9.10 Pointers and Arrays
- Differences between pointers and fixed arrays
    - When using `sizeof()`operator. When used on a fixed array, size of
    returns he size of the entire array (array length* element size).
      When used on a pointer, it returns the size of the memory address.
      
    - When using the address-of operator, the address of pointers yields the
    memory address of the pointer variable. The address of the array returns
      a pointer to the entire array. This pointers also ints to the first element of the
      array, but has type `int(*)[5]`
      
- When passing an array as an argument to a function, a fixed array decays into a pointer.
  Thus the `sizeof()` behaviours differently.
  
- Favor the pointer syntax (*) over the array syntax ([]) for array function parameters.

- Arrays in structs and classes don’t decay

## 9.13 Dynamic Memory Allocation with new and delete

- Set deleted pointers to 0 (or nullptr in C++11) unless they are
going out of scope immediately afterward.
  
- Memory leaks happen when your program loses the address of some bit of dynamically allocated memory before giving it back to the operating system. When this happens, your program can’t delete the dynamically allocated memory, because it no longer knows where it is. The operating system also can’t use this memory, because that memory is considered to be still in use by your program.

## 9.14 Dynamically allocating arrays

- Use `delete[]` when deleting a dynamically allocated array. Using the scalar version of `delete` on an array will result in undefined behavior, such as data corruption, memory leaks, crashes, or other problems.

## 9.16 references

- example
  ```cpp
  // ref is a reference to the argument passed in, not a copy
  void changeN(int& ref)
  {
  ref = 6;
  }
  ```
  When argument n is passed to the function, the function parameter ref is set as a reference to argument n. This allows the function to change the value of n through ref.
  
- Pass arguments by non-const reference (rather than by pointer) when the argument needs to be modified by the function.

