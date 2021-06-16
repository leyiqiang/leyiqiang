---
title: CPP Best Practices and Notes
date: 2021-06-16 17:02:56
tags:  [CPP]
categories: Dev Notes
---
Notes taken from <https://www.learncpp.com/>

## 9.2 Arrays II

- Explicitly initialize arrays, even if they would be initialized without an initializer list.

- When passing arrays to functions, C++ **does not** copy an array. The *actual* array is passed.