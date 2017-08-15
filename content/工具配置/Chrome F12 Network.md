---
title: Chrome F12 Network
layout: page
date: 2017-08-15 11:00
---

![](http://wiki.smallcpp.cn/static/images/chromef12/network.png)

- 序号 **0**: 保存 Log, 页面跳转后, 控制台和网络控制台的内容暂时不清空, 保留下来
- 序号 **1**: 禁用缓存, 打钩后, 刷新页面, 所有请求都是 200, 不打勾当你打开重复页面会出现 304
- 序号 **2**: 可以设置模拟网络, 打开当前页
- 序号 **3**: 本页面共发了多少请求, 本页面大小
    * finish: 每次加载完一个资源 (js, css, img) 时间都会累加
    * DOM: 页面的 dom 完成渲染时间
    * load: 就是 dom + finish 的时间, 整个页面完成呈现给客户的时间
