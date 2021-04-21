---
title: Swift ARC(自动引用计数器)
date: 2020-03-02 09:25:56
cover: https://github.com/dengfeng520/xiaoshiguangBlog/blob/master/girls/girl037.jpg?raw=true
---
<h6 align='right'>小时光</h6>


Swift 采用ARC的方式来管理和追踪程序中的内存使用情况。ARC的全称（Automatic Reference Counting），一般叫做**自动引用计数**。在大多数情况下，开发者无需考虑内存管理问题，当不再需要使用实例对象时，ARC会自动释放这些内存。

ARC的引用计数一般应用于类的实例或闭包，而数组(Array)、字典(Dictionary)、字符串(String)、结构体(Structure)、枚举(enum)都是值类型，不是引用的方式来存储和传递的。官方文档的原文是：`Reference counting applies only to instances of classes. Structures and enumerations are value types, not reference types, and aren’t stored and passed by reference.`

关于值类型和引用类型的区别，可参考官方博客：[Swift: Value and Reference Types](https://developer.apple.com/swift/blog/?id=10)

> ### 1、Swift中ARC是如何工作的


#### 1.1、How ARC Works

* 每次创建一个类的实例，ARC就会自动为其分配内存，用来存储这个实例及其相关的属性
* 当该实例不再被使用时，ARC会释放这个实例所占用的内存
* 继续访问已释放的实例，如调用其方法或属性，那么可能会造成程序crash
* 为了解决访问已释放实例造成的crash问题，ARC会追踪每个引用当前实例累的属性、常量、和变量的数量。只要有一个有效的引用，ARC就不会释放这部分内存。
* 为此每次将一个类的实例赋值给一个属性（也可以是常量或变量）。这个属性就是这个实例的强引用。之所以称为强引用，是因为该属性强持有这个实例，并且只要这个强引用还存在，就不能销毁这个实例。

用代码来说明，我有一个学生类，为其设置一个`name`属性用来保存这个学生的姓名，当我创建这类时，ARC会自动为这个类创建一部分空间用来保存`Student`实例及其属性。

为了更好的监听这个类的创建和销毁，我分别在`init`和`deinit`方法中通过打印来监听。

```swift
class Student: NSObject {
    var name: String
    init(name: String) {
        self.name = name
        print("init------------------Student")
    }
    
    deinit {
        print("deinit------------------Student")
    }
}
```

```swift
var studentTom: Student? = Student(name: "Tom") // 引用计数为1
```

```swift
print("init------------------Student")
```

运行上面的代码，此时可以打看打印结果,说明此时的引用计数为1。此时没有释放这部分内存,如果我将这个实例直接置为nil呢

```swift
studentTom = nil // 此时引用计数为0
print("deinit------------------Student")
```

当调用了`deinit`方法说明引用计数为0，ARC会自动释放该实例的内存。

```Swift
let studentName = studentTom!.name
```

当我把`studentTom`置为`nil`后，再次调用`studentTom`就会crash。Xcode同时会抛出一段异常:

```c
error: Execution was interrupted, reason: EXC_BAD_INSTRUCTION (code=EXC_I386_INVOP, subcode=0x0).
```

为了防止出现这种情况，一般在使用可选类型(Optionals)时，应该优先做解析处理。

```swift
// 可选绑定解包
if let studentTom = studentTom {
    let studentName = studentTom.name
}
// guard 语法解包
guard let studentTom = studentTom else { return }
let studentName = studentTom.name
```

如果因为需要我将该学生信息进行`copy`操作呢，此时引用计数就变成了2，为了验证我的猜想，修改代码如下：

```swift
var studentCopy: Student? = studentTom // 引用计数为2
studentTom = nil // 引用计数为1
```

再次运行代码，发现并没有调用`deinit`方法，当我进行`copy`操作的时候，其引用计数就变成了2，这时候再置为`nil`其引用计数是1，ARC并没有释放其内存。此时需要将`studentCopy`的值置空，将其引用计数清空，ARC就会自动清理这部分内存。

```swift
studentCopy = nil // 引用计数为0
deinit------------------Student
```

> ### 2、循环引用

前面说ARC为了保证被使用实例对象不被提前释放，而采用了强引用的方式。那么针对这种情况，对开发者而言是否就一劳永逸了呢，答案是否定的，当两个实例之前形成强持有环时，这两个实例的内存就永远不会得到释放，这就需要开发者来做一些处理保证这部分内存能够在不需要时得到释放。

#### 2.1、循环引用是如何产生的

 * 两个实例彼此保持对方的强引用，使得每个实例都使对方的保持有效时会发生循环引用。

   举例，现在我有一个老师类，对于老师和学生而言，老师要知道学生的信息，学生也要知道老师的信息，如老师的姓氏，所教授的课程等。

 ```swift
// 表示老师所教授的课程 
enum Course {
    case language // 语文
    case english // 英语
    case calculus // 微积分
    case quantumMechanics // 量子力学
    case geology // 地质学
}
class Teacher: NSObject {
    let lastName: String
    let course: Course
    var student: Student?
  
    init(lastName: String, course: Course) {
        self.lastName = lastName
        self.course = course
        print("init------------------Teacher")
    }
  
     deinit {
        print("deinit------------------Teacher")
     }
}

class Student: NSObject {
    var name: String
    var teacher: Teacher?
  
    init(name: String) {
        self.name = name
        print("init------------------Student")
    }
  
    deinit {
        print("deinit------------------Student")
    }
}
 ```



```swift
var studentTom: Student? = Student(name: "Tom")
var teacherMars: Teacher? = Teacher(lastName: "Mars", course: .calculus)

teacherMars?.student = studentTom
studentTom?.teacher = teacherMars

teacherMars = nil
studentTom = nil
```

运行上面的代码，发现无论如何都不会调用`deinit`方法。是因为他们各自引用这自己的对象，`studentTom`的`teacher`和`seacherMars`的`student`属性又相互引用了对方，此时在他们的引用计数都变成了2，于是就造成一个引用循环。他们之间的引用关系如下图所示：

![强引用环](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/5088863b043b449e8aae6343b3f0042d~tplv-k3u1fbpfcp-watermark.image)

#### 2.2、如何避免循环引用

为了解决上面的引用循环问题，根据属性是否可选而采取不同的解决方案，当属性为可选时可以用`weak`关键字修饰，表示该属性为弱引用。当属性不可选时，可以用`unowned`关键字来修饰。无论是`weak`还是`unowned`,他们的思路都是一样的，不让某种形式的引用增加引用计数就好了。

##### 2.2.1 弱引用

在上面的例子中，只需对任意一个属性设置为弱引用即可，当然也可以把两个属性都设置为`weak`,不过没有这么做的必要。

```swift
weak var student: Student?
```

此时两个实例之间的关系图如下所示：

![弱引用](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/10cee926d7674210b00b14c5642830ae~tplv-k3u1fbpfcp-watermark.image)

当我在弱引用下来释放`studentTom`的内存时，会是什么结果呢此时两个实例之间的关系如下所示：

```swift
studentTom = nil
teacherMars?.student
print("------------------\(String(describing: teacherMars?.student))")
```

```swift
init------------------Student
init------------------Teacher
deinit------------------Student
------------------nil
```

通过上面打印的结果来看，`studentTom`实例的内存顺利释放了，那么当`studentTom`为`nil`时，ARC根据当前的情况进行了操作呢？

* 首先`Student`对象就不再有任何`strong reference`了，`ARC`会立即回收这部分内存，同时`Teacher`对象的引用计数也会减一；
* 其次当`Student`对象被回收调之后，`teacher`这个`strong reference`也就不存在了。`Teacher`的引用计数就会减一；
* 由于`student`是一个`weak reference`,它的值会自动设置为`nil`，通过`teacherMars?.student`打印的结果为`nil`可以确认这一点。

![弱引用01](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/3c0565838b5241329594ef698da722f4~tplv-k3u1fbpfcp-watermark.image)

当我将其中任意一个属性设置为弱引用后，这时候把`teacherMars`和`studentTom`都设置为`nil`，`ARC`就能过顺利回收所有的内存，此时他们的关系如图所示：

```swift
teacherMars = nil
studentTom = nil
```

打印结果：

```swift
init------------------Student
init------------------Teacher
deinit------------------Student
------------------nil
deinit------------------Teacher
```

![弱引用02](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/5e685f24e6354696a9f99c2e1707b08c~tplv-k3u1fbpfcp-watermark.image)

关于如何使用`weak`修饰的属性总结：

* 弱引用不会增加实例的引用计数，因此不会阻止ARC销毁被引用的实例。所以使用弱引用后，即使两个实例互相持有也不会形成强引用环。
* 弱引用只能申明为变量类型，因为运行时他的值可能会改变。弱引用绝对不能申明为常量。在Swift中，用var关键字申明的为变量，用let关键字申明的为常量。
* 因为弱引用可以没有值，用弱引用来修饰的变量必须是**可选类型**。

##### 2.2.3 无主引用

虽然`weak`解决了循环引用的问题，但是不是所有的属性都是可选的，如果有一个不可以为`nil`的属性造成了循环引用，该怎么办呢？

* 我可以把这个不可为`nil`的属性修改为可以为`nil`
* 采用Swift为开发者提供的另一种解决方案，使用无主引用

和弱引用相似，无主引用也不强制有实例对象。**和弱引用不同的是，无主引用默认始终有值**。在属性和变量前添加`unowned`关键字，就可以申明一个无主引用。

为了演示这个过程，我为每个学生添加了家庭作业`homeWork`属性，当然并不是所有的学生都会按时写作业，所以`homeWork`的类型是`optional`,然后来实现`HomeWork`类；

```swift
// 家庭作业
var homeWork: HomeWork?
```

```swift
class HomeWork: NSObject {
    let student: Student
    let course: Course
  
    init(student: Student, course: Course) {
        self.student = student
        self.course = course
        print("init------------------HomeWork")
    }
    
    deinit {
        print("deinit------------------HomeWork")
    }
}
```

这里既然有了家庭作业，那么我就要知道是谁写的，是哪门课程的作业,这里`studentName`和`course`就不能是一个`optional`。

```swift
var david: Student? = Student(name: "David Taylor")
var homeWork: HomeWork? = HomeWork(student: david!, course: .quantumMechanics)
```

此处假设学生`david`完成了作业，那么可以用下面的代码来表示：

```swift
david?.homeWork = homeWork 
```

```swift
init------------------Student
init------------------HomeWork
```

运行代码，发现并没有调用`deinit`方法，此时学生`david`和`homeWork`就形成了一个引用循环，他们之间的持有的关系是`david`和`homeWork`各自引用着自己的对象，`david`和`homeWork`互相引用着彼此。

![homeWork](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/700cecb6aa074c93977d467e22d704d5~tplv-k3u1fbpfcp-watermark.image)

那么此时，我将`david`置为`nil`呢？

```swift
david = nil
```

```
init------------------Student
init------------------HomeWork
```

运行代码，发现依旧没有调用`deinit`方法，此时虽然`david`实例为`nil`,实例`homeWork`也离开了自己的作用域。此时在内存中`david.homeWork`和`homeWork.student`之间的引用关系依旧会把这两个对象保持在内存中,他们关系如下图所示：

![homework002](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7524800de1b34c93b9cf7e791a32110b~tplv-k3u1fbpfcp-watermark.image)

当然此处可以使用`weak`关键字将其中任意一个强持有改成弱引用来解决这个问题。此处也可以使用系统提供的另一种解决方案：**非可选类型的属性前加`unowned`**，无主引用解决循环引用问题。

```swift
unowned let student: Student
```

我们可以将任意一个强引用的属性前加`unowned`,就可以解决这个问题，唯一不同的是`Strong reference`变成了`unowned reference`,此时他们之间的引用关系是:

![Homework003](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a606dcd64e2048318a00a4bc076bb4dd~tplv-k3u1fbpfcp-watermark.image)

这时候再次运行代码：

```swift
david = nil
homeWork = nil
```

打印结果如下：

```swift
init------------------Student
init------------------HomeWork
deinit------------------Student
deinit------------------HomeWork
```

可以看到`david`和`homeWork`都可以正常的被回收了，当`david`为`nil`时`Student`对象就会被ARC回收，而当`homeWork`为`nil`时，`homeWork`也就失去了他的作用也会被ARC回收其内存。

![homewirkoo4](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a3075905820d4e6cb692f4fd2cea1f93~tplv-k3u1fbpfcp-watermark.image)

如果我调用被释放的内存之后会怎样呢？修改代码如下：

```
homeWork = nil
homeWork!.student
```

程序执行时会crash并提示：

```
Unexpectedly found nil while unwrapping an Optional value
```

所以使用`unowned`虽然能解决不可选属性循环引用问题，但在实际开发中也应该注意**在使用无主引用时要确保引用始终指向一个未销毁的实例**。

虽然使用`weak`和`unowned`解决了`Class`中的强引用循环问题，但是`Class`并不是Swift中唯一的引用类型，Swift中`Closure`也是引用类型，至于`Closure`如何解决内存管理问题，可参考官方文档[the swift programming language: Closures](https://docs.swift.org/swift-book/LanguageGuide/Closures.html)。


---
相关链接：

[《the swift programming language》Automatic Reference Counting](https://docs.swift.org/swift-book/LanguageGuide/AutomaticReferenceCounting.html)

[Handling non-optional optionals in Swift](https://www.swiftbysundell.com/articles/handling-non-optional-optionals-in-swift/)

[本文demo](https://github.com/dengfeng520/RPDemo/tree/main/ARC/)

