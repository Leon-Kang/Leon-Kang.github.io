---
layout: post
author: Leon-Kang
title: UICollectionView用法（Objective-C篇）
date:  2016-01-21
categories: iOS
tag: iOS
---

> UICollectionView是在iOS6.0以后登上舞台的，与UITableView比在一些地方十分方便和易用。这次先使用Objective-C语言使用一下，随后会使用swift来使用。先来看一下它的继承关系。


### 一、初始化和基本设置
> 图片来自[开发者网站](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UICollectionView_class/)，大家也可以直接看官方文档，说的比较详细，主要的代理方法都有列出。我们可以看到它继承自UIScrollView，与UITableView是一样的，在布局时必须小心谨慎，不然可能因为很多原因发生异常。与UITableView相比，UICollectionView在布局上会更加灵活，下面来一一介绍。

![](http://7xpo5x.com1.z0.glb.clouddn.com/UICollectionView01.png)


* 首先遵守协议，这里需要遵守两个协议"<UICollectionViewDataSource,  UICollectionViewDelegateFlowLayout>",第一个协议倾向于数据；第二个倾向于动作和布局，其实UICollectionViewDelegateFlowLayout是UICollectionViewDelegate的子协议，所以同时也遵守了UICollectionViewDelegate协议。

* 在创建UICollectionView对象时需要注意方法的使用。


方法一：如果还是像以往创建对象一样使用这种类似的方法，结果是会报错的，不要问我为什么知道的···：

{% highlight Objective-C %}
    CGRect screen = [UIScreen mainScreen].bounds;
    UICollectionView *collectionView = [[UICollectionView alloc] initWithFrame:CGRectMake(0, 25, screen.size.width,
    [self.view addSubview:collectionView];
{% endhighlight %}

报错原因，说在初始化时是需要布局参数的：

![](http://7xpo5x.com1.z0.glb.clouddn.com/UICollectionView02.png)

不过这种方法你只要补上：

{% highlight Objective-C %}
    UICollectionViewFlowLayout *flowLayout = [[UICollectionViewFlowLayout alloc] init];
    collectionView.collectionViewLayout = flowLayout;
{% endhighlight %}

这两句也是可以的，这个flowLayout到底有什么用呢？那用处可真的还比较大呢！来看UICollectionViewFlowLayout类的头文件：

![](http://7xpo5x.com1.z0.glb.clouddn.com/UICollectionView03.png)

里边包括了关于Header和Footer是否可见，大小如何，整体布局方向等等，如果初始化时没有这个类的支持，CollectionView君是不知道该如何显示界面的。这里我们通过flowLayout设置一下界面朝向，就是ScrollView可以拖拽的方向，朝向一共两种，Vertical和Horizontal，垂直还是水平，垂直的话就是一般的可以上下滑动，cell是横向排列的；水平的话是可以左右滑动，cell是垂直排布的。这里采用Vertical：

{% highlight Objective-C %}
    [flowLayout setScrollDirection:UICollectionViewScrollDirectionVertical];
{% endhighlight %}


方法二，初始化时直接调用添加flowLayout，优点就是省了一行代码，能省为什么不省呢，总之在初始化时不能遗漏UICollectionViewFlowLayout这个类的对象。

{% highlight Objective-C %}
    UICollectionViewFlowLayout *flowLayout = [[UICollectionViewFlowLayout alloc] init];
    UICollectionView *collectionView = [[UICollectionView alloc] initWithFrame:[UIScreen mainScreen].bounds collectionViewLayout:flowLayout];
    [self.view addSubview:collectionView];
{% endhighlight %}

* 然后不要忘了设置代理者，细节问题一定不要遗忘。

{% highlight Objective-C %}
    collectionView.delegate = self;
    collectionView.dataSource = self;
{% endhighlight %}

* 然后再对cell进行注册：

{% highlight Objective-C %}
    [collectionView registerClass:[UICollectionViewCell class] forCellWithReuseIdentifier:@"cell"];
{% endhighlight %}

###二、代理方法

* 协议中有两个方法是必须实现的，第一个方法是设置在一个Section中Item的数量，返回一个NSInteger类型的数字即可；第二个是初始化cell和决定cell的内容等，最后需要返回一个UICollectionViewCell 对象：

{% highlight Objective-C %}
@required

- (NSInteger)collectionView:(UICollectionView *)collectionView numberOfItemsInSection:(NSInteger)section;

// The cell that is returned must be retrieved from a call to -dequeueReusableCellWithReuseIdentifier:forIndexPath:
- (UICollectionViewCell *)collectionView:(UICollectionView *)collectionView cellForItemAtIndexPath:(NSIndexPath *)indexPath;
{% endhighlight %}

* 这里值得说一下的是在cell初始化时调用的方法，这个方法会根据ReuseIdentifier直接去栈（queue）中寻找相应的cell，这也是CollectionView和TableView相同的栈存储机制决定的，因为在屏幕上显示时，只会初始化可以看到的相应数量的cell，而你看不到的cell信息是储存栈中的，在滑动中如果加载到才会从栈中把相应信息取出来显示在屏幕上：

{% highlight Objective-C %}
    UICollectionViewCell *cell = [collectionView dequeueReusableCellWithReuseIdentifier:@"cell" forIndexPath:indexPath];
{% endhighlight %}

 而且注意一下在给cell添加控件时是专门有一个View层的，最好添加在这一个View上，来看代码：
 
 {% highlight Objective-C %}
// 定制cell
- (UICollectionViewCell *)collectionView:(UICollectionView *)collectionView cellForItemAtIndexPath:(NSIndexPath *)indexPath {
    
    UICollectionViewCell *cell = [collectionView dequeueReusableCellWithReuseIdentifier:@"cell" forIndexPath:indexPath];
    
    for (id subView in cell.contentView.subviews) {
        [subView removeFromSuperview];
    }
    
    cell.backgroundColor = [UIColor greenColor];
    
    UILabel *lable = [[UILabel alloc] initWithFrame:CGRectMake(0, 0, cell.frame.size.width, 20)];
    lable.textAlignment = NSTextAlignmentCenter;
    
    [cell.contentView addSubview:lable];
    
    lable.text = [NSString stringWithFormat:@"%ld", (long)indexPath.row];
    
    
    return cell;
}
{% endhighlight %}

* 现在运行的效果见图：

![](http://7xpo5x.com1.z0.glb.clouddn.com/UICollectionView04.png)


* 我们来调用方法设置一下cell的大小，这里只是简单地附一个值，也可以根据屏幕大小或者你需要的相对大小来设定：

{% highlight Objective-C %}
- (CGSize)collectionView:(UICollectionView *)collectionView layout:(UICollectionViewLayout *)collectionViewLayout sizeForItemAtIndexPath:(NSIndexPath *)indexPath {
    
    CGSize size = CGSizeMake(100, 150);
    
    return size;
}

{% endhighlight %}

效果图：
![](http://7xpo5x.com1.z0.glb.clouddn.com/UICollectionView05.png)

* 继续做一些微调什么的，这里都可以根据不同的Section来调整数据，我们把section换成两个而item换成每个section中有5个，这里注意一下section的编号也是从0开始的而不是1：

{% highlight Objective-C %}
// 每组的cell的边界
- (UIEdgeInsets)collectionView:(UICollectionView *)collectionView layout:(UICollectionViewLayout*)collectionViewLayout insetForSectionAtIndex:(NSInteger)section {
    
    UIEdgeInsets edgeInsets = UIEdgeInsetsMake(0, 20, 0, 20);
    
    if (section == 1) {
        edgeInsets = UIEdgeInsetsMake(0, 10, 0, 10);
    }
    
    return edgeInsets;
}

// 最小行间距
- (CGFloat)collectionView:(UICollectionView *)collectionView layout:(UICollectionViewLayout*)collectionViewLayout minimumLineSpacingForSectionAtIndex:(NSInteger)section {
    
    CGFloat minL = 0;
    
    if (section == 1) {
        minL = 5;
    }
    
    return minL;
}

// 最小列间距
- (CGFloat)collectionView:(UICollectionView *)collectionView layout:(UICollectionViewLayout*)collectionViewLayout minimumInteritemSpacingForSectionAtIndex:(NSInteger)section {
    
    CGFloat minI = 0.5;
    
    return minI;
}
{% endhighlight %}

![](http://7xpo5x.com1.z0.glb.clouddn.com/UICollectionView07.png)

* item大小，行间距，列间距其实使用flowLayout也是可以设置的，至于用哪种方式就看你自己需求而定了：

{% highlight Objective-C %}
    [flowLayout setMinimumInteritemSpacing:0];
    [flowLayout setMinimumLineSpacing:0];
    [flowLayout setItemSize:CGSizeMake(100, 150)];
{% endhighlight %}


* 来利用代理方法让cell变得可点击并且会弹出AlertView窗口，第一个方法是在点击cell时会触发的回调方法，这里被点击的话会弹出一个窗口显示这是第几个cell；而第二个方法则决定这个cell是否可以点击，我们在这里做了一个判断；当然选择事件也是有很多情况的回调方法的，这里只是最基础的：

{% highlight Objective-C %}
// 被选中时调用的方法
- (void)collectionView:(UICollectionView *)collectionView didSelectItemAtIndexPath:(NSIndexPath *)indexPath {
    
    NSString *msg = [NSString stringWithFormat:@"It's %ld", (long)indexPath.row];
    
    UIAlertController *alertC = [UIAlertController alertControllerWithTitle:@"Hello" message:msg preferredStyle:UIAlertControllerStyleAlert];
    UIAlertAction *cancel = [UIAlertAction actionWithTitle:@"cancel" style:UIAlertActionStyleCancel handler:nil];
    [alertC addAction:cancel];
    [self presentViewController:alertC animated:YES completion:nil];
    
}

// 是否可以被选择
- (BOOL)collectionView:(UICollectionView *)collectionView shouldSelectItemAtIndexPath:(NSIndexPath *)indexPath {
    
    if ((indexPath.row % 2) == 1) {
        return YES;
    }
    return NO;
}
{% endhighlight %}


效果如图：

![](http://7xpo5x.com1.z0.glb.clouddn.com/UICollectionView08.png)

###三、Header和Footer

* Header和Footer也是CollectionView和TableView所不一样的地方。在CollectionView中可以直接使用UICollectionReusableView这个类创建Header和Footer，也可以自己使用xib做定制，在页面布局上是十分强大的。

* 其实步骤跟Cell有些类似，首先设置使用flowLayout设置它们的高度和宽度。

{% highlight Objective-C %}
    flowLayout.headerReferenceSize = CGSizeMake(screen.size.width, 25);
    flowLayout.footerReferenceSize = CGSizeMake(screen.size.width, 20);
{% endhighlight %}

* 然后进行方法的代理方法的编写：

{% highlight Objective-C %}
- (UICollectionReusableView *) collectionView:(UICollectionView *)collectionView viewForSupplementaryElementOfKind:(NSString *)kind atIndexPath:(NSIndexPath *)indexPath
{
    UICollectionReusableView *reusableview = nil;
    UILabel *lable = [[UILabel alloc] initWithFrame:CGRectMake(0, 0, [UIScreen mainScreen].bounds.size.width, 20)];
    lable.textAlignment = NSTextAlignmentCenter;
    
    if (kind == UICollectionElementKindSectionHeader)
    {
        UICollectionReusableView *headerView = [collectionView dequeueReusableSupplementaryViewOfKind:UICollectionElementKindSectionHeader withReuseIdentifier:@"HeaderView" forIndexPath:indexPath];
        
        lable.textColor = [UIColor blueColor];
        lable.text = @"HeaderView";
        headerView.backgroundColor = [UIColor orangeColor];
        [headerView addSubview:lable];
        reusableview = headerView;
    }
    
    if (kind == UICollectionElementKindSectionFooter)
    {
        UICollectionReusableView *footerview = [collectionView dequeueReusableSupplementaryViewOfKind:UICollectionElementKindSectionFooter withReuseIdentifier:@"FooterView" forIndexPath:indexPath];
        
        lable.textColor = [UIColor whiteColor];
        lable.text = @"FooterView";
        footerview.backgroundColor = [UIColor blackColor];
        [footerview addSubview:lable];
        reusableview = footerview;
    }
    
    return reusableview;
}
{% endhighlight %}

* 最后进行注册：

{% highlight Objective-C %}
    [collectionView registerClass:[UICollectionReusableView class] forSupplementaryViewOfKind:UICollectionElementKindSectionHeader withReuseIdentifier:@"HeaderView"];
    [collectionView registerClass:[UICollectionReusableView class] forSupplementaryViewOfKind:UICollectionElementKindSectionFooter withReuseIdentifier:@"FooterView"];
{% endhighlight %}

效果如图，这里只是做个示范，所以很丑，请不要在意：
![](http://7xpo5x.com1.z0.glb.clouddn.com/UICollectionView11.png)

###四、showMenu
* 首先来看需要实现的方法，第一个方法是决定哪些cell是可弹出menu的方法，这里直接返回YES，令所有的cell都可以弹出；第二个方法则是用来确定支持哪一种编辑方法，在CollectionView中仅支持三种编辑方法，粘贴paste，复制copy，剪切cut，稍后看代码；当点击了某个编辑方法，则会回调第三个方法来执行某些操作：

{% highlight Objective-C %}
// These methods provide support for copy/paste actions on cells.
// All three should be implemented if any are.
- (BOOL)collectionView:(UICollectionView *)collectionView shouldShowMenuForItemAtIndexPath:(NSIndexPath *)indexPath;
- (BOOL)collectionView:(UICollectionView *)collectionView canPerformAction:(SEL)action forItemAtIndexPath:(NSIndexPath *)indexPath withSender:(nullable id)sender;
- (void)collectionView:(UICollectionView *)collectionView performAction:(SEL)action forItemAtIndexPath:(NSIndexPath *)indexPath withSender:(nullable id)sender;
{% endhighlight %}

点击效果如图，因为这里对第三个方法还没有写入任何内容，所以点击编辑方法时还没有任何效果:

![](http://7xpo5x.com1.z0.glb.clouddn.com/UICollectionView12.png)


###[代码在这里](https://github.com/Leon-Kang/UICollectionView)

