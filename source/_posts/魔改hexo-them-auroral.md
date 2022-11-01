---
title: 魔改hexo-thme-aurora
date: 2022-10-28 10:26:59
tags:
- hexo
categoryies:
- 记录
---

### 1.背景

最近想加友链功能时发现`aurora`并没有这个功能，直接添加新的网页也可以就是风格不统一，所以我打算修改`aurora`源代码这样就不存在风格不统一的问题了

### 2.魔改

![image-20221028104045725](http://image.hi-hufei.com/typora/image-20221028104045725.png)

主要对其做了添加功能，添加可以配置友链的配置代码，然后再渲染页面即可。本身并没有什么复杂可言，但意义不同后续我可以添加我任何想要的功能和样式，不再受配置文件的局限。

![image-20221028104308428](http://image.hi-hufei.com/typora/image-20221028104308428.png)

打包我修改的源代码去node_moudles替换源代码即可。

以后需要在配置文件中添加友链信息即可出现如下页面

![image-20221028104354909](http://image.hi-hufei.com/typora/image-20221028104354909.png)

### 3.最后

后续我可能会添加我想要的功能和页面。

项目地址[hexo-them-aurora](https://github.com/fernfei/hexo-them-aurora.git)

