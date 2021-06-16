---
title: Set modules to false in babel
date: 2019-05-22 16:01:30
tags: [Web Development, Babel]
categories: Dev Notes
---

Version 2 of webpack supports ES6 modules syntax natively, meaning you can use import and export without a tool like babel to handle this for you.

So when we config babel-preset-env we can set ["modules"](https://babeljs.io/docs/en/babel-preset-env#modules) to false, instead of using babel to compiling then into other module type.