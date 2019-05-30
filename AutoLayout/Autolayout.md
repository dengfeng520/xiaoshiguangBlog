<h3><center>iOS Auto Layout Study Notes</center></h3>
--


> 1、What's Auto Layout

Auto Layout是由苹果公司UIKit框架提供的一个用于动态计算UIView及其子类的大小和位置的库。Apple 在iOS 6就引入了Auto Layout这种自动布局的方式，但是个人觉得Apple提供的API真的容易让人掉头发。下面举例说明：
###### 1、iOS 6提供的NSLayoutConstraint语法添加约束

如我要设置一个宽高为120,居中显示的View，效果如下图：

![AutoLayoutdemo3](/Users/mac001/Desktop/Study学习资料/iOSNotes/AutoLayout/AutoLayoutdemo3.png)

用frame手写代码如下：

```
    UIView *centerView = [[UIView alloc] init];
    centerView.backgroundColor = [UIColor redColor];
    [self.view addSubview:centerView];
    CGFloat width = self.view.frame.size.width;
    CGFloat height = self.view.frame.size.height;
    [centerView setFrame:CGRectMake(width / 2 - (60), height / 2 - (60), 120, 120)];

```
只要几行代码就搞定了，如果使用NSLayoutConstraint语法呢？

```
   centerView.translatesAutoresizingMaskIntoConstraints = NO;
    NSLayoutConstraint *consW = [NSLayoutConstraint constraintWithItem:centerView
                                                             attribute:NSLayoutAttributeWidth
                                                             relatedBy:NSLayoutRelationEqual
                                                                toItem:self.view
                                                             attribute:NSLayoutAttributeWidth
                                                            multiplier:0
                                                              constant:120.0
                                 ];
    NSLayoutConstraint *consH = [NSLayoutConstraint constraintWithItem:centerView
                                                             attribute:NSLayoutAttributeHeight
                                                             relatedBy:NSLayoutRelationEqual
                                                                toItem:self.view attribute:NSLayoutAttributeHeight
                                                            multiplier:0
                                                              constant:120.0
                                 ];
    NSLayoutConstraint *consX = [NSLayoutConstraint constraintWithItem:centerView
                                                             attribute:NSLayoutAttributeCenterX
                                                             relatedBy:NSLayoutRelationEqual
                                                                toItem:self.view
                                                             attribute:NSLayoutAttributeCenterX
                                                            multiplier:1.0
                                                              constant:0.0
                                 ];
    NSLayoutConstraint *consY = [NSLayoutConstraint constraintWithItem:centerView
                                                             attribute:NSLayoutAttributeCenterY
                                                             relatedBy:NSLayoutRelationEqual
                                                                toItem:self.view
                                                             attribute:NSLayoutAttributeCenterY
                                                            multiplier:1.0
                                                              constant:0.0
                                 ];
    [self.view addConstraints:@[consW,consH,consX,consY]];

```
要用30多行代码才能是想同样的效果，实话说如果实现一个简单的效果就要30多行代码，那么就算我能熟练的使用这些API实现了效果呢，那么性能上和手写frame的性能相比如何呢？
下面演示用VFL语法来实现，

```
 centerView.translatesAutoresizingMaskIntoConstraints = NO;
    [self.view addConstraints:[NSLayoutConstraint constraintsWithVisualFormat:@"V:[centerView(160)]" options:0 metrics:nil views:views]];
    [self.view addConstraints:[NSLayoutConstraint constraintsWithVisualFormat:@"[centerView(160)]" options:0 metrics:nil views:views]];
    [self.view addConstraint:[NSLayoutConstraint constraintWithItem:centerView attribute:NSLayoutAttributeCenterY relatedBy:NSLayoutRelationEqual toItem:self.view attribute:NSLayoutAttributeCenterY multiplier:1 constant:0]];
    [self.view addConstraint:[NSLayoutConstraint constraintWithItem:centerView attribute:NSLayoutAttributeCenterX relatedBy:NSLayoutRelationEqual toItem:self.view attribute:NSLayoutAttributeCenterX multiplier:1 constant:0]];
```



> 2、Auto Layout的生命周期

前面说到苹果的Auto Layout是基于Cassowary算法的，苹果在此基础上提供了一套Layout Engine引擎，由它来管理页面的布局，来完成创建、更新、销毁等。

在APP启动后，会开启一个常驻线程来监听约束变化，当约束发生变化后会出发Deffered Layout Pass(延迟布局传递)，在里面做容错处理（如有些视图在更新约束时没有确定或缺失布局申明），完成后进入约束监听变化的状态。

当下一次刷新视图（如调用layoutIfNeeded()）时，Layout Engine会从上到下调用layoutSubviews()，然后通过Cassowary算法计算各个子视图的大小和位置，算出来后将子视图的frame从layout Engine里拷贝出来，在之后的处理就和手写frame的绘制、渲染的过程一样了。使用Auto Layout和手写frame多的工作就在布局计算上。

> 3、Auto Layout的性能如何



> 4、Auto Layout简单使用


如果你的App要兼容iOS 9以下的各个版本，建议使用[Masonry](https://github.com/SnapKit/Masonry),如果只兼容iOS 9以上的版本，建议使用[SnapKit](https://github.com/SnapKit/SnapKit)或者系统提供的[NSLayoutConstraint](https://developer.apple.com/documentation/uikit/nslayoutconstraint)，毕竟Masonry这个库已经2年没有更新了。



> 5、Auto Layout关于更新的几个方法的区别

* [setNeedsLayout()](https://developer.apple.com/documentation/uikit/uiview/1622601-setneedslayout): 告知页面需要更新，但是不会立刻开始更新。执行后会立刻调用layoutSubviews。

* [layoutIfNeeded()](https://developer.apple.com/documentation/uikit/uiview/1622507-layoutifneeded): 告知页面布局立刻更新。所以一般都会和setNeedsLayout一起使用。如果希望立刻生成新的frame需要调用此方法，利用这点一般布局动画可以在更新布局后直接使用这个方法让动画生效。

* [layoutSubviews()](https://developer.apple.com/documentation/uikit/uiview/1622482-layoutsubviews): 更新子View约束

*  [setNeedsUpdateConstraints()](https://developer.apple.com/documentation/uikit/uiview/1622450-setneedsupdateconstraints):需要更新约束，但是不会立刻开始

*  [updateConstraintsIfNeeded()](https://developer.apple.com/documentation/uikit/uiview/1622595-updateconstraintsifneeded):立刻更新约束

*  [updateConstraints()](https://developer.apple.com/documentation/uikit/uiview/1622512-updateconstraints):更新View约束

> 6、AutoLayout自适应UITableViewCell高度使用

![Autolayoutdemo1](/Users/mac001/Desktop/Study学习资料/iOSNotes/AutoLayout/Autolayoutdemo1.png)
![Autolayoutdemo2](/Users/mac001/Desktop/Study学习资料/iOSNotes/AutoLayout/Autolayoutdemo2.png)

如上图所示：这两个界面都是用Auto Layout + 自适应高度完成，下面我将一步步剖析如何利用 Auto Layout 和 estimatedRowHeight来完成一个简单的UITableView界面。

> 7、总结

如果UI比较简单或者单一的强烈建议使用`AutoLayout`，如果UI比较复杂，建议`frame`+手动计算高度的方法。

--
[本文demo](https://github.com/dengfeng520/One-Swift)

参考文章：

[深入剖析Auto Layout，分析iOS各版本新增特性](https://ming1016.github.io/2015/11/03/deeply-analyse-autolayout/#more)

[Auto Layout 是怎么进行自动布局的，性能如何？](https://time.geekbang.org/column/article/85332)

[Apple Developer High Performance Auto Layout](https://developer.apple.com/videos/play/wwdc2018/220)

[Apple Develope NSLayoutConstraint](https://developer.apple.com/documentation/uikit/nslayoutconstraint)