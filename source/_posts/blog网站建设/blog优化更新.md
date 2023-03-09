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

2023.3.7

之前一直有一个问题解决不了，Mathjax在网页中渲染公式的时候，总是出现一个底部横向滑动条，非常的不美观，查了一大堆资料也没解决。问了一下神奇的chatgpt，虽然没有一次弄对，但是提供了很多信息。

通过网页开发人员工具可以查看到，公式底部出现横向滚动条，是由于：
```html
<span class="mathjax-overflow" >
```

在btterfly主题下加入新的css文件，将这个类的属性给覆盖，就不会产生横向滚动条了。
```css
.mathjax-overflow{
    overflow-x: hidden !important;
}
```
最后将新加入的文件，添加在_config.butterfly.yml中：
```yml
inject:
  head:
      - <link rel="stylesheet" href="/css/新文件名.css">
```
