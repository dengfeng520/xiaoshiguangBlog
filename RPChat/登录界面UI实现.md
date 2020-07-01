
<h3><center>基于MVVM构建聊天App （二）</center></h3>
<h3><center>登录UI实现</center></h3>

### 1、一个完整的开发流程

一般的，一个正常的流程包括：

* 产品定需求，给出原型图
* 团队确认需求
* 由设计师开始设计图，同步开发做开发前的准备工作，如技术调查，前后端如何配合等
* 设计师完成设计后，团队对比设计图和原型图再次确认需求
* 开发团队开始开发工作
* 开发完成后由开发者自测 一般我们在开发中编写的单元测试代码也属于自测
* 开发者自测后由产品测试，事实上在开发过程中，产品也应该实时的关注开发
* 交由专业测试人员做测试
* 发布内测版本，做大规模测试
* 提交App Store审核


### 2、storyboard的App启动过程

**UIApplicationMain**

一般的如果新建了一个`Objective-C`工程，默认先`main`函数，`main`函数内部会调用`UIApplicationMain`函数。


```
int main(int argc, char * argv[]) {
    @autoreleasepool {
        return UIApplicationMain(argc, argv, nil, NSStringFromClass([AppDelegate class]));
    }
}
```

但在`Swift`项目中，并没有`main.m`文件也没有`main`函数。`Swift`工程是在`AppDelegate`中用一个`@UIApplicationMain`标签。那么`UIApplicationMain`做了哪些操作呢：

  * 创建`UIApplication`单例对象
  * 创建`UIApplication`的委托对象，即`AppDelegate`
  * 开启事件循环监听系统事件，当坚挺到对应的系统事件时会通知`AppDelegate`
* 创建最底层的`UIWindows`对象，
* 读取`Info.plist`中设置的默认启动`storyboard`文件名称
* 加载设置的`is Initial View Controller`**storyboard**文件，同时创建对应的`View Controller`对象
* 设置创建的`View Controller`为`UIWindows`的根视图`rootViewController`
* 显示`Windows`上的试图

 运行工程可以看到如下图： 最底层是一个**UIWindwos**

![windows](https://user-gold-cdn.xitu.io/2020/6/28/172fa5dc09551fd3?w=919&h=828&f=png&s=431032)


### 3、没有storyboard的启动

 一般的一个工程，默认从`main.storyboard`中的设置的`Initial view controller`启动的，那么我们该如何设置自己的初始启动`View Controller`呢？

在前面我们工程的结构：**RPChat_iOS**文件夹下是UI显示以及交互相关的代码，所以在**RPChat_iOS**中新建一个**SignIn文件夹**该目录下为登录相关的UI代码：

新建登陆界面`Controller`命名为`SignInViewController`


在**AppDelegate**`didFinishLaunchingWithOptions launchOptions`回调方法中添加代码：

```
if #available(iOS 13, *) {
    
} else {
     window = UIWindow.init()
     window?.frame = UIScreen.main.bounds
     window?.makeKeyAndVisible()
     let signInVC = SignInViewController()
     window?.rootViewController = signInVC
}
```

在**SceneDelegate**`options connectionOptions`方法中添加代码：

```
guard let windowScene = (scene as? UIWindowScene) else { return }

window = UIWindow(frame: windowScene.coordinateSpace.bounds)
window?.windowScene = windowScene
window?.backgroundColor = .white
let tabBar = SignInViewController()
window?.rootViewController = tabBar
window?.makeKeyAndVisible()
```


### 4、登录UI的实现

先看一下这是最终效果图：

![light Mode](https://user-gold-cdn.xitu.io/2020/7/1/1730924e872c0e71?w=1242&h=2688&f=png&s=130841)

> 1、`Auto Layout`

至于UI，考虑到版本兼容和后期维护我采用了系统`NSLayoutAnchor`适配，

`NSLayoutAnchor`常用属性

* leadingAnchor
* trailingAnchor
* leftAnchor
* rightAnchor
* topAnchor
* bottomAnchor
* widthAnchor
* heightAnchor
* centerXAnchor
* centerYAnchor
* firstBaselineAnchor
* lastBaselineAnchor

关于`Auto Layout`其他更多使用细节请参考官方文档:

[High Performance Auto Layout](https://developer.apple.com/videos/play/wwdc2018/202/)

[Apple Develope NSLayoutConstraint](https://developer.apple.com/documentation/uikit/nslayoutconstraint)

[Apple Develope NSLayoutAnchor](https://developer.apple.com/documentation/uikit/nslayoutanchor)

[WWDC 2018 What's New in Cocoa Touch](https://developer.apple.com/videos/play/wwdc2018/202/)

> 2、UI实现

新建一个`View`命名`SignInRootView`作为登录界面的主`View`：

```
lazy var logoImg: UIImageView = {
        self.addSubview($0)
        $0.translatesAutoresizingMaskIntoConstraints = false
        let top = $0.topAnchor.constraint(equalTo: self.safeAreaLayoutGuide.topAnchor, constant: 44)
        let centerX = $0.centerXAnchor.constraint(equalTo: self.centerXAnchor, constant: 0)
        let width = $0.widthAnchor.constraint(equalToConstant: 120)
        let height = $0.heightAnchor.constraint(equalToConstant: 120)
        NSLayoutConstraint.activate([top, centerX, width, height])
        return $0
    }(UIImageView())
```


> 3、Drak Mode适配

由于`iOS 13`之后苹果处理`Drak Mode`,作为开发者也应该做相应的兼容处理。现在我在代码中并没有此时调整模拟器为暗模式，运行工程可以看到在暗模式下，用户名和密码输入框背景色不见了。此时就应该做暗模式的兼容处理。

![drak mode](https://user-gold-cdn.xitu.io/2020/7/1/1730940682f94d7e?w=1242&h=2688&f=png&s=128055)

此处我的做法也很简单，对`UIColor`做`extension`处理，然后再扩展方法中分别对`Drak Mode`和`Light Mode`两种模式做对应的处理，代码如下： 

```
open class func configDarkModeViewColor() -> UIColor {
        let retColor: UIColor!
        if #available(iOS 13.0, *) {
            retColor = UIColor { (collection) -> UIColor in
                if (collection.userInterfaceStyle == .dark) {
                    return UIColor.init(red: 100/255, green: 100/255, blue: 100/255, alpha: 1)
                }
                return UIColor.white
            }
        } else {
            return UIColor.white
        }
        return retColor
    }
    
  open class func configDarkModeViewColorWithdDfaultColor(dfaultColor: UIColor) -> UIColor {
        let retColor: UIColor!
        if #available(iOS 13.0, *) {
            retColor = UIColor { (collection) -> UIColor in
                if (collection.userInterfaceStyle == .dark) {
                    return UIColor.init(red: 100/255, green: 100/255, blue: 100/255, alpha: 1)
                }
                return dfaultColor
            }
        } else {
            return dfaultColor
        }
        return retColor
    }
    
```

只需要在设置颜色时调用即可：

```
view.backgroundColor = UIColor.configDarkModeViewColorWithdDfaultColor(dfaultColor: UIColor.groupTableViewBackground)
```

再次运行项目，可以看到界面已经完美兼容了暗模式

![drak Mode](https://user-gold-cdn.xitu.io/2020/7/1/17309250eeaae02b?w=1242&h=2688&f=png&s=132283)