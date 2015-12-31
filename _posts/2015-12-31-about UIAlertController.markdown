---
layout: post
title: "iOS9关于UIAlertController的用法"
date:   2015-12-31
categories: first
---

# 关于UIAlertController的用法
## 简介

> 在新版本的Xcode中增加了UIAlertController而UIAlertView在调用时则会显示已经过期。本人在使用中经常会使用到Alert窗口，所以整理一下用法以方便自己使用。

## 基本语法

` UIAlertController *alertOne = [UIAlertController alertControllerWithTitle:@"I'm alertOne" message:@"I want to tell you something" preferredStyle:1]; `

* 这一段代码初始化了一个最基本名字叫alertOne；Title为『I'm alertOne』；显示内容为『I want to tell you something』；而Style设为1（最常见的弹出式alert）的UIAlertController对象。效果如下，因为没有添加AlertAction所以看起来很丑。

![](http://7xpo5x.com1.z0.glb.clouddn.com/UIAlertController1.png)

* 让我们来为它添加一个返回按钮一个确定按钮，让它看起来正常一些。

`   UIAlertAction *cancel = [UIAlertAction actionWithTitle:@"取消" style:UIAlertActionStyleCancel handler:nil];`

`	[alertOne addAction:cancel];`

`   UIAlertAction *certain = [UIAlertAction actionWithTitle:@"确定" style:UIAlertActionStyleDefault handler:nil];`

`    [alertOne addAction:certain];`
* 初始化方法中Title对应着显示出来按钮的名称；style对于的是按钮的类型，有三种类型，这里用到了Cancel和Default，取消和默认的样式；还有一种是Destructive (销毁)样式，我们稍后添加。这样，我们就给他安上了两个按钮，就实现最基本也是最常见的alert样式。来看一下效果：
![](http://7xpo5x.com1.z0.glb.clouddn.com/UIAlertController-2.png)

* 