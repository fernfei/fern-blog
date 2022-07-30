---
title: 记录log4j远程执行BUG漏洞范围
date: 2022-07-30 22:39:34
tags:
- log4j
- BUG
---

### 1.背景

今天公司让排查所有项目是否在log4j远程执行漏洞范围，在网上查资料时发现有些许错误最终去官网查找了资料做一记录

### 2.log4j-CVE-2021-44832

![image-20220730224241742](http://image.hi-hufei.com/typora/image-20220730224241742.png)

结论受影响版本范围` 2.0beta7-2.17.0`但其中`2.3.2、2.12.4、2.17.1`不受影响

> 参考材料log4j官网 https://logging.apache.org/log4j/2.x/
