---
title: nth-of-type不能用在class selector
date: 2018-07-02 00:07:04
tags: [css,nth-of-type,nth-child]
categories: CSS
---

今天踩雷，想用CSS控制class第以個做操控
nth-of-type 跟nth-child
nth-of-type是找 第幾個子元素
nth-child是找位置(位置是指所有元素)

不能抓`.aaa:first-of-type`不能正常跑
**只能對html元素**
不能對class

這個雷不要再踩到!!!