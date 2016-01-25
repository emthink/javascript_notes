---
layout: post
title: New Post
permalink: new-page.html
description: Some Description
date: 2016-01-25 15:03:48 +08:00
tags: "some tags here"
---
# 漫谈document.documentElement与document.body

在前端开发中，我们经常需要获取网页中滚动条滚过的长度，获取该值的方式一般通过scrollTop属性，如：document.body.scrollTop，是不是还经常看见document.documentElement.scrollTop，这两者都是经常用来获取文档滚动条滚过长度值的方式，他们又有什么区别呢？待下文，细细道来：

## DTD
在
