<h3><center>iOS 多线程概览</center></h3>
<h6 align='right'>小时光</h6>



> <h3>1、为什么所有的UI操作都在主线程中</h3>


不仅是iOS系统，包括Android等，所有的UI渲染、操作都在主线程中来完成。那为什么不采用多线程的方式呢？
使用多线程渲染UI更快，操作更流畅。但是系统设计者和开发者来说，需要解决线程问题的成本就更高了，也就是说成本远大于收益了。所以工程师们把所有的UI渲染和操作全都放在了主线程中。参考[为什么必须在主线程操作UI](https://juejin.cn/post/6844903763011076110)。


> <h3>2、为什么要使用多线程</h3>

即然所有的UI操作都是单线程的，那么为何还需要多线程呢？在App开发中，所遇到不仅有UI操作，还有一些其他的费时操作，如网络请求、文件读取操作、AR模型下载等。此时就需要开发者把费时的操作放到子线程中去，完成后再返回住线程执行一些UI操作。
![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/351466fe66794aff8ca268aae1cc9fdc~tplv-k3u1fbpfcp-watermark.image)
如上图所示，当进入这个Controller时开始下载图片，由于下载的图片较多需要几秒甚至十几秒，在下载时我同步滑动底部的`UISlider`,此时`UISlider`并没有滑动，直到所有的图片全部下载完成后才滑动。

试想如果我们的使用的每个App在费时操作时都需要等待很长时间用户才能操作，这样势必会给用户很不好的体验。此时的解决方案是创建一个新的线程来下载图片，这样既不影响用户的UI操作也不影响图片下载。


> <h3>3、多线程的实现方式</h3>

 Apple为开发者提供的三种多线程实现方式:

(1)、 Thread

   * 轻量级
   * 需要开发者手动管理线程生命周期和线程同步

(2)、 GCD（Grand Central Dispatch）

  * 相对`Thread`而言，不需要管理线程生命周期，操作更简单
  * 本身维护了一个线程池，会自动根据当前手机系统的情况来动态管理线程，不需要开发者来管理线程池和线程并发情况
  * 底层源码是开源的，点击[Apple Open Spurce](https://opensource.apple.com/tarballs/libdispatch/)查看源码

(3)、 Cocoa Operation

  * 面向对象的API
  * 可以取消、依赖、任务优先级、可以子类化

> <h3>4、多线程常用队列</h3>

多线程可以根据任务执行的队列方式分为三种队列：

* 主队列: 在主线程中执行的任务
* 串行队列（Serial Queue）: 任务按照先后顺序执行，同一时刻只会执行一个任务
* 并行队列（Concurrent Queue）: 多个任务同时执行，完成的顺序不一定

参考 [Apple Developer About Dispatch Queues](https://developer.apple.com/library/archive/documentation/General/Conceptual/ConcurrencyProgrammingGuide/OperationQueues/OperationQueues.html#//apple_ref/doc/uid/TP40008091-CH102-SW2)

> <h3>5、Serial Queue</h3>

* （1）、串行队列处理并发任务

前面说过，对于一些耗时操作，一般将其放到一个子线程中执行，待完成后再返回主线程中刷新UI。核心代码如下：

```
// 图片下载管理类`DownloaderManager`
public class DownloaderManager: NSObject {
    public class func downloadImageWithURL(_ url: String) -> UIImage? {
        guard let data = try? Data(contentsOf: URL(string: url)!) else { return nil }
        return UIImage(data: data)
    }
}

// 创建一个队列
let serialQueue = DispatchQueue(label: "TSN.RPChat.io")
for (index, imgurl) in DownloaderManager.imageArray.enumerated() {
   // 把下载任务加载到队列中
   serialQueue.async { 
      let image = DownloaderManager.downloadImageWithURL(imgurl)
      DispatchQueue.main.async {
         girlsImg.image = image
      }
   }
}
```

此时再次运行代码，发现在下载图片的同时也能滑动`UISlider`，同时发现图片的加载顺序是按照从上到下的顺序加载的。默认情况下，系统会创建一个串行队列，也就是下载完成第一张图片之后再去下载第二张。此时对我来说，我的目的是下载并把图片全部显示出来，我并不关心图片的下载和加载顺序。这样我就需要在创建队列时设置这个队列为一个并行队列。

![串行队列](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/0ff481b519e44cf1ab3c1442c628bbbd~tplv-k3u1fbpfcp-watermark.image)

> <h3>6、Concurrent Queue</h3>

作为队列，`concurrent queue`中的任务虽然是按照进入队列的顺序启动，但不用等待之前的任务完成，iOS会根据当前系统情况启动多个线程并行执行队列中的任务。

在创建队列时设置`attributes`属性为`concurrent`就创建了一个并行队列。

```
let concurrentQueue = DispatchQueue(label: "TSN.RPChat.io", attributes: .concurrent)
```

此时运行工程，可以看到图片并不是按照先后顺序加载的，说明同一个`concurrent queue`中的所有任务在并行执行。
![Concurrent Queue](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/12d73fba49ea4e6992822fc67e3279a1~tplv-k3u1fbpfcp-watermark.image)

上面创建的队列我使用的GCD（Grand Central Dispatch）方式，尽管GCD对线程管理进行了封装并加入了面向对象管理模式。但是如果我要对一个队列中的任务做更多的操作，如（查看状态、取消任务，控制任务的执行顺序等）仍然不太方便。考虑到这些问题，苹果为开发这提供了一个面向对象方式的多任务执行机制[Operation](https://developer.apple.com/documentation/foundation/operation)。

> <h3>7、面向对象的Cocoa Operation</h3>

除了`Thread`和`GCD`之外，iOS还提供了`Operation`这种多线程实现方式，`Operation`是基于`GCD`的对象封装。

(1)、**[Operation概览](https://developer.apple.com/documentation/foundation/operation)**

 Operation的一些使用状态：

  * **[isReady](https://developer.apple.com/documentation/foundation/operation/1412992-isready)**是否可执行，一般用于异步的情况下 
  * **[isExexuting](https://developer.apple.com/documentation/foundation/operation/1415621-isexecuting)**标记`Operation`是否正在执行中
  * **[isFinished](https://developer.apple.com/documentation/foundation/operation/1413540-isfinished)**标记`Operation`是否已经执行完成了，一般用于异步
  * **[isCancelled](https://developer.apple.com/documentation/foundation/operation#1661262)**标记`Operation`是否已经`cancel`了
  
  更多状态请参考[Apple Developer: Maintaining Operation Object States](https://developer.apple.com/documentation/foundation/operation#1661262)
  
 (2)、**[OperationQueue](https://developer.apple.com/documentation/foundation/operationqueue)**

 * **OperationQueue**可以加入多个`Operation`
 
 ```
 let ope1 = Operation()
 let ope2 = Operation()
        
 let que = OperationQueue()
 que.addOperation(ope1)
 que.addOperation(ope2)
 ```
 
 * `maxConcurrentOperationCount`可设置最大并发数当前,默认情况下，系统会根据当前情况动态确定最大并发数

```
que.maxConcurrentOperationCount = 5
```

此处需要注意的是最大并发数并不是线程数，最大并发数表示的是当前队列最多可同时执行的的任务（或线程）数量。
 
 * 可取消所有`Operation`，但当前正在执行的`Operation`不会取消
 * 所有的`Operation`执行完毕后退出销毁

(3)、**[BlockOperation](https://developer.apple.com/documentation/foundation/blockoperation)**

```
let queblock = BlockOperation.init(block: { [weak self] in
            
})
let que = OperationQueue.init()
que.maxConcurrentOperationCount = 3
que.addOperation(queblock)
```

(4)、**[completionBlock](https://developer.apple.com/documentation/foundation/operation/1408085-completionblock)**

当执行完一个任务时的回调.
我们可以通过创建Operation的方法，首先创建一个`Operation`对象，然后将其添加到队列中，这样做就可以通过设置`completionBlock`，在任务完成时得到通知。

(5)、默认优先级

苹果为**Operation**提供了优先级，**Operation**通过**[qualityOfService](https://developer.apple.com/documentation/foundation/operation/1413553-qualityofservice)**属性来控制其优先级,来看源码：

```
public enum QualityOfService : Int {
    
    case userInteractive = 33

    case userInitiated = 25

    case utility = 17

    case background = 9

    case `default` = -1
}
```

* **userInteractive**: 最高优先级，用于用户交互事件
* **userInitiated**:次高优先级，用于用户需要马上执行的事件
* **utility**:普通优先级，用于普通任务
* **background**:最低优先级，用于不重要的任务
* **default**:默认优先级，主线程和没有设置优先级的线程都默认为这个优先级

```
operation.qualityOfService = .default
```

通过**[queuePriority](https://developer.apple.com/documentation/foundation/operation/1411204-queuepriority)**属性来控制在`OperationQueue`中的优先级：

```
public enum QueuePriority : Int {

        case veryLow = -8

        case low = -4

        case normal = 0

        case high = 4

        case veryHigh = 8
    }
```

```
operation.queuePriority = .high
```

(6)、使用**OperationQueue**下载图片

```
// 创建一个队列
let queue = OperationQueue()

// 创建一个Operation
queue.addOperation {
et image = DownloaderManager.downloadImageWithURL(imgurl)
OperationQueue.main.addOperation {
     girlsImg.image = image 
   }
}

```

这里需要注意的是：更新UI的代码要放到主线程中完成。使用`OperationQueue.main`获取主线程队列，然后添加`addOperation`把更新UI的任务放到主线程。
如果需要在下载完成时做一些相关操作，可以使用`completionBlock`,

```
let operation = BlockOperation(block: {
     // 要执行的任务，如下载图片等
     let image = DownloaderManager.downloadImageWithURL(imgurl)
     // 下载完成后，返回主线程渲染图片
     OperationQueue.main.addOperation {
         girlsImg.image = image
     }
})
operation.completionBlock = {
     // 执行完成后
}
// 设置最大并发数 不设置时系统回根据当前情况动态设置最大并发数 设置为1时为串行队列
queue.maxConcurrentOperationCount = 5
// 将Operation添加到queue队列中
queue.addOperation(operation)
```


(7)、设置任务之间的关联性

![设置任务之间的关联性](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/fdaa9343667a42e4bf9562740fe05738~tplv-k3u1fbpfcp-watermark.image)
如图所示,当点击download按钮的时候开始下载图片，但是客户要求按照432的顺序加载，但是图片1不影响，此处需要用到`addDependency`方法，让图片按照432的顺序下载，图片1并行下载，核心代码如下：

```
let queue = OperationQueue()

let operation1 = BlockOperation(block: {
       let image = DownloaderManager.downloadImageWithURL(imgArray[0])
       OperationQueue.main.addOperation {
           self.girlsImg1.image = image
    }
})
operation1.completionBlock = {
    print("-------operation1")
 }
        
let operation2 = BlockOperation(block: {
        let image = DownloaderManager.downloadImageWithURL(imgArray[1])
        OperationQueue.main.addOperation {
           self.girlsImg2.image = image
     }
})
operation2.completionBlock = {
     print("-------operation2")
}
        
let operation3 = BlockOperation(block: {
    let image = DownloaderManager.downloadImageWithURL(imgArray[2])
        OperationQueue.main.addOperation {
          self.girlsImg3.image = image
    }
})
operation3.completionBlock = {
   print("-------operation3")
}
        
let operation4 = BlockOperation(block: {
    let image = DownloaderManager.downloadImageWithURL(imgArray[7])
    OperationQueue.main.addOperation {
         self.girlsImg4.image = image
    }
})
operation4.completionBlock = {
    print("-------operation4")
}

operation3.addDependency(operation4)
operation2.addDependency(operation3)
        
queue.addOperation(operation1)
queue.addOperation(operation4)
queue.addOperation(operation3)
queue.addOperation(operation2)
```
此处把添加`Operation`到`Queue`的操作，放到了`addDependency`之后，确保执行前有正确的依赖关系。多次运行代码可以看到，图片的下载顺序依然是4->3->2的顺序。

```
-------operation1
-------operation4
-------operation3
-------operation2
```
(8)、取消执行的任务

除了设置一个队列中任务关联性之外，还可以控制取消队列中的任务，但是取消的结果会根据任务的状态而不同：

 * 已经完成的任务，取消不影响其结果
 * 当一个任务被取消时所有与其关联的任务也会被取消
 * 任务被取消后，`completionBlock`依旧会执行

 ![取消图片下载](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d7b15b84bc8a490396eefb6115858fdc~tplv-k3u1fbpfcp-watermark.image)
 
 如图所示，当我点击`download`按钮后快速点击`cancel`按钮，可以看到图片一的下载任务被`cancel`了。
 
 ```
let cancelItem = UIBarButtonItem()
cancelItem.title = "cancel"
cancelItem.rx.tap.subscribe(onNext: {
    self.queue.cancelAllOperations()
}).disposed(by: disposeBag)
```
此处可以通过`Operation`的`isCancelled`属性来判断任务是否被`cancel`。当`isCancelled`返回`true`表示该任务被`cancel`了。

```
-------operation4,false
-------operation3,false
-------operation2,false
-------operation1,true
```


本文主要简单介绍了Operation的一些简单应用，正确的理解和应用这些多线程技术是构建复杂App的基础，关于更多多线程的应用可参考官方多线程的文档：

**[Apple Developer Threading Programming Guide](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/Multithreading/Introduction/Introduction.html)**


**[Apple Developer About Dispatch Queues](https://developer.apple.com/library/archive/documentation/General/Conceptual/ConcurrencyProgrammingGuide/OperationQueues/OperationQueues.html#//apple_ref/doc/uid/TP40008091-CH102-SW2)**

**[本文demo](https://github.com/dengfeng520/RPDemo/tree/main/OperationDemo)**
