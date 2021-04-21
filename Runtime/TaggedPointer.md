---
    
title: iOS 内存管理之Tagged Pointer

date: 2020-04-15 16:23:56

cover: /img/girl037.webp

---

`bit 64`优化，

```Objective-c
NSNumber *number1 = @3;
NSNumber *number2 = @100;
NSNumber *number3 = @10086;
NSNumber *number4 = @0xfffffffffffffff;
NSLog(@"number======%p,%p,%p,%p",number1,number2,number3,number4);
```

打印结果：

```C
number======0xb000000000000032,0xb000000000000642,0xb0000000000022b2,0x600003ce2fa0
```

`3`就是`3`，16进制的`64`转换为10进制为100，

本文参考：

---

[Apple Developer: Advances in Objective-C](https://developer.apple.com/videos/play/wwdc2013/404/)

