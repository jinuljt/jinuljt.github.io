---
layout: post
title: "zdic alfred workflow"
---

源起是因为最近一段时间有点迷[昆剧](https://zh.wikipedia.org/zh/%E6%98%86%E6%9B%B2)。与昆剧第一次接触是在上海朱家角古镇看了"[实景园林昆曲《牡丹亭》](http://baike.baidu.com/subview/39853/10758244.htm)"。它在园林中辟出一块山水亭台具备之地，美轮美奂，优雅非常。我独爱昆曲的优雅。我骨子里是一个附庸风雅的人，之后就爱上了。

因为看昆曲，所以就开始看[汤显祖](https://zh.wikipedia.org/wiki/%E6%B1%A4%E6%98%BE%E7%A5%96)的临川四梦，所以我就遇到很多生僻字。也经常有用[汉典](http://www.zdic.net)，于是就萌发了做一个[zdic alfred workflow](https://github.com/jinuljt/zdic.alfredworkflow) 的想法。


项目地址：[github](https://github.com/jinuljt/zdic.alfredworkflow)

项目是用python开发的，用到了一个开发alfred workflow的python框架。东西很简单，就是用requests获取网页数据，然后用beatifulsoup处理html。可以在github中直接下载到export出来的workflow，下载之后即可直接是用。

目前简单实现了查字的功能，会有一些bug存在，并且由于是解析汉典的html数据，所以汉典页面有改动就会影响到功能。我会在使用过程中随时修正，当然最好是汉典能有API了。


使用方式也很简单，keyword是 "z"，之后输入想要查询的中文字即可。

![z 饕](/images/2016/zdic.1.png)
