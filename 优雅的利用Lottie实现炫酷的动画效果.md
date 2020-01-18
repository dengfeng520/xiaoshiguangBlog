
<h1><center>优雅的利用Lottie实现炫酷的动画效果</center></h1>
<h6 align='right'>小时光</h6>

--


&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;`APP`中的交互动画对整个`APP`来说是十分重要的。对用户来说，使用一款`APP`可能并不是这个`APP`界面做的绚丽，动画做的美之类的原因。纵然如此，一款`APP`只有动画优美，界面上做好了，才会吸引到用户，用户才会来使用这个`APP`。

 >1、为什么用`Lottie`

&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;对于一些简单的动画我们开发者可以使用系统提供的一些方法来实现，但是在实际开发中设计师给出的动画都是很炫酷复杂的，如果采用手写代码的方式，就要面对很对问题：

* （1）、`Android`和 `iOS`要针对系统来编写代码动画
* （2）、同时由于不同系统之间的差异，比如`Android`能够实现的动画`iOS`不能实现，即使实现也要花费得不偿失的时间，这就造成在开发中一些动画被去掉或者简单化，最终还是没有达到设计师满意的效果
* （3）、即使开发者通过代码实现了设计师的效果，可能会和设计师给出的效果有一些出入，这样就造成了沟通和修改的成本
* （4）、 越是复杂的代码，编写的代码就会越多这样势必会对后期的维护造成很大的成本
* （5）、 考虑到不断的版本迭代中需求的变更，修改代码也会有很大的成本

所以综上所述的一些问题，此处给诸位推荐一个第三方动画库[Lottie](http://airbnb.io/lottie/)。

&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;下面几个gif动画都是用[Lottie](http://airbnb.io/lottie/)实现的。

![LaunchScreen.gif](https://github.com/dengfeng520/xiaoshiguangBlog/blob/master/Gif/LaunchScreen.gif?raw=true)

![refreshAnimationView.gif](https://github.com/dengfeng520/xiaoshiguangBlog/blob/master/Gif/refreshAnimationView.gif?raw=true)

![background.gif](https://upload-images.jianshu.io/upload_images/1214383-a014d4394034a502.gif?imageMogr2/auto-orient/strip)


* （1）、`Lottie`是专门为移动开发设计的一个第三方库，目前支持`Android、iOS  、Web、React Native`等平台；
* （2）、Lottie同时支持页面切换的过场动画(UIViewController Transitions)
* （3）、`Lottie`的使用非常简单，直接读取资源文件或者读取服务器资源链接即可；
* （4）、开发者可以轻松控制动画（播放进度、播放帧数、背景颜色等）；
* （5）、`Lottie`是设计师通过`After Effects`将动画导出JSON文件，然后由`Lottie`加载和渲染这个文件并转成相应的代码，由于是JSON文件，文件也会很小，可以减少App包的大小
* （6）、把动画制作和APP开发的工作分开，由设计师来完成动画的制作

>2、Lottie 的实现原理

&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;实际上，`Lottie`在iOS上的实现是：

**  将`After Effects`编辑的动画内容，通过JSON作为媒介一一映射到iOS的 `LayerModel`、`Keyframe`、`ShapeItem`、`DashElement`、`Marker`、`Mask`、`Transfrom`这些类的属性中并保存了下来，然后 再通过`CoreAnimation`进行渲染。**


>3、Lottie 的简单使用

######1、 导入工程

&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;`Lottie`的使用非常简单，只要集成了`Lottie`框架，然后在程序中通过`Loattie`的接口控制设计通过AE生成的JSON就行了。
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;官方推荐`iOS`开发者使用`Pod`来管理,其他平台开发者可参考[Lottie官网](http://airbnb.io/lottie/)给出的方案。
在`Podfile`文件中导入：

```
pod 'lottie-ios'
```
`cd`到工程目录下执行安装命令即可：

```
pod install
```

######2、 初始化一个动画
在需要的`Lottie`动画的类引入头文件，

```
import Lottie
```
```
// 加载本地资源
 let path : String = Bundle.main.path(forResource: "Refresh", ofType: "json")!
 let lottieAnimationView = AnimationView.init(filePath: path)
 let lottieAnimationView = AnimationView.init(name: "Refresh")
// load from URL
 let animationView = LOTAnimationView(contentsOf: WebURL)
 let lottieAnimationView = AnimationView.init(url: WebURL, closure:{
           down in
  })
```
######3、动画的播放和控制
运行代码，此时动画只停在第一帧，因为并没有让动画执行，执行播放：

```
 lottieAnimationView.play()
```
如果在动画执行完 还有其他的逻辑，可使用:

```
 lottieAnimationView.play { (animationFinished) in
    print("其他相关业务");
 }
```
如果需要这个动画循环播放：

```
lottieAnimationView.loopMode = .loop
```
同时可以控制帧数和播放进度来实现顺序播放或者逆序播放：

```
// 播放进度
lottieAnimationView.play(fromProgress: 0.25, toProgress: 0.5, loopMode: .loop) { (animationFinished) in
            
 }
// 播放帧数
lottieAnimationView.play(fromFrame: 0, toFrame: 5, loopMode: .loop) { (animationFinished) in

}
```
如果需要逆序播放，只需将帧数或进度的顺序调整即可，如顺序是0 --> 5帧，逆序则是5 --> 0:

```
lottieAnimationView.play(fromProgress: 0.5, toProgress: 0.25, loopMode: .loop) { (animationFinished) in
            
 }

lottieAnimationView.play(fromFrame: 5, toFrame: 0, loopMode: .loop) { (animationFinished) in

}
```

在使用`Lottie`动画时，由于程序业务关系，我们需要在这段动画没有执行完之前就执行下一段动画，此时可直接从某一帧执行：

```
 lottieAnimationView.play(fromFrame: 20, toFrame: 50, loopMode: .loop) { (animationFinished) in
            loadingAnimationView.play()
        }
```

如果要停止动画并保持当前帧数，可使用`pause()`,
如果要停止动画并回到初始帧，可使用`top()`

```
lottieAnimationView.pause()
lottieAnimationView.stop()
```

更多使用，如修改关键帧数的参数颜色等可参考[Lottie官网](http://airbnb.io/lottie/#/ios?id=ios-sample-app)


>4、总结

对于程序中比较复杂的动画用`iOS`系统为开发者提供的`API`无法实现的，建议使用`Lottie`动画，对于一些简单的动画，建议使用系统提供的`API`来实现，可以参考[iOS核心动画技巧](https://zsisme.gitbooks.io/ios-/content/index.html)。

以上是我在使用`Lottie`时的一些总结，更多使用请参考[Github](http://airbnb.io/lottie/ios.html#swift-examples)。

---

[本文demo](https://github.com/dengfeng520/One-Swift.git)

[Lottie官方demo](https://github.com/airbnb/lottie-ios)

[友情链接:SVGA](http://svga.io/)
