<h1><center>Runtime之指针和结构体</center></h1>

<h6 align='right'>小时光</h6>
--

> <h3>1、C语言指针</h3>


#####（1.1）、什么是指针


一般的，在定义数据对象时，需要说明对象的名称和数据类型，如


```
int test = 0;
```

申明数据类型的作用时告诉IDE要为多想分配多大的存储空间（字节），以及对象中要存储什么类型的值。

对象名称的作用是对应分配到的内存单元，允许按照名称来访问，如我定义`test`这个变量，在程序中需要使用时，就可以通过`tes`来访问这个变量。但是在一些特殊情况下如由于作用域问题无法直接访问这个变量，那么可以通过访问这个变量的内存地址来访问这个变量。

因此C语言中形象的将变量的内存地址称为**指针**，即一个对象的地址就是该对象的指针。

举个例子：我申请的int类型的`test`的指针为40001，但并不表示地址40001指的是`test`.

此处需要注意，**40001只是这个变量的首地址**，我申请的int类型的`test`在内存中占四个字节，所以40001，40002，40003，40004这四个内存地址才是`test`的内存。

所以我们常说的，**变量(对象)的指针实际上是这个变量(对象)在内存中的首地址**。

使用指针有哪些好处呢？

* 提高存储效率
* 间接访问由于作用域不可见的变量
* 访问动态的内存空间

#####（1.2）、指针和指针变量的区别

指针是地址值，指针变量是存储指针的变量，我们可以通过指针变量间接访问（或者存取）一个对象。

#####（1.3）、指针的分类

* （1.3.1）、普通的指针


```
int a = 111;
int *pa = &a;
char p = 'C';
char *pb = &p;
```

代码`int *pa = &a;`的作用是指针赋值，那么该如何验证是否赋值成功并如何使用这个值呢？


```
int *pdd = pa;
printf("取值========%d,%d\n",*pa,*pdd);


```

定义一个pdd指针，然后打印指针pdd所指向的值。最终看到的结果是：


```
取值========111,111

```
上面的代码已经完成了指针的赋值和取值操作。


![指针](https://github.com/dengfeng520/xiaoshiguangBlog/blob/master/Runtime/C_Pointer.png?raw=true)

上图演示了指针和变量在内存中的关系，111是int型，`C`是char型。`pa`是111的指针，指向111在内存中的首地址。同理pb也指向了`C`的首地址。

实际上，在内存中一个类型表示的是这变量在内存中所占的大小，因为int类型在内存中占用了4个字节的内存大小,`C`占用了一个字节的空间。

此时已经知道变量的类型是int类型、内存中的指针首地址以及该变量所占内存大小，就可以从首地址取四个字节的长度就代表着这个变量在内存中的值。

* （1.3.2）、指针的指针


```
// 指针的指针 赋值
int **ppa  = &pa; 
// 取值
printf("\n**ppa========%d",**ppa);
```

此处`ppa`指向的是`pa`的地址，`pa`指向的是int型a的地址，所以打印出来就是a的值。

![指针](https://github.com/dengfeng520/xiaoshiguangBlog/blob/master/Runtime/C_Point_Point.png?raw=true)

如上图所示：`ppa`指向的存放`&a`的首地址，即是`pa`,`ppb`指向存放`&b`的首地址。

既然普通的类型需要占用内存空间，那么指针也是需要占用内存空间的。
我们通过指针的指针拿到这个指针，再通过这个指针拿到这个指针所指向的内容。


* （1.3.3）、数组指针

指针指向的是一个对象在内存中的首地址，那么数组指针指向的是什么呢？

```
char array[] = "hello";
char *arrayPointer = array;
```

此处定义了一个char类型数组，然后取数组指针。此处我通过数组遍历对数组中的每个元素的内存地址，然后和数组指针做一次比较：

```
printf("\narrayPointer========%p\n",arrayPointer);
for (int i = 0;i<sizeof(array);i++) {
    printf("\ni,everyone======%d,%p\n",i,&array[i]);
 }

```

```
arrayPointer========0x7ffee815584a

i,everyone======0,0x7ffee815584a

i,everyone======1,0x7ffee815584b

i,everyone======2,0x7ffee815584c

i,everyone======3,0x7ffee815584d

i,everyone======4,0x7ffee815584e

i,everyone======5,0x7ffee815584f
```

可以看到数组的指针和这个数组第一个元素的指针是一样的，所以可以得出结论：__数组的指针并不是这个数组的指针，而是数组第一个元素的首地址指针__。
![数组的指针](https://github.com/dengfeng520/xiaoshiguangBlog/blob/master/Runtime/C_Pointer_Array.png?raw=true)

在获取数组元素的时候可以通过第一个元素的首地址+元素在内存中的字节长度，从而获取到第二个元素的内存地址，同理可以得到不同元素的指针.

```
for (int i = 0;i<sizeof(array);i++) {
   printf("\neveryone======%c\n",*(array + i));
}
```
```
everyone======h

everyone======e

everyone======l

everyone======l

everyone======o
```
那么从数组中取值就有2种方法，第一传统的下标取值，第二通过元素指针取值。


* （1.3.4）、函数指针

![函数的指针](https://github.com/dengfeng520/xiaoshiguangBlog/blob/master/Runtime/function_Pointer.png?raw=true)

函数的指针和函数非常类似，在函数前面加上一个指针括号，我们拿到函数的指针之后，就可以调用这个指针来执行函数。此处定义2个函数，一个返回int类型，一个返回int类型的指针,此处需要注意，函数指针和返回指针的函数是两个不同概念。


```
int getMax(int i,int j) {
    return i>j?i:j;
}


int *getMin(int i,int j) {
    int result = i<j?i:j;
    int *pointer = &result;
    return pointer;
}

```

那么，该如何通过指针调用函数呢？


```
 int (*funcOne)(int,int);
 funcOne = getMax;
 int result = funcOne(10001,10086);
 printf("\nresult=============%d\n",result);

```

定义一个函数指针并赋值，通过调用指针来调用这个函数。


```
int *funcTwo = getMin(10001, 10086);
printf("funcTwo=========%d",*funcTwo);
```

调用返回指针的函数时先定义指针来接收函数的返回值。这个指针类型应为函数返回指针所指向的类型。

> <h3>2、结构体</h3>

结构体是由一系列的相同或者不同的类型构成的数据集合，Swift中的元组和结构体非常类似。

#####（2.1）结构体的组成

结构体一般由结构体名、结构体变量、结构体成员组成.

```
// 结构体名 + 结构体成员 + 变量名
struct personAbout {
    char name;
    int age;
}person;
```
如代码所示，我定义了一个结构体，结构体名personAbout，变量名person，成员有2个，name和age; 

![Struct.png](https://github.com/dengfeng520/xiaoshiguangBlog/blob/master/Runtime/Struct.png?raw=true)

一般情况下，结构体的名和变量名可省略其中一个，但不能全部省去不写，也就是说下面这两种写法也可以定义一个结构体。

```
// 结构体名 + 结构体成员
struct personFrist {
    char name[20];
    int *age;
};
// 结构体变量 + 成员
struct {
    char name[20];
    int *age;
}personSecond;

```


#####（2.2）结构体的使用


* 继承

例如在前面我定义了一个`person`的结构体，现在我又有一个对象，也包含name和age属性，此时可以采用结构体继承的方式。结构体继承和类的继承一样，直接冒号，继承的父类。

```
struct student: personAbout {
    int number;
}student;
```

* 设置别名

一般情况下通过`typedef`关键字为结构体设置别名，

```
// 结构体的别名
typedef struct studentFrist: personAbout {
    int number;
    studentFrist() {
        number = 10001;
        age = new int(17);
        strcpy(name, "韩梅梅");
    }
}studentFrist;
```

* 结构体赋值 


方法一：直接赋值：

```
// 结构体赋值 方法一
int age = 17;
struct personAbout person = {"李雷",&age};
```

方法二：可通过结构体的变量名赋值：

```
student.number = 1000;
strcpy(student.name,"李雷");
student.age = new int(17);
```
方法三:在定义的时候就给结构体赋值:

```
// 结构体的继承
struct student: personAbout {
    int number;
    student() {
        number = 10000;
        age = new int(16);
        strcpy(name, "李雷");
    }
}student;
```

* 结构体取值

1、直接取值


```
struct studentZero stu;
int number = stu.number;
```

2、别名取值

```
studentFrist stuFrist;
int stuNumber = stuFrist.number;
```
3、指针取值

先拿到结构体的指针，然后通过->得到结构体中的某个变量值。

```
studentFrist *stuPoiner = &stuFrist;
int studentNumber = stuPoiner->number;
```


* 结构体的位域

部分属性在存储时，并不需要占用一个完整的字节， 而只需占几个或一个二进制位。为了节省存储空间，并使处理简便，Ｃ语言又提供了一种数据结构，称为__位域__。所谓称为__位域__是把一个字节中的二进位划分为几个不同的区域，并说明每个区域的位数。每个域有一个域名，允许在程序中按域名进行操作。 这样就可以把几个不同的对象用一个字节的二进制位域来表示。在结构体中8位域等于一子节。

```
  struct teacher {
        unsigned int a: 1;
        unsigned int b: 2;
        unsigned int c: 3;
    }tc,*ptc;
```

位域的赋值不能超过该域所能表示的最大值，如:

b只有2位，能表示的最大数为3，如超过3就会报黄点，Xcode会默认赋值为0，并显示警告:

```
Implicit truncation from 'int' to bit-field changes value from 4 to 0
```
所以在使用结构体位域时，我们应计算好位域的位数，避免出错。

--

[本文demo](https://github.com/dengfeng520/Clangdemo.git)





