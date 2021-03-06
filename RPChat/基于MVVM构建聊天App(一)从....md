<h3><center>基于MVVM构建聊天App （一）</center></h3>
<h3><center>从新建工程开始</center></h3>

<h6 align='right'>小时光</h6>
<h6  align='right'>北京体适能体育科技有限公司</h6> 


**在开发一个新的App时不仅要考虑当前版本的需求，更要考虑到后期的版本迭代和维护工作 《Clean Code》一书中也提出`代码大部分时候是用来维护的，而不是用来实现功能的`。所以在前期的框架设计，技术调查上应该慎之又慎。本次我将从个人开发者角度围绕着代码的可维护性、可测试性、可复用性来实现一个简单的聊天App。**


### 1、基本设置

新建一个工程，命名为`RPChat`,此处开发语言选择`Swift`:

(1)、在工程中设置**Info.plist**中设置**Allow Arbitrary Loads**为YES，参考:[stackoverflow Transport security has blocked a cleartext HTTP](https://stackoverflow.com/questions/31254725/transport-security-has-blocked-a-cleartext-http)

![https配置](https://github.com/dengfeng520/xiaoshiguangBlog/blob/master/RPChat/Images/https%E9%85%8D%E7%BD%AE.png?raw=true)

(2)、打开`Assets.xcassets`按照指定的图片大小添加App的Logo图片。


![Logo](https://github.com/dengfeng520/xiaoshiguangBlog/blob/master/RPChat/Images/Logo.png?raw=true)

### 2、工程结构

添加三个`framework`分别命名为**RPChat_iOS、RPChatUIKit、RPChatDataKit**

![添加framework](https://github.com/dengfeng520/xiaoshiguangBlog/blob/master/RPChat/Images/addframework1.png?raw=true)

![添加framework](https://github.com/dengfeng520/xiaoshiguangBlog/blob/master/RPChat/Images/addframework2.png?raw=true)

![工程目录](https://github.com/dengfeng520/xiaoshiguangBlog/blob/master/RPChat/Images/addframework3.png?raw=true)

* **RPChat_iOS**是和UI的显示以及交互相关的代码
* **RPChatUIKit**是整个项目中会用到的对UIKit的公共扩展
* **RPChatDataKit**是整个项目的数据存储以及访问接口，也可以理解为是App的View Model以及Model

### 3、使用carthage管理第三方开源库

##### 1、carthage使用

```
carthage version
```

```
cd /Users/****/Desktop/GitHub/RPChat
```
```
touch Cartfile
```

使用VSCode打开`Cartfile`文件，输入用到的第三方开源库：

```
github "Alamofire/Alamofire" // Http请求封装
github "ReactiveX/RxSwift" // 用于管理App中的事件
github "onevcat/Kingfisher" // 用于App中的缓存和下载图片
github "SwiftyJSON/SwiftyJSON" // 生成Model
gitHub "CoderMJLee/MJRefresh" // UItableView下拉组件
github "robbiehanson/CocoaAsyncSocket" 
```
执行更新命令： 

```
carthage update --platform iOS --no-use-binaries
```

##### 2、Using Carthage with Xcode 12

升级Xcode 12后，执行上面的命令可能会报错，解决方法可参考：
[Using Carthage with Xcode 12](https://github.com/Carthage/Carthage/blob/master/Documentation/Xcode12Workaround.md)

然后使用以下命令执行`carthage`更新：

```
carthage.sh bootstrap --platform iOS --cache-builds
```

更新完成后，打开工程，选择**TARGETS** -->**Build Phases**--> **Link Binary With Libries** 点击加号，选择 **Add File** --> **Carthage** --> **Build** --> **iOS** 添加所需的`FrameWork`,


下面的gif演示了如何添加第三方的`FrameWork`；

![gif](https://github.com/dengfeng520/xiaoshiguangBlog/blob/master/RPChat/Images/addgif.gif?raw=true)

接下来，点击+，选择`New Run Script Phase`，此时新建了`Run Script`,在执行命令中添加:

```
/usr/local/bin/Carthage copy-frameworks
```

![New Run Script](https://github.com/dengfeng520/xiaoshiguangBlog/blob/master/RPChat/Images/NewRunScript.png?raw=true)



##### 3、解决`the file couldn’t be saved.`报错问题

添加完后再次build可能会报错：`the file couldn’t be saved. command phasescriptexecution failed with a nonzero exit code`

解决方案参考：[carthage copy-frameworks produces "The file couldn’t be saved." error #3056](https://github.com/Carthage/Carthage/issues/3056)

Add to `run scripts`
```
rm -rf ${TMPDIR}/TemporaryItems/*carthage*
/usr/local/bin/carthage copy-frameworks
```
再次build成功。

在`Input Files`中引入我们要用到的库的路径:

```
$(SRCROOT)/Carthage/Build/iOS/Alamofire.framework
```

其作用是把`Carthage`引入的第三方库在打包的时候，拷贝到特定目录。


![Carthage](https://github.com/dengfeng520/xiaoshiguangBlog/blob/master/RPChat/Images/Carthage.png?raw=true)


在上传到git仓库时，不需要传`Carthage`下的文件，所以选择忽略，在`.gitignore`文件中添加

![.gitignore](https://github.com/dengfeng520/xiaoshiguangBlog/blob/master/RPChat/Images/gitignore.png?raw=true)


```
*.DS_Store
Carthage/
xcuserdata/
.idea/
```
### 4、兼容iOS 13之前的老版本

修改最低支持版本为`iOS 11`

![iOS 最低版本](https://github.com/dengfeng520/xiaoshiguangBlog/blob/master/RPChat/Images/iOSVer.png?raw=true)

此时发现再Build工程，发现已经有很多`Error`，这是版本兼容的问题，由于`Xcode 11`新建的工程默认为当前最高版本，Xcode新增了一个`SceneDelegate`文件，具体作用请参考官方文档：[Optimizing App Launch](https://developer.apple.com/videos/play/wwdc2019/423/)。现在要在`AppDelegate`和中`SceneDelegate`做兼容老版本处理。 方法就是对当前系统做一个判断，然后再根据不同系统版本分开处理。

![版本报错](https://github.com/dengfeng520/xiaoshiguangBlog/blob/master/RPChat/Images/vererror.png?raw=true)


在`SceneDelegate`直接加上`@available(iOS 13.0, *)`即可,
在`AppDelegate`做扩展处理：

代码如下： 

```
@available(iOS 13.0, *)
class SceneDelegate: UIResponder, UIWindowSceneDelegate {

}
```

```
@available(iOS 13.0, *)
extension AppDelegate {
    func application(_ application: UIApplication, configurationForConnecting connectingSceneSession: UISceneSession, options: UIScene.ConnectionOptions) -> UISceneConfiguration {
         return UISceneConfiguration(name: "Default Configuration", sessionRole: connectingSceneSession.role)
    }
    func application(_ application: UIApplication, didDiscardSceneSessions sceneSessions: Set<UISceneSession>) {
    }
}
```

### 5、国际化

##### 1、常用文案国际化

* 1、如下图所示： 创建所需国际化的语言，此处我创建了英语、繁体汉语、简体汉语：


![Localizable1](https://github.com/dengfeng520/xiaoshiguangBlog/blob/master/RPChat/Images/Localizable1.png?raw=true)

 * 2、在工程中新建一个`String File`文件，命名为`Localizable`

![国际化2](https://github.com/dengfeng520/xiaoshiguangBlog/blob/master/RPChat/Images/Localizable2.png?raw=true)

 点击到Localizable.strings文件，点击右侧Localize...按钮添加需要国际化的语言。

![国际化3](https://github.com/dengfeng520/xiaoshiguangBlog/blob/master/RPChat/Images/Localizable3.png?raw=true)

此时来做个测试，在`ViewController`新建一个名为`testLab`的`UILabel`;

```
@IBOutlet weak var testLab: UILabel!
```
展开`Localize.strings`文件，在英文中添加`test;`,在简体中文中添加:

```
"test" = "测试";
```
在`ViewController`中调用，
```
testLab.text = NSLocalizedString("test", comment: "")
```

![test](https://github.com/dengfeng520/xiaoshiguangBlog/blob/master/RPChat/Images/test1.png?raw=true)


![测试](https://github.com/dengfeng520/xiaoshiguangBlog/blob/master/RPChat/Images/test2.png?raw=true)

此时点击Run可以看到`testLab`显示内容为`test`;

![English](https://github.com/dengfeng520/xiaoshiguangBlog/blob/master/RPChat/Images/test3.png?raw=true)

修改模拟器语言为简体汉语，再次运行：


![简体汉语](https://github.com/dengfeng520/xiaoshiguangBlog/blob/master/RPChat/Images/test4.png?raw=true)

##### 2、App名称国际化

* （1）、同样的方法新建一个名为`InfoPlist`的`String`文件。
*  (2)、在`InfoPlist.strings(English)`文件中添加:

   ```
   CFBundleName = "CatchU";
   ```
* (3)、在 `InfoPlist.strings(Chinese,Simplified)`中添加
  
   ```
   CFBundleName = "畅聊吧";
   ```
* (4)、 打开`Info.Plist`设置`Bundle name`属性为`$CFBundleName`


![Bundle name](https://github.com/dengfeng520/xiaoshiguangBlog/blob/master/RPChat/Images/test5.png?raw=true)

* (5)、再次运行代码，可以看到，App的名称已经修改成功了。


![App Name](https://github.com/dengfeng520/xiaoshiguangBlog/blob/master/RPChat/Images/icon.png?raw=true)

本篇主要讲了App的新建和相关配置，包括：
*  新建工程，基本配置
* 通过三个`frameworks`去实现App不同层次的功能
* 通过`Carthage`管理工程中用的第三方开源库
* 对iOS 13之前的系统做一个兼容处理
* 国际化相关
