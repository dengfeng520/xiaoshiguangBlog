
<h3><center>登录ViewModel和Controller的实现</center></h3>


#### 1、准备工作
 
 * **（1）、导入第三方开源库**

由于我的工程中**RPChatDataKit**需要用到第三方库，此处需要导入几个用的库：
点击工程 --> TARGETS --> RPChatDataKit --> Build Phases --> Link Binary With Libraries --> 点击+  添加需要用到的**[RxSwift](https://github.com/ReactiveX/RxSwift)**和**[SwiftyJSON](https://github.com/SwiftyJSON/SwiftyJSON)**。

![导入第三方开源库](https://user-gold-cdn.xitu.io/2020/7/6/17322e3a0f79754b?w=1227&h=426&f=png&s=288690)
 
 * **（2）、Loading View 的显示和隐藏**

 [RPToastView](https://github.com/dengfeng520/RPToastView)
 
 ```
 extension RPToastView {
    /// loading
    public class func showLoading(_ withView: UIView?) {
        guard let withView = withView else { return }
        DispatchQueue.main.async {
            var display = Display()
            display.isView = withView
            display.mode = .rotateAndTextMode
            display.title = "Loading..."
            RPToastView.loading(display)
        }
    }
    /// toast
    public class func showToast(_ withView: UIView?, _ labelTxt: String?) {
        guard let withView = withView, let labelTxt = labelTxt else { return }
        DispatchQueue.main.async {
            var display = Display()
            display.isView = withView
            display.mode = .onlyTextMode
            display.title = labelTxt
            RPToastView.loading(display)
        }
    }
    /// dissmiss
    public class func dissmissLoading() {
        DispatchQueue.main.async {
            RPToastView.hidden(animation: true)
        }
    }
}
```
 
 对`View Controller`扩展并用一个属性来控制Loading View的显示和隐藏，具体代码:
 
 ```
 extension Reactive where Base: UIViewController {
    public var isAnimating: Binder<Bool> {
        return Binder(self.base, binding: { (vc, active) in
            if active == true {
                RPToastView.showLoading(vc.view)
            } else {
                RPToastView.dissmissLoading()
            }
        })
    }
}
```
 
 * **（3）、AES 加密处理**
 
和后台同事约定需要**AES加密**方式加密，在这里我才用第三方库**[CryptoSwift](https://github.com/krzyzanowskim/CryptoSwift)**库。对**String**扩展加密处理即可。具体可参考`CryptoSwift`开源文档。具体代码如下：

```
import CryptoSwift

extension String {
    /// 公钥
    private var publicKey: String {
        return "thanks,pig4cloud"
    }
    
    /// AES 加密处理
    public var encryptString: String {
        let data = self.data(using: String.Encoding.utf8)
        // byte 数组
        var encrypted: [UInt8] = []
        let (key, iv) = fetchAESKeyAndIv(publicKey.base64Encoded!)
        do {
            encrypted = try AES(key: key, blockMode: CBC(iv: iv), padding: .zeroPadding).encrypt(data!.bytes)
        } catch {
            print(error)
        }
        let encoded = Data(encrypted)
        // 加密结果要用Base64转码
        return encoded.base64EncodedString()
    }
    
    private func fetchAESKeyAndIv(_ base64edMixedKey: String) -> (Array<UInt8>, Array<UInt8>) {
        let key = Array<UInt8>(Data(base64Encoded: base64edMixedKey)!)
        let iv = Array<UInt8>(Data(base64Encoded: base64edMixedKey)!)
        
        return (key, iv)
    }
}

```

#### 2、登录界面的ViewModel

前面说过**RPChatDataKit.framework**是用来存放`ViewModel`和`Model`。在**RPChatDataKit**中新建`SignInViewModel`和`SignInModel`文件。 

![工程目录](https://user-gold-cdn.xitu.io/2020/6/15/172b76dc82706398?w=437&h=466&f=png&s=222750)

* 1、

```
public let inputStuNum = BehaviorSubject<String>(value: "")
public let inputPassWord = BehaviorSubject<String>(value: "")
```

* 2、控制UI状态的Subject

```
public let inputStuNumEnabled = BehaviorSubject<Bool>(value: true)
public let inputPassWordEnabled = BehaviorSubject<Bool>(value: true)
/// 控制signIn Button 是否可点击
public lazy var signInButtonEnabled = Observable.combineLatest( inputStuNum.asObservable(),
                                                                    inputStuNumEnabled.asObservable(),
                                                                    inputPassWord.asObservable(),
                                                                    inputPassWordEnabled.asObservable()) {
        (e: String, es: Bool, p: String, ps: Bool) -> Bool in
        return (e.count >= 6 && p.count >= 6 && !e.isEmpty && !p.isEmpty && es && ps)
    }.share()
```

* 3、请求服务相关的Subject

```
/// 表示错误状态的Subject
public let errorSubject : PublishSubject<String> = PublishSubject()
/// 成功状态Subject
public let successSubject: PublishSubject<String> = PublishSubject()
/// show  or dissmiss LoadingView Subject
public let loading : PublishSubject<Bool> = PublishSubject()
```

* 4、点击登录时 禁用UITextFile输入 和 按钮点击

```
/// 点击登录时 禁用UITextFile输入
private func indicateSigningIn() {
   inputStuNumEnabled.onNext(false)
   inputPassWordEnabled.onNext(false)
}
/// 登录失败时 重置
private func indicateSignInError() {
   inputStuNumEnabled.onNext(true)
   inputPassWordEnabled.onNext(true)
}
```
 
#### 3、登录界面的View Controller绑定View Model
 
 
 至此，登录界面的View Model的部分就实现完了。我在`SignInViewModel`中并没有引用`UIKit`,只引入了`import RxSwift`,也就是说它并不包含任何实际与UI相关的代码。

* 1、绑定View Model

* 2、响应登录按钮点击事件

* 3、绑定UI状态和View Model

* 4、UI交互和绑定
