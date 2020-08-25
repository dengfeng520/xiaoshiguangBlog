Array是在开发中最常用的数据类型之一，[官方文档](https://developer.apple.com/documentation/swift/array)对Array的定义是：`An ordered, random-access collection.`。通常表示一个有序的集合，这里所说的有序并不是大小有序，而是指`Array`中元素的先后位置关系。

### 1、Swift的方式来操作数组

* 创建Array的N种方法

```Swift
var testArray1: Array<Int> = Array<Int>()
var testArray2: [Int] = []
var testArray3 = [Int]()
var testArray4 = testArray3
```

在上面的代码块中，`Array<Int>`和`[Int]()`没有本质区别，随便使用哪种方式都可以初始化一个`Array`。而最后一种直接使用了一个空的`Array`生成了一个新的`Array`对象。

* 定义数组时同时指定初始值的方法

```
var testInts = [Int](repeating: 6, count: 3) // [6, 6, 6]
var sixInts = testInts + testInts // [6, 6, 6, 6, 6, 6]
```

* **count**和**isEmtpy**属性

  `count`返回数组集合中元素的个数，类型是`Int`

  ```
  let testArray = ["one","two","three","four","five","six"]
  let countInts = testArray.count // 6
  ```
  
  `isEmpty`表示数组是否为空，类型是`Bool`再对数组操作前我们可以先判断数组是否为空再下一步操作
  
```
let testArray = ["one","two","three","four","five","six"]
if testArray.isEmpty == false {
    let mapArray = testArray.map({ (mapString) -> String in
         return mapString
    })
} else {
    print("数组为空")
}
```

*  访问**Array**中的元素

  * 通过索引访问数组的某个元素

  在几乎所有的开发语言中都惯用索引（或下标）的方式访问数组数组中的元素，因为我们在使用索引访问数组的元素时没法保证索引的安全性，所以这种方式存在数组越界的风险，稍不注意就会导致整个程序直接`Crash`，从而给用户造成不好的体验。
  当然`Swift`也支持这种方式，但苹果却不推荐开发者使用这种方式访问数组元素。

  ```
  let testArray = ["one","two","three","four","five","six"]
  let sevenStr = testArray[7]
  ```

  如上面的代码，使用这种方式访问数组元素，如果直接访问不做处理会导致程序直接`crash`，在调试时，可以看到编译器直接报错：`Thread 1: Fatal error: Index out of range`, 所以在使用索引访问数组元素时应该慎重在慎重。那么在`Swift`中如何优雅的访问数组元素呢？事实上，Swift的设计者是不推荐开发者使用这种下标取值的方式。
  
  使用type方法可以看到，这种方式返回的值为`String`类型，这说明在使用下标取值时需要确认，取到的值为String类型，而不是`Optionals<String>`类型，我们在使用这种方式取值时没有`Optionals`保护数组越界的情况，在使用下标取值的方法时我们就要承担**crash**的风险。


  * 访问数组中某个范围内的元素

    在`Swift`中我们可以通过使用`range operator`访问数组的一个范围,通过`range operator`方式得到的并不是一个`Array`,而是一个[ArraySlice](https://developer.apple.com/documentation/swift/arrayslice),[官方文档](https://developer.apple.com/documentation/swift/arrayslice)对`ArraySlice`的定义是`A slice of an `Array`, `ContiguousArray`, or `ArraySlice` instance.`通俗来说，就是`Array`某一段内容的`View`,不保存数组的内容，只保存这个`view`引用的数组的范围。我们可以通过这个`view`创建一个新的`Array`。

    ```
    let testArray = ["one","two","three","four","five","six"]
    let rangeArray = Array(testArray[0...2])
    ```

* 向数组中添加或删除元素

  向数组的末尾添加元素，可以使用`append`方法, 或者使用`+=`的方式:

  ```
  var testArray = ["one","two","three","four","five","six"]
  testArray.append("seven") // ["one", "two", "three", "four", "five", "six", "seven"]
  testArray += ["eight","nine"] // ["one", "two", "three", "four", "five", "six", "seven", "eight", "nine"]
  ```

  如果要在`Array`中间位置添加元素，可使用`insert`方法：

  ```
  var testArray = ["one","three","four","five","six"]
  testArray.insert("two", at: 1) // ["one","two","three","four","five","six"]
  testArray.insert("seven", at: 7) // Thread 1: Fatal error: Array index is out of range`
  ```

`insert`方法的第一个参数表示要插入的值，第二个参数表示要插入的位置索引，此处要注意的是：**这个位置必须是一个合法的范围，即`0...array1.endIndex`,如果超出这个范围就会导致程序`Crash`**

删除数组中的元素:

删除数组中的最后一个元素可以使用`removeLast()`方法，删除数组中的第一个元素可以使用`removeFirst()`方法，

```
var testArray = ["one","two","three","four","five","six","seven"]
testArray.removeLast() // ["one","two","three","four","five","six"]
testArray.removeFirst() // ["two","three","four","five","six"]
testArray.remove(at: 7) // Fatal error: Index out of range
var secondTestArray = [String]()
secondTestArray.removeFirst() // Fatal error: Can't remove first element from an empty collection
secondTestArray.removeLast() // Fatal error: Can't remove last element from an empty collection
secondTestArray.popLast() // nil 
```

**当我们删除数组中的某个元素时，首先要确保数组不能为空，然后还要保证删除的索引在合法的范围内，然后在进行删除操作**


查找元素在数组中的位置:

如我要查找元素5在数组中的位置，可以使用`firstIndex`,返回的是一个`Optional<Int>`

```
let testList = [1,2,3,4,5,6]
let oneIndex = testList.firstIndex { $0 == 5 }
print("ondexIndex===============\(oneIndex ?? 0)")
```


* 数组遍历

  在开发中对`Array`每个元素的遍历也是常用操作:

  * (1)、最简单的方式

  ```
  let testArray = ["one","two","three","four","five","six"]
  for vaule in testArray {
      print(vaule)
  }
  ```

  * (2)、遍历的时候，同时获得索引和值

    使用数组对象的`enumerated()`方法，它会返回一个`Sequence`对象，包含了每个成员的索引和值。

   ```
    let testArray = ["one","two","three","four","five","six"]
    for (index, value) in testArray.enumerated() {
       print("\(index): \(value)")
    }
   ```

 * (3)、通过闭包`closure`遍历
  
    通过闭包`closure`，我们可以使用`Array`的`forEach`方法来遍历数组:
 
  ```
  let testArray = ["one","two","three","four","five","six"]
      testArray.forEach { (vaule) in
          print(vaule)
      }
   ```

 `for in`和 `forEach`遍历的效果是一样的，但是如果使用`forEach`方法遍历数组就不能通过`break`或`continue`来退出循环，如果要退出遍历就只能使用`for in`方法。

   ```
   testArray.forEach { (testChar) in
    if testChar == "three" {
           continue // 'continue' is only allowed inside a loop
       }
    }
  ```

   * (4)、map 、 filter 遍历数组
   
   事实上，Swift的设计者并不推荐开发者使用传统的C语言风格的for循环。Swift为开发者提供了更高级的方法，当我们需要循环一个数组时，可以根据自己的需求，使用Map 和 Filter来实现。
   
   举例一：老师现在已经得到了学生的试卷成绩，再为每个学生加上平时成绩30分，这样可以得出学生的最终成绩,如果使用传统的C语言循环来实现：
   
   ```
   let fractionArray: [Int] = [40,53,59,43,56,54,33,55,66,70,22,69]
   var finallyList = [Int]()
   for num in fractionArray {
        finallyList.append(num + 30)
   }
   // [70, 83, 89, 73, 86, 84, 63, 85, 96, 100, 52, 99]
   ```
   
在看看Swift为开发者提供的**map**方法：
   
   ```
   let fractionArray: [Int] = [40,53,59,43,56,54,33,55,66,70,22,69]
   let finallyArray: [Int] = fractionArray.map {
       return $0 + 30
   }
   // [70, 83, 89, 73, 86, 84, 63, 85, 96, 100, 52, 99]
   ```
   
`map`用于把数组中的所有元素按照指定规则操作变换并返回一个新的数组，这样比使用for循环更具表现力。
   
   **使用`map`来完成这类操作，大大提高了代码的可读性；
   同时使用`map`直接返回了一个常量数组；**
   
   当然`Swift`的设计者只是对`map`封装了`for`循环的代码，其核心代码如下：
   
   ```
   extension Array {
    public func myselfMap<T>(_ transform: (Element) -> T) -> [T] {
         var tmp: [T] = []
         tmp.reserveCapacity(count)
        
         for value in self {
             tmp.append(transform(value))
         }
         return tmp
      }
   }  
   ```   
    举例二： 老师在算出学生成绩后要查看那些人的成绩是优秀，此时我们可以使用`filter`
    
    ```
    let excellentArray = finallyArray.filter {
       $0 >= 85
    }
    // [89, 86, 85, 96, 100, 99]
    ```
   **map在遍历数组的同时可以对每个参数做指定规则的操作，同时返回一个新的数组；
   filter只按指定规则遍历数组，同时返回一个新的数组**
   
   `filter`是怎么实现的呢，我们可以根据`map`的实现方法来实现,其核心代码如下：
   
   ```
   extension Array {
    func myfilter(_ predicate: (Element) -> Bool) -> [Element] {
        var tmp: [Element] = []
        for value in self where predicate(value) {
            tmp.append(value)
        }
        return tmp
    }
}
   ```
    
   * (5)、min 、 max
   
   同样老师如果需要计算本次考试的最高分和最低分，只要数组中的元素实现了`Equatable protocol`协议，开发者不用对数组进行任何操作，可直接调用`min 、 max`即可：
   
   ```
   finallyArray.min() // 52
   finallyArray.max() // 100
   ```

* `sorted`和`partition`对数组进行排序

接上面，如果老师要对本班成绩按从大到小或者从小到大的顺序排列，这样可以更直观的看到本班的成绩：

```
let mixArray = finallyArray.sorted() // [52, 63, 70, 73, 83, 84, 85, 86, 89, 96, 99, 100]
let maxArray = finallyArray.sorted(by: >) // [100, 99, 96, 89, 86, 85, 84, 83, 73, 70, 63, 52]
```

* 判断两个数组是否相等

```
finallyArray.elementsEqual(mixArray, by: {
    $0 == $1
}) // false
```

* 数组是否是以特定的排序开头

如老师要计算出本次考试成绩，是否有满分的:

```
mixArray.starts(with: [100], by: {
    $0 == $1
}) // true
```

* 计算数组中元素和

 例如老师要计算本次考试的总成绩，然后计算出本次考试的平均成绩：
 
 如果使用C语言风格，我们首先要对成绩做一个遍历，然后累加最终得到全班成绩总和：
 
 ```
 var allNum = Int()
 for num in finallyList {
    allNum = allNum + num
 }
 allNum / (finallyList.count)
 ```
 
 `Swift`为开发者提供了更简单有效的方法：
 
 ```
 finallyList.reduce(0, +) / (finallyList.count) // 81
 ```
 
* 按条件把数组中的元素分类

例如老师要分别统计出及格和没及格的成绩,我们认为60分为及格：

```
 let pass = mixArray.partition(by: {
    $0 > 60
   })
  let failedArray = mixArray[0 ..< pass] // 不及格的 [52]
  let passArray = mixArray[pass ..< mixArray.endIndex] // 及格的 [63, 70, 73, 83, 84, 85, 86, 89, 96, 99, 100]
```

  
### 2、Array和NSArray


* `Array` 是结构体，属于值类型, `NSArray` 是类，属于引用类型。

* `Array`是否可以被修改完全是通过`var`和`let`关键字来决定的，`Array`类型自身并不解决它是否可以被修改。

* `Array`如何转换为`NSArray`

   参考： [stackoverflow Swift - How to convert a swift array to NSArray?](https://stackoverflow.com/questions/35811482/swift-how-to-convert-a-swift-array-to-nsarray)

* 赋值时内存

  看下面两段代码
  
  (1) 、 `Array`的`copy`

  ```
  var testArray = ["one","two","three","four","five","six"]
  let copyArray = testArray
  testArray.append("seven") // ["one", "two", "three", "four", "five", "six", "seven"]
  print(copyArray) // ["one", "two", "three", "four", "five", "six"]
  ```

   (2) 、 `NSArray`的`copy`

  ```
  let mutArray = NSMutableArray(array: ["one","two","three","four","five","six"])
  let copyArray: NSArray = mutArray
  mutArray.insert("seven", at: 6) // ["one", "two", "three", "four", "five", "six", "seven"]
  print(copyArray) // ["one", "two", "three", "four", "five", "six", "seven"]
  ```

  
  第一段代码中：复制`testArray`数组后并向`testArray`中添加元素，`copyArray`的内容并不会改变，因为在复制`testArray`数组时，并不是内容复制，而是内存地址复制，两个数组仍旧共用同一个内存地址，只有当修改了其中一个`Array`的内容时，才会生成一个新的内存。

  第二段代码：复制`mutArray`数组，尽管`copyArray`为`NSArray`,此时我希望`copyArray`的值是不会改变的，但实际上当我修改了`mutArray`的值后`copyArray`的值也随之改变了。由于这个赋值执行的是引用拷贝，这两个数组指向的是同一个内存地址，所以当我们修改`mutArray`的内容时，`copyArray`也就间接受到了影响。

  * 当我门使用`NSArray`和`NSMutableArray`时，`Swift`种的`var`和`let`关键字就不起作用了。

  
  本文主要介绍了`Swift`中最常用的集合`Array`的一些基本用法，和`Objective-C`的`NSArray`的区别。
  
  友情链接：
  
  [Apple developer Array](https://developer.apple.com/documentation/swift/array)
  
  [泊学：Swift 3 Collections](https://boxueio.com/series/collection-types)
  
  [极客时间：集合类](https://time.geekbang.org/course/detail/100034001-134747?utm_campaign=guanwang&utm_source=baidu-ad&utm_medium=ppzq-pc&utm_content=title&utm_term=baidu-ad-ppzq-title)
