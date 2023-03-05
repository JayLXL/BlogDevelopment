---
title: blog优化更新
categories: 网站日志
tags: blog搭建
cover: https://s1.ax1x.com/2023/03/04/ppEAXz4.jpg
---
2023.3.4
增加Hexo渲染latex功能

[Hexo渲染latex](https://www.jianshu.com/p/9b9c241146bc)

命令：
```BASH
npm uninstall hexo-math --save
npm install hexo-renderer-mathjax --save
```
_config.butterfly.yml修改:
```txt
# MathJax Support
mathjax:
  enable: true
  per_page: true
```
post开头添加：
```txt
---
title: Hexo渲染LaTeX公式关键
date: 2020-09-30 22:27:01
mathjax: true
--
```

命令：
```
npm uninstall hexo-renderer-marked --save
npm install hexo-renderer-kramed --save
```
魔改/node_modules\kramed\lib\rules\inline.js：
```txt
//  escape: /^\\([\\`*{}\[\]()#$+\-.!_>])/,
  escape: /^\\([`*\[\]()#$+\-.!_>])/,
```
```txt
//  em: /^\b_((?:__|[\s\S])+?)_\b|^\*((?:\*\*|[\s\S])+?)\*(?!\*)/,
  em: /^\*((?:\*\*|[\s\S])+?)\*(?!\*)/,
```
重启：
```BASH
hexo clean && hexo g -d
```