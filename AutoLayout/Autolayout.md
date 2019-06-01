<h3><center>iOS Auto Layout Study Notes</center></h3>
--


> 1、What's Auto Layout

Auto Layout是由苹果公司UIKit框架提供的一个用于动态计算UIView及其子类的大小和位置的库。

说到Auto Layout就不得不说Cassowary算法，因为Auto Layout是构建在Cassowary算法的基础之上的。1997年，Auto Layout用到的布局算法论文发表，被称为高效的线性方程求解算法。

2011年苹果利用Cassowary算法为开发者提供了Auto Layout自动布局库中。由于Cassowary算法的本身的优秀，不仅是苹果公司，许多开发者将其运用到各个不同的开发语言中，如JavaScript、ASP.NET、Java、C++等都有运用Cassowary算法的库。从这里也可以看出Cassowary算法自身的优秀和先进性，不然不会被运用的如此广泛。

苹果公司在iOS 6系统时引入了Auto Layout，但是直到现在已经更新到iOS 12了，还有很多开发者还是不愿使用Auto Layout。主要是对其反人类的语法以及对其性能问题的担忧。

针对Auto Layout的一些问题，在iOS9发布时，苹果推出了更简单语法的[NSLayoutAnchor](https://developer.apple.com/documentation/uikit/nslayoutanchor)。同时发布了模仿前端[Flexbox](https://www.runoob.com/w3cnote/flex-grammar.html)布局思路的[UIStackView](https://developer.apple.com/documentation/uikit/uistackview)，以此为开发者在自动布局上提供更好的选择。

在苹果[WWDC 2018 High Performance Auto Layout](https://developer.apple.com/videos/play/wwdc2018/220)中苹果工程师说: iOS 12将大幅度提升Auto Layout性能，使滑动屏幕时达到满帧。
在[WWDC 2018 What's New in Cocoa Touch](https://developer.apple.com/videos/play/wwdc2018/202/)苹果的工程师说了iOS 12对Auto Layout优化后的表现。

![AutoLayoutdemo4](/Users/mac001/Desktop/Study学习资料/iOSNotes/AutoLayout/AutoLayoutdemo4.png)

从图上可以看出，iOS 11中视图嵌套的数量的性能快成指数级别增长了，在iOS 12中已经基本和手写frame布局的性能类似了。

从iOS 6到iOS 12，苹果也在不断的优化Auto Layout的性能，同时为开发者提供更简洁的API，如果你还在使用frame手写布局，不妨试试Auto Layout。下面我将介绍Auto Layout的几种不同用法。



> 2、Auto Layout的用法


如我要设置一个宽高为120,居中显示的View，效果如下图：

![AutoLayoutdemo3](/Users/mac001/Desktop/Study学习资料/iOSNotes/AutoLayout/AutoLayoutdemo3.png)


###### 1、用frame手写布局


```
 UIView *centerView = [[UIView alloc] init];
 centerView.backgroundColor = [UIColor redColor];
 [self.view addSubview:centerView];
 CGFloat width = self.view.frame.size.width;
 CGFloat height = self.view.frame.size.height;
 [centerView setFrame:CGRectMake(width / 2 - (60), height / 2 - (60), 120, 120)];

```

###### 2、NSLayoutConstraint语法添加约束

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

###### 3、使用VFL语法

```
 centerView.translatesAutoresizingMaskIntoConstraints = NO;
    [self.view addConstraints:[NSLayoutConstraint constraintsWithVisualFormat:@"V:[centerView(120)]" options:0 metrics:nil views:views]];
    [self.view addConstraints:[NSLayoutConstraint constraintsWithVisualFormat:@"[centerView(120)]" options:0 metrics:nil views:views]];
    [self.view addConstraint:[NSLayoutConstraint constraintWithItem:centerView attribute:NSLayoutAttributeCenterY relatedBy:NSLayoutRelationEqual toItem:self.view attribute:NSLayoutAttributeCenterY multiplier:1 constant:0]];
    [self.view addConstraint:[NSLayoutConstraint constraintWithItem:centerView attribute:NSLayoutAttributeCenterX relatedBy:NSLayoutRelationEqual toItem:self.view attribute:NSLayoutAttributeCenterX multiplier:1 constant:0]];
```

###### 4、使用第三方开源框架[Masonry](https://github.com/SnapKit/Masonry)或[SnapKit](https://github.com/SnapKit/SnapKit)接口


```
 __weak typeof (self) weakSelf = self;
 [centerView mas_makeConstraints:^(MASConstraintMaker *make) {
     make.size.mas_equalTo(CGSizeMake(120, 120));
     make.center.equalTo(weakSelf.view);
 }];

```

```
 let centerView:UIView = UIView.init()
 view.addSubview(centerView)
 centerView.backgroundColor = UIColor.red
 centerView.snp.makeConstraints { (make) in
    make.width.equalTo(120)
    make.height.equalTo(120)
    make.center.equalTo(view)
 }
```

###### 5、使用iOS 9之后Apple提供的[NSLayoutAnchor](https://developer.apple.com/documentation/uikit/nslayoutanchor)

```
 let centerView:UIView = UIView.init()
 view.addSubview(centerView)
 centerView.backgroundColor = UIColor.red
 centerView.translatesAutoresizingMaskIntoConstraints = false
 centerView.centerXAnchor.constraint(equalTo: view.centerXAnchor, constant: 0).isActive = true
 centerView.centerYAnchor.constraint(equalTo: view.centerYAnchor, constant: 0).isActive = true
 centerView.widthAnchor.constraint(equalToConstant: 120).isActive = true
 centerView.heightAnchor.constraint(equalToConstant: 120).isActive = true
```
通过上面的代码对比，使用frame手写布局只要几行代码就搞定了，使用NSLayoutConstraint语法和VFL语法是最复杂的，尤其是NSLayoutConstraint语法要用30多行代码才能是想同样的效果，代码行数越多出错的概率也就成正比上升，所以这就是很多开发者不愿使用Auto Layout（或者说不愿意使用系统提供API来实现）的原因之一吧。

 如果你的App要兼容iOS 9以下的各个版本，建议使用[Masonry](https://github.com/SnapKit/Masonry),如果只兼容iOS 9以上的版本，建议使用[SnapKit](https://github.com/SnapKit/SnapKit)或者系统提供的[NSLayoutAnchor](https://developer.apple.com/documentation/uikit/nslayoutconstraint)API，毕竟Masonry这个库已经2年没有更新了。

在这里我推荐优先使用NSLayoutAnchor，第三方的开源库随时都面临这一些问题：

*  iOS 系统版本的更新造成的适配和兼容问题，如果是开源代码要等到苹果发布新版本，代码的作者再做兼容和适配
*  代码的作者停止更新这些代码了，这对我们开发者来说就很被动了，我们要么自己修改这些代码，要么选择更新的开源代码
*  使用系统库可在打包时可以减少包大小


> 3、Auto Layout的生命周期

前面说到苹果的Auto Layout是基于Cassowary算法的，苹果在此基础上提供了一套Layout Engine引擎，由它来管理页面的布局，来完成创建、更新、销毁等。

在APP启动后，会开启一个常驻线程来监听约束变化，当约束发生变化后会出发Deffered Layout Pass(延迟布局传递)，在里面做容错处理（如有些视图在更新约束时没有确定或缺失布局申明），完成后进入约束监听变化的状态。

当下一次刷新视图（如调用layoutIfNeeded()）时，Layout Engine会从上到下调用layoutSubviews()，然后通过Cassowary算法计算各个子视图的大小和位置，算出来后将子视图的frame从layout Engine里拷贝出来，在之后的处理就和手写frame的绘制、渲染的过程一样了。使用Auto Layout和手写frame多的工作就在布局计算上。

> 4、NSLayoutAnchor常用API



更多使用方法请查阅[Apple Developer NSLayoutAnchor](https://developer.apple.com/documentation/uikit/nslayoutanchor#//apple_ref/occ/instm/NSLayoutAnchor/constraintEqualToAnchor:constant:)

> 5、Auto Layout关于更新的几个方法的区别

* [setNeedsLayout()](https://developer.apple.com/documentation/uikit/uiview/1622601-setneedslayout): 告知页面需要更新，但是不会立刻开始更新。执行后会立刻调用layoutSubviews。

* [layoutIfNeeded()](https://developer.apple.com/documentation/uikit/uiview/1622507-layoutifneeded): 告知页面布局立刻更新。所以一般都会和setNeedsLayout一起使用。如果希望立刻生成新的frame需要调用此方法，利用这点一般布局动画可以在更新布局后直接使用这个方法让动画生效。

* [layoutSubviews()](https://developer.apple.com/documentation/uikit/uiview/1622482-layoutsubviews): 更新子View约束

*  [setNeedsUpdateConstraints()](https://developer.apple.com/documentation/uikit/uiview/1622450-setneedsupdateconstraints):需要更新约束，但是不会立刻开始

*  [updateConstraintsIfNeeded()](https://developer.apple.com/documentation/uikit/uiview/1622595-updateconstraintsifneeded):立刻更新约束

*  [updateConstraints()](https://developer.apple.com/documentation/uikit/uiview/1622512-updateconstraints):更新View约束

> 6、NSLayoutAnchor使用注意事项

* 1、在使用NSLayoutAnchor为视图添加约束时一定要先把`translatesAutoresizingMaskIntoConstraints`设置`false`

```
 centerView.translatesAutoresizingMaskIntoConstraints = false

```

* 2、在使用safeAreaLayoutGuide适配iPhone X 等机型时要对iOS 11之前的系统做兼容

```
if #available(iOS 11.0, *) {
     tableView.topAnchor.constraint(equalTo: self.view.safeAreaLayoutGuide.topAnchor, constant: 0).isActive = true
 } else {
     tableView.topAnchor.constraint(equalTo: self.view.topAnchor, constant: 0).isActive = true
 }
```

* 3、设置约束后要将其激活，即设置`isActive`为true

```
let centerX: NSLayoutConstraint = centerView.centerXAnchor.constraint(equalTo: view.centerXAnchor, constant: 0)
centerX.isActive = true
```

* 4、如何刷新某个约束

如我要修改一个view的宽度：
通过代码添加约束，可把view的宽度设置类属性，然后在需要的地方修改constant的参数，然后在刷新约束即可，代码如下：

```
 var centerView: UIView! = nil
 var centerWidth: NSLayoutConstraint! = nil

```

```

self.centerView = UIView.init()
view.addSubview(self.centerView)
self.centerView.backgroundColor = UIColor.red
self.centerView.translatesAutoresizingMaskIntoConstraints = false
self.centerView.centerXAnchor.constraint(equalTo: view.centerXAnchor, constant: 0).isActive = true
self.centerView.centerXAnchor.constraint(equalTo: view.centerXAnchor, constant: 0).isActive = true
self.centerView.centerYAnchor.constraint(equalTo: view.centerYAnchor, constant: 0).isActive = true
self.centerWidth = self.centerView.widthAnchor.constraint(equalToConstant: 120)
self.centerWidth.isActive = true
self.centerView.heightAnchor.constraint(equalToConstant: 120).isActive = true
```

```
self.centerWidth.constant = 250
weak var weakSelf = self
UIView.animate(withDuration: 0.35, animations: {
   weakSelf?.centerView.superview?.layoutIfNeeded()
}) { (finished) in
            
}
```
效果如下：
![layoutDemo5.gif](/Users/mac001/Desktop/Study学习资料/iOSNotes/AutoLayout/layoutDemo5.gif)

> 7、Auto Layout自适应UITableViewCell高度使用

![Autolayoutdemo1](/Users/mac001/Desktop/Study学习资料/iOSNotes/AutoLayout/Autolayoutdemo1.png)
![Autolayoutdemo2](/Users/mac001/Desktop/Study学习资料/iOSNotes/AutoLayout/Autolayoutdemo2.png)

如上图所示：这两个界面都是用Auto Layout + 自适应高度完成，下面我将一步步剖析如何利用 Auto Layout 和 estimatedRowHeight来完成一个简单的UITableView界面。

本demo中的图片加载使用了猫神写的框架[Kingfisher]()


> 8、 Compression Resistance Priority 和 Hugging Priority使用

 Compression Resistance Priority 和 Hugging Priority

> 9、小结



如果UI比较简单或者单一的强烈建议使用`AutoLayout`，如果UI比较复杂，建议`frame`+手动计算高度的方法。

--
[本文demo](https://github.com/dengfeng520/One-Swift)

参考文章：

[深入剖析Auto Layout，分析iOS各版本新增特性](https://ming1016.github.io/2015/11/03/deeply-analyse-autolayout/#more)

[Auto Layout 是怎么进行自动布局的，性能如何？](https://time.geekbang.org/column/article/85332)

[Apple Developer High Performance Auto Layout](https://developer.apple.com/videos/play/wwdc2018/220)

[WWDC 2018 What's New in Cocoa Touch](https://developer.apple.com/videos/play/wwdc2018/202/)

[Apple Developer NSLayoutanchor](https://developer.apple.com/documentation/uikit/nslayoutanchor)