---
title: iOS 多线程之GCD
date: 2020-03-01 16:01:56
cover: https://github.com/dengfeng520/xiaoshiguangBlog/blob/master/girls/girl032.jpg?raw=true
---
<h6 align='right'>小时光</h6>

由于GCD简单易用，任务更简单纯粹，执行效率高，本身性能高这些优点，使得GCD在实际开发的使用和面试中出现的频率非常高。掌握GCD及其多线程技术点并将其运用于开发中是开发一个良好易用App的基础之一。本文主要整理了GCD的一些知识点和基础用法。

### 1、GCD概览

GCD全称是`Grand Central Dispatch`，是Apple为开发者提供的一个多线程编程的方案，它是一个在线程池模式的基础上执行的并发任务，GCD充分利用硬件的多核性能，让开发者编写出更高效的代码。

### 2、GCD的队列和任务


#### 2.1、队列


  * 主队列(main Queue): 在主线程中执行的任务,主要用于处理UI相关的任务

  ```
  let main = DispatchQueue.main
  ```

  * 串行队列（serial Queue）: 任务按照先后顺序执行，一般只分配一个线程，同一时刻只会执行一个任务，并严格按照任务顺序执行

  ```
  let serialQueue = DispatchQueue(label: "com.tsn.demo.serialQueue")
  ```
例如串行队列中有三个下载图片任务，系统会根据这三个任务加入到队列的顺序执行，下载完成第一张图片后才会下载第二张图片，第二张图片下载完成后才会下载第三张图片。

   ![串行队列](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/32d364cdc3bc4ce1aae755df1436cd63~tplv-k3u1fbpfcp-watermark.image)
       
  * 并行队列（concurrent Queue）: 多个任务同时执行，完成的顺序不一定

  ```
  let concurrentQueue = DispatchQueue(label: "com.tsn.demo.concurrentQueue", attributes: .concurrent)
  ```

创建队列时设置`attributes`属性为`concurrent`就是并行队列。GCD会根据当前系统的状况至少为并行队列分配一个线程，且线程允许被任何队列的任务阻塞。

如果是用并行队列来下载图片，GCD会根据当前系统状况开启多个线程下载图片，下载图片任务之间互不影响，完成的时间也不一定。

![并行队列](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f19855970a864b9d9e5a76c10f9b5588~tplv-k3u1fbpfcp-watermark.image)

  * 全局并发队列 （global Queue）

  ```
  let globalQueue = DispatchQueue.global()
  let globalQueue = DispatchQueue.global(qos: .background)
  ```

   全局并发队列系统提供了6个不同的优先级别，分别是`background、utility、default、userInitiated、userInteractive、unspecified`。创建队列时，其中`qos`参数表示的是队列的优先级别,可以使用默认优先级，也可以单独指定。

#### 2.2、任务

 GCD的任务一般我们认为是程序执行时做的事情，如一个API调用，一个方法、函数等。

 * 同步任务


 ```
 queue.sync {
 
 }
 ```
 **同步任务一经提交会在当前线程执行立即执行，不会开启新线程，并且会阻塞当前线程，当前任务在执行完并返回结果后才能执行下一步。**

 如图所示，今天公司给我安排了一个开发任务A，当我正在开发时又出现了一个很紧急任务B此时我只能暂停做到一半的任务A来执行任务B，当B任务完成后才能继续开发任务A，这种执行方式为同步。

  ![同步任务](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/cbd50ceaae5c477e86e5bc76614bee27~tplv-k3u1fbpfcp-watermark.image)  

  

 * 异步任务

 ```
 queue.async {
 
 }
 ```
  **异步任务提交后不会阻塞当前线程，会新建另一个线程执行。**

 如图所示，今天给我安排了任务A，同时给另一个同事安排了任务B，我们两开发的是同一个App此处可以看作是一个队列，无论是谁先完成任务告知对方，然后完成代码merge即可。这种执行方式可以看作是异步。

![异步任务](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/99b0bec6cc0f494f889fe889b58b3c2e~tplv-k3u1fbpfcp-watermark.image)

 * 栅栏任务

 栅栏任务会对队列中的任务进行阻隔，先把队列中已有的任务全部执行完然后再执行栅栏任务。

 ![栅栏任务](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/9b57508eec65454b8cd7ab1b5ae3b6ca~tplv-k3u1fbpfcp-watermark.image)

 * 迭代任务

 如果说使用并行队列是为了提高程序的执行效率，那么迭代任务就是为了更高效的更全面的利用手机性能来执行任务。
 GCD使用`concurrentPerform`方法执行迭代任务，类似于`Objective-C`的`dispatch_apply`方法。

```
let musicArray = Array<AnyObject?>(repeating: nil, count: 10)
DispatchQueue.concurrentPerform(iterations: musicArray.count) { (index) in
    print("-----------执行查找操作-----------")
}
```


#### 2.3、队列详细属性

  前面已经给出一般我们创建队列，系统提供的完成的创建方法:

   ```
   let concurrentQueue = DispatchQueue(label: "com.tsn.demo.concurrentQueue", qos: .default, attributes: .concurrent, autoreleaseFrequency: .inherit, target: nil)
   ```

###### 2.3.1 常用API

* Dispatch.main 主线程队列
* Dispatch.global 全局队列
* DispatchQueue(label:, qos:, attributes:, autoreleaseFrequency:, target:) 新建一个队列
* queue.label 设置线程名称，label为队列名称，一般采用**`com`+公司缩写+工程名+线程名**的命名方式。
* setTarget(queue: DispatchQueue?) 设置指定队列 
  
  ​    
###### 2.3.2、QoS: 队列在执行上是有优先级的，更高的优先级可以享受更多的计算资源，从高到低的顺序为：

   * userInteractive
   * userInitiated
   * default
   * utility
   * background

###### 2.3.3、Attributes标识队列类型

   * concurrent: 标识队列为并行队列
   * initiallyInactive: 标识队列运行中的任务需要手动触发，不设置此属性，向队列中添加任务自动运行，通过`queue.activate()`方法触发任务


```
let initiallyInactiveQueue = DispatchQueue(label: "com.tsn.demo.initiallyInactiveQueue", qos: .default, attributes: .initiallyInactive, autoreleaseFrequency: .inherit, target: nil)
initiallyInactiveQueue.async {
    // do something
}
initiallyInactiveQueue.async {
    // do something
}
initiallyInactiveQueue.async {
    // do something
}
initiallyInactiveQueue.activate()
```

###### 2.3.4、AutoreleaseFrequency: 表示`autorelease pool`的自动释放频率，`autorelease pool`用来处理任务对象的内存周期。系统提供了三个属性:

  * inherit: 继承目标队列的该属性
  * workItem: 跟随每个任务的执行周期自动创建和释放
  * never: 需要手动管理内存

###### 2.3.5、Target: 一般创建的队列不设置，如果没有设置系统会自动设置，最终都指向系统`主队列`或`全局并发队列`。

 ```
 let queue = DispatchQueue(label: "com.tsn.demo.queue", qos: .default, attributes: .concurrent, autoreleaseFrequency: .inherit, target: nil)
 ```
  即然最终都是指向`主队列`或`全局并发队列`，那为什么不直接将任务添加到`主队列`或`全局并发队列`中呢？通过自定义队列可以将任务分组管理，这样可以防止自定义队列阻塞主队列。

  ```
   let queue = DispatchQueue(label: "com.tsn.demo.queue", qos: .default, attributes: .concurrent, autoreleaseFrequency: .inherit, target: .main)
 let queue = DispatchQueue(label: "com.tsn.demo.queue", qos: .default, attributes: .concurrent, autoreleaseFrequency: .inherit, target: .global())

  ```

### 3、任务和队列不同组合方式

#### 3.1 同步 + 串行

```
print("3.1-----------\(Thread.current)-----------start")
serialQueue.sync {
    sleep(1)
    print("3.1.1-----------\(Thread.current)-----------")
}
serialQueue.sync {
    sleep(1)
    print("3.1.2-----------\(Thread.current)-----------")
}
serialQueue.sync {
    sleep(1)
    print("3.1.3-----------\(Thread.current)-----------")
}
serialQueue.sync {
    sleep(1)
    print("3.1.4-----------\(Thread.current)-----------")
}
print("3.1-----------\(Thread.current)-----------end")
```

```
3.1-----------<NSThread: 0x600000f58700>{number = 1, name = main}-----------start
3.1.1-----------<NSThread: 0x600000f58700>{number = 1, name = main}-----------
3.1.2-----------<NSThread: 0x600000f58700>{number = 1, name = main}-----------
3.1.3-----------<NSThread: 0x600000f58700>{number = 1, name = main}-----------
3.1.4-----------<NSThread: 0x600000f58700>{number = 1, name = main}-----------
3.1-----------<NSThread: 0x600000f58700>{number = 1, name = main}-----------end
```

可以得出结论： 

* 不开启新线程，所有任务都是在主线程中执行的
* 任务是按顺序执行的，每次只执行一个任务


#### 3.2 异步 + 串行

```
print("3.2-----------\(Thread.current)-----------start")
serialQueue.async {
    sleep(1)
    print("3.2.1-----------\(Thread.current)-----------")
}
serialQueue.async {
    sleep(1)
    print("3.2.2-----------\(Thread.current)-----------")
}
serialQueue.async {
    sleep(1)
    print("3.2.3-----------\(Thread.current)-----------")
}
serialQueue.async {
    sleep(1)
    print("3.2.4-----------\(Thread.current)-----------")
}
print("3.2-----------\(Thread.current)-----------end")
```

打印结果为：

```
3.2-----------<NSThread: 0x600001974840>{number = 1, name = main}-----------start
3.2-----------<NSThread: 0x600001974840>{number = 1, name = main}-----------end
3.2.1-----------<NSThread: 0x6000019748c0>{number = 11, name = (null)}-----------
3.2.2-----------<NSThread: 0x6000019748c0>{number = 11, name = (null)}-----------
3.2.3-----------<NSThread: 0x6000019748c0>{number = 11, name = (null)}-----------
3.2.4-----------<NSThread: 0x6000019748c0>{number = 11, name = (null)}-----------
```

* 开启新线程，注意此处只开启了一条新线程
* 所有的任务是在`start`和`end`之后执行的，异步任务不做等待会直接执行后面的任务
* 所有的任务是按顺序执行的


#### 3.3 同步 + 并行

```
print("3.3-----------\(Thread.current)-----------start")
concurrentQueue.sync {
    sleep(1)
    print("3.3.1-----------\(Thread.current)-----------")
}
concurrentQueue.sync {
    sleep(1)
    print("3.3.2-----------\(Thread.current)-----------")
}
concurrentQueue.sync {
    sleep(1)
    print("3.3.3-----------\(Thread.current)-----------")
}
concurrentQueue.sync {
    sleep(1)
    print("3.3.4-----------\(Thread.current)-----------")
}
print("3.3-----------\(Thread.current)-----------end")
```

打印结果：

```
3.3-----------<NSThread: 0x600003914840>{number = 1, name = main}-----------start
3.3.1-----------<NSThread: 0x600003914840>{number = 1, name = main}-----------
3.3.2-----------<NSThread: 0x600003914840>{number = 1, name = main}-----------
3.3.3-----------<NSThread: 0x600003914840>{number = 1, name = main}-----------
3.3.4-----------<NSThread: 0x600003914840>{number = 1, name = main}-----------
3.3-----------<NSThread: 0x600003914840>{number = 1, name = main}-----------end
```

* 所有的任务都是在主线程中执行的，没有开启新线程
* 所有的任务是在`start`和`end`之间执行的，同步任务需要等待队列的任务执行结果
* 任务是按照先后顺序执行的，虽然并发队列可以开启多个线程并且可以同时执行多个任务，但是本身不能创建新线程。

#### 3.4 异步 + 并行

```
print("3.4-----------\(Thread.current)-----------start")
concurrentQueue.async {
     sleep(1)
     print("3.4.1-----------\(Thread.current)-----------")
}
concurrentQueue.async {
     sleep(1)
     print("3.4.2-----------\(Thread.current)-----------")
}
concurrentQueue.async {
     sleep(1)
     print("3.4.3-----------\(Thread.current)-----------")
}
concurrentQueue.async {
     sleep(1)
     print("3.4.4-----------\(Thread.current)-----------")
}
print("3.4-----------\(Thread.current)-----------end")
```
打印结果

```
3.4-----------<NSThread: 0x6000035407c0>{number = 1, name = main}-----------start
3.4-----------<NSThread: 0x6000035407c0>{number = 1, name = main}-----------end
3.4.3-----------<NSThread: 0x600003550340>{number = 4, name = (null)}-----------
3.4.4-----------<NSThread: 0x6000035450c0>{number = 5, name = (null)}-----------
3.4.1-----------<NSThread: 0x600003545340>{number = 6, name = (null)}-----------
3.4.2-----------<NSThread: 0x600003544000>{number = 7, name = (null)}-----------
```

* 所有的任务是在`start`和`end`之后执行的，异步任务不做等待会直接执行后面的任务
* 每个任务都开启了一个新线程来执行，异步执行可以开启新线程，并发队列可以同时执行多个任务

#### 3.5 同步+主队列

##### 3.5.1 在主线程中调用 同步 + 主队列

```
let mainQueue = DispatchQueue.main
mainQueue.sync {
   sleep(1)
   print("1-----------\(Thread.current)-----------")
}
mainQueue.sync {
   sleep(1)
   print("2-----------\(Thread.current)-----------")
}
mainQueue.sync {
   sleep(1)
   print("3-----------\(Thread.current)-----------")
}
```

运行代码，发现没有打印任何东西，原因是是我把任务放在了主线程队列中，由于同步任务会等待当前队列中的任务完成后才会继续执行的特性，此时主队列和我添加的任务会互相等待，所以任务不会执行也没有任何打印。

##### 3.5.1 在其他线程中调用 同步 + 主队列

```
let queue = DispatchQueue(label: "com.tsn.test.queue", attributes: .concurrent)
queue.async {
   print("0-----------\(Thread.current)-----------start")
   let mainQueue = DispatchQueue.main
   mainQueue.sync {
      sleep(1)
      print("1-----------\(Thread.current)-----------")
   }
   mainQueue.sync {
      sleep(1)
      print("2-----------\(Thread.current)-----------")
   }
   mainQueue.sync {
      sleep(1)
      print("3-----------\(Thread.current)-----------")
   }
   print("4-----------\(Thread.current)-----------end")
}
```

打印结果：

```
0-----------<NSThread: 0x600001f08580>{number = 1, name = main}-----------start
1-----------<NSThread: 0x600001f08580>{number = 1, name = main}-----------
2-----------<NSThread: 0x600001f08580>{number = 1, name = main}-----------
3-----------<NSThread: 0x600001f08580>{number = 1, name = main}-----------
4-----------<NSThread: 0x600001f08580>{number = 1, name = main}-----------end
```

* 虽然我新建了一个线程，但任务依旧是在主线程中执行的,没有开启新线程
* 所有任务都是在`start`和`end`之间执行的
* 主队列是串行队列，所以任务是按顺序执行的

#### 3.6 异步+主队列

```
print("3.6-----------\(Thread.current)-----------start")
let mainQueue = DispatchQueue.main
mainQueue.async {
     sleep(1)
     print("3.6.1-----------\(Thread.current)-----------")
}
mainQueue.async {
     sleep(1)
     print("3.6.2-----------\(Thread.current)-----------")
}
mainQueue.async {
     sleep(1)
     print("3.6.3-----------\(Thread.current)-----------")
}
print("3.6-----------\(Thread.current)-----------end")
```

打印结果：

```
3.6-----------<NSThread: 0x600002590240>{number = 1, name = main}-----------start
3.6-----------<NSThread: 0x600002590240>{number = 1, name = main}-----------end
3.6.1-----------<NSThread: 0x600002590240>{number = 1, name = main}-----------
3.6.2-----------<NSThread: 0x600002590240>{number = 1, name = main}-----------
3.6.3-----------<NSThread: 0x600002590240>{number = 1, name = main}-----------
```

* 所有的任务都是在主线程中执行的，没有开启新线程
* 所有任务都是在`start`和`end`之后执行的
* 任务是按顺序执行的

对于上面的各个组合做一个整理，如下表所示:

| 区别 | 串行队列 | 并行队列 | 主队列 |
| :-----| ----: | :----: | :----: |
| 同步(sync) |不开启新线程，串行执行|没有开启新线程，串行执行|1、在主线程中调用时会造成死锁；2、在其他线程中调用时:不开启新线程在主线程中执行，串行执行|
| 异步(async)|开启一条新线程，串行执行|开启多个新线程，并行执行|没有开启新线程，串行执行|

### 4、GCD队列和任务详解

前面简单介绍了GCD的任务和队列，此处结合例子和示例代码来进一步说明GCD实际开发中的使用。

 (1)、串行队列和并行队列: 拿平时下载电影来举例说明，默认情况下视频资源的下载按照从上到下的顺序来下载（这就是串行队列）。但此时我的的目标是下载所有的电影，我并不关心电影下载的顺序，所以此时可以让多个下载任务同时执行。（这就是并行队列）。

```
// 串行队列
let serialQueue = DispatchQueue(label: "com.tsn.demo.serialQueue")
// 并行队列 
let concurrentQueue = DispatchQueue(label: "com.tsn.demo.concurrentQueue", attributes: .concurrent)
```

 (2)、对于多个下载任务而言，可以多个下载任务同时下载各个下载任务之间互不影响（这是异步执行）。但是在播放视频时只能等前一个视频播放完了才能播放第二个视频(这是同步执行)。

```
// 同步执行
queue.sync {
    // 播放视频一
}
queue.sync {
    // 播放视频二
}
queue.sync {
    // 播放视频三
}
```
```
// 异步执行
queue.async {
    // 下载视频一
}
queue.async {
    // 下载视频二
}
queue.async {
    // 下载视频三
}
```

 (3)、如果视频还在下载中就点了播放键，此时要保证视频、音频、弹幕这三个捆绑到一起下载（这是任务组）。只有这三个下载任务全部完了在播放视频（栅栏任务）。


栅栏任务示例代码：

```
let barrierQueue = DispatchQueue(label: "com.tsn.demo.barrierQueue", attributes: .concurrent)

let barrierTask = DispatchWorkItem(qos: .default, flags: .barrier) {
    // 点击开始播放视频
    print("-----------开始播放------------")
}

barrierQueue.async {
   // 下载视频
    print("-----------下载视频------------")
}
barrierQueue.async {
    // 下载音频
    print("-----------下载音频------------")
}
barrierQueue.async {
    // 下载弹幕
    print("-----------下载弹幕------------")
}
// 栅栏任务
barrierQueue.async(execute: barrierTask)
```
运行代码，打印的结果为：

```
-----------下载视频------------
-----------下载音频------------
-----------下载弹幕------------
-----------开始播放------------
```

在实际的开发中，视频的播放、下载、缓存要比上面的举例要复杂的多。如在传输过程中可能会出现丢包、掉帧等情况。

(4)、如果在下载途中，因为其他操作如我点击了暂停，或来了个电话（这是挂起队列），过了一会又点击继续开始下载（唤醒队列），同时这些操作还要用到下载任务的暂停和继续。

GCD可以把尚未执行的任务挂起，但是不影响正在执行和已执行的任务，挂起的任务后续可手动在其唤醒。
`suspend()`方法挂起任务，`resume()`方法唤醒任务，此处要注意的是**调用唤醒的次数必须等于挂起的次数，否则就会出现不可预测的错误。**

下面的代码简单

```
class SuspendAndResum {
    let queue = DispatchQueue(label: "com.tsn.demo.concurrentQueue", attributes: .concurrent)
    // 记录队列挂起的次数
    var index = 0
    
    init() {
        // 模拟任务挂起
        configQueue()
        DispatchQueue.main.asyncAfter(deadline: .now() + 5) {
            // 模拟任务唤醒
            self.testResume()
            // 唤醒任务后继续下载 下载完成后播放视频
            self.goOnQueue()
        }
    }

    func configQueue() {
        queue.async {
            print("-----------模拟下载视频-----------")
        }
        queue.async {
            print("-----------模拟下载音频-----------")
        }
        queue.async {
            print("-----------模拟下载弹幕-----------")
        }
        // 通过栅栏挂起任务
        queue.async(execute: DispatchWorkItem(flags: .barrier) {
            self.testSuspend()
        })
    }
    
    func goOnQueue() {
        queue.async {
            print("-----------继续下载视频-----------")
        }
        queue.async {
            print("-----------继续下载音频-----------")
        }
        queue.async {
            print("-----------继续下载弹幕-----------")
        }
        let barrierTask = DispatchWorkItem(qos: .default, flags: .barrier) {
            print("-----------下载完成，开始播放------------")
        }
        queue.async(execute: barrierTask)
    }
    
    // 挂起
    func testSuspend() {
        index = index + 1
        queue.suspend()
        print("-----------任务挂起-----------")
    }
    // 唤醒
    func testResume() {
        if index == 1 {
            queue.resume()
            index == 0
            print("-----------任务唤醒-----------")
        } else if index < 1 {
            print("-----------唤醒次数超过挂起次数-----------")
        } else {
            queue.resume()
            index = index - 1
            print("-----------还需要\(index)才可以唤醒-----------")
        }
    }
}
```

```
let test = SuspendAndResum()
```

运行代码，最终打印的结果为： 

```
-----------模拟下载视频-----------
-----------模拟下载音频-----------
-----------模拟下载弹幕-----------
-----------任务挂起-----------
-----------任务唤醒-----------
-----------继续下载视频-----------
-----------继续下载音频-----------
-----------继续下载弹幕-----------
-----------下载完成，开始播放------------
```

* (5)、一般一个队列都可以设置最大并发数，即使不设置GCD回根据当前系统的状态自动设置并发数。拿下载视频来说，我可以设置同时最大下载数，这样就可以控制同时进行的任务数。同时也可以通过信号量的方式来控制并发数。

```
let queue = DispatchQueue(label: "com.tsn.demo.concurrentQueue", attributes: .concurrent)
// 设置最大并发数为5
let semap = DispatchSemaphore.init(value: 5)
// 信号量减1
semap.wait()
queue.async {
    // 信号量加1
    semap.signal()
}
// 信号量减1
semap.wait()
```

* (6)、我非常喜欢在睡前刷视频，这样我就有个需求，当我看完这个视频，比如说20分钟后就把视频关了，此时就用到了延迟任务（asyncAfter）。

```
DispatchQueue.main.asyncAfter(deadline: .now() + 20 * 60) {
    print("-----------20分钟后关闭视频------------")
}
```

* (7)、我要查找出2020年所有新歌歌名里含有`爱`字的歌曲，由于要查找的数据源数据太多，采用遍历的方式效率并不是很好，此时就可以用到迭代任务（concurrentPerform）。

```
// 迭代任务
let musicArray = Array<AnyObject?>(repeating: nil, count: 1008611)
DispatchQueue.concurrentPerform(iterations: 1008611) { (index) in
    print("-----------执行查找操作-----------")
}
```

> <h3>5、任务组</h3>

任务组是将多个任务放到一个组里，`DispatchGroup`会等待组里面的任务都完成了之后通过`notify()`方法通知外部队列任务已全部完成。同时采用`enter()`和`leave()`配对方法，标识任务加入或离开任务组。

此处同样用下载视频来举例,将下载视频、音频、弹幕作为并发任务

```
// 创建任务组
let group = DispatchGroup()
// 下载视频
let videoQueue = DispatchQueue(label: "com.tsn.demo.video", attributes: .concurrent)
videoQueue.async(group: group) {
    print("-----------开始下载视频-----------")
}
// 下载音频
let audioQueue = DispatchQueue.init(label: "com.tsn.demo.audio", attributes: .concurrent)
audioQueue.async(group: group) {
    print("-----------开始下载音频-----------")
}
// 下载弹幕
let bulletScreenQueue = DispatchQueue.init(label: "com.tsn.demo.audio", attributes: .concurrent)
audioQueue.async(group: group) {
    print("-----------开始下载弹幕-----------")
}
```


* `enter()`和`leave()`方法

```
// 进入任务组
group.enter()
videoQueue.async(group: group) {
    print("-----------开始下载视频-----------")
    // 退出任务组
    group.leave()
}
```

* `notify()`方法

```
// 任务组通知
group.notify(queue: .main) {
    print("-----------全部下载完成-----------")
}
```

* `wait()`方法

该方法会在所有任务完成后再执行当前线程中的后续代码，主要是起到阻塞线程的作用.同时也可以设置阻塞时间，如果所有任务都在指定时间内完成，否则等到时间结束后再恢复阻塞线程。

```
group.wait()
group.wait(timeout: .now() + 1)
group.wait(wallTimeout: .now() + 1)
```

> <h3>6、DispatchSource</h3>

`DispatchSource`用来监听系统底层一些对象的活动，当这些事件发生时，会自动向队列提交一个异步任务来处理这些事件。如我可以用`DispatchSource`监听电影是否成功下载到本地。

`DispatchSource`的常用方法有:

* 使用`makeFileSystemObjectSource、makeTimerSource`创建`source`
* 通过`setEventHandler`方法设置监听
* 调用`resume()`方法开始监听
* 调用`setCancelHandler`方法取消监听

一般也常用于创建计时器，示例代码，

```
var num = 5
let source: DispatchSourceTimer = DispatchSource.makeTimerSource(flags: [], queue: .global())
source.schedule(deadline: .now(), repeating: 1)
// 设置监听
source.setEventHandler {
    num = num - 1
    if num < 0 {
        source.cancel()
    } else {
        print("num-----------\(num)-----------")
    }
}
source.resume()
```
打印结果

```
num-----------4-----------
num-----------3-----------
num-----------2-----------
num-----------1-----------
num-----------0-----------
```

更多使用方法可参考[Apple Developer DispatchSource](https://developer.apple.com/documentation/dispatch/dispatchsource)

> <h3>7、线程安全</h3>

多个线程同时访问同一个数据源时很容易引发数据错乱和数据安全问题，这就是线程安全要解决的问题。

```
for index in 0..<123 {
    queue.async {
        array.remove(at: index)
        print("-----------\(index)-----------")
    }
}

for index in 0..<123 {
    queue.async {
        lock.lock()
        array.remove(at: index)
        print("-----------\(index)-----------")
        lock.unlock()
    }
}
```

如上面的代码，虽然两段代码打印的结果是一样的，但是第一段代码是线程不安全的，第二段代码是安全的。

#### 1、线程死锁

一般指**队列中的多个线程互相等待而造成的线程循环等待**。常见的有线程死锁有：

* 主队列同步

```
// 线程死锁
DispatchQueue.main.sync {
    print("-----------死锁-----------")
}
```

* 串行 + 同步 + 嵌套同步自身队列

```
// 串行队列 + 同步 嵌套自身同步
let serialQueue = DispatchQueue.init(label: "com.tsn.LockTestClass")
serialQueue.sync {
    serialQueue.async {
        print("-----------死锁-----------")
    }
}
```

* 串行 + 异步 + 嵌套同步自身队列

```
// 串行队列 + 异步 嵌套自身同步
let serialQueue = DispatchQueue.init(label: "com.tsn.LockTestClass")
serialQueue.async {
    serialQueue.async {
        print("-----------死锁-----------")
    }
}
```

在这种情况下，Xcode会抛出crash。

```
error: Execution was interrupted, reason: EXC_BAD_INSTRUCTION (code=EXC_I386_INVOP, subcode=0x0).
```

得出结论：**不要在串行或主队列中嵌套执行同步任务，才能有效避免线程死锁。**

#### 2、线程安全的一些概念

* 临界区：就是一段代码不能被并发执行，也就是说两个线程不能同时执行同一段代码。
* 竞态条件：两个或多个线程读写某些共享数据，而最后的结果取决于额线程运行的精确时序。
* 优先级反转： 

如图所示，有多个不同优先级的线程，在第一个时间点，低优先级的线程获取到了锁的资源，在第二个时间点高优先级的线程去获取这个加了锁的资源，但是此时这个资源正被低优先级的线程持有，此时高优先级的线程就会被挂起并等待资源的释放。此时我的中优先级的线程不需要获取锁所以会比低优先级优先运行，低优先级的线程只能等待中优先级的线程执行完之后才能去执行，所以低优先级线程也被挂起等待中优先级线程执行完成后在执行。造成的结果就是中优先级的线程会执行的非常顺利，而高优先级和中优先级线程却不能按照预期执行。这种情况就是优先级反转。

![优先级反转](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/671ce35400ae4d3da1c129681df3c352~tplv-k3u1fbpfcp-watermark.image)

* 并发与并行

这两个概念和手机内核有关，并发是指多个线程同时执行。
并发是指通过内部的算法，把多个线程的执行做切换，看起来像多个线程是同时执行的。


#### 3、各种不同的线程锁

 [不再安全的 OSSpinLock](https://blog.ibireme.com/2016/01/16/spinlock_is_unsafe_in_ios/)这篇文中列举了iOS中各种不同的锁及其性能比较。

* 1、自旋锁 (Spin Lock）

自旋锁，任意时刻只有一个线程能够获得锁，其他线程忙等待直到获得锁。SpinLock会一直检测是否获取某个锁，虽然效率很好，但是非常消耗系统内存。所以应该慎重使用。


* 2、互斥锁（Mutex Lock）

在`Objective-C`中可以用`@synchronized`来实现互斥锁。其本质是调用`objc_sync_enter`和`objc_sync_exit`方法。因此在Swift中互斥锁的实现方式是:

```
- (void)configMutex(id)lockData {
    @synchronized(lockData)
}

func synchronized(lockData: AnyObject, ) {
   objc_sync_enter(lockData)
   objc_sync_exit(lockData)
}
```

参考[猫神：LOCK](https://swifter.tips/lock/)


关于iOS中更多的线程锁可参考： [掘金 深入理解 iOS 开发中的锁](https://juejin.cn/post/6844903446177382413)


---

本文主要整理了**GCD**的基础知识点和简单使用，如果有错误或不对的地方，欢迎指出和补充。

相关链接：

 [Apple Developer Dispatch](https://developer.apple.com/documentation/dispatch)

 [Apple Developer Concurrency Programming Guide](https://developer.apple.com/documentation/dispatch)

 [GCD Apple官方源码](https://github.com/apple/swift-corelibs-libdispatch)

 [GCD Target Queues](https://www.humancode.us/2014/08/14/target-queues.html)

 [Swift核心技术与实战: 多线程](https://time.geekbang.org/course/detail/100034001-161815)

 [掘金 iOS多线程：『GCD』详尽总结](https://juejin.cn/post/6844903566398717960)


 [本文demo](https://github.com/dengfeng520/RPDemo/tree/main/GCD)
