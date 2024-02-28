# In App Purchase Work Flow

[toc]

## 什么是内购

苹果现有的支付系统：

1. Apple Pay：使用Apple Pay来销售诸如杂货，衣服和电器之类的实物商品；酒店预订和活动门票等服务，购买的东西是实体物商品非应用内消耗的商
2. In-App Purchase：直接在您的应用程序内通过应用程序内购买向客户提供额外的内容和功能，包括高级内容，数字商品和订阅，是一种在应用内消耗的商
   1. 使用条件：如果您想要在 app 内解锁特性或功能 (解锁方式有：订阅、游戏内货币、游戏关卡、优质内容的访问权限或解锁完整版等)，则必须使用 App 内购买项目；
   2. 订阅必须适用于可使用该 app 的所有用户设备；
   3. 多平台服务：跨平台运行的 app 可以允许用户访问他们在其他平台上的相应 app 中或您的网站上获取的内容、订阅或功能，包括多平台游戏中的消耗品，前提是这些项目也在 app 中以 App 内购买项目的形式提供。您不得直接或间接引导 iOS 用户使用非 App 内购买机制进行购；
   4. 在appStore付费下载(安装)不属于内购，内购一定是在App内部完成的

![内购官方图](https://blog-1257063273.cos.ap-chengdu.myqcloud.com/InAppPurchase.png)



## 内购流程大纲

![内购流程大纲](https://blog-1257063273.cos.ap-chengdu.myqcloud.com/InAppPurchaseFlow.png)

## 登记税务信息

1. 提供应用内购买之前，您需要签署付费应用协议并设置银行和税务信息。

   去 [appstoreconnect](https://appstoreconnect.apple.com/) 登记银行相关的税务信息，具体操作这里就赘述了，没什么技术性的，依葫芦画瓢就好了

2. 其中会需要银行国际代码，可以自己打电话给开户行问或者在下面网站查询：

   查询银行国际标识的网站，内购填银行协议的时候会用到：[CNAPS CODE 查询地址](https://e.czbank.com/CORPORBANK/query_unionBank_index.jsp)

3. 这部份信息一定要填完整及正确，如果出错或者不完整都会导致在下一步创建内购商品的时候，创建出的商品无法使用

   

## 创建内购商品

1. 登录[appstoreconnect](https://appstoreconnect.apple.com/)，切换到具体项目，进行创建，如下图；

2. 商品类型说明：
   1. 消耗型商品：只可使用一次的商品，使用之后即失效，必须再次购买；示例：钓鱼 App 中的鱼食；
   2. 非消耗型商品：只需购买一次，不会过期或随着使用而减少的商品。示例：游戏 App 的赛道。
   3. 自动续期订阅：允许用户在固定时间段内购买动态内容的商品。除非用户选择取消，否则此类订阅会自动续期；
      示例：每月订阅提供流媒体服务的 App，月、年会员等。
   4. 非续期订阅：允许用户购买有时限性服务的商品。此 App 内购买项目的内容可以是静态的。此类订阅不会自动续期；
      示例：为期一年的已归档文章目录订阅。
   
3. 商品描述填写：
   1. 创建时所录入的产品ID，命名要规范些，因为代码中要用到

   2. 至少要有一个本地化版本信息，跟据你APP出售的地区来添加对应的本地化；

   3. 基础栏的信息必须完整：如订阅时限、产品ID等要

   4. 审核信息：
      1. 审核备注项一定要填写，不能开发中访问商品会报无效：如测试用户帐户和密码等关于 App 内购买项目的额外信息，可能在 Apple 审核提交时有所帮助。审核备注不得超过 4,000 个字符；
      2. 截屏：此项可以先不填，但在提交审核前要一定要补填完整
         1. App 内购买项目的截屏，表示所售项目。例如，如果 App 内购买项目是一本图书，您可以提交图书图像的截屏。您也可以提交购买页的截屏。该截屏仅用于 Apple 审核，不会在 App Store 中显示。
         2. 截屏要求如下：
            - iOS 至少需要 640 x 920 像素
            - Apple tvOS 需要 1920 x 1080 像素
            - macOS 需要 1280 x 800 像素
         3. App 审核图像上传后，可以替换，但无法移除。当您的 App 内购买项目处于审核中时，您无法更新截屏。

   5. 保存，确保上面的信息都填写完整了，还是会报元数据缺失，没关系，那是因为还没提交对应有内购功能的App版本，不影响功能开发。

   6. App 内购买项目状态：

      > App 内购买项目产品具有一个状态指示器，显示其处于“准备提交”或“已批准”等状态。该状态直观地提示您是否需要关注您的产品。

      1. 红色状态指示器（![img](https://help.apple.com/app-store-connect/zh_CN.lproj/Art/status_red_icon.png)）表明在产品可用前，您需要先执行某些操作
      2. 黄色状态指示器（![img](https://help.apple.com/app-store-connect/zh_CN.lproj/Art/status_yellow_icon.png)）表示某些由您或 Apple 控制的流程正在进行中
      3. 绿色状态指示器（![img](https://help.apple.com/app-store-connect/zh_CN.lproj/Art/status_green_icon.png)）表明该 App 内购买项目可用
   4. 具体状态说明，在文章末尾有说明。
   

![创建内购商品](https://blog-1257063273.cos.ap-chengdu.myqcloud.com/createPurchase.png)

## 项目配置

1. http://developer.apple.com/](http://developer.apple.com/)创建App Bundle Identifier的时候，必须要勾选 `In-App Purchase`功能项，不过从2016年开始创建时默认就是勾选的(这个时间不太确定)

2. 使用Xcode为您的应用启用应用内购买服务：Xcode 中添加`In-App Purchase`功能项，这个需要自己手动添加

   ![image-20200629164651184](https://blog-1257063273.cos.ap-chengdu.myqcloud.com/createIAP.png)

3. 导入系统的`StoreKit.framework`库

4. 开始编写代码



## 编写内购代码

1. 在对应的文件中`import StoreKit`

2. 兼容性

      - 自动续订型订阅：iOS >= 4.2, macOS >= 10.9

3. 请求商品：通过刚创建商品时录入的ID进行请求商品，这步还是比较简单：

   1. 发起请求：`SKProductsRequest`,

      ```swift
      init(productIds: Set<String>, callback: @escaping InAppProductRequestCallback){
          self.callback = callback
          request = SKProductsRequest(productIdentifiers: productIds)
          super.init()
          request.delegate = self
      }
      func start() {
          request.start()
      }
      func cancel() {
          request.cancel()
      }
      ```

   2. 通过代理方法，处理请求回调：`SKProductsRequestDelegate`

      1. 两个比较重要的参数说明下：
         1. `invalidProductIdentifiers`：无效商品
         2. `retrievedProducts`：可售商品对象，这数组里面的商品才能进行出售，开发阶段也一样
         3. 可以将可售商品对象保存到起来，减少每次购买前都请求商品信息

      ```swift
      // MARK: SKProductsRequestDelegate
      func productsRequest(_ request: SKProductsRequest, didReceive response: SKProductsResponse) {
          let retrievedProducts = Set<SKProduct>(response.products)
          let invalidProductIDs = Set<String>(response.invalidProductIdentifiers)
          performCallback(RetrieveResults(retrievedProducts: retrievedProducts,
              invalidProductIDs: invalidProductIDs, error: nil))
      }
      // 请求完成，可能成功也可能是失败
      func requestDidFinish(_ request: SKRequest) {
      }
      // 请求发生错误回调
      func request(_ request: SKRequest, didFailWithError error: Error) {
          performCallback(RetrieveResults(retrievedProducts: Set<SKProduct>(), invalidProductIDs: Set<String>(), error: error))
      }
      ```

4. 展示商品：展示给用户，用户进行购买

5. 发起购买，通过刚保存`retrievedProducts`商品对象发起购买：

   1. 关键代码部份：`SKPaymentQueue`,`SKMutablePayment`

      ```swift
      let skPayment = SKMutablePayment(product: payment.product)
      // 用户标识，为了后续好查找确认对应的订单
      skPayment.applicationUsername = payment.applicationUsername
      // 购买数量
      skPayment.quantity = payment.quantity
      // 监听交易事务的回调 SKPaymentTransactionObserver
      SKPaymentQueue.default().add(self)
      // 添加到交易队列
      SKPaymentQueue.default().add(skPayment)
      ```

   2. 交易事务的回调方法：`SKPaymentTransactionObserver`

      ```swift
      // 更新交易的时候：具体查看`SKPaymentTransactionState`
      func paymentQueue(_ queue: SKPaymentQueue, updatedTransactions transactions: [SKPaymentTransaction]) {
        // transaction.transactionState
        // do something
        // 如果是 .restored .purchased 则可以进行下一步的验证购买操作，后面会讲到验证的操作
        // 具体还得根据对应需求和商品类型本身
      }
      
      // 从交易队列中移除的交易事务
      func paymentQueue(_ queue: SKPaymentQueue, removedTransactions transactions: [SKPaymentTransaction]) { }
      
      // 恢复购买失败
      func paymentQueue(_ queue: SKPaymentQueue, restoreCompletedTransactionsFailedWithError error: Error) { }
      
      // 恢复购买完成
      func paymentQueueRestoreCompletedTransactionsFinished(_ queue: SKPaymentQueue){ }
      
      // 如果使用的 Apple 内购托管功能会涉这个方法，即从Apple下载你托管的商品
      func paymentQueue(_ queue: SKPaymentQueue, updatedDownloads downloads: [SKDownload]) { }
      
      /**
      将在开始购买的时候，这里如果返回false则不会立即让用户购买，这里如果你不是真的要取消这笔单，可以先将`SKPayment`进行保存，等完成你的任务再进入购买交易
      Continuing a previously deferred payment
      SKPaymentQueue.default().add(savedPayment)
      */
      #if os(iOS) && !targetEnvironment(macCatalyst)
      func paymentQueue(_ queue: SKPaymentQueue, shouldAddStorePayment payment: SKPayment, for product: SKProduct) -> Bool {
       return true
      }
      #endif
      ```
      
      ```swift
      @available(OSX 10.7, *)
      public enum SKPaymentTransactionState : Int {
      	case purchasing = 0 //购买中
      	case purchased = 1 // 已经购买的
      	case failed = 2 // 失败
      	case restored = 3 // 购买过，恢复的
        @available(OSX 10.10, *)
        case deferred = 4 // 事务在队列中，但其最终状态正在等待外部操作 
        // deferred：官方说明：The transaction is in the queue, but its final status is pending external action.
      }
      ```

6. 验证购买：关键代码：这里会涉及到一个***共享密钥***，在创建内购商品的那个界面进行设置

   1. 获取本地收据数据（加密的）

      ```swift
      // Get the receipt if it's available
      if let appStoreReceiptURL = Bundle.main.appStoreReceiptURL,
          FileManager.default.fileExists(atPath: appStoreReceiptURL.path) {
          do {
              let receiptData = try Data(contentsOf: appStoreReceiptURL, options: .alwaysMapped)
              print(receiptData)
              let receiptString = receiptData.base64EncodedString(options: [])
              // Read receiptData
          } catch { 
            print("Couldn't read receipt data with error: " + error.localizedDescription) }
      }
      ```

   2. 发送本地收据数据到AppStore

      1. Test environment URL `https://sandbox.itunes.apple.com/verifyReceipt` ，when testing your app in the sandbox and while your application is in review. 

      2. Production URL `https://buy.itunes.apple.com/verifyReceipt`，when your app is live in the App Store

      3. 这个验证涉及到两个方式：

         1. 单机模式：客户端自己去苹果验证，然后客户端自己下发商品（解锁）操作

            1. app从app store 获取产品信息
            2. 用户选择需要购买的产品
            3. app发送支付请求到app store
            4. app发送支付请求到app store
            5. app store 处理支付请求，并返回transaction信息
            6. app将购买的内容展示给用户

         2. 服务器模式：客户端将数据发送给自己的服务器，让服务器去处理验证及下发（解锁）对应商品

            1. app从服务器获取产品标识列表

            2. app从app store 获取产品信息

            3. 用户选择需要购买的产品

            4. app 发送 支付请求到app store

            5. app store 处理支付请求，返回transaction信息

            6. app 将transaction receipt 发送到服务器

            7. 服务器收到收据后发送到app stroe验证收据的有效性

            8. app store 返回收据的验证结果

            9. 根据app store 返回的结果决定用户是否购买成功

      4. 两者之间的区别：

         1. 上述两种模式的不同之处主要在于：交易的收据验证，内建模式没有专门去验证交易收据，而服务器模式会使用独立的服务器去验证交易收据。内建模式简单快捷，但容易被破解。服务器模式流程相对复杂，但相对安全.
         2. 针对自动续订的设备上验证与服务器端验证
         
         | 操作（英文为官方原文）                                       | 设备上验证 | 服务器端验证 |
         | :----------------------------------------------------------- | :--------: | :----------: |
         | 验证收据的真实性                                             |     是     |      是      |
         | 续订交易(Includes renewal transactions)                      |     是     |      是      |
         | 用户订阅信息(Includes additional user subscription information) |    没有    |      是      |
         | 处理续签而无需客户依赖性(Handles renewals without client dependency) |    没有    |      是      |
         | 对防设备时钟变化(Resistant to device clock change)           |    没有    |      是      |

      ```swift
      public func validate(receiptData: Data, completion: @escaping (VerifyReceiptResult) -> Void) {
      	let storeURL = URL(string: service.rawValue)! // safe (until no more)
      	let storeRequest = NSMutableURLRequest(url: storeURL)
      	storeRequest.httpMethod = "POST"
          let receiptString = receiptData.base64EncodedString(options: [])
      	let requestContents: NSMutableDictionary = [ "receipt-data": receiptString ]
      	// password if defined （共享密钥）
      	if let password = sharedSecret {
      		requestContents.setValue(password, forKey: "password")
      	}
      	// Encore request body
      	do {
      		storeRequest.httpBody = try JSONSerialization.data(withJSONObject: requestContents, options: [])
      	} catch let e {
      		completion(.error(error: .requestBodyEncodeError(error: e)))
      		return
      	}
      	// Remote task
      	let task = URLSession.shared.dataTask(with: storeRequest as URLRequest) { data, _, error -> Void in
      		// there is an error
      		if let networkError = error {
      			completion(.error(error: .networkError(error: networkError)))
      			return
      		}
      		// there is no data
      		guard let safeData = data else {
      			completion(.error(error: .noRemoteData))
      			return
      		}
      		// cannot decode data
      		guard let receiptInfo = try? JSONSerialization.jsonObject(with: safeData, options: .mutableLeaves) as? ReceiptInfo ?? [:] else {
      			let jsonStr = String(data: safeData, encoding: String.Encoding.utf8)
      			completion(.error(error: .jsonDecodeError(string: jsonStr)))
      			return
      		}
      		// get status from info
      		if let status = receiptInfo["status"] as? Int {
      			let receiptStatus = ReceiptStatus(rawValue: status) ?? ReceiptStatus.unknown
      			if case .testReceipt = receiptStatus {
                      self.service = .sandbox
                      self.validate(receiptData: receiptData, completion: completion)
      			} else {
      				if receiptStatus.isValid {
      					completion(.success(receipt: receiptInfo))
      				} else {
      					completion(.error(error: .receiptInvalid(receipt: receiptInfo, status: receiptStatus)))
      				}
      			}
      		} else {
      			completion(.error(error: .receiptInvalid(receipt: receiptInfo, status: ReceiptStatus.none)))
      		}
      	}
      	task.resume()
      }
      ```

   3. 刷新收据：`SKReceiptRefreshRequest`

      > 如果收据无效或丢失，请使用此API请求新的收据。在沙盒环境中，您可以请求具有任意属性组合的收据，以测试与“批量购买计划”收据相关的状态转换

   4. 解析苹果返回的数据：

      1. json的结构大纲：

         ```json
         {
             "environment":"Sandbox",
             "status":0,
             "receipt":{
                 "in_app":[{存放所有应用内购买交易的应用内购买收据字段的数组}]
             },
             "latest_receipt":"这里存放的是最新的Base64编码的应用程序收据",
             "latest_receipt_info":[{ 所有应用内购买交易的数组 }],
             "pending_renewal_info":[{ 存放的是尝试自动续订的结果信息 }],
             "is-retryable":""  // 只有状态码为21000-21199时会出现
         }
         ```

         | 字段                        | 类型                                    | 备注                                                         |
         | --------------------------- | --------------------------------------- | ------------------------------------------------------------ |
         | environment                 | string                                  | Sandbox-沙盒环境 PROD-正式环境                               |
         | notification_type           | string                                  | INITIAL_BUY-订阅初次购买 CANCEL-取消交易 RENEWAL-已过期的订阅续订成功 INTERACTIVE_RENEWAL-续订被延迟的订阅 DID_CHANGE_RENEWAL_PREF-用户修改了订阅计划，会影响下次的订阅 |
         | password                    | string                                  | 内购产品密钥                                                 |
         | original_transaction_id     | string                                  | 初次交易的id                                                 |
         | cancellation_date           | string, interpreted as an RFC 3339 date | 取消交易的时间，仅当通知类型为CANCEL时返回                   |
         | web_order_line_item_id      | string                                  | 用于区分订阅内购，仅当通知类型为CANCEL时返回                 |
         | latest_receipt              | string                                  | 最新的交易凭据，仅当通知类型为RENEWAL或INTERACTIVE_RENEWAL时返回 |
         | latest_receipt_info         | []                                      | 最新的交易凭据中的信息，当通知类型为CANCEL时不返回，其他情况会返回 |
         | latest_expired_receipt      | string                                  | 最新的过交易凭据，仅当有过期交易凭据时返回                   |
         | latest_expired_receipt_info | string                                  | 最新的交易凭据中的信息，仅当通知类型为RENEWAL或CANCEL或订阅过期且续订失败时返回 |
         | auto_renew_status           | string                                  | 是否自动续订 true-是 false-否                                |
         | auto_renew_adam_id          | string                                  | 内购产品的数字序号                                           |
         | auto_renew_product_id       | string                                  | 内购产品的id                                                 |
         | expiration_intent           | string                                  | 过期原因，仅当通知类型为RENEWAL或INTERACTIVE_RENEWAL时返回   |

      2. `status`：说明

         | 状态码 |    设备上验证  |
         | :---------------- | :----------------------------------------------------------: |
         | 21000  | The request to the App Store was not made using the HTTP POST request method.未使用HTTP POST请求方法向App Store发送请求。 |
         | 21001             |     This status code is no longer sent by the App Store.`receipt-data`此状态代码不再由App Store发送。     |
         | 21002             | The data in the receipt-data property was malformed or the service experienced a temporary issue. Try again.属性中的数据格式不正确，或者服务遇到了临时问题。再试一次。 |
         | 21003             |           The receipt could not be authenticated.收据无法认证。     |
         | 21004             | The shared secret you provided does not match the shared secret on file for your account.您提供的共享密钥与您帐户的文件共享密钥不匹配。 |
         | 21005        | The receipt server was temporarily unable to provide the receipt. Try again.收据服务器暂时无法提供收据。再试一次。 |
         | 21006        | This receipt is valid but the subscription has expired. When this status code is returned to your server, the receipt data is also decoded and returned as part of the response. Only returned for iOS 6-style transaction receipts for auto-renewable subscriptions.该收据有效，但订阅已过期。当此状态代码返回到您的服务器时，收据数据也会被解码并作为响应的一部分返回。仅针对自动续订的iOS 6样式的交易收据返回。 |
         | 21007        | This receipt is from the test environment, but it was sent to the production environment for verification.该收据来自测试环境，但已发送到生产环境以进行验证。 |
         | 21008        | This receipt is from the production environment, but it was sent to the test environment for verification.该收据来自生产环境，但是已发送到测试环境以进行验证。 |
         | 21009        | Internal data access error. Try again later.内部数据访问错误。稍后再试。 |
         | 21010        | The user account cannot be found or has been deleted.找不到或删除用户帐户。 |
         |21100	| 21100-21199 其他的数据错误 |

      3. 个别特殊字段说明：` in_app`与`latest_receipt_info`

         > 测试时发现，这两个字段的数值几乎相同，<u>**有几点需要注意**</u>：

         1. `latest_receipt_info`：所有应用内购买交易的数组。这不包括已被您的应用标记为完成的消耗品交易。仅针对包含自动续订的收据返回
         2. `receipt.In_app`：包含所有应用内购买交易的应用内购买收据字段的数组
            1. 自动续订订阅类型，在到期后会再生成一条购买记录，这条记录会出现在`last_receipt_info`里，但不会出现在`in_app`里
            2. 自动续订订阅类型可以配置试用，试用记录只有在`latest_receipt_info`里，`is_trial_period`字段才是`true`
         3. 消耗型购买记录不会出现在`latest_receipt_info`，因此需要检查in_app来确保校验正确，标记为完成的消耗品交易，也不会保存
         4. 首次购买` is_trial_period = true`， `is_in_intro_offer_period` 是否是在试用期 ； `expires_date-purchase_date` 就是免费的周期。

   5. 验证结果：

      1. 成功：解锁对应功能或道具
         
         1. 如果有服务器：服务器保存单据，可以进行用户对帐或者数据分析等；
         
      2. 没有服务器的，APP不保存，保存本地也没什么意义;
         
         3. 完成交易(购买)：在确认完成后一定要调用：`finishTransaction:`方法，目的：
         
            1. 是告诉appstore，这笔交易完成了，下次的时候这笔订单就不会再主动推给APP：
            2. 每个商品同时只能存在一个交易，无法进行下一次购买；如果完成购买后没有调用此方法，用户再点击购买此商品时，将提示“你已购买此商品，将免费恢复”；
            3. 如果是消耗器，调用此API后，相关收据将会从`receipt`中消失；
            4. 所以调用此方法一定是在已经处理好解锁高功能或者已下发商品给用户后。
         
            ```swift
            SKPaymentQueue.default() 
            func finishTransaction(_ transaction: SKPaymentTransaction)
            ```

7. 失败`SKError`：根据失败原因进行处理，提示等...，下面是常见失败代码：

      ```swift
      public func getSKErrorDetail(_ err: SKError) -> String {
            switch err.code {
            case .unknown:
                return "未知的错误"
            case .clientInvalid:
                return "不允许客户端执行的操作"
            case .paymentCancelled:
                return "用户取消了付款请求"
            case .paymentInvalid:
                return "App Store无法识别付款参数"
            case .paymentNotAllowed:
                return "不允许用户授权付款"
            case .storeProductNotAvailable:
                return "所请求的产品在商店中不可用"
            case .privacyAcknowledgementRequired:
                return "用户尚未确认Apple的隐私政策"
            case .unauthorizedRequestData:
                return "该应用程序正在尝试使用其不具备必需权利的属性"
            case .invalidOfferIdentifier:
                return "商品标识符无效"
            case .invalidOfferPrice:
                return "在App Store Connect中指定的价格不再有效"
            case .invalidSignature:
                return "签名无效"
            case .missingOfferParams:
                return "缺少折扣中参数"
            default:
                break
            }
            return ""
        }
      ```
## Testing In-App Purchases - 测试应用内购买

1. 创建沙盒测试账号：登录 [appstoreconnect](https://appstoreconnect.apple.com/) 选择用户与职能，找到管理按钮，然后进行创建，创建时所用的邮箱可以随便写，只要格式符合邮箱格式就可以；

2. 退出Mac AppStore 已经登录的AppleID；

3. 运行你自己的内购APP，进行内购测试；

4. 购买时会让你先登录到AppStore，这时输入刚创建的沙盒测试账号；

5. 注意项：

   1. 不能使用正常的AppleID进行内购测试；

   2. 沙盒测试账号无法通过appStore进行查看具体订阅情况；

   3. 沙盒测试账号无法主动退订订阅；

   4. 沙盒账号测试时，针对订阅型商品：会在你主动订阅后自动续订6期，6次后自动退订，然后又需要手动订阅，依此方式循环下去；

   5. macOS下无法使用`testflight`进行测试，`testflight`只适用于：iPhone，iPad， iPod

   6. 沙盒测试环境下订阅时效问题：

      在我们测试自动续期订阅时，时限会缩短。此外，测试订阅最多仅能自动续期 **6** 次。

      | 实际时限 | 测试时限 |
      | :------: | :------: |
      |   1 周   |  3 分钟  |
      |  1个月   |  5 分钟  |
      |  2 个月  | 10 分钟  |
      |  3 个月  | 15 分钟  |
      |  6 个月  | 30 分钟  |
      |   1 年   |  1 小时  |

   7. 所以测试此功能可能相对麻烦些，测试中可以记下订阅/购买时间，需要精确到分钟，然后对比下内购Demo展示的结果



## 其它综合性问题

1. 签署《付费应用程序协议》：若要提供 App 内购买项目，您必须在 App Store Connect 中签署《付费应用程序协议》。有关如何操作的详细信息，请参阅[协议、税务和银行业务概述](https://help.apple.com/itunes-connect/developer/#/devb6df5ee51)。

2. 程序内付费协议(针对用户)：需要有自己的付费协议，并符合当地的法律，以以往的经历，苹果审核的时候一般会看；

4. 用户通过苹果支持进行退订：

   要检查`Apple Customer Support`是否已取消购买，请在收据中查找`Cancellation Date`字段。如果该字段包含日期，则无论订阅的到期日期如何，购买都会被取消。关于提供内容或服务，将取消的交易视为从未进行过购买。

5. 覆盖可见性设置(Overriding Visibility Settings)

   您可以决定是否按设备进行可见或隐藏的应用内购买。 例如，您可能希望隐藏客户已经进行的应用内购买，并仅显示他们仍可以购买的产品。

   例如，要在用户购买产品后隐藏产品`Pro Subscription`，请获取产品信息并使用`.hide`设置更新默认商店促销控制器，如下所示。 `Pro Subscription`应用内购买将不再出现在给定设备上。

   ```swift
   // Updating Visibility Override of a Promoted In-App Purchase
   // Fetch Product Info for "Pro Subscription"
   // 在此示例中，可见性设置为`.hide`，根据您在`App Store Connect`中设置的默认值隐藏或显示产品。
   
   let storePromotionController = SKProductStorePromotionController.default()
   storePromotionController.update(storePromotionVisibility: .hide, forProduct: proSubscription, completionHandler: { (error: Error?) in
      // Complete
   })
   ```

6. 在App Store运营产品的尝试续订订阅问题：

   1. App Store可能会尝试续订最多60天的订阅。 您可以在收据中检查订阅重试标记`Subscription Retry Flag`，以确定App Store是否仍在尝试续订订阅。
   2. 升级和计划变更可以查看收据的`Subscription Auto Renew Preference`字段，以了解用户选择的任何计划更改，这些更改将在下一个续订日期生效
   3. 若用户取消订阅，会走[Server-to-Server Notifications](https://developer.apple.com/documentation/storekit/in-app_purchase/subscriptions_and_offers/enabling_server-to-server_notifications)，但需要在App Store Connect中为应用程序配置订阅状态URL

7. 定制内购商店相关说明，主要跟产品和设计有关，请参阅：[promoting-in-app-purchases](https://developer.apple.com/app-store/promoting-in-app-purchases/)

![Server-to-Server Notifications](https://blog-1257063273.cos.ap-chengdu.myqcloud.com/stos.png)



## 内购项目状态详细说明

| 状态                                                         | 描述                                                         |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| ![img](https://help.apple.com/app-store-connect/zh_CN.lproj/Art/status_yellow_icon.png) 元数据丢失 | 您的 App 内购买项目已创建，但您尚未上传截屏或完成元数据      |
| ![img](https://help.apple.com/app-store-connect/zh_CN.lproj/Art/status_yellow_icon.png) 准备提交 | 您已上传了所有所需的元数据，但您尚未将其发送给 Apple 进行审核。 |
| ![img](https://help.apple.com/app-store-connect/zh_CN.lproj/Art/status_yellow_icon.png) 正在等待上传 | 您的 App 内购买项目内容尚未上传。<br/>此状态仅适用于设置为由 Apple 托管的非消耗型产品。 |
| ![img](https://help.apple.com/app-store-connect/zh_CN.lproj/Art/status_yellow_icon.png) 正在处理内容 | 您的 App 内购买项目产品内容交付正在处理。                    |
| ![img](https://help.apple.com/app-store-connect/zh_CN.lproj/Art/status_yellow_icon.png) 二进制文件待批准 | 与此 App 内购买项目相关联的 App 当前正在审核中。             |
| ![img](https://help.apple.com/app-store-connect/zh_CN.lproj/Art/status_yellow_icon.png) 正在等待审核 | 您已经提交了 App 内购买项目，正在等待 Apple 审核。<br/>您可以在产品处于此状态时对其进行编辑。 |
| ![img](https://help.apple.com/app-store-connect/zh_CN.lproj/Art/status_yellow_icon.png) 正在审核 | 您的 App 内购买项目产品当前正在由 Apple 进行审核。<br/>仅当产品为此状态时，才可对其参考名称、定价和销售范围进行编辑。 |
| ![img](https://help.apple.com/app-store-connect/zh_CN.lproj/Art/status_green_icon.png) 已批准 | Apple 已批准您的 App 内购买项目与其相关联的 App 在 App Store 上架。<br/>如果该产品与某个 App 版本一起发布，<br/>那么该产品在 App 被批准之前将不会具有“已批准”状态。 |
| ![img](https://help.apple.com/app-store-connect/zh_CN.lproj/Art/status_red_icon.png) 被拒绝 | Apple 在审核过程中拒绝了您的 App 内购买项目产品。<br/><br/>您不能重新提交一个被拒绝的 App 内购买项目，而是需要提交一个新的。<br/><br/>如果更改 App 内购买项目的某一详细信息，<br/>则您的 App 内购买项目产品状态将更改为“需要开发者操作”。 |
| ![img](https://help.apple.com/app-store-connect/zh_CN.lproj/Art/status_yellow_icon.png) 需要开发者操作 | 您提交的 App 内购买项目产品更改已被拒绝。<br/>您需要编辑详细信息或取消更改详细信息的请求，此 App 内购买项目才能再次审核。 |
| ![img](https://help.apple.com/app-store-connect/zh_CN.lproj/Art/status_red_icon.png) 被开发者下架 | 您已将该 App 内购买项目下架。<br/>您将一个 App 内购买项目下架后，已购买该产品的顾客无法将其恢复至设备；<br/>如果是自动续期订阅，则顾客无法续期该 App 内购买项目。<br/><br/>如果该产品准许销售，则状态会更改为“已批准”。 |
| ![img](https://help.apple.com/app-store-connect/zh_CN.lproj/Art/status_red_icon.png) 被下架 | 当 Apple 将一个 App 内购买项目产品下架时显示。               |



## 安全性补充

1. SKPayment类的applicationUsername属性用哈希后的字符串来填充

   ```objective-c
   #import <CommonCrypto/CommonCrypto.h>
    
   // Custom method to calculate the SHA-256 hash using Common Crypto
   - (NSString *)hashedValueForAccountName:(NSString*)userAccountName
   {
       const int HASH_SIZE = 32;
       unsigned char hashedChars[HASH_SIZE];
       const char *accountName = [userAccountName UTF8String];
       size_t accountNameLen = strlen(accountName);
    
       // Confirm that the length of the user name is small enough
       // to be recast when calling the hash function.
       if (accountNameLen > UINT32_MAX) {
           NSLog(@"Account name too long to hash: %@", userAccountName);
           return nil;
       }
       CC_SHA256(accountName, (CC_LONG)accountNameLen, hashedChars);
    
       // Convert the array of bytes into a string showing its hex representation.
       NSMutableString *userAccountHash = [[NSMutableString alloc] init];
       for (int i = 0; i < HASH_SIZE; i++) {
           // Add a dash every four bytes, for readability.
           if (i != 0 && i%4 == 0) {
               [userAccountHash appendString:@"-"];
           }
           [userAccountHash appendFormat:@"%02x", hashedChars[i]];
       }
    
       return userAccountHash;
   }
   ```

   


## 相关官方链接

1. [Create a sandbox tester account](https://help.apple.com/app-store-connect/#/dev8b997bee1)
2. [in-app-purchase 综合文档首页](https://developer.apple.com/in-app-purchase/)
3. [in-app_purchase开发文档](https://developer.apple.com/documentation/storekit/in-app_purchase)
4. [App Store Connect 帮助：协议，税收和银行业务概述](https://help.apple.com/app-store-connect/#/devb6df5ee51)
5. [Xcode帮助：向目标添加功能](https://help.apple.com/xcode/mac/current/#/dev88ff319e7)
6. [App Store连接 帮助：创建应用内购买](https://help.apple.com/app-store-connect/#/devae49fb316)
7. [App Store Connect 帮助文档(开发、产品、设计、测试)](https://help.apple.com/app-store-connect/#/dev0067a330b)
8. [自动续期订阅管理](https://developer.apple.com/cn/app-store/subscriptions/#subscription-management-to-aid-retention)

