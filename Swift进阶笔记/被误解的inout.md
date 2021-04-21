---
title: 被误解的inout
date: 2021-03-17 21:33:56

cover: /cartoon/cartoon014.webp
---
<h6 align='right'>小时光</h6>
<h6 align='right'><a href='https://dengfeng520.github.io/'>我的博客</a></h6>

在Swift中，函数的参数默认都是常量是不可以修改的，如果我需要在函数内部修改函数的某个参数，或者通过参数返回内容，就需要用`input`关键字来修饰这个参数。

如老师需要为学生的笔试成绩加上30分的平时成绩，就需要用到`input`参数：

```swift
func configStudent(_ fraction: inout Int) -> Int {
    return fraction + 30
}

var num = 53
configStudent(&num)
```

在其他C系列语言中，如`C/C++`和`Objective-C`中，一般会认为是在传递变量的指针。实际上我在此之前也是这么认为的，直到在读到[官方文档](https://docs.swift.org/swift-book/)函数部分章节后才知道其实`inout`关键字的用途并非如此。

#### 1、inout 只是 in - out

在官方文档中是这样说的：

`You write an in-out parameter by placing the inout keyword right before a parameter’s type. An in-out parameter has a value that’s passed in to the function, is modified by the function, and is passed back out of the function to replace the original value. `

这里说的很明确，`inout`就是把参数传递给函数，被函数修改之后再替换初始值。也就是说`inout`就只是简单的`in-out`而没有其他的意思。

#### 2、inout的使用

###### 2.1、变量可以、常量不可以

*  （1） 、使用`inout`只能修饰变量

```swift
func configStudent(_ fraction: inout Int) -> Int {
    return fraction + 30
}
let num = 53
configStudent(&num)
```

在编译的时候，系统会直接报错`Cannot pass immutable value as inout argument: 'num' is a 'let' constant`

修改代码`var num = 53`编译成功，

```swift
var num = 53
configStudent(&num)
```

* （2） 、可变数组中的常量也可以用`inout`

但是同样的，如果我修改一个可变数组中的参数吗？

```swift
var testArray = [1,2,3,4,5,6]
testArray[1] = configStudent(&testArray[1]) // [1, 32, 3, 4, 5, 6]
```

编译代码，正常运行，说明数组中的下标操作也可以使用`inout`来修饰参数。

* （3）、自定义类型属性使用`inout`关键字

如我现在要记录学生在上课签到时的位置，在得到学生的位置信息后再做一次校正处理，使用自定义类型，代码如下：

```swift
struct Location {
    var lat: Double
    var lon: Double
}

func configLocation(_ num: inout Double) -> String {
    return "\(num + 0.01)"
}

// 调用
var location = Location(lat: 37.33020, lon: -122.024348)
configLocation(&location.lat)
```

编译代码，正常运行。

使用上面的代码，我在得到学生的位置不仅要知道经纬度,还想知道这个学生的平时成绩，一般默认情况下老师会给所有学生设置平时成绩为30分，新增`fraction`属性作为平时成绩，代码如下：

```swift
struct Location {
    var lat: Double
    var lon: Double
    
    var fraction: Int {
        return 30
    }
}

func configFraction(_ fraction: inout Int) -> Int {
    return fraction + 30
}
configFraction(&location.fraction)
```

此时我再次运行代码，编译时直接报错: `Cannot pass immutable value as inout argument: 'fraction' is a get-only property`,也就是说对于自定义类的只读属性，不能使用`inout`参数；

 最终的结论是：**自定义类型的属性同时有`get`和`set`方法，也可以作为`inout`参数。自定义类型中的只读属性不能使用`inout`参数**

---

本文主要说了我曾经对`inout`修饰参数的一些误解，及`inout`修饰参数的使用，如果有不对的地方请指出。

本文参考:[the swift programming language：Functions](https://docs.swift.org/swift-book/LanguageGuide/Functions.html)

[本文代码](https://github.com/dengfeng520/RPDemo/tree/main/Closures)

