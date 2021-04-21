---

title: Swift Protocol(协议)

date: 2021-03-31 19:29:56

cover: /img/girl025.webp

---



### 1、协议语法

在Swift中协议的语法和`class、struct`非常类似，不同的是协议是采用`protocol`关键字修饰的：

```Swift
protocol name {
   // do something...
}
```



#### 2、协议要求

协议要求

#### 3、协议组合

协议组合

#### 4、协议继承

协议继承

#### 5、可选协议

可选协议

##### 5.1、采用Swift的方式

```Swift
protocol Named {
    var name: String { get }
    var lastName: String { get }
}
```

对协议做扩展处理即可：

```Swift
extension Named {
    var lastName: String {
        return ""
    }
}
```

使用可选协议：

```Swift
class Student: Named {
    var name: String = String()
}
```

##### 5.2、采用`Objective-C`的`optional`关键字

在Objective-C中的协议一般使用`optional`和`required`关键字来标明该协议是否必须实现，在Swift中也可以使用这种方式：

```Swift
@objc protocol StudyCourse {
    @objc optional func configCourse(course: String)
}

class Student: Named {
    var name: String = String()
}
```

