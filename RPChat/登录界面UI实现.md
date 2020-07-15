
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


![SignIn](https://user-gold-cdn.xitu.io/2020/7/1/173095ed9d6b11e0?w=202&h=386&f=png&s=121214)


新建登陆界面`Controller`命名为`SignInViewController`


在**AppDelegate**`didFinishLaunchingWithOptions launchOptions`回调方法中添加代码，设置SignInViewController为默认启动控制器：

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

* 1、`Auto Layout`

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

* 2、UI实现

新建一个`View`命名`SignInRootView`作为登录界面的主`View`，采用懒加载的方式初始化视图

最顶部的`Logo`图片实现代码：

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

用户名输入框实现代码：

```
 lazy var accountNumberView: UIView = {
        self.addSubview($0)
        $0.translatesAutoresizingMaskIntoConstraints = false
        let top = $0.topAnchor.constraint(equalTo: logoImg.bottomAnchor, constant: 20)
        let left = $0.leftAnchor.constraint(equalTo: self.leftAnchor, constant: 40)
        let right = $0.rightAnchor.constraint(equalTo: self.rightAnchor, constant: -40)
        let height = $0.heightAnchor.constraint(equalToConstant: 50)
        NSLayoutConstraint.activate([top, left, right, height])
        $0.layer.cornerRadius = 25
        return $0
    }(UIView())
 
    
    lazy var accountNumberLab: UITextField = {
        accountNumberView.addSubview($0)
        $0.translatesAutoresizingMaskIntoConstraints = false
        $0.topAnchor.constraint(equalTo: accountNumberView.topAnchor, constant: 0).isActive = true
        $0.leftAnchor.constraint(equalTo: accountNumberView.leftAnchor, constant: 16).isActive = true
        $0.rightAnchor.constraint(equalTo: accountNumberView.rightAnchor, constant: -16).isActive = true
        $0.bottomAnchor.constraint(equalTo: accountNumberView.bottomAnchor, constant: 0).isActive = true
        $0.font = UIFont.init(name: "PingFangTC-Semibold", size: 19)
        return $0
    }(UITextField())
```

密码输入框实现代码：

```
lazy var inputPasswordView: UIView = {
           self.addSubview($0)
           $0.translatesAutoresizingMaskIntoConstraints = false
           let top = $0.topAnchor.constraint(equalTo: accountNumberView.bottomAnchor, constant: 20)
           let left = $0.leftAnchor.constraint(equalTo: self.leftAnchor, constant: 40)
           let right = $0.rightAnchor.constraint(equalTo: self.rightAnchor, constant: -40)
           let height = $0.heightAnchor.constraint(equalToConstant: 50)
           NSLayoutConstraint.activate([top, left, right, height])
           $0.layer.cornerRadius = 25
           return $0
       }(UIView())
    
       
    lazy var inputPasswordTxt: UITextField = {
           inputPasswordView.addSubview($0)
           $0.translatesAutoresizingMaskIntoConstraints = false
           $0.topAnchor.constraint(equalTo: inputPasswordView.topAnchor, constant: 0).isActive = true
           $0.leftAnchor.constraint(equalTo: inputPasswordView.leftAnchor, constant: 16).isActive = true
           $0.rightAnchor.constraint(equalTo: inputPasswordView.rightAnchor, constant: -16).isActive = true
           $0.bottomAnchor.constraint(equalTo: inputPasswordView.bottomAnchor, constant: 0).isActive = true
           $0.font = UIFont.init(name: "PingFangTC-Semibold", size: 19)
           return $0
       }(UITextField())
```

登录按钮实现代码： 

```
lazy var signInBtn: UIButton = {
        self.addSubview($0)
        $0.translatesAutoresizingMaskIntoConstraints = false
        let top = $0.topAnchor.constraint(equalTo: inputPasswordView.bottomAnchor, constant: 20)
        let left = $0.leftAnchor.constraint(equalTo: self.leftAnchor, constant: 40)
        let right = $0.rightAnchor.constraint(equalTo: self.rightAnchor, constant: -40)
        let height = $0.heightAnchor.constraint(equalToConstant: 50)
        NSLayoutConstraint.activate([top, left, right, height])
        $0.layer.cornerRadius = 25
        $0.titleLabel?.font = UIFont.init(name: "PingFangTC-Semibold", size: 20)
        $0.setTitle(NSLocalizedString("Sign In", comment: ""), for: .normal)
        return $0
   }(UIButton())
```

此处代码较多，具体实现请看代码： [gitub RPChat](https://github.com/dengfeng520/RPChat)

* 3、设置背景颜色

由于设计师给出的颜色一般为16进制，此处需要做一个转码处理：

```
public class func hexStringToColor(_ hexadecimal: String) -> UIColor {
        var cstr = hexadecimal.trimmingCharacters(in:  CharacterSet.whitespacesAndNewlines).uppercased() as NSString;
        if(cstr.length < 6){
            return UIColor.clear;
        }
        if(cstr.hasPrefix("0X")){
            cstr = cstr.substring(from: 2) as NSString
        }
        if(cstr.hasPrefix("#")){
            cstr = cstr.substring(from: 1) as NSString
        }
        if(cstr.length != 6){
            return UIColor.clear;
        }
        var range = NSRange.init()
        range.location = 0
        range.length = 2
        let rStr = cstr.substring(with: range);
        range.location = 2;
        let gStr = cstr.substring(with: range)
        range.location = 4;
        let bStr = cstr.substring(with: range)
        var r :UInt32 = 0x0;
        var g :UInt32 = 0x0;
        var b :UInt32 = 0x0;
        Scanner.init(string: rStr).scanHexInt32(&r);
        Scanner.init(string: gStr).scanHexInt32(&g);
        Scanner.init(string: bStr).scanHexInt32(&b);
        return UIColor.init(red: CGFloat(r)/255.0, green: CGFloat(g)/255.0, blue: CGFloat(b)/255.0, alpha: 1)
    }
```

然后就可以用设计师给的16进制设置背景颜色：

```
signInBtn.backgroundColor = UIColor.hexStringToColor("0xF5BE62")
```

* 4、Drak Mode适配

由于`iOS 13`之后苹果处理`Drak Mode`,作为开发者也应该做相应的兼容处理。现在我在代码中并没有此时调整模拟器为暗模式，运行工程可以看到在暗模式下，用户名和密码输入框背景色不见了。此时就应该做暗模式的兼容处理。

![drak mode](https://user-gold-cdn.xitu.io/2020/7/1/1730940682f94d7e?w=1242&h=2688&f=png&s=128055)

此处我的做法也很简单，对`UIColor`做`extension`处理，然后再扩展方法中分别对`Drak Mode`和`Light Mode`两种模式做对应的处理，代码如下： 

```
extension UIColor {
   /// 当前是否是暗模式
    public class var drakMode: Bool {
        if #available(iOS 13.0, *) {
            let currentMode = UITraitCollection.current.userInterfaceStyle
            if currentMode == .dark {
                return true
            }
        }
        return false
    }
    public class func isDrakMode() -> Bool {
        if #available(iOS 13.0, *) {
            let currentMode = UITraitCollection.current.userInterfaceStyle
            if currentMode == .dark {
                return true
            }
        }
        return false
    }
    
    /// UIView背景颜色
    public class var darkModeViewColor: UIColor {
        if #available(iOS 13.0, *) {
            return .systemBackground
        } else {
            return .white
        }
    }
    public class func configDarkModeViewColor() -> UIColor {
        if #available(iOS 13.0, *) {
            return .systemBackground
        } else {
            return .white
        }
    }
    /// 文字颜色
    public class var darkModeTextColor: UIColor {
        if #available(iOS 13.0, *) {
            if drakMode == true {
                return .white
            } else {
                return .black
            }
        } else {
            return .black
        }
    }
    public class func configDarkModeTxtColor() -> UIColor {
        if #available(iOS 13.0, *) {
            if drakMode == true {
                return .white
            } else {
                return .black
            }
        } else {
            return .black
        }
    }
    /// 子UIView背景颜色
    public class var subDarkModeViewColor: UIColor {
        if #available(iOS 13.0, *) {
            if drakMode == true {
                return UIColor(red: 100/255, green: 100/255, blue: 100/255, alpha: 1)
            } else {
                return .groupTableViewBackground
            }
        } else {
            return .groupTableViewBackground
        }
    }
    public class func configSubDarkModeViewColor() -> UIColor {
        if #available(iOS 13.0, *) {
            if drakMode == true {
                return UIColor(red: 100/255, green: 100/255, blue: 100/255, alpha: 1)
            } else {
                return .groupTableViewBackground
            }
        } else {
            return .groupTableViewBackground
        }
    }
    
    /// 设置Placeholder文字颜色
    public class var placeholderColor: UIColor {
        if #available(iOS 13.0, *) {
            if drakMode == true {
                return UIColor(red: 255, green: 255, blue: 255, alpha: 0.25)
            } else {
                return UIColor(red: 0, green: 0, blue: 0, alpha: 0.25)
            }
        } else {
            return UIColor(red: 0, green: 0, blue: 0, alpha: 0.25)
        }
    }
    public class func configPlaceholderColor() -> UIColor {
        if #available(iOS 13.0, *) {
            if drakMode == true {
                return UIColor(red: 255, green: 255, blue: 255, alpha: 0.25)
            } else {
                return UIColor(red: 0, green: 0, blue: 0, alpha: 0.25)
            }
        } else {
            return UIColor(red: 0, green: 0, blue: 0, alpha: 0.25)
        }
    }
}
```


只需要在设置颜色时通过方法或者属性设置即可：

```
accountNumberView.backgroundColor = .subDarkModeViewColor
inputPasswordView.backgroundColor = .subDarkModeViewColor
```
```
accountNumberView.backgroundColor = UIColor.configSubDarkModeViewColor()
inputPasswordView.backgroundColor = UIColor.configSubDarkModeViewColor()
```

从结果上来说，这两种方式没有任何区别。一般用属性的话在设置或计算时看起来更自然一些，例如我用 `a = 0`会比`a = getData()` 看起来更直观自然一些。
《Clearn Code》一书中强调，对方法或函数的命名尽量使用动词或动词短语，所以在使用一个方法时，通常的代码表示要做一些事情。此处我只是修改`UIView`的背景颜色，所以我个人觉得使用属性设置更直观一些。


再次运行项目，可以看到界面已经兼容了暗模式：

![drak Mode](https://user-gold-cdn.xitu.io/2020/7/1/17309250eeaae02b?w=1242&h=2688&f=png&s=132283)

本文主要写了： 

* 不通过storyboard启动App
* 登录UI的实现
* Drak Mode的适配


[本文demo: Github RPChat](https://github.com/dengfeng520/RPChat)
