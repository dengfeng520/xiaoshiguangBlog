

<h1><center>从资源和代码方面为App瘦身处理</center></h1>

<h6 align='right'>小时光</h6>
<h6  align='right'>西安乐推网络科技有限公司</h6> 

--
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;对App包瘦身处理是为了减少包的大小，节约用户下载App流量。在App Store下载App，如果超过了150MB就必须在Wi-Fi环境下载或更新，这样如果超过了150M，可能就会间接失去了大部分用户。如果我们的App要兼容iOS7和iOS 8, [苹果官方规定:主二进制text段的大小不能超过60M](https://help.apple.com/app-store-connect/#/dev611e0a21f)，如果超过这个标准，就没办法向App Strore提交审核。

&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;下面这张表格列举了当前国内外的部分常用`App`的包大小。

|  APP | 版本|大小   | 
| :----:|:----:|:----:|
|支付宝|10.1.60|167.5M|
|淘宝|8.6.10|207.6M|
|微信|7.0.3|230M|
|滴滴|5.2.45|202.4M|
|抖音|5.7.0|170.2M|
|新浪微博|9.3.1|191.5M|
|网易云音乐|6.1.0|147.7M|
|Facebook|216.0|256.5M|
|Twitter|7.47|110.9M|
|Instagram|88.0|90.2M|

&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;通过上面的表格可以看出目前包少于150M的只有`网易云音乐`,`Instagram`,`Twitter `。那么作为开发者可以通过哪些方法对自己的`App`包大小做优化处理呢？

>1、苹果提供的 App Thinning

&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;我们知道不同的设备的屏幕大小、分辨率、`CPU`、`GPU`、硬盘大小等都不相同。针对这种情况，苹果官方为开发者提供了 `App Thinning`技术，对于不同设备提供只适用当前设备的包为用户提供下载。如`iPhone 5C`只会下载适用于32位运行芯片的库和2x的图片资源的包，`iPhone 8P`则会下载适用于64位的库和3x的图片资源。

######（1）、`App Thinning`有三种方式: `App Slicing`,`Bitcode`、`On-Demand Resources`。

 *  `App Slicing`：在向`iTunes Connect`上传`App`包后，对`App`做切割处理，创建不同的变体包，这样就可以适用到不同的设备；
* `Bitcode`:针对特定的设备进行包大小优化，优化效果不是很明细，一般优化的结果不是很理想。
* `On-Demand Resources`:主要是为又下多关卡的情况服务的，`On-Demand Resources`会根据用户的关卡下载进度下载随后几个关卡的资源，并且已经过关的资源会被干掉，这样可以减少初始`App`包的大小。
 
######（2）、那么如何在项目中适用`App Thinning`呢？

&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;在使用`App Thinning`的时候，大部分工作是由`Xcode`和`App Store`来完成的，在开发中只需要创建`xcassets`目录，然后将图片加入其中即可。在创建一个工程的时候`Xcode`会默认为开发者创建一个名为`Assets.xcassets`的目录，在开发过程中只要把图片资源加入其中即可。
![Asssts.xcassets](https://upload-images.jianshu.io/upload_images/1214383-2d55bcb91235bdd5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

当然也可以自己新建，选中工程目录`New File`--->`Resources`--->`Asset Catalog`即可新建一个。

![create xcassets ](https://upload-images.jianshu.io/upload_images/1214383-73f6427da5db8cba.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

 &ensp;&ensp;&ensp;&ensp;&ensp;&ensp; 我们在使用`xcassets`时，添加对应的2x分辨率和3x分辨率的图片，会在上传到`App Store`后被创建成不同安装包以减少`App`安装包的大小。同时苹果会自动根据的设备不同，在创建的安装包中自动加入当前设备需要的芯片指令集架构文件。

>2、删除废止、无用的资源

&ensp;&ensp;&ensp;&ensp;&ensp;&ensp; 在`App`的版本迭代中，我们不断的加入新的资源。但是由于后期的需求、设计、交互等变更，前期加的一些资源就没有用了，但是当时并没有删除这些资源，导致我们的资源越来越臃肿，`App`包越来越大。如我们的`App`中加入了圣诞版本的素材，但是这个版本之后这些素材就没用了。而删除这些无用资源也是最简单最有效的能为`App`包大小带来最明显的效果的操作，那么，该如找到并删除这些无用的资源呢？

* (1)、在提审前，使用最简单的方法，通过`Xcode`的`Find`功能在代码中搜索图片资源名称，如果没有搜到，说明没有没有使用这张图片，直接删除图片资源即可。但是这样做的比较麻烦，而且效率也比较低;
* (2)、通过`Mac`终端`Find`命令来搜索工程中的所有图片资源，确认是无用的图片资源后删除，此处可以使用系统提供的`NSFileManger`类来删除;
* (3)、此处为各位推荐一个开源工具[LSUnusedResources](https://github.com/tinymind/LSUnusedResources.git),`LSUnusedResources`不仅拥有`GUI`，而且添加和删除操作也很简单。

![LSUnusedResources](https://upload-images.jianshu.io/upload_images/1214383-813b078b2f62188f.gif?imageMogr2/auto-orient/strip)

* (4)、删除一些其他的文件，如在我们的项目中使用了[lottie](https://github.com/airbnb/lottie-ios)动画，所以在本地会有一些对应的`json`文件，对于废止的文件,可以通过上面的方法删除；


>3、图片资源压缩处理

试想，如果设计师给的图片非常大，一张图片达到了几十`KB`,甚至是几`MB`,如果这样的资源很多，那么最终打包下来的安装包自然就很大，如在我们的项目中设计师给出的一张全屏背景图仅1x的图片就达到了53KB，这样对图片的压缩处理就显得刻不容缓了。
![压缩前](https://upload-images.jianshu.io/upload_images/1214383-f062e1ca842fb263.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

我们要的目的是为了在不损失图片质量的情况下尽可能大的对图片做压缩处理，这样就能节省一些空间，减少安装包的大小。此处推荐`Google`的方案将图片转成[WebP](https://developers.google.com/speed/webp/)(这里需要梯子才能打开)。

######(1)、为什么要使用`WebP`
   * `WebP`压缩率高，肉眼基本看不出差异，支持有损和无损两种模式，压缩之后不仅不损失图片质量，官方提出相对于我们常用的`PNG`格式能减少26%的大小。
  * `WebP`支持`Alpha`透明和`24-bit`颜色数，不会像`PNG8`那样因为色彩不够出现毛边；
* `WebP`兼容目前主流的浏览器和平台；

###### (2)、`WebP`的安装

使用`Homebrew`命令安装`webp`,在终端输入：

```
brew install webp
```

如我要把`all_background.png`这张图片采用无损压缩方式转成`WebP`可使用命令:

```
cwebp -lossless all_background.png -o all_background.webp
```

压缩完成之后，只占了3K内存，优化效果非常明显。
![压缩后](https://upload-images.jianshu.io/upload_images/1214383-eff7a42c78adaafa.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

###### (3)、`WebP`的使用

`WebP`图片的加载可参考:[WebP解码支持（Objc & Swift）]([https://www.jianshu.com/p/f33422951795](https://www.jianshu.com/p/f33422951795))


```
let path : String = Bundle.main.path(forResource: "all_background", ofType: "webp")!;
self.backgroundImg.image = UIImage.init(webPPath: path);
```

关于`WebP`的更多使用方式可参考[官方网站](https://developers.google.com/speed/webp/docs/using)。

 >4、删除废止的代码文件

在产品的不断更新和迭代中，代码中不可避免的会删除或废止一些功能，但是作为开发者有时候并没有及时的删除这些废止的代码。这样随着代码量的增加，APP中的冗余或无效代码就越来越多，我们对代码瘦身的目的就是找出这些代码并删除。那么可以通过哪些方法来达到目的呢？

（1）、使用AppCode做静态分析

 可以直接使用[AppCode](https://www.jetbrains.com/objc/download/)来做静态分析，操作也很简单，打开 AppCode --->code--->Inspect Code；
![AppCode Inspect Code](https://upload-images.jianshu.io/upload_images/1214383-9fed250771fdc114.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
在静态分析完成之后，可以在`Unused code`中看到废弃的代码，如下图所示：
![Unused code](https://upload-images.jianshu.io/upload_images/1214383-ec5208465bf01a2c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* `Unused class`是废弃类
* `Unused import statement`是无用类引用申明
* `Unused property `是无用的属性
* `Unused method`是无用的方法
* `Unused parameter`是无用的参数
* `Unused instance variable`是无用的实例变量
* `Unused local variable`是无用的局部变量
* `Unused value`是无用的值
* `Unused macro`是无用的宏定义
* `Unused global decaration`是无用的全局申明