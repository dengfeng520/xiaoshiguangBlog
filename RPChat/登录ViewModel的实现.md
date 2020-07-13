

#### 1、

![工程目录](https://user-gold-cdn.xitu.io/2020/6/15/172b76dc82706398?w=437&h=466&f=png&s=222750)

前面说过**RPChatDataKit.framework**是用来存放`ViewModel`和`Model`。在**RPChatDataKit**中新建`SignInViewModel`和`SignInModel`文件。

* 2、导入第三方开源库

由于我的工程中**RPChatDataKit**需要用到第三方库，此处需要导入几个用的库：
点击工程 --> TARGETS --> RPChatDataKit --> Build Phases --> Link Binary With Libraries --> 点击+  添加需要用到的`RXSwift`和`SwiftyJSON`。

![导入第三方开源库](https://user-gold-cdn.xitu.io/2020/7/6/17322e3a0f79754b?w=1227&h=426&f=png&s=288690)

