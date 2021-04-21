---
	
title: Swift Timer循环引用问题

date: 2021-04-10 16:27:56

cover: /img/girl034.webp

---

<h6 align='right'>小时光</h6>

<h6 align='right'><a href='https://dengfeng520.github.io/'>我的博客</a></h6>

### 1、Timer产生循环引用的原因

iOS 10之前使用`Timer`会因为循环引用造成持有`Timer`的`Controller`释放不掉，从而导致内存泄漏。iOS 10之后系统优化了这个问题。一般在iOS 10之前使用`Timer`的代码如下：

```Swift
var time: Timer?

override func viewDidLoad() {
     super.viewDidLoad()
     time = Timer.scheduledTimer(timeInterval: 2, target: self, selector: #selector(timePrint), userInfo: nil, repeats: true)
}
func timePrint() {
    // do something...
}

deinit {
    print("deinit---------------------11111")
    time?.invalidate()
    time = nil
}
```

当我在`Controller`中使用了`Timer`之后，这个`Controller`被`pop`或`dismiss`之后，其内存并不会释放，可以看到计时器也在正常运行，那么这是由于什么原因造成的呢？

##### 1.1、不仅仅是强引用问题

一般情况下，两个实例对象之前相互强引用会造成循环引用，那么按照理解，`Timer`和`Controller`之间的引用关系可能是这样的：

![Timer001.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a983c288796b424c823c43418576d670~tplv-k3u1fbpfcp-watermark.image)

针对这种两者之间的强引用造成的循环引用，只要让其中一个为弱引用就可以解决问题，那么就来试试吧。

修改`time`为弱引用

```Swift
weak var time: Timer?
```

再次运行代码，这时理想中他们之间的引用关系如下图所示，当`Controller`被释放时，因为是弱引用的关系此时`Timer`的内存也会被释放：

![Timer002.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/5c7b6440fd314eca82c15ad3799094ca~tplv-k3u1fbpfcp-watermark.image)

再次运行代码，发现并没有如我所愿，当`Controller`被释放`Timer`依旧能够正常运行，所以他们的内存还是没有有效释放。为什么我使用了弱引用其内存还是没有释放掉呢？

##### 1.2、`Timer`和`RunLoop`之间的强引用

这里忽略了一个问题，`Timer`和`RunLoop`之间的关系，当`Timer`在创建之后会被当前线程的`RunLoop`进行一个强引用，如果这个对象是在主线程中创建的，那么就由主线程持有`Timer`。当我使用了弱引用后他们之间的引用关系是：

![Timer003.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7ede08b64090422288f15cfcea8b0608~tplv-k3u1fbpfcp-watermark.image)

虽然使用了弱引用，但是由于主线程中的`RunLoop`是常驻内存同时对`Timer`的强引用，`Timer`同时又对`Controller`强引用，那么此时即使这个`Controller`被`pop`或`dismiss`,此时这个`Controller`间接的被`RunLoop`间接的强引用。即使这个`Controller`被`pop`或`dismiss`，因为强引用的关系这部分内存也不能正常释放，这就会造成内存泄漏，并且可能会造成整个App Crash。当`Controller`被`pop`或`dismiss`时，他们在内存中的引用关系是：

![Timer004.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/fe258ad368904e12b35a112ea0e9a9c0~tplv-k3u1fbpfcp-watermark.image)

### 2、How to solve it ?

知道了造成`Timer`造成循环引用的原因，那么该如何解决`Timer`造成的循环引用问题呢？

##### 2.1、使用系统提供Block方法

iOS 10之后，系统已经优化了这个问题，如果是iOS 10之后的版本，完全可以使用系统提供的`Block`回调方式：

```Swift
if #available(iOS 10.0, *) {
      /// iOS 10之后采用`Block`方式解决Timer 循环引用问题
      time = Timer.scheduledTimer(withTimeInterval: 2, repeats: true, block: { [weak self] (timer) in
            guard let `self` = self else { return }
            self.timePrint()
      })
}
```

事实上现在开发一款新的App时可以不考虑iOS 10以下的兼容处理，因为苹果官方统计的数据：[Apple Developer: iOS and iPadOS Usage](https://developer.apple.com/support/app-store/)。截止2021年4月10号，只有**8%**的iPhone用户还在使用`iOS 13`以下的版本，就连微信这种达亿级用户的`App`都只支持iOS 11以后版本。当然对于一些比较老的App要支持iOS 10之前的系统或者你有一个爱抬杠的产品经理，那么只能做老系统的兼容处理。

##### 2.2、使用GCD提供的`DispatchSource`替换`Timer`

```Swift
var source: DispatchSourceTimer?

source = DispatchSource.makeTimerSource(flags: [], queue: .global())
source.schedule(deadline: .now(), repeating: 2)
source.setEventHandler {
    // do something...
}
source.resume()

deinit {
     source?.cancel()
     source = nil
}
```

##### 2.3、模仿系统提供的`closure`

对系统的`Timer`做扩展处理：

```Swift
extension Timer {
    class func rp_scheduledTimer(timeInterval ti: TimeInterval, repeats yesOrNo: Bool, closure: @escaping (Timer) -> Void) -> Timer {
        return self.scheduledTimer(timeInterval: ti, target: self, selector: #selector(RP_TimerHandle(timer:)), userInfo: closure, repeats: yesOrNo)
}
    
    @objc class func RP_TimerHandle(timer: Timer) {
        var handleClosure = { }
        handleClosure = timer.userInfo as! () -> ()
        handleClosure()
    }
}
```

调用方法：

```Swift
if #available(iOS 10.0, *) {
      /// iOS 10之后采用`Block`方式解决Timer 循环引用问题
      time = Timer.scheduledTimer(withTimeInterval: 2, repeats: true, block: { [weak self] (timer) in
            guard let `self` = self else { return }
            self.timePrint()
      })
} else {
      /// iOS 10之前的解决方案： 模仿系统的`closure` 解决Timer循环引用问题
      time = Timer.rp_scheduledTimer(timeInterval: 2, repeats: true, closure: { [weak self] (timer) in
            guard let `self` = self else { return }
            self.timePrint()
      })
}
        
func timePrint() {
     // de something...
}
    
deinit {
    time?.invalidate()
    time = nil 
}
```

##### 2.4、其他解决方法

* 使用`Runtime`给对象添加消息处理的方法
* 使用`NSProxy`类作为中间对象

本文主要分析了在开发中使用`Timer`造成循环引用的原因和一些常用的解决方案。