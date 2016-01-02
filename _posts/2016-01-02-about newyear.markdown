---
layout: post
author:Leon-Kang
title: 解决github不统计贡献度
date:   2016-01-02
categories: iOS
tag: iOS
---

#####今天看自己的贡献度面板，白花花一片。一直有使用的github桌面版应用和Xcode提交应用。那么问题来了---为什么别人的都是绿油油一片，而我的就一次commit都没有统计到呢？

看了一些资料，我感觉可能是我本地配置邮箱和用户名不匹配（后来改过一次user.name）但是没有更新本地配置。

然后照着[官方文档](https://help.github.com/articles/set-up-git/)重新配置了用户名和邮箱。

问·题·解·决·了······

仅仅需要两条命令：

`
git config --global user.name "YOUR NAME"
`

`
git config --global user.email "YOUR EMAIL ADDRESS"
`

好几个月的板子就这么白了，虽然我比较水，但是也终归是一种记录和激励自己学习的途径吧。如果大家有不统计贡献度这种问题，也可以尝试一些重新配置用户名以及邮箱。