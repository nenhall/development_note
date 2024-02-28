## AFNetworking有5个模块：

* NSURLSession 网络请求模块

* Reachability 监测网络状态模块

* Security 安全策略模块

* Serialization 序列化模块

* UIKit UI相关的一些类目

  

#### 一、NSURLSession 网络请求模块

**AFURLSessionManager：** AFHTTPSessionManager 继承自AFURLSessionManager

#### **网络请求的过程：**

> 创建NSURLSessionConfig对象--用创建的config对象配置初始化NSURLSession--创建NSURLSessionTask对象并resume执行，用delegate或者block回调返回数据。

**AFURLSessionManager请求过程：**
1. 初始化AFURLSessionManager。
2. 获取AFURLSessionManager的Task对象
3. 启动Task：
   * AFURLSessionManager会为每一个Task创建一个AFURLSessionmanagerTaskDelegate对象，manager会让其处理各个Task的具体事务，从而实现了manager对多个Task的管理

   * 初始化好manager后，获取一个网络请求的Task，生成一个Task对象，并创建了一个AFURLSessionmanagerTaskDelegate并将其关联，设置Task的上传和下载delegate，通过KVO监听download进度和upload进度
4. NSURLSessionDelegate的响应：
   因为AFURLSessionmanager所管理的AFURLSession的delegate指向其自身，因此所有的
   NSURLSessiondelegate的回调地址都是AFURLSessionmanager，而AFURLSessionmanager又会根据是否需要具体处理会将AF delegate所响应的delegate，传递到对应的AF delegate去



#### 二、Reachability 监测网络状态模块

##### AFNetworkReachabilityManager



#### 三、Security 安全策略模块

##### AFSecurityPolicy

> iOS项目将服务器端的证书保存导入到项目中，AFN根据项目中的服务器证书来进行验证，验证服务器，保证访问服务器的安全性。
>
> **验证证书的模式有三种：**
>
> 1. AFSSLPinningModeNone 不验证
> 2. AFSSLPinningModePublicKey 只验证公钥
> 3. AFSSLPinningModeCertificate 验证证书的所有内容



#### 四、Serialization 序列化模块

网络通信信息序列化、反序列化模块

AFURLRequestSerialization
AFURLResponseSerialization





#### 五、UIKit UI相关的一些类目