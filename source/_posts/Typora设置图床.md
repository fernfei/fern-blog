---
title: Typora设置图床
date: 2022-06-30 01:11:37
tags: Typora
---

### 1.背景

最近搭建了博客网站用Typora写作可是其本身储存图片在本地，发不到博客网站上时这些图片都不能查看所以需要一个OSS储存。

### 2.搭建OOS储存

我使用的是七牛云的OSS因为它只要绑定域名就送10G储存空间，具体搭建方式自行百度（因为我不记得了😂）

### 3.安装picgo

```linux
npm install picgo -g
```

### 4.设置uploader

```linux
picgo set uploader
```

![image-20220630011739190](http://image.hi-hufei.com/typora/image-20220630011739190.png)

| z0   | 华东 |
| ---- | ---- |
| z1   | 华北 |
| Z3   | 华南 |

```linux
picgo use uploader
```

### 5.设置

```linux
picgo upload "图片路径" 
```

### 6.Typora设置

![image-20220630012355234](http://image.hi-hufei.com/typora/image-20220630012355234.png)
