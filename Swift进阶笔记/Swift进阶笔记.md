<h1><center>Swift笔记</center></h1>

<h6 align='right'>小时光</h6>
<h6  align='right'>西安乐推网络科技有限公司</h6> 
--


> 1、可变元素和不可变元素

在Objective-C中，通常标记一个对象可变和不可变的方法：

```
//不可变
NSArray *ary = [NSArray alloc] init];
//可变
NSMutableArray *mutableAry = [NSMutableArray alloc] init];
```
在Swift中标记一个对象可变和不可变的方法:

```
//不可变
let ary = Array<Any>()
//可变
var mutableAry = Array<Any>()
```

**可变是指对象的值是可变的，对象的类型是不可变的**

```
var test = 123
test = "123"
```

如上面代码就会报错`Cannot assign value of type 'String' to type 'Int'`。

对数组的一些操作：

```
mutableAry.append(8)
mutableAry.append(contentsOf:[2,3])
mutableAry.insert(9, at: 5)
mutableAry.remove(at: 2)
mutableAry.removeAll()
```

**使用let定义的类实例对象（也就是说对于引用类型）时，它保证的是这个引用永远不会发生变化，你不能再给这个引用赋一个新的值，但是这个引用所指向的对象却是可以改变的。**

对数组索引的一些常规操作：

* 1、数组遍历

 ```
 array.forEach({ (obj) in
 
 })
 ```
 
 ```
 for obj in data {
            
 }
 ```
 
* 2、遍历除第一个元素外的数组其余部分

 ```
 for obj in data.dropFirst() {
            
 }
 ```
*  3、遍历除最后一个元素外的数组其余部分
 
 ```
 for obj in data.dropLast() {
            
 }
 ```
 
* 4、遍历除了最后5个元素之外数组元素

 ```
 for obj in assayAry.dropLast(5) {
            
 }
 ```
 
*  5、列举数组中的元素和对应的下标

 ```
 for (num,element) in assayAry.enumerated() {
            
 }
 ```
 
