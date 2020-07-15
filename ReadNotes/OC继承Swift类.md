<h3><center>Inherit from a Swift class in Objective-C (OC继承Swift类)</center></h3>

搜到的相关资料基本来自这个帖子**[Inherit from a Swift class in Objective C
](https://stackoverflow.com/questions/35244592/inherit-from-a-swift-class-in-objective-c)**。事实上一楼回复已经说的很明确了，`Objective-C`不能继承`Swift`类。

**Unfortunately, it's not possible to subclass a Swift class in Objective-C. Straight from the docs:**

那么换个思路，为什么一定要用继承呢？用`category`可以吗？经过我的测试是可以的，代码如下： 

**Swift文件**

```
import UIKit

@objc open class TestModel: NSObject {
    @objc var testName: String = String()
}
```

**OC的.h文件**

```
#import <Foundation/Foundation.h>
// 桥接文件
#import "****-Swift.h"


@interface TestModel (Add)
- (void)configTestName;
@end

```
**OC的.m文件**

```
#import "TestModel+Add.h"

@implementation TestModel (Add)

- (void)configTestName {
    self.testName = @"12323";
}

@end
```
