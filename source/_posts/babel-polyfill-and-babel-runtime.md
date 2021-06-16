---
title: babel-polyfill and babel-runtime
date: 2018-10-14 16:24:28
tags: [Web Development, Babel]
categories: Dev Notes
---

Use `@babel/runtime` when you don't want to pollute global namespace(e.g. you are building a library).

Install `@babel/plugin-transform-runtime` as development dependency.

```text
npm install --save-dev @babel/plugin-transform-runtime
```

Install `@babel/runtime` as a production dependency.

```text
npm install --save @babel/runtime
```

Use `@babel/polyfill` for writing an app.

Source: https://babeljs.io/docs/en/babel-plugin-transform-runtime
