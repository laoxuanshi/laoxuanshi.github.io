---
layout: post
title: uiwebview 吸顶功能的坑
categories: [前端开发]
description: some word here
---

# 一、背景

开发活动页时有一个需求，往下滚动到tab消失时，把tab吸顶；往上滚动则取消吸顶。主流的做法是监听scroll事件，通过滚动距离判断tab当前位置，并给tab添加position：fixed等属性。

在Android和IOS 内置浏览器中，这样做能改完美實現吸頂效果，但在ios app内打开时，吸顶和取消会有一定的时延，体验不好。

进过排查，发现ios app打开h5页面采用的是uiwebview，scroll事件发生时js脚本被阻塞，在滚动停止的时候才执行js实现吸顶。


# 二、解决方案

## 1. 更换app的webview类型
和app开发商量，把uiwebview更换为wkwebview，可以完美的解决这个问题。但app发版本需要审核时间，可能影响活动页的上线。

## 2. 采用iScroll等模拟滚动的库
经过尝试，发现uiwebview在touchmove事件发生时，不会阻塞js脚本的执行，因此我们采用了iScroll（https://github.com/cubiq/iscroll），它的实现原理正是通过监听touchstart、touchmove、touchend等事件，得到滚动的方向和距离，然后使用transform: translate(x, y)来模拟滚动的效果。

![](/images/web/w28.png)

缺陷：在列表数据量比较大时，transform的性能会受到限制，可能会出现滚动明显变卡顿的情况。