---
layout: post
author: Leon-Kang
title: iOS页面传值问题（swift2.0篇）
date:  2016-01-10
categories: iOS
tag: swift
---

#iOS页面传值问题（swift2.0篇）

> 同样是介绍iOS开发中的页面传值，此篇与上一篇[使用Objective-C传值](http://leon-kang.github.io/ios/2016/01/05/页面传值问题OC/)对应。在OC中的方法在swift中也是同样适用的，而且还要介绍一些swift特有的方式。


##一、协议
> 使用协议还是3+2一共五个步骤，按部就班来实现就可以了。还是先想清楚谁委托谁代理，在逻辑上不要出现错误。

* 新的项目中创建了两个新的类：A类负责显示，B类负责输入。所以说A只要负责拿到数据即可，让B类来作委托方。这里的协议是写在委托方的文件之中的，并没有重新创建。

借用上一篇的图来继续实现各个步骤：
![](http://7xpo5x.com1.z0.glb.clouddn.com/passValue030.png)

* 继续来委托方的两步走，因为这里代码行数真的不多，而且不分.h和.m，我们来看一下B类的所有代码：


{% highlight swift linenos %}
import UIKit

/**
 *    @author kl, 16-01-10 17:01:25
 *
 *    @brief 传值协议
 */
protocol passValueDelegate {
    func passValue(var text: String)
}

class BViewController: UIViewController {
    // 1、声明属性
    var delegate: passValueDelegate?
    
    @IBOutlet weak var textField: UITextField!
    
    override func viewDidLoad() {
        super.viewDidLoad()
        
    }
    
    @IBAction func buttonActive(sender: AnyObject) {
        
        // 2、调用代理方法，把值传输出去
        self.delegate?.passValue(textField.text!)
        
        self.dismissViewControllerAnimated(true, completion: nil)
    }
}
{% endhighlight %}

* 接着是代理方的三步走，还是来看全部代码：
{% highlight swift linenos %}
import UIKit

class AViewController: UIViewController, passValueDelegate // 1、遵守协议
{

    @IBOutlet weak var textLabel: UILabel!
    
    override func viewDidLoad() {
        super.viewDidLoad()

    }
    
    @IBAction func buttonAction(sender: AnyObject) {
        let bVC = BViewController()
        
        // 2、设置代理
        bVC.delegate = self
        
        self.presentViewController(bVC, animated: true, completion: nil)
        
    }

    // 3、重写协议方法，取到传值
    func passValue(text: String) {
        textLabel.text = text
    }

}
{% endhighlight %}

##二、闭包
> 在swift中对闭包有了很好的支持，相当于C和Objective-C的blocks或者Python的lambdas。而且相对于blocks更加灵活一些。这里简单介绍一下使用闭包传递信息的方法。

* 需要在B类文件中做的事情：

1、在B类先利用typealias声明一个闭包类型：
{% highlight swift linenos %}
typealias nameValue = (String)->Void
{% endhighlight %}

2、然后在声明一个此类型的属性：
{% highlight swift linenos %}
    var nameText: nameValue?
{% endhighlight %}

3、掉用一次闭包属性把值传出去，此处是在Button的跳转方法中调用的：
{% highlight swift linenos %}
        // 使用闭包传值出去
        self.nameText!(self.bNameTextField.text!)
{% endhighlight %}

4、再添加一个方法用于传值：
{% highlight swift linenos %}
    // 闭包方法
    func returnNameValue(name: nameValue) {
        self.nameText = name
    }
{% endhighlight %}

* 在A类文件中做的事情：

1、调用一次A类对象的闭包传值方法拿到值，这里是在Button的跳转方法里调用的：
{% highlight swift linenos %}
        // 取到闭包传递的值
        bVC.returnNameValue { (name) -> Void in
            self.nameTextField.text = name
        }
{% endhighlight %}

* 最后有两个页面的全部代码，可以参照整体来看。


##三、使用NSNotificationCenter
> 在swift语言中通知机制几乎没什么太大变化，还是以NSNotificationCenter为中介者，通过String类型标识来广播和接受信息。一般步骤是广播-接收-移除观察者，最后一步千万不能忘，收到信息记得移除观察者，不然会产生一些意料之外不可预测的情况发生，而且对系统资源也是一种浪费。

1、A发出一个名为"name"的广播，广播内容是A页面上的textField所输入的内容，这里是写在跳转语句后边的，以确保发送的广播是输入完成以后的：

{% highlight swift linenos %}
    @IBAction func buttonAction(sender: AnyObject) {
        let bVC = BViewController()
        
        // 取到闭包传递的值
        bVC.returnNameValue { (name) -> Void in
            self.nameTextField.text = name
        }
        
        // 2、设置代理
        bVC.delegate = self
        
        self.presentViewController(bVC, animated: true, completion: nil)
        
        // 发出广播
        NSNotificationCenter.defaultCenter().postNotificationName("name", object: self.nameTextField.text)
    }
{% endhighlight %}

2、在B类中接收信息，并在收到时执行指定方法，方法的参数也还是跟Objective-C中一样，第一个为要注册的观察者；第二个同样是SEL类型的参数，不过在swift中没有了@selector关键字，采用以String类型输入要执行的方法名，这个跟OC中是有些区别的；第三个name参数即为要接收的信息的名字，这里采用String类型作为标识；最后一个参数同样是是否指定信息的发送者，填写的话便表示你只需要你填写的那个对象发出的信息，这里nil表示不指定对象：

{% highlight swift linenos %}
    override func viewDidLoad() {
        super.viewDidLoad()
        self.bNameTextField.clearButtonMode = .WhileEditing
        self.textField.clearButtonMode = .WhileEditing
        
        // 注册为观察者，接收信息
        NSNotificationCenter.defaultCenter().addObserver(self, selector: "printName:", name: "name", object: nil)
    }
{% endhighlight %}

* 方法内容：

{% highlight swift linenos %}
    func printName(notifaction: NSNotification) {
        print(notifaction.object)
        self.bNameTextField.text = notifaction.object as? String
    }
{% endhighlight %}

3、在方法中添加移除观察者者的代码：

{% highlight swift linenos %}
    func printName(notifaction: NSNotification) {
        print(notifaction.object)
        self.bNameTextField.text = notifaction.object as? String
        NSNotificationCenter.defaultCenter().removeObserver(self)
    }
{% endhighlight %}

最后还是借用上次的图：
![](http://7xpo5x.com1.z0.glb.clouddn.com/passValue020.png)

* 这样A中的nameTextField和B中的bNameTextField就相连接起来了，在这两个控件中输入信息在更换页面时都会显示在另一个控件上。从B到A是采用的是闭包传值，而从A到B时采用的是广播传值。

####最后的A类文件内容：

{% highlight swift linenos %}
import UIKit

class AViewController: UIViewController, passValueDelegate // 1、遵守协议
{
    @IBOutlet weak var textLabel: UILabel!
    
    @IBOutlet weak var nameTextField: UITextField!
    
    override func viewDidLoad() {
        super.viewDidLoad()
        self.nameTextField.clearButtonMode = .WhileEditing
    }
        
    @IBAction func buttonAction(sender: AnyObject) {
        let bVC = BViewController()
        
        // 取到闭包传递的值
        bVC.returnNameValue { (name) -> Void in
            self.nameTextField.text = name
        }
        
        // 2、设置代理
        bVC.delegate = self
        
        self.presentViewController(bVC, animated: true, completion: nil)
        
        // 发出广播
        NSNotificationCenter.defaultCenter().postNotificationName("name", object: self.nameTextField.text)
    }
    
    // 3、重写协议方法，取到传值
    func passValue(text: String) {
        textLabel.text = text
    }
    

}
{% endhighlight %}

#####最后的B类文件内容：

{% highlight swift linenos %}
import UIKit

protocol passValueDelegate {
    func passValue(var text: String)
}

typealias nameValue = (String)->Void

class BViewController: UIViewController {
    // 1、声明属性
    var delegate: passValueDelegate?
    
    @IBOutlet weak var textField: UITextField!
    @IBOutlet weak var bNameTextField: UITextField!
    var nameText: nameValue?
    
    override func viewDidLoad() {
        super.viewDidLoad()
        self.bNameTextField.clearButtonMode = .WhileEditing
        self.textField.clearButtonMode = .WhileEditing
        
        // 注册为观察者，接收信息
        NSNotificationCenter.defaultCenter().addObserver(self, selector: "printName:", name: "name", object: nil)
    }
    
    @IBAction func buttonActive(sender: AnyObject) {
        // 2、调用代理方法，把值传输出去
        self.delegate?.passValue(textField.text!)
        
        // 使用闭包传值出去
        self.nameText!(self.bNameTextField.text!)
        
        self.dismissViewControllerAnimated(true, completion: nil)
    }
    
    func printName(notifaction: NSNotification) {
        print(notifaction.object)
        self.bNameTextField.text = notifaction.object as? String
        NSNotificationCenter.defaultCenter().removeObserver(self)
    }
    
    // 闭包方法
    func returnNameValue(name: nameValue) {
        self.nameText = name
    }
}
{% endhighlight %}

####[需要代码戳这里](https://github.com/Leon-Kang/PassValue)