---
title: Swift Closures
date: 2021-03-05 10:25:56
cover: /img/girl052.webp

---

<h6 align='right'>小时光</h6>
<h6 align='right'><a href='https://dengfeng520.github.io/'>我的博客</a></h6>

### 1、What's the closure?

作为`iOS`开发者对于`Objective-C`中的`Block`一定非常熟悉，在其他开发语言中，也把`closure`也称作`lambdas`等。简答来说，闭包就是一个独立的函数，一般用于捕获和存储定义在其上下文中的任何常量和变量的引用。

`closure`的语法如下：

```Swift
{ (parameters) -> return type in
    statements
}
```

`closure`能够使用常量形式参数、变量形式参数和输入输出形式的参数，但不能设置默认值。可变形式参数也可以使用，但需要在行参列表的最后使用。元组也可以被用来作为形式参数和返回类型。

实际上全局函数和内嵌函数也是一种特殊的闭包，（关于函数的相关概念可参考官方文档[The Swift Programming Language: Functions](https://docs.swift.org/swift-book/LanguageGuide/Functions.html)），闭包会根据其捕获值的情况分为三种形式：

* 全局函数是一个有名字但不会捕获任何值的闭包
* 内嵌函数是一个有名字且能从其上层函数捕获值的闭包
* 闭包表达式是一个轻量级语法所写的可以捕获其上下文中常量或变量值的没有名字的闭包

### 2、各种不同类型的闭包

如果需要将一个很长的闭包表达式作为函数最后一个实际参数传递给函数，使用尾随闭包将增强函数的可读性。尾随闭包一般作为函数行参使用。如系统提供的`sorted`,`map`等函数就是一个尾随闭包。

##### 2.1、尾随闭包（Trailing Closure）

尾随闭包虽然写在函数调用的括号之后，但仍是函数的参数。使用尾随闭包是，不要将闭包的参数标签作为函数调用的一部分。

```Swift
let strList = ["1","2","3","4","5"]
let numList: [Int] = strList.map { (num) in
    return Int(num) ?? 0
}
```

##### 2.2、逃逸闭包 （Escaping Closure）

当闭包作为一个实际参数传递给一个函数的时候，并且它会在函数返回之后调用，我们就说这个闭包逃逸，一般用` @escaping`修饰的函数形式参数来标明逃逸闭包。

* 逃逸闭包一般用于异步任务的回调
* 逃逸闭包在函数返回之后调用
* 让闭包` @escaping`意味着必须在闭包中显式地引用self

```Swift
// 逃逸闭包
func requestServer(with URL: String,parameter: @escaping(AnyObject?, Error?) -> Void) {
    
}
// 尾随闭包
func requestServerTrailing(losure: () -> Void) {
    
}
class EscapingTest {
    var x = 10
    func request() {
        // 尾随闭包
        requestServerTrailing {
            x = x + 1
        }
        // 逃逸闭包
        requestServer(with: "") { (obj, error) in
            x = x + 1
        }
    }
}
```

```Swift
Reference to property 'x' in closure requires explicit use of 'self' to make capture semantics explicit
```

修改代码：

```Swift
requestServer(with: "") { [weak self] (obj, error) in
     guard let self = `self` else {
          return
     }
     self.x = self.x + 1
}
```

* 逃逸闭包的实际使用

如我要设计一个下载图片管理类，异步下载图片下载完成后再返回主界面显示，这里就可以使用逃逸闭包来实现，核心代码如下：

```Swift
struct DownLoadImageManager {
    // 单例
    static let sharedInstance = DownLoadImageManager()
    
    let queue = DispatchQueue(label: "com.tsn.demo.escapingClosure", attributes: .concurrent)
    // 逃逸闭包
    // path: 图片的URL
    func downLoadImageWithEscapingClosure(path: String, completionHandler: @escaping(UIImage?, Error?) -> Void) {
        queue.async {
            URLSession.shared.dataTask(with: URL(string: path)!) { (data, response, error) in
                if let error = error {
                    print("error===============\(error)")
                    DispatchQueue.main.async {
                        completionHandler(nil, error)
                    }
                } else {
                    guard let responseData = data, let image = UIImage(data: responseData) else {
                        return
                    }
                    DispatchQueue.main.async {
                        completionHandler(image, nil)
                    }
                }
            }.resume()
        }
    }
    // 保证init方法在外部不会被调用
    private init() {
        
    }
}
```

下载图片并显示：

```Swift
 let path = "https://gimg2.baidu.com/image_search/src=http%3A%2F%2Finews.gtimg.com%2Fnewsapp_match%2F0%2F12056372662%2F0.jpg&refer=http%3A%2F%2Finews.gtimg.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=jpeg?sec=1618456758&t=3df7a5cf69ad424954badda9bc7fc55f"
DownLoadImageManager.sharedInstance.downLoadImageWithEscapingClosure(path: path) { (image: UIImage?, error: Error?) in
    if let error = error {
        print("error===============\(error)")
    } else {
        guard let image = image else { return }
        print("图片下载完成，显示图片: \(image)")
        let imageView = UIImageView(image: image)
        imageView.layer.cornerRadius = 5
    }
}
```



![下载图片并展示](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ffc81edc75454b09bb6195ecd8ae6f66~tplv-k3u1fbpfcp-watermark.image)

以上的代码虽然能够完成对图片的下载管理，事实上在项目中下载并显示一张图片的处理要复杂的多，这里不做更多赘述，可参考官方的demo: [Asynchronously Loading Images into Table and Collection Views](https://developer.apple.com/documentation/uikit/uiimage/asynchronously_loading_images_into_table_and_collection_views)

##### 2.3、自动闭包 （Auto Closure）

* 自动闭包是一种自动创建的用来把座位实际参数传递给函数的表达式打包的闭包
* 自动闭包不接受任何参数，并且被调用时，会返回内部打包的表达式的值
* 自动闭包能过省略闭包的大括号，用一个普通的表达式来代替显式的闭包
* 自动闭包允许延迟处理，因此闭包内部的代码直到调用时才会运行。对于有副作用或者占用资源的代码来说很用

如我有个家庭作业管理类，老师需要统计学生上交的作业同时处理批改后的作业，为了演示自动闭包，我用以下的代码来实现：

```Swift
enum Course {
    case spacePhysics // 空间物理
    case nuclearPhysics // 原子核物理
    case calculus // 微积分
    case quantumMechanics // 量子力学
    case geology // 地质学
}
struct StudentModel {
    var name: String = String()
    var course: Course!
    
    init(name: String, course: Course) {
        self.name = name
        self.course = course
    }
}
// MARK: - 自动闭包
class StudentManager {
    var studentInfoArray: [StudentModel] = [StudentModel]()
    // 某个学生交了作业
    func autoAddWith(_ student: @autoclosure() -> StudentModel) {
        studentInfoArray.append(student())
    }
    // 老师批改完了某个学生的作业
    func autoDeleteWith(_ index: @autoclosure() -> Int) {
        studentInfoArray.remove(at: index())
    }
}
```

其中，`autoAddWith`表示学生某个学生交了作业，`autoDeleteWith`表示老师批改完了某个学生的作业。一般调用方式为：

```Swift
let studentManager: StudentManager = StudentManager()

// Kate Bell 交了作业
studentManager.autoAddWith(StudentModel(name: "Kate Bell", course: .spacePhysics))
// Kate Bell 交了作业
studentManager.autoAddWith(StudentModel(name: "Kate Bell", course: .nuclearPhysics))
// Anna Haro 交了作业
studentManager.autoAddWith(StudentModel(name: "Anna Haro", course: .calculus))

// 老师批改完了第一份作业
studentManager.autoDeleteWith(0)
```

##### 2.4、自动 + 逃逸 （Autoclosure + Escaping ）

如果想要自动闭包逃逸，可以同时使用`@autoclosure`和`@escaping`来标志。

```Swift
func autoAddWith(_ student: @autoclosure @escaping() -> StudentModel) {
    studentInfoArray.append(student())
}
```

### 3、闭包捕获值

前面简单介绍了**尾随闭包、逃逸闭包、自动闭包**的概念和基本使用，这里来说闭包是如何捕获值的。在Swift中，值类型变量一般存储于栈（Stack）中，而像`func class closure`等引用类型存储于堆(Heap)内存中。而`closure`捕获值**本质上是将存在栈(Stack)区的值存储到堆(Heap)区**。

为了验证`closure`可以捕获哪些类型的值，用下面的代码做一个测试：

```Swift
class Demo: NSObject {
    var test = String()
}
// 常量
let index = 10086
// 变量
var number = 1008611
// 引用类型
let demo = Demo()
var capturel = {
    number = 1008611 - 998525
    demo.test = "block test"
    print("index==========\(index)")
    print("number==========\(number)")
    print("demo.test==========\(demo.test)")
}
number = number + 1
demo.test = "test"
capturel()

// 打印结果
// index==========10086
// number==========10086
// demo.test==========block test
```

上面的代码中，无论是常量、变量、还是引用类型调用`capturel()`后都可以正常打印数据。 无论是常量、变量、值类型还是引用类型，`Closure`都可捕获其值。事实上在Swift中作为优化**当`Closure`中并没有修改或者在闭包的外面的值时，Swift可能会使用这个值的copy而不是捕获**。同时Swift也处理了变量的内存管理操作，当变量不再需要时会被释放。

在来看一个实现递增的例子：

```swift
func makeIncrementer(_ amount: Int) -> () -> Int {
    var total = 0
    // 内嵌函数 也是一种特殊的Closure
    func incrementerClosure() -> Int {
        total = total + amount
        return total
    }
    return incrementerClosure
}
```

在上面的代码中,`incrementerClosure`中捕获了`total`值，当我返回`incrementerClosure`时，理论上包裹`total`的函数就不存在了，但是`incrementerClosure`仍然可以捕获`total`值。可以得出结论：**即使定义这些变量或常量的原作用域已经不存在了，但`closure`依旧能捕获这个值**。

```Swift
let incrementerTen = makeIncrementer(10) // () -> Int
incrementerTen() // 10
incrementerTen() // 20
incrementerTen() // 30
let incrementerSix = makeIncrementer(6) // () -> Int
incrementerSix() // 6
incrementerSix() // 12
incrementerTen() // 40
let alsoIncrementerTen = incrementerTen // () -> Int
alsoIncrementerTen() // 50
```

在上面的代码中，调用了递增闭包`incrementerTen`每次+10，当我新建一个`incrementerSix`闭包时就变成了+6递增，也就说产生了一个新的变量引用。

当调用`alsoIncrementerTen`后，返回的值是50，这里可以确定`Closure`是引用类型,是因为`alsoIncrementerTen` 引用了`incrementerTen`他们共享同一个内存。如果是值类型，`alsoIncrementerTen`返回的结果会是10,而不是50；

根据上面的代码关于闭包捕获值做出总结：

* `closure`捕获值本质上是将存在栈(Stack)区的值存储到堆(Heap)区
* 当`Closure`中并没有修改或者在闭包的外面的值时，Swift可能会使用这个值的copy而不是捕获
* `Closure`捕获值时即使定义这些变量或常量的原作用域已经不存在了`closure`依旧能捕获这个值
* 如果建立了一个新的闭包调用，将会产生一个新的独立的变量的引用
* 无论什么时候赋值一个函数或者闭包给常量或变量，实际上都是将常量和变量设置为对函数和闭包的引用

### 4、Closure循环引用

Swift中的`closure`是引用类型,我们知道Swift中的引用类型是通过`ARC`机制来管理其内存的。在Swift中，两个引用对象互相持有对方时回产生强引用环，也就是常说的循环引用。虽然在默认情况下，Swift能够处理所有关于捕获的内存的管理的操作，但这并不能让开发者一劳永逸的不去关心内存问题，因为相对于对象产生的循环引用`Closure`产生循环引用的情况更复杂，所以在使用`Closure`时应该更小心谨慎。那么在使用`Closure`时一般哪些情况会产生循环引用问题呢？

##### 4.1、`Closure`捕获对象产生的循环引用

当分配了一个`Closure`给实例的属性，并且`Closure`通过引用该实例或者实例的成员来捕获实例，将会在`Closure`和实例之间产生循环引用。

这里我用学生`Student`类来做演示,假设现在学生需要做一个单项选择题，老师根据其返回的答案来判断是否正确。我将对照`Objective-C`中的`Block`来做一个对比,在Xcode中编写如下代码：

```Objective-c
typedef NS_ENUM(NSInteger, AnswerEnum) {
    A,
    B,
    C,
    D,
};
@interface Student : NSObject
@property (copy, nonatomic) NSString *name;
@property (copy, nonatomic) void (^replyClosure)(AnswerEnum answer);
@end
@implementation Student
- (instancetype)init {
    self = [super init];
    if (self) {
        if (self.replyClosure) {
            self.replyClosure(B);
        }
    }
    return self;
}
@end
```

```Objective-c
@interface Teacher : NSObject
@property (assign, nonatomic) BOOL isRight;
@property (strong, nonatomic) Student *student;
@end

@implementation Teacher
- (instancetype)init {
    self = [super init];
    if (self) {
        self.student.replyClosure = ^(AnswerEnum answer) {
             // Capturing 'self' strongly in this block is likely to lead to a retain cycle
             NSLog(@"%@",self.student.name);
        };
    }
    return self;
}
@end
```

其实上面的代码，不用运行在Build的时候就会警告``Capturing 'self' strongly in this block is likely to lead to a retain cycle``。

那么在Swift中使用`closure`是否同样也会产生循环引用呢？我把`Objective-C`代码转换成`Swift`：

```Swift
enum Answer {
    case A
    case B
    case C
    case D
}
class Student: CustomStringConvertible {
    var name: String = String()
    var replyClosure: (Answer) -> Void = { _ in }
    
    var description: String {
        return "<Student: \(name)>"
    }
    
    init(name: String) {
        self.name = name
        print("==========Student init==========\(name)")
        replyClosure(.B)
    }
    
    deinit {
        print("==========Student deinit==========\(self.name)")
    }
}

class Teacher {
    var isRight: Bool = false
    init() {
        print("==========Teacher init==========")
        let student = Student(name: "Kate Bell")
        let judgeClosure = { (answer: Answer) in
            print("\(student.name) is \(answer)")
        }
        student.replyClosure = judgeClosure
    }
    
    deinit {
        print("==========Teacher deinit==========")
    }
}
```

`Student`类有两个属性：`name`表示学生姓名，`replyClosure`表示学生回答问题这一动作并返回答题结果。

```Swift
// 调用并运行代码
Teacher()
// 打印结果
==========Teacher init==========
==========Student init==========Kate Bell
==========Teacher deinit==========
```

运行上面的代码，通过打印结果可以看到`Student`类并没有调用`deinit`方法，此处说明`Student`在被初始化后内存并没有释放。实际上在`judgeClosure`内部，只要我调用(捕获)了`student`，无论是任何操作,该部分内存都不能有效释放了。那么为什么会造成这种现象呢？下面做逐步分析：

* 当我调用了闭包之后，闭包才会捕获值，在执行`student.replyClosure = judgeClosure`之后，在内存中他们的关系是这样的：

![closure001.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ba985325d30c491db6f833de0699f0d0~tplv-k3u1fbpfcp-watermark.image)

在Swift中，`class、func、closure`都是引用类型，因此在上面的代码中，`student`和`judgeClosure`都指向各种对象的`strong reference`。

同时由于在闭包中捕获了`student`,因此`judgeClosure`闭包就有了一个指向`student`的强引用。最后当执行`student.replyClosure = judgeClosure`之后，让`replyClosure`也成了`judgeClosure`的强引用。此时`student`的引用计数为1，`judgeClosure`的引用计数是2。

* 当超过作用域后，`student`和`judgeClosure`之间的引用关系是这样的：

![closure002.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/6fcb9017fe6346f7a8cebba11d29c759~tplv-k3u1fbpfcp-watermark.image)

此时，只有`Closure`对象的引用计数变成了1。于是`Closure`继续引用了`student`，`student`继续引用了他的对象`replyClosure`，而这个对象继续引用着`judgeClosure`。这样就造成了一个引用循环，所以就会出现内存无法正常释放的情况。

#####4.2、closure属性的内部实现捕获self产生的循环引用

同样的这里我先利用`Objective-C`的代码来举例，修改`Student`类的代码如下：

```Objective-c
@implementation Student
- (instancetype)init {
    self = [super init];
    if (self) {
       if (self.replyClosure) {
          self.replyClosure = ^(AnswerEnum answer) {
             // Capturing 'self' strongly in this block is likely to lead to a retain cycle
             NSLog(@"%@",self);
          };
       }
    }
    return self;
}
@end
```

同样的在Build时编译器会警告，`Capturing 'self' strongly in this block is likely to lead to a retain cycle`。

在Swift中虽然编译器不会警告，但也会产生同样产生循环引用问题。修改`Student`中定义`replyClosure`代码如下：

```Swift
lazy var replyClosure: (Answer) -> Void = { _ in
     print("replyClosure self=============\(self)")
}
```

为了保证在`replyClosure`内部调用`self`时`replyClosure`闭包已经正确初始化了，所以采用了`lazy`懒加载的方式。修改调用的代码为：

```Swift
Student(name: "Tom").replyClosure(.B)
```

运行代码，打印结果：

```Swift
==========Student init==========Kate Bell
replyClosure self=============<Student: Tom>
```

由于`Student`实例和`replyClosure`是互相强持有关系，即使超出了作用域他们之间依然存在着引用，所以内存不能有效释放。此时他们之间的引用关系是：

![closure004.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/9c0669bdd5c842c5bcb16b3439ca9d98~tplv-k3u1fbpfcp-watermark.image)

在`Objective-C`中一般采用弱引用的方式解决`Block`和实例循环引用的问题，这里对`Block`和类实例之间产生循环引用的原因不做赘述，关于`Objective-C`中`Block`的更多使用细节可查阅[Objective-C高级编程: iOS与OS X多线程和内存管理](https://book.douban.com/subject/24720270/)一书和苹果官方文档[Getting Started with Blocks](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/Blocks/Articles/bxGettingStarted.html#//apple_ref/doc/uid/TP40007502-CH7-SW4)的内容。那么在Swift中该如何处理循环引用呢？在Swift中需要根据`closure`和`class`对象生命周期的不同，而采用不同的方案来解决循环引用问题。

### 5、无主引用（unowned）

##### 5.1、使用unowned处理closure和类对象的引用循环

为了更易理解，我修改`Teacher`类的代码：

```Swift
class Teacher {
    var isRight: Bool = false
    init() {
        print("==========Teacher init==========")
        let student = Student(name: "Kate Bell")
        let judgeClosure = { [student] (answer: Answer) in
            print("student===========\(student)")
        }
        student.replyClosure = judgeClosure
    }
    deinit {
        print("==========Teacher deinit==========")
    }
}
```

这里只考虑`student`和`teacher`之间的引用关系，此时student`和`closure`之间存在着强引用关系，他们的引用计数都是2，在内存中他们之间的引用关系为：


![closure007.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7212bf008f964c66b94a87d414a9bd09~tplv-k3u1fbpfcp-watermark.image)

超过作用域后，由于互相存在强引用`student`和`clousre`的引用计数并不为0，所以内存无法销毁，此时他们之间的引用关系为：

![closure008.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e5178657660e44f19daa1349cc734554~tplv-k3u1fbpfcp-watermark.image)

对于这种引用关系在前面的[ARC](https://dengfeng520.github.io/2021/03/05/Swift%E8%87%AA%E5%8A%A8%E5%BC%95%E7%94%A8%E8%AE%A1%E6%95%B0%E5%99%A8/)就已经说过，把循环的任意一方变成`unowned`或`weak`就好了。我将`student`设置为无主引用，代码如下：

```Swift
let judgeClosure = { [unowned student] (answer: Answer) in
    print("\(student.name) is \(answer)")
}
```
使用无主引用后，他们之间的引用关系如下图所示：

![closure009.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e6676faa700e423a8b835c4a5a8d83e0~tplv-k3u1fbpfcp-watermark.image)

运行代码并打印：

```Swift
// 运行
Teacher()
// 打印结果
==========Teacher init==========
==========Student init==========Kate Bell
==========Student deinit==========Kate Bell
==========Teacher deinit==========
```

可以看到`student`和`teacher`都可以正常被回收了，说明`closure`的内存也被回收了。当`closure`为`nil`时，`student`对象就会被`ARC`回收，而当`student`为`nil`时，`teacher`也就失去了他的作用会被ARC回收其内存。

![closure010.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/4b3cda113c1047f49705317c66be9fbb~tplv-k3u1fbpfcp-watermark.image)

##### 5.2、`unowned`并不能解决所有的循环引用问题

虽然`unowned`能解决循环引用问题，但并不意味着，遇到的所有`closure`循环引用问题都可以用无主引用（unowned）来解决:

###### 5.2.1、示例代码一

同样用`Student`对象来举例，在"Kate Bell"学生回答完问题后，另一个`Tom`又回答了问题，他选择了**C**答案，代码如下：

```Swift
var student = Student(name: "Kate Bell")
let judgeClosure = { [unowned student] (answer: Answer) in
     print("student===========\(student)")
}
student = Student(name: "Tom")
student.replyClosure = judgeClosure
student.replyClosure(.C)

// 打印结果
// ==========Student init==========Kate Bell
// ==========Student init==========Tom
// ==========Student deinit==========Kate Bell
```

运行代码，程序会Crash并报错，`error: Execution was interrupted, reason: signal SIGABRT.`来分析一下为什么会这样：

* 代码中首先创建了一个名为`Kate Bell`的学生对象，`judgeClosure`捕获了这个`student`对象
* 当`student = Student(name: "Tom")`之后，由于`judgeClosure`是按照`unowned`的方式捕获的，此时`judgeClosure`内的`student`对象实际上已经不存了
* 名为`Tom`的`student`对象引用了`replyClosure`闭包
* 调用`student.replyClosure(.C)`的时候，`replyClosure`之前捕获的`student`对象已经不存在，此时就产生了Crash

###### 5.2.1、示例代码二

那么，如果我将` student.replyClosure = judgeClosure`移动到最前面呢？修改代码如下： 

```Swift
var student = Student(name: "Kate Bell")
let judgeClosure = { [unowned student] (answer: Answer) in
    print("student===========\(student)")
}
student.replyClosure = judgeClosure
student = Student(name: "Tom")
student.replyClosure(.C)
// 打印结果
// ==========Student init==========Kate Bell
// ==========Student init==========Tom
// ==========Student deinit==========Kate Bell
```

可以看到，名为`"Kate Bell"`的`student`对象正常销毁了，但是`Tom`学生对象并没有正常销毁，这是由于`replyClosure`闭包在其内部捕获了`self`造成的循环引用。此时他们之间的引用关系为：

![closure004.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/9c0669bdd5c842c5bcb16b3439ca9d98~tplv-k3u1fbpfcp-watermark.image)

对于这种情况使用`unowned`并不能解决循环引用问题，所以只能采用另一种解决循环引用的方案**弱引用(weak)**,来告诉`closure`**当`closure`所捕获的对象已经被释放时，就不用在访问这个对象了。**

### 6、弱引用（weak）

##### 6.1、使用weak处理closure和类对象之间的引用循环

为了解决上面的的循环引用问题，我把`replyClosure`的代码修改为：

```Swift
lazy var replyClosure: (Answer) -> Void = { [weak self] _ in
     print("replyClosure self=============\(self)")
 }
```

重新执行代码，可以看到`Tom`学生对象可以正常释放了：

```Swift
// ==========Student init==========Kate Bell
// ==========Student init==========Tom
// ==========Student deinit==========Kate Bell
// ==========Student deinit==========Tom
```

让`self`为弱引用后，`student`之间的引用关系是：

![closure005.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/0a6d3a6872634f52b07d06a97fd3a253~tplv-k3u1fbpfcp-watermark.image)

当我使用了`weak`时，就意味这这个对象可能为`nil`,而在`closure`里捕获和使用一个`Optional`的值可能会发生一些不可预期的问题，此处需要做`unwrap`操作：

```Swift
lazy var replyClosure: (Answer) -> Void = { [weak self] _ in
     guard let value = self else { return }
     print("replyClosure self=============\(value)")
}
```

当离开作用域后，`student`和`closure`的引用计数都为0，他们的内存就会合理的释放，他们之间的引用关系如下图所示：


![closure006.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/aef0d9a77af74ad9a9ee915350db41da~tplv-k3u1fbpfcp-watermark.image)

关于`closure`和类对象之间的循环问题，如何判断两者之间是否会产生循环引用，要根据一个类对象是否真的拥有正在使用的`closure`。如果类对象没有持有这个`closure`，那么就不必考虑循环引用问题。

##### 6.2、 weak并不能解决所有的循环引用问题

虽然`unowned`和`weak`能够解决`Closure`和类实例之间的循环引用问题，但这并不表示在任何`Closure`中都可以使用这种方案来解决问题。相反有时候滥用弱引用还会给带来一些诡异的麻烦和内存问题。

###### 6.2.1、滥用弱引用可能会造成一些不必要的麻烦

这里我同样用`Student`类来举例，为学生添加一个写作业的任务：

```Swift
func doHomeWork() {
   // 全局队列
   let queue = DispatchQueue.global()
   queue.async { [weak self] in
          print("\(self?.name):开始写作业")
          sleep(2)
          print("\(self?.name):完成作业")
   }
}
// 模拟做家庭作业
Student(name: "Kate Bell").doHomeWork()
```

打印结果：

```Swift
==========Student init==========
==========Student deinit==========
Optional("Kate Bell"):开始写作业
nil:完成作业
```

为什么完成的作业是`nil`呢？实际上这里并不需要使用弱引用，因为`async`方法中使用的`closure`并不归`student`对象持有，虽然`closure`会捕获`student`对象，但这两者之间并不会产生循环引用，反而因为弱引用的问题学生对象被提前释放了，但是如果这里我使用了强制拆包就又可能导致程序Crash。所以正确的理解`closure`和类对象的引用关系并合理的使用`weak`和`unowned`才能从本质上解决问题。

###### 6.2.2、使用withExtendedLifetime改进这个问题

那么有没有方法可以避免在错误的使用了`weak`之后造成的问题呢？这里可以使用Swift提供的`withExtendedLifetime`函数，它有两个参数： 第一个参数是要延长生命的对象，第二个对象是`clousre`，在这个`closure`返回之前，第一个参数会一直存活在内存中，修改`async closure`里的代码如下：

```Swift
let queue = DispatchQueue.global()
queue.async { [weak self] in
   withExtendedLifetime(self) {
      print("\(self?.name):开始写作业")
      sleep(2)
      print("\(self?.name):完成作业")
   }
}
```

重新编译代码，打印结果：

```Swift
==========Student init==========
Optional("Kate Bell"):开始写作业
Optional("Kate Bell"):完成作业
==========Student deinit==========
```

###### 6.2.3、改进withExtendedLifetime语法

虽然`withExtendedLifetime`能过解决弱引用问题，如果有很多地方有要这样访问对象这样就很麻烦。这里有一个解决方案是对`withExtendedLifetime`做一个封装，,给`Optional`做一个扩展（extension）处理:

```Swift
extension Optional {
    func withExtendedLifetime(_ body: (Wrapped) -> Void) {
        guard let value = self else { return }
        body(value)
    }
}
```

调用的代码： 

```Swift
func doHomeWork() {
    // 全局队列
    let queue = DispatchQueue.global()
    queue.async { [weak self] in
        self.withExtendedLifetime { _ in
            print("\(self?.name):开始写作业")
            sleep(2)
            print("\(self?.name):完成作业")
        }
    }
}
```

最终打印的结果和之前一样，并且我还可以其他地方调用：

```Swift
==========Student init==========
Optional("Kate Bell"):开始写作业
Optional("Kate Bell"):完成作业
==========Student deinit==========
```

本文主要介绍了`closure`基本概念、`closure`的类型、`closure`和类对象之间的内存问题及其解决方法，如果您发现我的理解有错误的地方，请指出。

---

本文参考：

[The Swift Programming Language: Closures](https://docs.swift.org/swift-book/LanguageGuide/Closures.html)

[The Swift Programming Language: Automatic Reference Counting](https://docs.swift.org/swift-book/LanguageGuide/AutomaticReferenceCounting.html#ID57)

[容易让人犯错的closure内存管理](https://boxueio.com/series/understand-ref-types/episodes/184)

[本文demo](https://github.com/dengfeng520/RPDemo/tree/main/Closures)