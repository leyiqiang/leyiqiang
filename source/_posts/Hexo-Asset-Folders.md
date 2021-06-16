---
title: Hexo Asset Folders
date: 2018-10-15 09:15:41
tags: [Web Development]
categories: Dev Notes
---

In root's `_config.yml`, set post_asset_folder to true:
```
post_asset_folder: true
```

When you create a new post, hexo will generate an asset folder with the same name as the post.
<!-- more -->

To include assets in markdown:
```
{% asset_path slug %}
{% asset_img slug [title] %}
{% asset_link slug [title] %}
```

Examples:
```
{% asset_img example.jpg This is an example image %}
{% asset_img "spaced asset.jpg" "spaced title" %}
```
