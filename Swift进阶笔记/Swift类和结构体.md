---
title: Swift 类和结构体
date: 2020-03-31 11:56:56
cover: /img/girl025.webp
---
<h6 align='right'>小时光</h6>

### 1、值类型和引用类型

在iOS中虚拟内存分为五大内存分区：**堆区、栈区、全局区、常量区、代码区**。在Swift中根据对象在内存的存储位置不同分为值类型和引用类型。

* 值类型： Swift中的值类型主要有：enum，struct、Array、Dictionary、Tuple`等

* 引用类型：Swift中的引用类型主要有：`methods,class,clousre`

##### 1.1、值类型和引用类型的内存分配和管理方式

Swift 中的值类型，一般存储于栈内存中（也不一定），由于栈的特性这部分内存是由CPU直接管理和优化的，对于值的copy也是深拷贝(deep copy)，当使用完一个值后系统会立即释放这部分内存。所以存储于栈上的内存在创建、使用、释放都非常效率。

一般情况下，当创建一个`struct`默认被存储于栈区。当编译器侦测到结构体变量被一个函数闭合的时候，此时这个结构体将存储在堆上。此处可参考[《Swift 进阶》](https://objccn.io/products/advanced-swift/)一书中的**结构体和类章节 闭包和可变性**小节的内容。

Swift中的引用类型，一般存储于堆区，苹果采用ARC的方式来管理这部分内存，对于这部分内存的追踪就是对引用计数的追踪，当类对象被引用时引用计数+1，当引用计数为0时，ARC会释放这部分内存。

### 2、复杂的值类型`struct`

Swift提供了多种可以结构化存储数据的方式，`struct、enum、class`等。这里先说经常在开发中用作`Model`来使用的`struct`。

##### 2.1、struct的定义和初始化

从定义一个`struct`开始，这里我定义一个用来表示颜色的`struct`

```Swift
struct Color {
    var red: Double
    var green: Double
    var blue: Double
    // 透明度
    var alpha: Double? = 1
}
```

我定义了四个属性，`red、double、blue`表示红绿蓝三色值，`alpha`表示透明度。这些属性所占用的内存空间决定了`Color`的大小。当我定义完四个属性后直接build,没有任何问题，这里首先来了解`struct`的初始化方法。

###### 2.1.1、`Memberwise initializer`

当我定义一个`struct`而不为其创建任何`init`方法，也可以正常运行，这是由于Swift编译器自动为这个`struct`创建了一个初始化方法，这种`init`方法叫做`Memberwise initializer`.当我需要使用这个`struct`时可以这样初始化一个`color`对象：

```Swift
var color = Color(red: 200, green: 200, blue: 200)
```

###### 2.1.2 、`Default initializer`

如果我想在创建`color`对象的时候不指定参数,自动的给属性设置默认值，这时有两种方法可供选择：

* 在定义每个属性时都为其设置默认值

  ```Swift
  struct Color {
      var red: Double = 0
      var green: Double = 0
      var blue: Double = 0
      
      var alpha: Double? = 1
  }
  ```

  这样就可以在初始化`color`对象时，不用指定参数来初始化`color`对象了。

  ```Swift
  var color = Color()
  ```

  这么做的要求就是在设置属性时必须为每个属性都设置默认值，因为Swift要求`init`方法必须初始化自定义类型的每一个属性。

* 在`init`方法中为每个属性设置默认值

```Swift
struct Color {
    var red: Double
    var green: Double 
    var blue: Double
    
    var alpha: Double?
    
    init(red: Double = 0, green: Double = 0, blue: Double = 0, alpha: Double? = 1) {
        self.red = red
        self.green = green
        self.blue = blue
        self.alpha = alpha
    }
}
```

这和之前直接给属性设置默认值的方法效果是一样的。当为某个`struct`创建了`init`方法后一定要保证其正确性，因为当重写了`init`方法后，系统就不会在创建默认的`init`方法了。

###### 2.1.3、Failable init

在使用`struct`作为model时，如果要将其显示在界面上最终都需要转换成字符串`String`，这里可以使用系统提供的`Codable`协议来把服务器返回的进行转换，当然也可以使用其他开源库来完成这些操作。由于在初始化时可能会解析`Data`数据失败，这里采用`init?`的方式来初始化，当解析失败时，直接返回`nil`。

```Swift
struct Color {
    var red: Double
    var green: Double 
    var blue: Double
    
    var alpha: Double? = 1
  
    private enum CodingKeys: String,CodingKey {
        case red
        case green
        case blue
        case alpha
    }
}

extension Color: Codable {
    init?(data: Data) {
        guard let model = try? JSONDecoder().decode(Color.self, from: data) else { return nil }
        self = model
    }
}
```

###### 2.1.4、`Type property`设置一个常用的值

对于一个`struct`经常会使用的值，我们可以采用在`struct`中定义成`Type property`。如App的主题颜色是我在代码中要经常使用的，可以采用`Type roperty`的初始化方式。

```Swift
extension Color {
    static let themeColor = Color(red: 200, green: 200, blue: 200, alpha: 1)
}
// 当我要使用App主题颜色时
let color = Color.themeColor
```

当创建了默认主题颜色时，它不是`struct`对象的一部分，因此不会增加`	color`对象的大小,还可以使代码看起来更简洁明了。

##### 2.2、为struct添加方法

在Swift中不仅可以为`struct`添加属性还可以添加方法，只不过`struct`的方法，默认都是只读的，例如我要为`Color`添加一个修改透明度的方法：

```Swift
func modifyWith(alpha: Double) {

}
```

##### 2.3、mutating关键字

当我要在`struct`的方法中修改`struct`中的某个属性值时，要在这个方法前面加上`mutating`关键字。当添加`mutating`之后，Swift会隐式的把`self`标记为`inout`,这样就可以在方法中修改`struct`中的属性值。

```Swift
mutating func modifyWith(alpha: Double) {
    self.alpha = alpha
}
```

##### 2.4、修改`struct`值

我定义了一个`color`对象，为了更好观察这个变量被修改时发生了什么，给他添加一个`didSet clousre`,只要`color`的值发生变化，就可以看到打印的内容：

```Swift
var color = Color(red: 200, green: 200, blue: 200) {
    didSet {
        print("color============\(color)")
    }
}
```

现在我修改`color`的值，再看打印的结果。

```Swift
let colorB = Color(red: 100, green: 100, blue: 100)
color = colorB
// 打印结果
color============Color(red: 100.0, green: 100.0, blue: 100.0, alpha: Optional(1.0))
```

这里修改了`color`的值所以触发了`didSet`方法。

如果只修改`color`其中某个属性值,如我要修改`red`属性值为110，	

```Swift
color.red = 110
// 打印结果
color============Color(red: 110.0, green: 100.0, blue: 100.0, alpha: Optional(1.0))
```

可以看到依旧会打印，也就是说**只要修改`color`的任何一个属性值，其实整个`color`变量都被修改了**。

### 3、引用类型`class`

##### 3.1、class的定义和初始化

当我定义一个表示颜色的类`MyColor`,为其设置四个属性：

```Swift
class MyColor {
    var red: Double
    var green: Double
    var blue: Double

    var alpha: Double? = 1
}
```

这里我让这个类没有父类，也可以根据情况设置其父类。如果没有没有fu此时编译器会提示`Class 'MyColor' has no initializers`,这是由于**类是引用类型必须有一个完整的生命周期，类必须被明确的初始化、使用、最后被明确的释放**。所以当我定义了一个类时必须明确的构建`init`方法。这也是`class`和`struct`的一个区别之一。

###### 3.1.1、默认init

一般的最简单的初始化方法可以直接调用`init`方法

```Swift
let color = MyColor()
```

如果我想像这样初始化一个`color`对象，可以使用`class`默认的初始化方法，`class`的默认初始化方法有两种：

* 为每个属性设置默认值

  ```Swift
  class MyColor {
      var red: Double = 0.0
      var green: Double = 0.0
      var blue: Double = 0.0
  
      var alpha: Double? = 1
  }
  ```

  再次运行代码，就可以编译成功。这种方式的确解决了编译报错问题，如果我想在初始化时为类的每个属性设置默认值就会报错，所以为每个属性都设置默认值的方式只适合表意简单的并且初始值固定或者在其内部赋值的`class`。如果我想类的外部为其设置属性值，可以采用其他的初始化方式：

  ```Swift
  let color = MyColor(red: 100, green: 100, blue: 100) // Argument passed to call that takes no arguments
  ```

* 在`init`方法中为每个属性设置默认值

```Swift
init(_ red: Double = 0, _ green: Double = 0, _ blue: Double = 0, _ alpha: Double? = 1) {
     self.red = red
     self.green = green
     self.blue = blue
     self.alpha = alpha
}
// 初始化
let mycolorA = MyColor()
let mycolorB = MyColor(100, 100, 100)
```

这样就可以根据实际的需求来初始化`MyColor`类了。在Swift中，**初始化类的`init`方法必须定义在`class`内部，而不能定义在`extension`里**，否则会导致编译错误。**而`struct`的`init`方法是可以定义在`extension`中**，这也是`class`和`struct`的区别之一。

##### 3.2、 Convenience init

如果构造方法前面没有`convenience`关键字称作**便利构造方法**。如果没有称作**指定构造方法**。

* 便利构造方法： 初始化方法前有`convenience`关键字，不用对所有的属性进行初始化，因为便利构造方法依赖于指定构造方法。如果想给系统提供的类提供一个快捷创建的方法，就可以自定义一个便利构造方法
* 指定构造方法：必须对所有的属性初始化

```Swift
convenience init(at: (Double, Double, Double, Double?)) {
    self.init(at.0, at.1, at.2, at.3)
}
```

##### 3.3、Failable init

在大多数时候和服务器交互数据为了统一和方便，会把所有的数据都采用字符串（String）格式， 这就需要在初始化的时候做一些处理：

```Swift
convenience init?(at: (String, String, String, String?)) {
     guard let red = Double(at.0), let green = Double(at.1), let blue = Double(at.2) else {
          return nil
     }
     self.init(red, green, blue)
}
```

由于`String`的`init`可能会失败，这里采用可选的形式来定义。在其实现中，如果`String`转`Double`失败，就返回`nil`，表示初始化失败。

### 3、比较`struct`和`class`

前面分别简单介绍了`struct`和`class`，这里对这两者做一个比较：

##### 3.1 `struct`和`class`的共同点

* 都可以定义属性并用来保存值
* 都可以构建方法
* 都可以设置其每个属性的初始值以设置其初始状态
* 都可以采用下标的方式来访问他的值
* 都可以对其做`extension`操作，用来扩展其超出默认实现的功能
* 都可以遵循某个协议用来提供某些标准的功能

##### 3.2、struct`和`class`的区别

* `struct`会默认生成`init`方法，`class`必须明确指定init方法

* `struct`不能继承（但是可以遵循协议）,`class`是可以继承的
* `struct`更多的是关注其值，当我修改`struct`其中任意属性值时整个`struct`都会被重新修改一次，`class`更多的是关注的是对象本身

### 4、struct`和`class的选择

Swift中`struct`和`class`有这么多共同点，那在实际开发中要如何选用`struct`和`class`呢，作为开发者需要根据当前的使用时机来选择使用哪种类型：

##### 4.1、默认情况下使用`struct`

一般创建一个`struct`其会被存储于栈区，因为`struct`一般不涉及到堆内存分配，无论是创建、追踪还是销毁都非常快，所以默认情况下优先选择`struct`。

##### 4.2、是否需要继承或`Protocol`

* 如果对继承和协议没有要求，优先使用`struct`
* 如果需要继承，那么只能使用`class`

##### 4.3、需要和`Objective-C`时，使用`class`

当`Swift`和`Objective-C`交互时，可以在`class`前面加`@objcMembers`，或者要调用的方法和变量前加`@objc`，在要调用的`Objecrtive-C`文件中导入`#import "工程名-Swift.h"`,即可使用调用Swift类。

```Swift
// Swift 文件中
@objcMembers class Model {
    func coverModel() {
     
    }
}
```

```Objective-C
// Objective-C 文件中
#import "工程名-Swift.h"

Model *model = [[Model alloc] init];
[model coverModel];
```

##### 4.4、需要控制身份时使用`class`

* 当需要使用``===``比较两个实例一致性时。`===`会自动检查两个对象是否完全一致，包括存储数据的内存地址

```Swift
let mycolorA = MyColor()
let mycolorB = mycolorA
if mycolorA === mycolorB {
    
}
```

* 当需要创建用于共享、可改变的数据时

##### 4.5、不控制身份时使用`struct`

* 当需要使用`==`比较实例数据，用于比较两个值是否相等

```Swift
var colorA = Color(red: 200, green: 200, blue: 200)
let colorB = Color(red: 100, green: 100, blue: 100)
if colorA.alpha == colorB.alpha {
    
}
```

* 当需要在多线程中修改值时，优先使用`struct`，因为`struct`是线程安全的

```Swift
var colorArray = [colorA, colorB, colorC, colorD, colorE]
let queue = DispatchQueue.global()
let count = colorArray.count
queue.async { [colorArray] in
    for index in 0..<colorArray.count {
        print("index=========\(colorArray[index])")
        Thread.sleep(forTimeInterval: 1)
    }
}
queue.async {
    Thread.sleep(forTimeInterval: 0.5)
    colorArray.remove(at: 2)
    print("-------\(colorArray.count)")
}
```

以上代码可以正常运行。

当然所谓的线程安全也是相对而言的，修改如下代码：

```Swift
var colorArray = [colorA, colorB, colorC, colorD, colorE]
let queue = DispatchQueue.global()
let count = colorArray.count
queue.async {
    for index in 0..<count {
        print("index=========\(colorArray[index])")
        Thread.sleep(forTimeInterval: 1)
    }
}
queue.async {
    Thread.sleep(forTimeInterval: 0.5)
    colorArray.removeLast()
    print("-------\(colorArray.count)")
}
```

再次运行代码就会Crash并打印错误：

```C
Fatal error: Index out of range: file Swift/ContiguousArrayBuffer.swift, line 444
```

这是由于我移除了数组的最后一个元素，当需要打印最后一个（也就是第五个）元素时，数组中其实已经没有这个元素了，所以会数组越界Crash。而第一段代码之所以不会Crash，是因为在新的线程中会copy一份数组内容。所以当新建一个线程操作数据时copy值类型到新线程操作是线程安全的。

本文主要介绍了`struct`和`class`及其异同点，同时简单分析了在开发中应该如何选择`struct`还是`class`。如果我的理解有不对地方欢迎指出。

---

本文参考：

[Apple Developer: Choosing Between Structures and Classes](https://developer.apple.com/documentation/swift/choosing_between_structures_and_classes)

[ The Swift Programming Language: Structures and Classes](https://docs.swift.org/swift-book/LanguageGuide/ClassesAndStructures.html)

[Swift进阶：机构体和类](https://book.douban.com/subject/27044368/)