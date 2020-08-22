#### 3、网络请求封装

参考[猫神](https://onevcat.com/#blog)的[面向协议编程与 Cocoa 的邂逅 (上)](https://onevcat.com/2016/11/pop-cocoa-1/)一文中的方案封装。我使用了**[Alamofire](https://github.com/Alamofire/Alamofire)**和**[SwiftyJSON](https://github.com/SwiftyJSON/SwiftyJSON)**两个开源框架。核心代码如下：

```
import Alamofire
import SwiftyJSON

protocol Request {
    var path: String? {get}
    var method: HTTPMethod {get}
    var parameter: [String: AnyObject]? {get}
    var host: String? {get}
}

extension Request {
    var parameter: [String: AnyObject] {
        return [:]
    }
}
enum ApiResult {
    case success(JSON)
    case failure(RequestError)
}

enum RequestError: Error {
    case unknownError
    case connectionError
    case authorizationError(JSON)
    case invalidRequest
    case notFound
    case invalidResponse
    case serverError
    case authenticationFailed(JSON)
    case serverUnavailable
    case statusCodeError(JSON)
    case isAlert(JSON)
    // 登录失效
    case signInExpired
}

protocol Request {
    var path: String? {get}
    var method: HTTPMethod {get}
    var parameter: [String: AnyObject]? {get}
    var host: String? {get}
}

extension Request {
    var parameter: [String: AnyObject] {
        return [:]
    }
}

protocol RequestSender {
    func requestWithMap<T: Request>(_ r: T, completion: @escaping (ApiResult) -> Void)
}

extension RequestSender {
    // 可选方法
    func requestWithMap<T: Request>(_ r: T, completion: @escaping (ApiResult) -> Void) {
        
    }
    func authRemoteAPIWith<T: Request>(_ r: T, completion: @escaping (ApiResult) -> Void) {
        
    }
}


struct HTTPRequest: RequestSender {
    // MARK: - POST
    func requestWithMap<T: Request>(_ r: T, completion: @escaping (ApiResult) -> Void) {
        guard r.path != nil else {
            completion(ApiResult.failure(.serverError))
            return
        }
        guard r.host != nil else {
            completion(ApiResult.failure(.serverError))
            return
        }
        guard r.host?.isEmpty == false else {
            completion(ApiResult.failure(.serverError))
            return
        }
        let path = URL(string: r.host!.appending(r.path!))!
        var headers: HTTPHeaders!
        // 缓存token
        if let token = AccountData.fetchToken() {
            headers = ["Content-Type" : "application/x-www-form-urlencoded; application/json; charset=utf-8;",
                       "Cookie" : "host=a",
                       "Authorization" : "Bearer \(token)"]
        } else {
            let authorization = "Basic " + "app:app".base64Encoded!
            headers = ["Content-Type" : "application/x-www-form-urlencoded; application/json; charset=utf-8;",
                       "Cookie" : "host=a",
                       "Authorization" : "\(authorization)"]
        }
        // body
        guard let body = r.parameter else {
            return
        }
        
        AF.request(path, method: r.method, parameters: body, headers: headers).response { response in
            if let error = response.error {
                print(error)
                completion(ApiResult.failure(.connectionError))
            } else if let data = response.data ,let responseCode = response.response {
                do {
                    let responseJson = try JSON(data: data)
                    switch responseCode.statusCode {
                    case 200:
                        completion(ApiResult.success(responseJson))
                    case 201:
                        completion(ApiResult.failure(.isAlert(responseJson)))
                    case 400...499:
                        completion(ApiResult.failure(.authorizationError(responseJson)))
                    case 500...599:
                        completion(ApiResult.failure(.serverError))
                    case 601:
                        completion(ApiResult.failure(.authorizationError(responseJson)))
                    case 602:
                        completion(ApiResult.failure(.authorizationError(responseJson)))
                    default:
                        completion(ApiResult.failure(.unknownError))
                        break
                    }
                }
                catch let parseJSONError {
                    completion(ApiResult.failure(.unknownError))
                    print("error on parsing request to JSON : \(parseJSONError)")
                }
            }
        }
    }
}
```


#### 4、AES 加密
 
 此处需要用到第三方库，**[CryptoSwift](https://github.com/krzyzanowskim/CryptoSwift)**,要用到的key也要和后台协议好。 代码如下： 
 
 ```
 import CryptoSwift
 
 public class ConfigAES: NSObject {
    /// 密钥
    let publicKey = "thanks,pig4cloud"
    
    public func encryptStringWith(strToEncode: String) -> String {
        
        let data = strToEncode.data(using: String.Encoding.utf8)
        // byte 数组
        var encrypted: [UInt8] = []
        let (key, iv) = fetchAESKeyAndIv(publicKey.base64Encoded!)
        do {
            encrypted = try AES(key: key, blockMode: CBC(iv: iv), padding: .zeroPadding).encrypt(data!.bytes)
        } catch {
            print(error)
        }
        let encoded = Data(encrypted)
        //加密结果要用Base64转码
        return encoded.base64EncodedString()
    }
    
    func fetchAESKeyAndIv(_ base64edMixedKey: String) -> (Array<UInt8>, Array<UInt8>) {
        let key = Array<UInt8>(Data(base64Encoded: base64edMixedKey)!)
        let iv = Array<UInt8>(Data(base64Encoded: base64edMixedKey)!)
        
        return (key, iv)
    }
}

 ```
 使用方法：
 
 ```
 let pwd = ConfigAES().encryptStringWith(strToEncode: data)
 ```
