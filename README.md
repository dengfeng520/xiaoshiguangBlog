<a href="https://dengfeng520.github.io/iOSNotes/iOSNotes.html"><center>iOS Notes</center></a>

<h1><center>iOS Notes</center></h1>

<h6 align='right'>小时光</h6>
<h6  align='right'>西安乐推网络科技有限公司</h6> 


--
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
|```git log```|查看提交Log|
|```git remote add origin URL```|关联远端仓库|
|```git log -p -2```|显示最近两次提交Log|
|```git log --stat```|查看每次提交的简略信息 修改了（新增或者删除）那些文件,修改文件的哪一行|
|```git log --graph --pretty=oneline --abbrev-commit```|查看loa详情|
|```git reset HEAD README.md```|取消暂存, 在add之后执行，如果commit或者push之后执行无效|
|``` git checkout -- README.md```|撤消之前所做的修改|
|```git remote -v```|显示远程仓库及其URL|
|```git remote show origin```|查看远端库信息|
|```git remote rename A B```|把远端库|
|```git push origin master```|把代码推到远端分支上|
|```git push origin master close #BUGID```|把代码推到远端分支上,同时关闭相关BUG|
|```git branch```|查看分支|
|```git branch -r```|查看远程版本库分支列表|
|```git branch -a```|查看所有分支列表，包括本地和远程|
|```git checkout -b TEST```|新建`TEST`分支并切换到`TEST`分枝上，是`git branch TEST`和`git checkout TEST`的合并|
|```git remote add origin https://github.com/dengfeng520/OADemo.git```||
|```git branch -D fenzhi```|删除本地分支|
|```git branch -r -D origin/TEST```|删除本地分支的远程分支|
|```git push origin -d TEST```|删除服务器上的分支|
|```git log --grep=""```|查找已经提交的commit|
|```git rebase --continue```|解决冲突后继续执行|
|```git rebase --abort```|终止rebase|
|```git config --global pull.rebase true```|设置git pull时默认—rebase|
|```git stash save “” ```|Git缓存到本地|
|```git checkout -b （本地分支）（远端分支）```|拉取别人远端分支命令|
|```git log reflog```||
|```git reset  -—soft  commit_ID```|代码回滚,所有commit的修改都会退回到git缓冲区|
|``` git reset --hard  commit_ID ```|代码回滚,所有commit的修改直接丢弃|
|```git reset --hard HEAD^```|回退到上个版本|
|```git reset --hard HEAD~3```|回退到前3个版本|
|```（1）、git reflog (2)、git  reset -—soft  HEAD@{3}```|取消当前Rebase|
|```（1）、git log (2)、git rebase -i commit_ID (3)、i (4)、pick改成squash```|合并commit|

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
###26、多线程相关

**同步（sync）：**

一个接着一个，前一个没有执行完，后面不能执行，不开线程。

**异步（async）：**

开启多个新线程，任务同一时间可以一起执行。异步是多线程的代名词。

**队列：**

装载线程任务的队形结构。(系统以先进先出的方式调度队列中的任务执行)。在GCD中有两种队列：串行队列和并发队列。

**串行队列：**

只开启一个线程，串行执行多个任务

示例代码:

```
// 串行队列
dispatch_queue_t queue = dispatch_queue_create("test", DISPATCH_QUEUE_SERIAL);

```

**并发队列：**

线程可以同时一起进行执行。实际上是CPU在多条线程之间快速的切换。（并发功能只有在异步（dispatch_async）函数下才有效）

```

// 并发队列
dispatch_queue_t queue1 = dispatch_queue_create("test", DISPATCH_QUEUE_CONCURRENT);

```

**串行队列，同步任务**

有顺序的执行，并且不会开启新线程，就在当前线程执行；FMDB,它为什么要设计成串行队列，同步任务，为了保证数据的安全

**串行队列，异步任务**

有顺序的执行,并且在开辟的新的线程中执行,并且只开一条线程!!!耗时操作,并且有严格先后顺序

**并发队列，同步任务**

没有开启新线程，同时按照顺序执行，几乎不用；

**并发队列，异步任务**

特点回开启N条线程，没有顺序， 运用场景: 多图下载；

--

**线程和队列**
一个线程可以有多个队列，主线程可有多个队列。


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

####29、屏幕截屏相关

**方法一:**

```
UIView *view2 = [self.view snapshotViewAfterScreenUpdates:YES];
```

**方法二:**

```
-(void)saveImg:(UIButton *)sender{
    
    UIImage *photo = [self save];
    //保存到相册
    UIImageWriteToSavedPhotosAlbum(photo, self, @selector(imageSavedToPhotosAlbum:didFinishSavingWithError:contextInfo:), nil);
}

// MARK: - 保存图片的回调
- (void)imageSavedToPhotosAlbum:(UIImage *)image didFinishSavingWithError:(NSError *)error contextInfo:(void *)contextInfo
{
    NSString *message = @"";
    if (!error) {
        
        message = @"成功保存到相册";
        
    }else{
        message = [error description];
    }
    NSLog(@"message is %@",message);
}


- (UIImage *)save
{
    //开启图片上下文
    UIGraphicsBeginImageContextWithOptions(self.loadImg.bounds.size, NO, 0);
    //获取上下文
    CGContextRef context = UIGraphicsGetCurrentContext();
    //截屏
    [self.loadImg.layer renderInContext:context];
    //获取图片
    UIImage *image = UIGraphicsGetImageFromCurrentImageContext();
    //关闭图片上下文
    UIGraphicsEndImageContext();
    
    return [self fixOrientation:image rotation:UIImageOrientationUp];
}


- (UIImage *)fixOrientation:(UIImage *)image rotation:(UIImageOrientation)orientation
{
    long double rotate = 0.0;
    CGRect rect;
    float translateX = 0;
    float translateY = 0;
    float scaleX = 1.0;
    float scaleY = 1.0;
    
    switch (orientation) {
        case UIImageOrientationLeft:
            rotate = M_PI_2;
            rect = CGRectMake(0, 0, image.size.height, image.size.width);
            translateX = 0;
            translateY = -rect.size.width;
            scaleY = rect.size.width/rect.size.height;
            scaleX = rect.size.height/rect.size.width;
            break;
        case UIImageOrientationRight:
            rotate = 33 * M_PI_2;
            rect = CGRectMake(0, 0, image.size.height, image.size.width);
            translateX = -rect.size.height;
            translateY = 0;
            scaleY = rect.size.width/rect.size.height;
            scaleX = rect.size.height/rect.size.width;
            break;
        case UIImageOrientationDown:
            rotate = M_PI;
            rect = CGRectMake(0, 0, image.size.width, image.size.height);
            translateX = -rect.size.width;
            translateY = -rect.size.height;
            break;
        default:
            rotate = 0.0;
            rect = CGRectMake(0, 0, image.size.width, image.size.height);
            translateX = 0;
            translateY = 0;
            break;
    }
    
    UIGraphicsBeginImageContext(rect.size);
    CGContextRef context = UIGraphicsGetCurrentContext();
    //做CTM变换
    CGContextTranslateCTM(context, 0.0, rect.size.height);
    CGContextScaleCTM(context, 1.0, -1.0);
    CGContextRotateCTM(context, rotate);
    CGContextTranslateCTM(context, translateX, translateY);
    
    CGContextScaleCTM(context, scaleX, scaleY);
    //绘制图片
    CGContextDrawImage(context, CGRectMake(0, 0, rect.size.width, rect.size.height), image.CGImage);
    
    UIImage *newPic = UIGraphicsGetImageFromCurrentImageContext();
    
    return newPic;
}
```
####30、CALayer

 **1、添加一个CALayer**

```
 //---------------------------- 添加蓝色的CALayer
 _blueLayer = [CALayer layer];
 _blueLayer.frame = CGRectMake(50, 50, 50, 50);
 _blueLayer.backgroundColor = [UIColor blueColor].CGColor;
 [_layerView.layer addSublayer:_blueLayer];
    
```
**2、CALayer的contents属性**

`contents`属性为`id`类型，理论上可以为任何类型，但是，入股给contents赋的不是CGImage，那么得到的图层将是空白的。

```
 UIImage *image = [UIImage imageNamed:@"showGrils"];
 _blueLayer.contents = (__bridge id)image.CGImage;
```
```
 _layerView.layer.contents = (__bridge id)image.CGImage;
 //设置图片显示模式
 _layerView.contentMode = UIViewContentModeScaleAspectFit;
```

**3、contentsScale属性**

`contentsScale`属性定义了寄宿图的像素尺寸和视图大小的比例，默认情况下它是一个值为1.0的浮点数.可使用`contentsScale`属性来控制视图的缩放比例。

```
 _layerView.layer.contentsGravity = kCAGravityCenter;
 _layerView.layer.contentsScale = image.scale;
```
**4、maskToBounds 属性**

`UIView`有一个叫做`clipsToBounds `的属性可以用来决定是否显示超出边界的内容，`CALayer`对应的属性叫做`masksToBounds`，把它设置为`YES`.

```
 _layerView.layer.masksToBounds = true;
```
**5、图像绘制**

```
//----------------------------  Custom Drawing
_blueLayer.delegate = self;
[_blueLayer display];

// MARK: - ======================================= Custom Drawing
- (void)drawLayer:(CALayer *)layer inContext:(CGContextRef)ctx{
    //draw a thick red circle
    CGContextSetLineWidth(ctx, 5.0f);
    CGContextSetStrokeColorWithColor(ctx, [UIColor redColor].CGColor);
    CGContextStrokeEllipseInRect(ctx, layer.bounds);
}
```
**6、布局**


 `UIView`有三个比较重要的布局属性：`frame，bounds`和`center`，`CALayer`对应地叫做`frame`，`bounds`和`position`。为了能清楚区分，图层用了`position`，视图用了`center`，但是他们都代表同样的值。`frame`代表了图层的外部坐标（也就是在父图层上占据的空间），`bounds`是内部坐标（{0, 0}通常是图层的左上角），`center`和`position`都代表了相对于父图层`anchorPoint`所在的位置。`anchorPoint`的属性表示图层的中心点。
 
 ![`frame，bounds`和`center`](https://zsisme.gitbooks.io/ios-/content/chapter3/3.1.jpeg)

**7、组透明**

`UIView`有一个叫做`alpha`的属性来确定视图的透明度。`CALayer`有一个等同的属性叫做`opacity`，这两个属性都是影响子层级的。如果给一个图层设置了`opacity`属性，那它的子图层都会受此影响。

```
 _testView.layer.opacity = 0.5f;

```
**8、2D仿射变换**

>旋转
```
// 旋转90度
_layerView.transform = CGAffineTransformMakeRotation(M_PI_4);
```
效果图:

![testdemo1](https://github.com/dengfeng520/iOSNotes/blob/master/testdemo1.png?raw=true)

>轴偏移量 Y轴偏移量

```
// X轴偏移量 Y轴偏移量
_layerView.transform = CGAffineTransformMakeScale(-100, -50);

```
效果图:
![testdemo2](https://github.com/dengfeng520/iOSNotes/blob/master/testdemo2.png?raw=true)

>缩放处理 

```
//缩放处理 宽缩放比例 高缩放比例
_layerView.transform = CGAffineTransformMakeScale(2.5,1.5);
```

效果图:

![testdemo4](https://github.com/dengfeng520/iOSNotes/blob/master/testdemo4.png?raw=true)

>组合仿射一:  组合的同时，偏移X、Y轴坐标

```
// 组合仿射  组合的同时，偏移X、Y轴坐标
CGAffineTransform viewTransform = CGAffineTransformConcat(CGAffineTransformMakeRotation(M_PI_4),CGAffineTransformMakeTranslation(-10, -50));
_layerView.transform = viewTransform;

```
效果图:

![testdemo3](https://github.com/dengfeng520/iOSNotes/blob/master/testdemo3.png?raw=true)

>组合仿射二:

 ```
 //---------------------------- CGAffineTransform
    //create a new transform
    CGAffineTransform transform = CGAffineTransformIdentity;
    //scale by 50%
    transform = CGAffineTransformScale(transform, 1.8, 1.35);
    //rotate by 30 degrees
    transform = CGAffineTransformRotate(transform, M_PI / 180.0 * 45.0);
    //translate by 200 points
    transform = CGAffineTransformTranslate(transform, 0, 0);
    //apply transform to layer
    _layerView.layer.affineTransform = transform;
 ```
 效果图:
 
 ![testdemo5](https://github.com/dengfeng520/iOSNotes/blob/master/testdemo5.png?raw=true)
 
 **9、3D仿射变换**
 
 ![iPhone上的X轴 Y轴 Z轴示意图](https://zsisme.gitbooks.io/ios-/content/chapter5/5.7.jpeg)
 
 >3D 旋转
 
 ```
 // 绕Y轴旋转45度
 CATransform3D transform = CATransform3DMakeRotation(M_PI_4, 0, 1, 0);
 _layerView.layer.transform = transform;
 ```
 效果图:
  
  ![testdemo6](https://github.com/dengfeng520/iOSNotes/blob/master/testdemo6.png?raw=true)
  
  >3D X轴 Y轴 Z轴缩放比例
  
  ```
  //X轴 Y轴 Z轴 缩放比例
  CATransform3D transform = CATransform3DMakeScale(1.8, 1.35, 1);
  ```
  
  效果图:
  
   ![testdemo7](https://github.com/dengfeng520/iOSNotes/blob/master/testdemo7.png?raw=true)

>3D 透视旋转

```
 //----------------------------
 //create a new transform
 CATransform3D transform = CATransform3DIdentity;
 //apply perspective
 transform.m34 = - 1.0 / 500.0;
 //rotate by 45 degrees along the Y axis
 transform = CATransform3DRotate(transform, M_PI_4, 0, 1, 0);
 //apply to layer
 _layerView.layer.transform = transform;
```
效果图:

 ![testdemo8](https://github.com/dengfeng520/iOSNotes/blob/master/testdemo8.png?raw=true)
 
####30、CMSampleBufferRef  ---->  UIImage

 
```
- (UIImage *)imageConvert:(CMSampleBufferRef)sampleBuffer{
    //制作 CVImageBufferRef
    CVImageBufferRef buffer;
    buffer = CMSampleBufferGetImageBuffer(sampleBuffer);
    
    CVPixelBufferLockBaseAddress(buffer, 0);
    
    //从 CVImageBufferRef 取得影像的细部信息
    uint8_t *base;
    size_t width, height, bytesPerRow;
    base = CVPixelBufferGetBaseAddress(buffer);
    width = CVPixelBufferGetWidth(buffer);
    height = CVPixelBufferGetHeight(buffer);
    bytesPerRow = CVPixelBufferGetBytesPerRow(buffer);
    
    //利用取得影像细部信息格式化 CGContextRef
    CGColorSpaceRef colorSpace;
    CGContextRef cgContext;
    colorSpace = CGColorSpaceCreateDeviceRGB();
    cgContext = CGBitmapContextCreate(base, width, height, 8, bytesPerRow, colorSpace, kCGBitmapByteOrder32Little | kCGImageAlphaPremultipliedFirst);
    CGColorSpaceRelease(colorSpace);
    
    //透过 CGImageRef 将 CGContextRef 转换成 UIImage
    CGImageRef cgImage;
    UIImage *image;
    cgImage = CGBitmapContextCreateImage(cgContext);
    image = [UIImage imageWithCGImage:cgImage];
    CGImageRelease(cgImage);
    CGContextRelease(cgContext);
    
    CVPixelBufferUnlockBaseAddress(buffer, 0);
    
    return image;
 }
```

 
####31、

>1、类对象只能调用类方法
>2、实例对象只能调用实例方法

```
-[TestClass testNameWithString:]: unrecognized selector sent to instance 0x600003a648e0
```
```

```

>3、实例方法里的`self`，是对象的首地址
>4、类方法里的`self`,是`Class`


对象模型:实例--->类--->元类 
**查找一个方法是通过对象的isa,在isa对象的方法列表中查找指定对象**
如果传入的

>5、类方法在元类方法列表中
>6、实例方法在对象	方法列表中

####32、通过代码更新约束

```
    NSLayoutConstraint *bottom = [self.toolbarView.bottomAnchor constraintEqualToAnchor:self.view.safeAreaLayoutGuide.bottomAnchor];
    [bottom setActive:YES];
    bottom.constant = -height;
    [weakSelf.toolbarView setNeedsUpdateConstraints];

```