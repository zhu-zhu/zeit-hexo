---
title: Hexo文章图片放置问题
date: 2020-03-07 20:11:58
thumbnail: 699815.jpg
categories: Development
tags:
- Hexo
- Development
---

<!--
  thumbnail 900 * 290
 -->

## 使用插件

```bash
npm install hexo-asset-image # or yarn add hexo-asset-image
```

<!-- more -->

## 设置Hexo

```bash
# 找到_config.yml文件中的选项
post_asset_folder: true
```

## 新建文章

```bash
hexo new post new-post
```

![img1](img1.png)

可以看到创建了new-post.md的同时也创建了new-post文件夹，加下来只要吧文章要用到的图片放入文件夹下再用MD的语法引用就可以使用图片啦！

```bash
![img1](img1.png)
```

![示例](示例.png)

## Done
