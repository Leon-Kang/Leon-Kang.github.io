---
layout: post
author: Leon-Kang
title: 关于UIAlertController的用法
date:   2015-12-31
categories: iOS
tag: iOS, Objective-C, UIALertController
---



# 关于UIAlertController的用法

##简介

> 在新版本的Xcode中增加了UIAlertController而UIAlertView在调用时则会显示已经过期。本人在使用中经常会使用到Alert窗口，所以整理一下用法以方便自己使用。

## 基本语法

### 一、基础控件的添加(原UIAlertView)

* 创建一个UIAlertController对象：


{% highlight Objective-C linenos %}
UIAlertController *alertOne = [UIAlertController alertControllerWithTitle:@"I'm alertOne" message:@"I want to tell you something" preferredStyle:UIAlertControllerStyleAlert]; 
[self presentViewController:alertOne animated:YES completion:nil];
{% endhighlight %}

* 这一段代码初始化了一个最基本名字叫alertOne；Title为『I'm alertOne』；显示内容为『I want to tell you something』；而Style设为UIAlertControllerStyleAlert（最常见的弹出式alert。一共有两种，还有一种从下弹出式，稍后讲解）的UIAlertController对象。效果如下，因为没有添加AlertAction所以看起来很丑。

![](http://7xpo5x.com1.z0.glb.clouddn.com/UIAlertController-01.png)

* 让我们来为它添加一个『取消』按钮和一个『确定』按钮，让它看起来正常一些。


{% highlight Objective-C linenos %}
UIAlertAction *cancel = [UIAlertAction actionWithTitle:@"取消" style:UIAlertActionStyleCancel handler:nil];         
[alertOne addAction:cancel];    
UIAlertAction *certain = [UIAlertAction actionWithTitle:@"确定" style:UIAlertActionStyleDefault handler:nil];    
[alertOne addAction:certain];
{% endhighlight %}
    
* 初始化方法中Title对应着显示出来按钮的名称；style对于的是按钮的类型，有三种类型，这里用到了Cancel和Default，取消和默认的样式；还有一种是Destructive (销毁)样式，我们稍后添加。这样，我们就给他安上了两个按钮，就实现最基本也是最常见的alert样式。来看一下效果：

![](http://7xpo5x.com1.z0.glb.clouddn.com/UIAlertController-02.png)

* 接着我们继续在AlertController上插入一个textField控件，实现输入监听。


{% highlight Objective-C linenos %}
[alertOne addTextFieldWithConfigurationHandler:^(UITextField * _Nonnull textField) {
      // 监听
      [[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(handleTextFieldTextDidChangeNotification:) name:UITextFieldTextDidChangeNotification object:textField];
      // 定制键盘和输入框背景文字及clearButton
      textField.placeholder = @"请输入";
      textField.clearButtonMode = UITextFieldViewModeWhileEditing;
    }];
}
{% endhighlight %}
    
   * 这一段是在其自带的block代码块中利用NSNotificationCenter来实现值的传输。如果输入有变动的话就会传出变化的值。当然也可以根据需要写在按钮的方法block块中，以实现点击『取消』或『确定』按钮时实现相应的功能。然后我们继续完成handleTextFieldTextDidChangeNotification:方法：
    
{% highlight Objective-C linenos %}
- (void)handleTextFieldTextDidChangeNotification:(NSNotification *)notification
{
    UITextField *textField = notification.object;
    self.textLabel.text = textField.text;
}
{% endhighlight %}
 
![](http://7xpo5x.com1.z0.glb.clouddn.com/UIAlertController-03.png) 


### 二、从下方弹出式UIAlertController(原UIActionSheet)
* 上边我们使用的是基于UIAlertControllerStyleAlert风格的，下边来尝试一下另一种风格UIAlertControllerStyleActionSheet的使用方法。



{% highlight Objective-C linenos %}
NSString *title = NSLocalizedString(@"AlertTwo", nil);
UIAlertController *alertTwo = [UIAlertController alertControllerWithTitle:title message:nil preferredStyle:UIAlertControllerStyleActionSheet];
{% endhighlight %}

![](http://7xpo5x.com1.z0.glb.clouddn.com/UIAlertController-04.png)

* 这是最原始的形态，还是一样的丑，而且一样无法返回，添加按钮方法同上，但是这次我们把一个换成Destructive风格来看一下效果。


{% highlight Objective-C linenos %}
UIAlertAction *cancel = [UIAlertAction actionWithTitle:@"取消" style:UIAlertActionStyleCancel handler:nil];
[alertTwo addAction:cancel];    
UIAlertAction *certain = [UIAlertAction actionWithTitle:@"清空" style:UIAlertActionStyleDestructive handler:^(UIAlertAction * _Nonnull action) {
        self.textLabel.text = nil;
    }];
[alertTwo addAction:certain];
{% endhighlight %}

![](http://7xpo5x.com1.z0.glb.clouddn.com/UIAlertController-06.png)

* 再添加按钮以后，把alertTwo对象的Title设为nil，便可以把最上方的Title栏去掉，变成如下效果。

![](http://7xpo5x.com1.z0.glb.clouddn.com/UIAlertController-07.png)


* 这种风格的AlertController是不可以添加textField控件的。报错信息为『reason: 'Text fields can only be added to an alert controller of style UIAlertControllerStyleAlert'』,苹果官方很明确地表示了不可以。

* UIAlertControllerStyleAlert说：『我就看看，不说话！』

![](http://7xpo5x.com1.z0.glb.clouddn.com/UIAlertController-09.png)

* UIAlertController用法比较简单，但是比较实用。如果想要实现更繁杂的功能或者效果还是需要自己去定制的。

 [需要源代码点我](https://github.com/Leon-Kang/studyA/tree/master/AboutUIAlertController)

###未完待续！