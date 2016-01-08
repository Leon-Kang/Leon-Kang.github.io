---
layout: post
author: Leon-Kang
title: 利用Swift2.0编写UIAlertController
date:   2016-01-05
categories: iOS
tag: iOS
---

##利用Swift2.0编写UIAlertController
* 上次使用Objective-C语言介绍了UIAlertController大致的用法，最近正好在学习swift，这次我们使用swift语言来添加UIAlertController。
另外先附上上一篇的链接，里边介绍了UIAlertController的大致用法。[关于UIAlertController的用法](http://leon-kang.github.io/ios/2015/12/31/关于UIAlertController的用法/)

> 下面我们仍然采用上一篇的结构来写，方便对两种语言下的编写形成对照。还是两种风格分开介绍。如果有什么没有涉及到也可以结合上一篇来看。

###一、AlertView
* 还是首先创建一个UIAlertController对象。这里其实我的写法还是骨子里透着OC的灵魂，各个地方都比较规整。在第一部分先这样写，跟OC对照着看效率会比较高，在下一部分会采用『能省则省』的写法来写。

{% highlight swift linenos %}
let alertView: UIAlertController = UIAlertController.init(title: "AlertView", message: "I want to tell you something", preferredStyle: UIAlertControllerStyle.Alert)
{% endhighlight %}

* 然后我们再给它添加两个按钮，如果不添加任何按钮跳转到UIAlertController是无法返回的。

{% highlight swift linenos %}
let alertViewAction: UIAlertAction = UIAlertAction.init(title: "确定", style: UIAlertActionStyle.Default, handler: { (UIAlertAction) -> Void in 
      })          
let alertViewCancelAction: UIAlertAction = UIAlertAction.init(title: "取消", style: UIAlertActionStyle.Cancel, handler: nil)           
alertView.addAction(alertViewAction)
alertView.addAction(alertViewCancelAction)
{% endhighlight %}

效果如图：
![](http://7xpo5x.com1.z0.glb.clouddn.com/SwiftUIAlertController01.png)

* 当然按钮的style还是三种『默认』、『取消』、『销毁』，我们再给它添加一个『销毁』风格的按钮：

{% highlight swift linenos %}
let alertViewDestructive: UIAlertAction = UIAlertAction.init(title: "销毁", style: UIAlertActionStyle.Destructive, handler: { (UIAlertAction) -> Void in
 })
alertView.addAction(alertViewDestructive)
{% endhighlight %}

如图：
![](http://7xpo5x.com1.z0.glb.clouddn.com/SwiftUIAlertController02.png)
* 如果需要在点击时触发某些事件，可以写在handler：这个属性后边的闭包中（相当于OC中的block又有些许不同）。比如我们继续添加一个textField然后在闭包中设置一些属性。

{% highlight swift linenos %}
 alertView.addTextFieldWithConfigurationHandler({ (UITextField) -> Void in
                UITextField.placeholder = "name"
                UITextField.clearButtonMode = UITextFieldViewMode.WhileEditing
            })
{% endhighlight %}

效果如图：
![](http://7xpo5x.com1.z0.glb.clouddn.com/SwiftUIAlertController04.png)

* 最后千万记得跳转到我们创建的alertView上：
{% highlight swift linenos %}
self.presentViewController(alertView, animated:true , completion: nil)
{% endhighlight %}


###二、ActionSheet

* 创建一个ActionSheet风格的AlertController：

{% highlight swift linenos %}
let alertSheet = UIAlertController.init(title: "alertSheet", message: "no message", preferredStyle: .ActionSheet)
{% endhighlight %}

* 跟上边相比较，少了明确声明类型和选择风格时的前缀。这也是我很喜欢的一点，特别是选择风格时只需要使用点语法就可以，再加上Xcode强大的提示和功能，一目了然，而且很难选错。

* 然后同上边一样我们继续给他添加上三个风格的按钮。

{% highlight swift linenos %}
let alertSheetAction = UIAlertAction.init(title: "确定", style: .Default, handler: nil)
alertSheet.addAction(alertSheetAction)
let alertSheetCancel = UIAlertAction.init(title: "取消", style: .Cancel, handler: nil)
alertSheet.addAction(alertSheetCancel)
let alertSheetDestructive = UIAlertAction.init(title: "销毁", style: .Destructive, handler: nil)
alertSheet.addAction(alertSheetDestructive)
{% endhighlight %}

效果如图：
![](http://7xpo5x.com1.z0.glb.clouddn.com/SwiftUIAlertController05.png)

* 当然也同OC中一样，把title与message两个属性设置为nil便可以隐藏掉最上边一栏只剩下按钮。

{% highlight swift linenos %}
let alertSheet = UIAlertController.init(title: nil, message: nil, preferredStyle: .ActionSheet)
{% endhighlight %}

效果如下：
![](http://7xpo5x.com1.z0.glb.clouddn.com/SwiftUIAlertController06.png)

* 在上一篇中没有提到，其实这种方法在AlertView风格中也是试用的。不过····效果你自己看吧！

![](http://7xpo5x.com1.z0.glb.clouddn.com/SwiftUIAlertController07.png)

* 真的蛮不可接受的····。去掉textField还可以。

![](http://7xpo5x.com1.z0.glb.clouddn.com/SwiftUIAlertController08.png)

###顺便补上监听传值的那一部分

* 这是设置监听的那一部分，在textField的闭包中设置



{% highlight swift linenos %}
alertView.addTextFieldWithConfigurationHandler({ (UITextField) -> Void in
UITextField.placeholder = "name"
UITextField.clearButtonMode = UITextFieldViewMode.WhileEditing
                
// 设置监听
 _ = NSNotificationCenter.defaultCenter().addObserver(self, selector: "textChange:" , name: UITextFieldTextDidChangeNotification, object: UITextField)
                
})
{% endhighlight %}

* 监听方法

{% highlight swift linenos %}
func textChange(notification: NSNotification) {
   let textFied = notification.object as! UITextField      
   textLabel.text = textFied.text
}
{% endhighlight %}

* 释放监听

{% highlight swift linenos %}
deinit {
    NSNotificationCenter.defaultCenter().removeObserver(self)
}
{% endhighlight %}





##总结
> 控件的使用方法几乎是没有变化的，而且swift在语法上要简洁很多很多。使用起来也非常方便高效，如果你还在OC的坑里摸不着头脑，直接学习swift吧！

[代码传送门](https://github.com/Leon-Kang/UIAlertController)

