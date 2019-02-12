
<a href="https://dengfeng520.github.io/iOSNotes/iOSNotes.html"><center>iOS Notes</center></a>

<h1><center>iOS Notes</center></h1>

<h6 align='right'>小时光</h6>
<h6  align='right'>西安乐推网络科技有限公司</h6> 

---
###1、按钮透明度为0时不响应点击事件

个人推测，当`UIButton`透明度为0时，默认调用`Hidden`方法，所以不响应点击事件。

```
//设置按钮`Type`可避免该问题
_heartbtn = [UIButton buttonWithType: UIButtonTypeCustom];

 [self.view addSubview:_heartbtn];
```



###2、Git相关
  
| 命令字  |  相关操作   | 
| :-------------:|:-------------:| 
|```git init```|初始化Git|
| ```git clone URL```|下载远端仓库代码|
|```git add .``` |把代码中的所有变化都提交到缓存区域,不包括删除的文件|
|```git add -u```|把代码中的所有变化都提交到缓存区域,不包括新增的文件|
|```git add -A```|A时ALL的缩写，上面两个的集合|
|```git commit -a -m "做了哪些修改"```|将代码提交到本地仓库，此时并没有提交到远端仓库|
|```git fetch origin master```|获取远端分支代码|
|```git merge origin master```|合并代码|
|```git pull origin master```|上面两步操作的集合|
|```git status```|查看缓存区的状态|
|```git log```|查看提交日志|
|```git push origin master```|把代码推到远端分支上|
|```git push origin master close #BUGID```|把代码推到远端分支上,同时关闭相关BUG|
|```git branch```|查看分支|
|```git branch -r```|查看远程版本库分支列表|
|```git branch -a```|查看所有分支列表，包括本地和远程|
|```git checkout -b TEST```|新建`TEST`分支并切换到`TEST`分枝上，是`git branch TEST`和`git checkout TEST`的合并|
|```git remote add origin https://github.com/dengfeng520/OADemo.git```||
|```git branch -D fenzhi```|删除本地分支|
|```git branch -r -D origin/fenzhi```|删除本地分支的远程分支|
|```git push origin -d fenzhi```|删除服务器上的分支|
|git log --grep=""|查找已经提交的commit|

###3、设置圆角

```
// MARK: - 设置顶部两个圆角
-(void)SettingSharaviewRound:(float)Round selfview:(UIView *)selfview
{
    UIBezierPath *maskPath = [UIBezierPath bezierPathWithRoundedRect:selfview.bounds byRoundingCorners:UIRectCornerTopLeft | UIRectCornerTopRight cornerRadii:CGSizeMake(Round, Round)];
    CAShapeLayer *maskLayer = [[CAShapeLayer alloc] init];
    maskLayer.frame = selfview.bounds;
    maskLayer.path = maskPath.CGPath;
    selfview.layer.mask = maskLayer;
}
```
通常如果使用`Masonry`添加约束会导致用以上代码设置不起作用，解决方案是在设置圆角之前调用父`view`的`layoutIfNeeded `方法即可。

```
 [superView layoutIfNeeded];

```

###4、通过Blocks来关联UIButton的点击事件
在开发中通常使用的按钮点击：

```
    UIButton *chatBtn = [[UIButton alloc]initWithFrame:CGRectMake(100, 100, 100, 44)];
    [self.view addSubview:chatBtn];
        [settingbtn mas_makeConstraints:^(MASConstraintMaker *make) {
            //距离顶部
            make.top.equalTo(weakSelf.mas_top).with.offset(20.f);
            //距离右边
            make.right.equalTo(weakSelf.mas_right).with.offset(-15.f);
            //设置大小
            make.size.mas_equalTo(CGSizeMake(50, 50));
        }];
    [chatBtn addTarget:self action:@selector(TouchChatBtn:) forControlEvents:UIControlEventTouchUpInside];

```

```
-(void)TouchChatBtn:(UIButton *)sender
{
  printf("\n================\n");
}
```

>通过代码关联


```
#import <objc/runtime.h>

  void (^addTargetBlock)(void) = ^(void){
        
        printf("\n================\n");
    };
    objc_setAssociatedObject(chatBtn, &clickChatBtnType, addTargetBlock, OBJC_ASSOCIATION_COPY_NONATOMIC);
    
    
```

```
-(void)TouchChatBtn:(UIButton *)sender
{
    void(^addTargetBlock)(void) = objc_getAssociatedObject(sender, &clickChatBtnType);
    if(addTargetBlock)
    {
        addTargetBlock();
    }
}
```

参考：[iOS开发·runtime原理与实践: 关联对象篇](http://www.cocoachina.com/ios/20180518/23429.html)

[Effective Objective-C 2.0 第10条:在既有类中使用关联对象存放自定义数据](https://book.douban.com/subject/25829244/)

###5、方法标记
```
// FIXME: - update
```
```
// ???: - update
```
```
// !!!: - update
```
```
// MARK: - update
```
```
// TODO: - update
```
###6、先pop当前UIViewController,再push到下一个UIViewController

```

  LTBaseViewController *InputNewPasswordView = [[NSClassFromString(@"InputNewPasswordViewController") alloc]init];

            // 获取当前路由的控制器数组
            NSMutableArray *vcArray = [NSMutableArray arrayWithArray:self.navigationController.viewControllers];
            // 获取档期控制器在路由的位置
            int index = (int)[vcArray indexOfObject:self];
            // 移除当前路由器
            [vcArray removeObjectAtIndex:index];
            // 添加新控制器
            [vcArray addObject: InputNewPasswordView];

            SEL aSelector = NSSelectorFromString(@"setReset_code:");
            if ([InputNewPasswordView respondsToSelector:aSelector]) {
                IMP aIMP = [InputNewPasswordView methodForSelector:aSelector];
                void (*setter)(id, SEL, NSString*) = (void(*)(id, SEL, NSString*))aIMP;
                setter(InputNewPasswordView, aSelector,@"1008611");
            }

            [self.navigationController setViewControllers:vcArray animated:YES];
```
###7、Lottie动画

[Lottie动画](http://airbnb.io/lottie/ios.html#getting-started-on-ios-or-macos)
 
 >1、作为开发者，不能直接编辑或者控制，只能用生成好的JSON文件
 
 >2、内存问题，我在APP中使用了Lottie动画之后，CPU使用率和内存瞬间上升，尤其是全屏的动画之后，目前没有找到相关优化方案；
 
 
 ```
     _LoaingAnimation = [LOTAnimationView animationNamed:@"MatchWaitingLoading"];
        _LoaingAnimation.frame = CGRectMake(ScreenW / 2 - (73 / 2), ScreenH - 93, 73, 73);
        [self.view addSubview:_LoaingAnimation];
        _LoaingAnimation.loopAnimation = true;
        [weakSelf.LoaingAnimation playWithCompletion:^(BOOL animationFinished) {
                        
                    }];   
 ```
 
 ```
 [_LoaingAnimation removeFromSuperview];
        _LoaingAnimation = nil;
 ```
 
>控制播放帧数

```
[_LoaingAnimation playFromFrame:15 toFrame:0 withCompletion:^(BOOL animationFinished) {
        
    }];
``` 
>停止播放保持最后帧数

```
 [_LoaingAnimation pause];
```
>停止播放 恢复初始帧数

```
[_LoaingAnimation stop];
```
>强制跳转至某一帧

例如：我在播放一段动画时，需要突然阻断并且需要逆向执行，此时可强制跳转到最后一帧，然后再逆向播放。

```
 [_exploreAnimation setProgressWithFrame:[NSNumber numberWithInteger:46]];

```
 
###8、Socket and Protocol Buffers

>Protocol Buffers

[Download Protocol Buffers](https://developers.google.com/protocol-buffers/docs/downloads)
####1、安装:
 ```
 cd protobuf-3.3.0
 ./configure
sudo make
sudo make install
 ```
####2、创建.proto文件

打开Xcode ，选择新建`Empty`文件，根据和后台的约定的参数，

```
syntax = "proto3";

message ResponseExplore {
    string  nickname = 1;
    int32   sex = 2;
    int32   astrology = 3;
    int32   birthday = 4;
    string  avatar = 5;
    int32   distance = 6;
    string  room_token = 7;
    int32   end_time = 8;
    bool    like = 9;
    bool    opposite_like = 10;
}
```
####3、生成Model文件:
 
 ```
 protoc --proto_path=./Protobuf  --objc_out=./Protobuf   ChatIM.proto
 ```
 同理，生成Android用的Model文件可使用命令:
 ```
 protoc --proto_path=./Protobuf  --objc_out=./Protobuf   ChatIM.proto
 ```
 
####4、使用

```
  pod 'Protobuf'
```

>Socket


####1、导入:
```
pod CocoaAsyncSocket
```
####2、创建Socket并连接:

```
 if (_socket == nil) {
            _socket = [[GCDAsyncSocket alloc]initWithDelegate:self delegateQueue:dispatch_get_main_queue()];
            NSError *error = nil;
            if (![_socket connectToHost:SOCKET_HOST onPort:SOCKET_PORT withTimeout:3 error:&error]) {
                NSLog(@"连接错误 %@", error);
            }
        }
```
####2、主动断开Socket连接:
```
[_socket disconnect];
```
###9、无动画pop再push，替换当前控制器
```
 // 新建将要push的控制器
                CompleteViewController *newVC = [[CompleteViewController alloc] init];
                
                // 获取当前路由的控制器数组
                NSMutableArray *vcArray = [NSMutableArray arrayWithArray:self.navigationController.viewControllers];

                // 获取档期控制器在路由的位置
                int index = (int)[vcArray indexOfObject:self];
                
                // 移除当前路由器
                [vcArray removeObjectAtIndex:index];
                
                // 添加新控制器
                [vcArray addObject: newVC];

                // 重新设置当前导航控制器的路由数组
                [self.navigationController setViewControllers:vcArray animated:YES];
```

###10、Push的同时释放之前的内存
```
 //获取要跳转的界面
        UIViewController *LoginView = [[NSClassFromString(@"ViewController") alloc]init];
        // 获取navigationcontroller的栈
        NSMutableArray *navStack = [thisView.navigationController.childViewControllers mutableCopy];
        // 修改栈
        [navStack replaceObjectAtIndex:0 withObject:LoginView];
        // 重新设置navigation的栈
        [thisView.navigationController setViewControllers:navStack animated:YES];
        // 出栈并跳转至目标控制器(控制器和当前控制器之间的对象将全部释放)
        [thisView.navigationController popToViewController:LoginView animated:true];
```
 
###11、APP内购相关问题

###12、按钮超出父控件后无法响应点击的解决方法

###13、如何将UIView覆盖到状态栏之上

如果直接添加到window上，会和电池状态栏重叠，解决方法是修改Window的UIWindowLevel属性，


[参考](https://www.jianshu.com/p/23114e107ef1)

###14、通过Runtimeh+分类的方式实现UITextView的placeHolder占位文字
```
#import <objc/runtime.h>

@implementation UITextView (Category)

-(void)getplaceholder:(NSString *)placeholderString fontsize:(float)fontsize
{
    UILabel *placeholder = [[UILabel alloc] init];
    placeholder.text = placeholderString;
    placeholder.numberOfLines = 0;
    placeholder.textColor = [UIColor lightGrayColor];
    [placeholder sizeToFit];
    [self addSubview:placeholder];
    
    self.font = [UIFont systemFontOfSize:fontsize];
    placeholder.font = [UIFont systemFontOfSize:fontsize];
    
    [self setValue:placeholder forKey:@"_placeholderLabel"];
}

@end
```
###14、RAC
>1、创建信号量

```
    RACSignal * signal = [RACSignal createSignal:^RACDisposable * _Nullable(id<RACSubscriber>  _Nonnull subscriber) {
    NSLog(@"创建信号量");
        
        //3、发布信息
        [subscriber sendNext:@"I'm send next data"];
        
        self.subscriber = subscriber;
        
        NSLog(@"那我啥时候运行");
        
        return [RACDisposable disposableWithBlock:^{
            NSLog(@"disposable");
        }];
    }];

```

>2、订阅信号量

```
 //2、订阅信号量
 RACDisposable *disposable = [signal subscribeNext:^(id  _Nullable x) {
        NSLog(@"%@",x);
    }];    
```
>3、发布信息
>

>4、主动取消订阅

```
 //主动触发取消订阅
 [disposable dispose];   
```


###15、导入PCH文件之后报错unknown type name 'NSString'

#####解决方法：在PCH文件中引用`Foundation`

```
#ifdef __OBJC__

#import <Foundation/Foundation.h>

#endif
```
###16、Masonry

>距离上下左右边距

```
[self.blueView mas_makeConstraints:^(MASConstraintMaker *make) {

        make.edges.equalTo(self.view).with.insets(UIEdgeInsetsMake(10, 10, 10, 10));
}];
```
>大于等于和小于等于某个值的约束

```
[self.textLabel mas_makeConstraints:^(MASConstraintMaker *make) {
    make.center.equalTo(self.view);
    // 设置宽度小于等于200
    make.width.lessThanOrEqualTo(@200);
    // 设置高度大于等于10
    make.height.greaterThanOrEqualTo(@(10));
}];
```
>设置比例约束

```
[self.redView mas_makeConstraints:^(MASConstraintMaker *make) {
    make.center.equalTo(self.view);
    make.height.mas_equalTo(30);
    make.width.equalTo(self.view).multipliedBy(0.2);
}];
```

>设置底部距离 适配X

```
[self.redView mas_makeConstraints:^(MASConstraintMaker *make) {
            make.left.right.mas_equalTo(0);
            make.height.mas_equalTo(50);
            make.bottom.mas_equalTo(self.view.mas_bottomMargin);
        }];
```
pod导入Masonry使用mas_topMargin闪退问题

如果报错，	在`pod`中修改`Masonry`最低支持版本即可，

[参考 Masonry v1.1.0 crash with UIView margins #461](https://github.com/SnapKit/Masonry/issues/461)

[参考Stack Overflow Set deployment target for CocoaPods's pod](https://stackoverflow.com/questions/37160688/set-deployment-target-for-cocoapodss-pod/37289688#37289688)

###17.Application Loader stuck at “Authenticating with the iTunes store” when uploading an iOS app



[Stack overflow](https://stackoverflow.com/questions/22443425/application-loader-stuck-at-authenticating-with-the-itunes-store-when-uploadin)

###18、设计



[Effective Objective-C 2.0 第24条:将累的实现代码分散到便于管理的数个分类之中](https://book.douban.com/subject/25829244/)

[Apple Developer Model-View-Controller](https://developer.apple.com/library/archive/documentation/General/Conceptual/CocoaEncyclopedia/Model-View-Controller/Model-View-Controller.html#//apple_ref/doc/uid/TP40010810-CH14)

[iOS的MVC框架之控制层的构建](https://www.jianshu.com/p/02d6397436dc)


###19、Tomcat

[Tomcat Download](https://tomcat.apache.org/download-90.cgi)
#####1、Tomcat路径:
```
cd ~/Library/ApacheTomcat/bin
```

#####2、启动Tomcat:

```
./startup.sh
```

#####3、如果报错，说明没有权限：


`-bash: ./startup.sh: Permission denied`



```
chmod u+x *.sh
```

#####4、验证是否开启成功：
 电脑端：

[http://localhost:8080](http://localhost:8080)

手机端:电脑IP+端口号

查看本机IP:

```
ifconfig
```

#####5、关闭Tomcat

```
./shutdown.sh
```

#####6、查看版本号
```
sh catalina.sh version
```


#####7、查看Tomcat相关文件夹

```
cd ~/Library/ApacheTomcat/bin

cd ..

ls
```

***


>1、bin:存放tomcat命令
>
>2、conf:存放tomcat配置信息,里面的server.xml文件是核心的配置文件
>
>3、lib:支持tomcat软件运行的jar包和技术支持包(如servlet和jsp)

>4、logs:运行时的日志信息
>
>5、temp:临时目录
>
>6、webapps:共享资源文件和web应用目录
>
>7、work:tomcat的运行目录.jsp运行时产生的临时文件就存放在这里

###20、Category、Extension、Protocol区别
###21、assign、strong、retain、weak、copy区别
###22、removeFromSuperview
###23、iOS 11之后系统没有导航栏时 WKWebView下移问题

```
     [(UIScrollView *)[[_webview subviews] objectAtIndex:0] setBounces:NO];
    _webview.scrollView.bounces = NO;
    _webview.scrollView.showsHorizontalScrollIndicator = NO;
    [_webview sizeToFit];
     if(isiPhoneXDevice){
        _webview.scrollView.contentInset = UIEdgeInsetsMake(-44,0,-44,0);
    }else{
        _webview.scrollView.contentInset = UIEdgeInsetsMake(-20,0,0,0);
    }
```
###24、KVC字典转Model

```
[Model setValuesForKeysWithDictionary:dic];

```
###25、SDWebimage做了什么

```
[cell.bookImg sd_setImageWithURL:imgURL placeholderImage:[UIImage imageNamed:@"Placeholder"] options:1 progress:^(NSInteger receivedSize, NSInteger expectedSize, NSURL * _Nullable targetURL) {

} completed:^(UIImage * _Nullable image, NSError * _Nullable error, SDImageCacheType cacheType, NSURL * _Nullable imageURL) {

}]; 
```
###26、线程安全

**串行队列，同步任务**
有顺序的执行，并且不会开启新线程，就在当前线程执行；FMDB,它为什么要设计成串行队列，同步任务，为了保证数据的安全
**串行队列，异步任务**
有顺序的执行,并且在开辟的新的线程中执行,并且只开一条线程!!!耗时操作,并且有严格先后顺序
**并发队列，同步任务**
没有开启新线程，同时按照顺序执行，几乎不用；
**并发队列，异步任务**
特点回开启N条线程，没有顺序， 运用场景: 多图下载；

同一时间内多个线程同时访问同一个资源时，会引发数据错乱和数据安全问题。如同一时间内，多个线程同时修改数据库中的同一张表的数据。
解决方法：互斥锁(同步锁)

```
 @synchronized (self) {
 
 }
```

###27、Blocks笔记

Blcoks的本质就是结构体，

```

-(void(^)(void))whereTest{
    return ^{
        NSLog(@"----------------------天王盖地虎");
    };
}

-(void(^)(NSString *))testBlock{
    
    void(^block)(NSString *textChar) = ^(NSString *textChar){
        
        NSLog(@"----------------------%@",textChar);
        
    };
    return block;
}

```

###28、循环动画

```
///
@property (strong, nonatomic) UIView *testView;
//-------------------------------
_testView = [[UIView alloc]initWithFrame:CGRectMake(0, 60, 20, 20)];
[self.view addSubview:_testView];
_testView.backgroundColor = [UIColor redColor];
```

1、创建动画的方式 有2种:

```
// MARK: - 创建动画
-(void)firstCreateAnimation{
    //-------------------------------
    __weak typeof (self) weakSelf = self;
    dispatch_async(dispatch_get_main_queue(), ^{
        
        //-------------------------------
        [UIView animateWithDuration:1 delay:1 options:UIViewAnimationOptionAutoreverse|UIViewAnimationOptionRepeat animations:^{
            weakSelf.testView.frame = CGRectMake(40, 120, weakSelf.testView.frame.size.width, weakSelf.testView.frame.size.height);
        } completion:^(BOOL finished){
            
        }];
    });
}

-(void)SecondCreateAnimation{
    //-------------------------------
    __weak typeof (self) weakSelf = self;
    dispatch_async(dispatch_get_main_queue(), ^{
        //-------------------------------
        CABasicAnimation *shakeLeft = [CABasicAnimation animationWithKeyPath:@"transform.translation.y"];
        shakeLeft.removedOnCompletion = NO;
        shakeLeft.duration = 0.5;
        shakeLeft.autoreverses = YES;
        shakeLeft.repeatCount = MAXFLOAT;
        shakeLeft.fromValue = [NSNumber numberWithFloat:0];
        shakeLeft.toValue = [NSNumber numberWithFloat:+60];
        [weakSelf.testView.layer addAnimation:shakeLeft forKey:@"pointLeft"];
    });
}


// MARK: - 暂停动画
-(void)pauseAnimation{
    CFTimeInterval pauseTime = [_testView.layer convertTime:CACurrentMediaTime() fromLayer:nil];
    _testView.layer.timeOffset = pauseTime;
    _testView.layer.speed = 0;
}

// MARK: - 继续动画
-(void)proceedAnimation{
    CFTimeInterval pauseTime = _testView.layer.timeOffset;
    CFTimeInterval timeSincePause = CACurrentMediaTime() - pauseTime;
    _testView.layer.timeOffset = 0;
    _testView.layer.beginTime = timeSincePause;
    _testView.layer.speed = 1;
}

// MARK: - 移除动画
-(void)removeAnimation{
    [_testView.layer removeAllAnimations];
}

```
运行效果：

![loopAnimation](https://github.com/dengfeng520/iOSNotes/blob/master/loopAnimation.gif?raw=true)

---
