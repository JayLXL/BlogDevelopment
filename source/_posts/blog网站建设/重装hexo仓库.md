---
title: 重装hexo仓库
categories: 网站日志
tags: 
- blog搭建
cover: https://s1.ax1x.com/2023/03/02/ppFFObT.jpg
---
不知道为什么，本地的Hexo库突然就失效了，于是重新装了一下。

首先新建一个文件夹，给hexo安装一大堆依赖库
```BASH
npm uninstall hexo-renderer-marked --save
```
```BASH
hexo init

npm install --save hexo-renderer-jade hexo-generator-feed hexo-generator-sitemap hexo-browsersync hexo-generator-archive hexo-theme-butterfly hexo-deployer-git 
hexo-renderer-kramed
```

之后处理和添加git仓库
```BASH
git init
git remote add https://xxxxxx

git fetch --all
git reset --hard origin/master

git push --set-upstream origin master
```
需要先安装完所有库再git pull，魔改过的modules:
```txt
hexo-theme-butterfly 
hexo-renderer-kramed
```