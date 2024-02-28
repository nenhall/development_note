**首先来看一张相关联的图**(这图来自于百度搜出来的，做得很好，就直接拿来用了)，**接下来分别对每个关联类进行讲解**

![](https://blogimage-1257063273.cos.ap-guangzhou.myqcloud.com/20190409113333.png)

## NSURLRequest ( NSMutableURLRequest )

1. NSURLRequest：无法自定义请求设置，如get、post、boby，这种情况只能用`NSMutableURLRequest`

2. 常用属性说明：

   ```objective-c
   //缓存策略
   @property (readonly) NSURLRequestCachePolicy cachePolicy;
   
   //请求超时时间
   @property (readonly) NSTimeInterval timeoutInterval;
   
   //主文档地址 这个地址用来存放缓存
   @property (nullable, readonly, copy) NSURL *mainDocumentURL;
   
   //获取网络请求的服务类型 枚举如下
   @property (readonly) NSURLRequestNetworkServiceType networkServiceType API_AVAILABLE(macos(10.7), ios(4.0), watchos(2.0), tvos(9.0));
   
   //获取是否允许使用服务商蜂窝网络
   @property (readonly) BOOL allowsCellularAccess  API_AVAILABLE(macos(10.8), ios(6.0), watchos(2.0), tvos(9.0));
   
   //HTTP请求方式  POST GET 等
   @property (nullable, readonly, copy) NSString *HTTPMethod;
   
    //得到一个字典数据，设置的 HTTP请求头的键值数据
   @property (nullable, readonly, copy) NSDictionary<NSString *, NSString *> *allHTTPHeaderFields;
   
   //设置http请求头中的字段值
   - (nullable NSString *)valueForHTTPHeaderField:(NSString *)field;
   
   //请求体
   @property (nullable, readonly, copy) NSData *HTTPBody;
    
   //http请求体的输入流
   @property (nullable, readonly, retain) NSInputStream *HTTPBodyStream;
    
   //设置发送请求时是否发送cookie数据
   @property (readonly) BOOL HTTPShouldHandleCookies;
    
   //设置的请求时是否按顺序收发 默认禁用 在某些服务器中设为YES可以提高网络性能
   @property (readonly) BOOL HTTPShouldUsePipelining API_AVAILABLE(macos(10.7), ios(4.0), watchos(2.0), tvos(9.0));
    
   //设置http请求头中的字段值
   - (void)setValue:(nullable NSString *)value forHTTPHeaderField:(NSString *)field;
    
   //向http请求头中添加一个字段
   - (void)addValue:(NSString *)value forHTTPHeaderField:(NSString *)field;
   ```

   

3. 基本使用：

   ```objective-c
   NSMutableURLRequest *mRequest = [[NSMutableURLRequest alloc] initWithURL:url];
   mRequest.cachePolicy = NSURLRequestReturnCacheDataDontLoad;
   mRequest.HTTPMethod = @"POST";
   mRequest.HTTPBody = [@"" dataUsingEncoding:NSUTF8StringEncoding];
   [mRequest setValue:@"..." forHTTPHeaderField:@"..."];
   ```

3. NSURLRequestCachePolicy

```objective-c
typedef NS_ENUM(NSUInteger, NSURLRequestCachePolicy)
{
    //    默认缓存策略
    NSURLRequestUseProtocolCachePolicy = 0,
    
    //    URL应该加载源端数据，不使用本地缓存数据
    NSURLRequestReloadIgnoringLocalCacheData = 1,
    
    //    本地缓存数据、代理和其他中介都要忽视他们的缓存，直接加载源数据
    NSURLRequestReloadIgnoringLocalAndRemoteCacheData = 4, // Unimplemented
    
    //    从服务端加载数据，完全忽略缓存。和NSURLRequestReloadIgnoringLocalCacheData一样
    NSURLRequestReloadIgnoringCacheData = NSURLRequestReloadIgnoringLocalCacheData,
    
    //    使用缓存数据，忽略其过期时间；只有在没有缓存版本的时候才从源端加载数据
    NSURLRequestReturnCacheDataElseLoad = 2,
    
    //    只使用cache数据，如果不存在cache，就请求失败，不再去请求数据  用于没有建立网络连接离线模式
    NSURLRequestReturnCacheDataDontLoad = 3,
    
    //    指定如果已存的缓存数据被提供它的源段确认为有效则允许使用缓存数据响应请求，否则从源段加载数据。
    NSURLRequestReloadRevalidatingCacheData = 5, // Unimplemented
};
```

## NSURLSessionConfiguration

> 1. 为 NSURLSession 会话定义行为和策略的配置对象；
> 2. 当一个会话被创建时，配置对象的副本被创建，你不能修改 session 的配置在它被创建之后；
> 3. 您可以使用该对象来配置超时值、缓存策略、连接需求以及您打算使用 NSURLSession 对象的其他类型的信息；
> 4. 一旦配置完毕，会话对象将忽略您对NSURLSessionConfiguration对象所做的任何更改。
>
> **注意：**
> 在某些情况下，在此配置中定义的策略可能被用于任务的 NSURLRequest 对象指定的策略覆盖。除非会话的策略更加严格，否则在 NSURLRequest 对象上指定的任何策略都是受尊敬的。

1. 常规属性

   ```objective-c
   //默认的会话配置使用一个持久的基于磁盘的缓存(除非结果被下载到文件中)，并在用户的钥匙串中存储凭据。
   @property (class, readonly, strong) NSURLSessionConfiguration *defaultSessionConfiguration;
   
   //返回一个会话配置，该配置不使用持久存储来存储缓存、cookie或凭证。  
   //一个临时会话配置对象与默认会话配置对象类似，只是对应的会话对象不存储缓存、凭据存储或任何与会话相关的数据到磁盘。    
   相反，会话相关数据存储在RAM中。短暂会话将数据写入磁盘的惟一时间是当您告诉它将URL的内容写入文件时。
   @property (class, readonly, strong) NSURLSessionConfiguration *ephemeralSessionConfiguration;
   
   /*
   返回一个会话配置对象，该对象允许在后台执行HTTP和HTTPS上传或下载。  
   当应用程序在后台运行时，使用此方法初始化一个适合于传输数据文件的配置对象。  
   配置此对象的会话将传输的控制权移交给系统，该系统在一个单独的进程中处理传输。  
   在iOS中，这种配置使得即使应用程序本身被暂停或终止，传输也可以继续进行。 
   */
   + (NSURLSessionConfiguration *)backgroundSessionConfigurationWithIdentifier:(NSString *)identifier API_AVAILABLE(macos(10.10), ios(8.0), watchos(2.0), tvos(9.0));
   
   /*
   配置对象的后台会话标识符。  
   只有在使用backgroundSessionConfigurationWithIdentifier:方法创建配置对象时，才会设置此属性的值。 
   */
   @property (nullable, readonly, copy) NSString *identifier;
   
   /*
   确定何时从缓存返回响应的预定义常量。  
   此属性根据此配置确定会话中任务使用的请求缓存策略。  
   默认值是 NSURLRequestUseProtocolCachePolicy
   */
   @property NSURLRequestCachePolicy requestCachePolicy;
   
   /*
   等待额外数据时使用的超时间隔。  
   此属性根据此配置确定会话中所有任务的请求超时时间间隔。  
   请求超时间隔控制任务在放弃之前等待额外数据到达的时间(以秒为单位)。  
   每当新数据到达时，与此值关联的计时器将被重置。  
   当请求计时器到达指定的时间间隔而不接收任何新数据时，它会触发超时。  
   默认值 60 
   */
   @property NSTimeInterval timeoutIntervalForRequest;
   
   /*
   应该允许资源请求使用的最长时间。  
   此属性根据此配置确定会话中所有任务的资源超时间隔。  
   资源超时间隔控制在放弃之前等待整个资源传输的时间(以秒为单位)。  
   资源计时器在启动请求时启动并计数，直到请求完成或到达此超时间隔为止，无论哪个优先。默认值为7天。
   */
   @property NSTimeInterval timeoutIntervalForResource;
   
   // 网络服务的类型
   @property NSURLRequestNetworkServiceType networkServiceType;
   
   /*
   该值决定是否应该通过蜂窝网络进行连接，默认值是YES。
   此属性控制基于此会话配置的会话中的任务是否允许通过蜂窝网络进行连接。  
   */
   @property BOOL allowsCellularAccess;
   
   /* 一个布尔值，该值指示会话是否应该等待连接可用，或立即失败。 
   由于几个原因，连接可能暂时不可用。  
    例如，当allowsCellularAccess设置为false时，设备可能只有一个蜂窝连接，或者设备可能需要VPN连接，但没有可用的连接。  
    如果该属性的值为true，且没有足够的连接，会话将调用URLSession:taskIsWaitingForConnectivity: NSURLSessionTaskDelegate的方法并等待连接。  
    当连接可用时，任务开始工作，并最终调用委托或完成处理程序。  
    如果该属性的值为false，且连接不可用，则该连接将立即出现错误，如NSURLErrorNotConnectedToInternet。 
    此属性仅在建立连接时相关。  
    如果建立了连接，然后掉线，则完成处理程序或委托会接收到一个错误，如NSURLErrorNetworkConnectionLost。 
    默认值是 NO  
    此属性被后台会话忽略，而后台会话总是等待连接。
    */
   @property BOOL waitsForConnectivity API_AVAILABLE(macos(10.13), ios(11.0), watchos(4.0), tvos(11.0));
   
   /*
   它决定是否可以根据系统的自由裁量来安排后台任务，以获得最佳性能。 
   对于使用backgroundSessionConfigurationWithIdentifier:方法创建的配置对象，可以使用此属性在传输发生时对系统进行控制。对于使用其他方法创建的配置对象，此属性将被忽略。
   */
   @property (getter=isDiscretionary) BOOL discretionary API_AVAILABLE(macos(10.10), ios(7.0), watchos(2.0), tvos(9.0));
   
   /*
   后台URL会话文件被下载的共享容器的标识符。  
   要创建一个用于应用程序扩展的URL会话，必须将该属性设置为应用程序扩展与其包含的应用程序之间共享的容器的有效标识符。
   */
   @property (nullable, copy) NSString *sharedContainerIdentifier API_AVAILABLE(macos(10.10), ios(8.0), watchos(2.0), tvos(9.0));
   
   /*
   当后台任务完成或需要auth时，允许应用程序在后台重新启动。  
   这只适用于使用+backgroundSessionConfigurationWithIdentifier创建的配置: 默认值是YES。
   */
   @property BOOL sessionSendsLaunchEvents API_AVAILABLE(ios(7.0), watchos(2.0), tvos(9.0)) API_UNAVAILABLE(macos);
   
   /*
   在通过协议中应接受的最小TLS协议。  
   此属性根据此配置确定在会话中的任务的最小支持TLS协议版本。  
   默认值是SSL 3.0 (kSSLProtocol3)
   */
   @property SSLProtocol TLSMinimumSupportedProtocol;
   
   /*
   在这个会话中连接时，客户机应该请求的最大TLS协议版本。  
   此属性根据此配置确定在会话中的任务的最大支持TLS协议版本。
   默认值是系统支持的最新版本的TLS(当前TLS 1.2，或kTLSProtocol12)。
   */
   @property SSLProtocol TLSMaximumSupportedProtocol;
   
   // 它决定了会话是否应该使用HTTP管道,默认值是 NO
   @property BOOL HTTPShouldUsePipelining;
   
   /*
   用来确定请求是否应该包含来自cookie存储的cookie，默认值是 YES
   此属性控制基于此配置的会话中的任务是否应该在发出请求时自动从共享cookie存储中提供cookie。  
   如果您希望自己提供Cookie，请将该值设置为NO，并通过会话的HTTPAdditionalHeaders属性或使用自定义的NSURLRequest对象在每个请求级别提供一个Cookie头
   */
   @property BOOL HTTPShouldSetCookies;
   
   /*
   一个策略常量，它决定何时应该接受cookie
   默认值是NSHTTPCookieAcceptPolicyOnlyFromMainDocumentDomain
   如果您想要更直接地控制哪些cookie被接受，那么将这个值设置为NSHTTPCookieAcceptPolicyNever，然后使用allHeaderFields和cookiesWithResponseHeaderFields:forURL:从URL响应对象中提取cookie的方法
   */
   @property NSHTTPCookieAcceptPolicy HTTPCookieAcceptPolicy;
   
   //附加头的字典，以发送请求。 
   @property (nullable, copy) NSDictionary *HTTPAdditionalHeaders;
   
   /*
   对给定主机的同时连接的最大数量。  
   此属性决定了基于此配置的会话内的任务对每个主机的最大并发连接数。  
   这个限制是每个会话，另外，根据您与Internet的连接，会话可能会使用比您指定的更低的限制。 默认值在macOS中为6，在iOS中为4。 
   */
   @property NSInteger HTTPMaximumConnectionsPerHost;
   
   /*
   在这个会话中存储cookie的cookie存储。  
   此属性确定基于此配置的会话中所有任务所使用的cookie存储对象。
   */
   @property (nullable, retain) NSHTTPCookieStorage *HTTPCookieStorage;
   
   /*
   提供身份验证凭证的凭据库。  
   此属性确定基于此配置在会话中使用的凭据存储对象。  
   不要使用凭据存储，将此属性设置为nil。
   */
   @property (nullable, retain) NSURLCredentialStorage *URLCredentialStorage;
    
   /*
   在会话中为请求提供缓存响应的URL缓存。  
   此属性根据此配置确定会话中任务使用的URL缓存对象。  
   要禁用缓存，将此属性设置为nil。
   对于默认会话，默认值是共享的URL缓存对象。  
   对于后台会话，默认值为nil。  
   对于短暂会话，默认值是一个私有缓存对象，它只存储内存中的数据，并在您使会话无效时被销毁。
   */
   @property (nullable, retain) NSURLCache *URLCache;
   
   /*
   为所创建的任何tcp套接字启用扩展的后台空闲模式。  
   启用此模式要求系统在进程移动到后台时保持套接字的打开和延迟
   */
   @property BOOL shouldUseExtendedBackgroundIdleMode API_AVAILABLE(macos(10.11), ios(9.0), watchos(2.0), tvos(9.0));
   
   /*
   处理会话中请求的额外协议子类的数组。  
   该数组中的对象是与您定义的自定义NSURLProtocol子类对应的类对象。  
   默认情况下，URL会话对象支持许多通用的网络协议。  
   使用此数组将可用于会话的默认公共网络协议集扩展到您定义的一个或多个自定义协议。  
   在处理请求之前，NSURLSession对象首先搜索默认协议，然后检查自定义协议，直到找到能够处理指定请求的为止。  
   它使用了canInitWithRequest的协议:类方法返回YES，表示该类能够处理指定的请求。  
   Note：在后台会话中不能使用自定义的NSURLProtocol子类。默认值是一个空数组。
   */
   @property (nullable, copy) NSArray<Class> *protocolClasses;
   
   //用于连接的多路径服务类型。默认是NSURLSessionMultipathServiceTypeNone
   @property NSURLSessionMultipathServiceType multipathServiceType API_AVAILABLE(ios(11.0)) API_UNAVAILABLE(macos, watchos, tvos);
   
   //指定不应该使用多路径tcp。连接将使用单个流。
   //这是默认值。设置此值不需要权限。 
   NSURLSessionMultipathServiceTypeNone = 0,  
   
   //指定只使用辅助子流。
   //当主子流没有充分执行时。
   NSURLSessionMultipathServiceTypeHandover = 1,   
   
   //指定是否应该使用secodary子流。
   //主子流没有充分执行(包丢失、高往返时间、带宽问题)。
   //与nsurlsessionmultipathservicetype切换相比，第二子流将更具有侵略性。
   NSURLSessionMultipathServiceTypeInteractive = 2, 
   
   //指定跨多个接口的多个子流应该用于更好的带宽。
   //此模式仅可用于为开发使用配置的设备进行试验。
   //它可以在设置应用程序的Developer部分启用。
   NSURLSessionMultipathServiceTypeAggregate = 3
   ```

2. 基本使用示例：

   ```objective-c
   NSURLSessionConfiguration *config = [NSURLSessionConfiguration defaultSessionConfiguration];
   config.allowsCellularAccess = YES;
   config.networkServiceType = NSURLNetworkServiceTypeDefault;
   if (@available(iOS 11.0, *)) {
       config.waitsForConnectivity = YES;
   }
   config.TLSMinimumSupportedProtocol = kTLSProtocol12;
   config.HTTPCookieAcceptPolicy = NSHTTPCookieAcceptPolicyAlways;
   NSHTTPCookieStorage *storage = [NSHTTPCookieStorage sharedCookieStorageForGroupContainerIdentifier:@"xxxxxxxxxxx"];
   config.HTTPCookieStorage = storage;
   config.HTTPShouldSetCookies = YES;
   config.HTTPAdditionalHeaders = @{};
   
   _urlSession = [NSURLSession sessionWithConfiguration:config delegate:self delegateQueue:[NSOperationQueue mainQueue]];
   
   ```

## NSURLSessionTask

> NSURLSessionTask 是一个抽象类，如果要使用那么只能使用它的子类
> NSURLSessionTask 有两个子类
> - NSURLSessionDataTask,可以用来处理一般的网络请求，如 GET | POST 请求等
>   - NSURLSessionDataTask 有一个子类为 NSURLSessionUploadTask,用于处理上传请求的时候有优势
> - NSURLSessionDownloadTask,主要用于处理下载请求，有很大的优势

![](https://blogimage-1257063273.cos.ap-guangzhou.myqcloud.com/20190409125143.png)







## NSURLSession

> 1. NSURLSession 支持 http2.0 协议
> 2. 在处理下载任务的时候可以直接把数据下载到磁盘
> 3. 支持后台下载|上传
> 4. 同一个 session 发送多个请求，只需要建立一次连接（复用了TCP）
> 5. 提供了全局的 session 并且可以统一配置，使用更加方便
> 6. 下载的时候是多线程异步处理，效率更高

NSURLSession 对象在使用的时候，如果设置了代理，那么 session 会对代理对象保持一个强引用，在合适的时候应该主动进行释放
可以在控制器调用 viewDidDisappear 方法的时候来进行处理，可以通过调用 invalidateAndCancel 方法或者是 finishTasksAndInvalidate 方法来释放对代理对象的强引用

* invalidateAndCancel 方法直接取消请求然后释放代理对象

* finishTasksAndInvalidate 方法等请求完成之后释放代理对象。