---
title: 重装hexo仓库
categories: 笔记
tags: 
- blog搭建
---
不知道为什么，本地的Hexo库突然就失效了，于是重新装了一下。

首先新建一个文件夹，给hexo安装一大堆依赖库
```BASH
hexo init

npm install --save hexo-renderer-jade hexo-generator-feed hexo-generator-sitemap hexo-browsersync hexo-generator-archive hexo-theme-butterfly hexo-deployer-git
```

之后处理和添加git仓库
```BASH
git init
git remote add https://xxxxxx

git fetch --all
git reset --hard origin/master

git push --set-upstream origin master
```