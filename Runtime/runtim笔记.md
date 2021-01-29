### 1、strong、copy、weak、assign的区别

 * （1）、assign主要用用于修饰基本数据类型，（int,NSInteger）
 * （2）、weak修饰符表示指向并不持有改对象，引用计数不会+1，多用于避免循环引用的地方，weak不可修饰基本数据类型
 * （3）、strong修饰符表示指向并持有改对象，引用计数+1，
 * （4）、copy  和 strong 类似,

 
 ```
 @property (strong, nonatomic) NSString *strongStr;
 @property (copy, nonatomic) NSString *strCopy;

 NSString *testStr = @"test";
 self.strongStr = testStr;
 self.strCopy = testStr;
 NSLog(@"test string===============%p,%p,%p",testStr,self.strongStr,self.strCopy);
 // test string===============0x10b5ba2c8,0x10b5ba2c8,0x10b5ba2c8
 ```
 
 **这里copy修饰的字符串的地址并没有改变，这里的copy是浅拷贝，并没有生成新的对象。**
 
 ```   
 NSMutableString *mutabTest = [NSMutableString stringWithString:@"mutableTest"];
 self.strongStr = mutabTest;
 self.strCopy = mutabTest;
 NSLog(@"test string===============%p,%p,%p",mutabTest,self.strongStr,self.strCopy);
 // test string===============0x60000061a820,0x60000061a820,0x6000009c76a0
 ```
 
 **当源字符串是NSMutableString时，使用strong只会增加引用计数，但是copy会执行一次深拷贝，产生新的对象。**
 

### 2、Category 和 Extension 区别
 
 * （1）、Extension是在编译期决议的，它是类的一部分，是头文件的@interface的替代。Extension 一般用来隐藏类的私有信息或者减少类文件的代码行数。
 * （2）、Category是运行期决议的，