{
​    environment = Sandbox;
​    receipt =     {
​        "adam_id" = 0;

        //App Store用于唯一标识创建事务的应用程序的字符串
        //如果您的服务器支持多个应用程序，则可以使用此值来区分它们。
        //仅在生产环境中为应用程序分配标识符，因此对于在测试环境中创建的收据，此键不存在。
        //Mac应用程序不提供此字段。
        "app_item_id" = 0;
        "application_version" = "0.0.14";//应用版本:这对应于CFBundleVersion（在iOS中）或CFBundleShortVersionString（在macOS中）的值Info.plist。


        "bundle_id" = "com.facebac.MontnetsLiveCloud";
        "download_id" = 0;
    
        //应用程序内购买收据
        "in_app" =         (
                        {
                //订阅到期日
                "expires_date" = "";
    
                //订阅自动续订状态
                //“1” - 订阅将在当前订阅期结束时续订。
                //“0” - 客户已关闭其订阅的自动续订。
                //此密钥仅适用于自动续订订阅收据，用于活动订阅或过期订阅。此密钥的值不应被解释为客户的订阅状态。您可以使用此值在应用程序中显示备用订阅产品，例如，客户可以从其当前计划降级到的较低级别订阅计划。
                "auto_renew_status" = "";
    
                //订阅自动续订首选项
                //此密钥仅适用于自动续订订阅收据。此密钥的值对应于productIdentifier客户订阅续订的产品的属性。您可以使用此值在当前订阅期结束之前向客户提供备用服务级别。
                "auto_renew_product_id" = "";
    
                //认购价格(提价)同意状态
                //“1” - 客户已同意提价。订阅将以更高的价格续订。
                //“0” - 客户未就增加的价格采取行动。如果客户在续订日期之前未采取任何措施，则订阅将到期。
                "price_consent_status" = "";
    
                //订阅到期
                //“1” - 客户取消了他们的订阅。 “2” - 结算错误; 例如，客户的付款信息不再有效。“3” - 客户不同意最近的价格上涨。“4” - 续订时无法购买产品。“5” - 未知错误
                //此密钥仅适用于包含已过期的自动续订订阅的收据。您可以使用此值来决定是否在应用中显示适当的消息，以便客户重新订阅
                "expiration_intent" = "";
    
                //订阅重试标志
                //“1” - App Store仍在尝试续订订阅。
                //“0” - App Store已停止尝试续订订阅。
                "is_in_billing_retry_period" = "";
    
                //取消日期:对于Apple客户支持取消的交易，取消的时间和日期。对于已升级的自动更新订阅计划，升级事务的时间和日期。
                //已取消的应用内购买仍会无限期地保留在收据中。仅适用于非消费品，自动续订，非续订或免费订阅的退款。
                "cancellation_date" = "";
    
                //取消原因
                //“1” - 由于您的应用中存在实际或感知问题，客户已取消其交易。
                //“0” - 交易因其他原因被取消，例如，如果客户意外地进行了购买。
                "cancellation_reason" = "";
    
                //订阅给定价格期间：此密钥仅适用于自动续订订阅收据。此密钥的值是，"true"如果客户的订阅当前处于介绍性价格期间，或者"false"如果不是。
                //注意：如果收据中的先前订阅期对于密钥is_trial_period或is_in_intro_offer_period密钥的值为“true” ，则该用户不符合该订阅组中的免费试用或介绍价格。
                "_in_intro_offer_period" = ""
    
                //订阅试用期:是否处于免费试用期,此密钥的值是"true"客户的订阅当前是否处于免费试用期，或者"false"如果不是
                //注意：如果收据中的先前订阅期对于密钥is_trial_period或is_in_intro_offer_period密钥的值为“true” ，则该用户不符合该订阅组中的免费试用或介绍价格
                "is_trial_period" = false;
    
                //原始购买日期,This value corresponds to the original transaction’s transactionDate property.
                //在自动续订的订阅收据中，这表示订阅期的开始，即使订阅已续订。
                "original_purchase_date" = "2018-07-10 09:18:12 Etc/GMT";
    
                "original_purchase_date_ms" = 1531214292000;
                "original_purchase_date_pst" = "2018-07-10 02:18:12 America/Los_Angeles";
    
                //对于恢复先前事务的事务，原始事务的事务标识符。否则，与事务标识符相同。
                //此值对应于原始事务的transactionIdentifier属性。对于为特定订阅生成的所有收据，此值都相同
                "original_transaction_id" = 1000000415925712;
    
                //此值对应于存储在事务productIdentifier属性中的SKPayment对象的payment属性。
                "product_id" = "com.facebac.MontnetsLiveCloud.number01_07";
    
                //购买商品的日期和时间,This value corresponds to the transaction’s transactionDate property.
                //对于恢复先前交易的交易，购买日期与原始购买日期相同。使用原始购买日期来获取原始交易的日期。在自动续订的订购收据中，购买日期是购买或续订订购的日期（有或没有失效）。对于在当前期间到期日发生的自动续订，购买日期是下一期间的开始日期，与当前期间的结束日期相同。
                "purchase_date" = "2018-07-10 09:18:12 Etc/GMT";
    
                "purchase_date_ms" = 1531214292000;
                "purchase_date_pst" = "2018-07-10 02:18:12 America/Los_Angeles";
    
                //此值对应于存储在事务quantity属性中的SKPayment对象的payment属性。
                quantity = 1;
    
                //已购买商品的交易标识符,This value corresponds to the transaction’s transactionIdentifier property.
                //对于恢复先前交易的交易，该值与原始购买交易的交易标识符不同。在自动更新的订阅收据中，每次订阅在新设备上自动续订或恢复时，都会生成事务标识符的新值。
                "transaction_id" = 1000000415925712;
            }
        );
        "original_application_version" = "1.0";
        "original_purchase_date" = "2013-08-01 07:00:00 Etc/GMT";
        "original_purchase_date_ms" = 1375340400000;
        "original_purchase_date_pst" = "2013-08-01 00:00:00 America/Los_Angeles";
    
        //创建应用收据的日期,验证收据时，使用此日期验证收据的签名
        //expiration_date:应用收据到期的日期,此密钥仅适用于通过批量购买计划购买的应用程序。如果此密钥不存在，则收据不会过期。
验证收据时，将此日期与当前日期进行比较，以确定收据是否已过期。请勿尝试使用此日期来计算任何其他信息，例如到期前的剩余时间
​        "receipt_creation_date" = "2018-07-10 09:18:13 Etc/GMT";
​        "receipt_creation_date_ms" = 1531214293000;
​        "receipt_creation_date_pst" = "2018-07-10 02:18:13 America/Los_Angeles";
​        "receipt_type" = ProductionSandbox;
​        "request_date" = "2018-07-10 09:18:16 Etc/GMT";
​        "request_date_ms" = 1531214296077;
​        "request_date_pst" = "2018-07-10 02:18:16 America/Los_Angeles";

        //外部版本标识符:对于在测试环境中创建的收据，此密钥不存在。使用此值可标识客户购买的应用程序版本。
        "version_external_identifier" = 0;
    };
    status = 0;//如果接收是0有效的，或者在列出的错误代码中的一个
}

1000000415925712












21000

App Store无法读取您提供的JSON对象。

21002

该receipt-data物业的数据格式错误或遗失。

21003

收据无法通过身份验证。

21004

您提供的共享密码与您帐户的共享密钥不符。

21005

收据服务器当前不可用。

21006

此收据有效但订阅已过期。当此状态代码返回到您的服务器时，收据数据也会被解码并作为响应的一部分返回。

仅针对自动续订订阅的iOS 6样式交易收据返回。

21007

此收据来自测试环境，但已发送到生产环境进行验证。而是将其发送到测试环境。

21008

此收据来自生产环境，但已发送到测试环境进行验证。而是将其发送到生产环境。

21010

此收据无法获得授权。对待这个就像从未进行购买一样。

21100-21199

内部数据访问错误。





### 返回示例：

```
{
    environment = Sandbox;
    receipt =     {
        "adam_id" = 0;
        "app_item_id" = 0;
        "application_version" = "0.0.14";
        "bundle_id" = "com.facebac.MontnetsLiveCloud";
        "download_id" = 0;
        "in_app" =         (
                        {
                "is_trial_period" = false;
                "original_purchase_date" = "2018-07-10 09:18:12 Etc/GMT";
                "original_purchase_date_ms" = 1531214292000;
                "original_purchase_date_pst" = "2018-07-10 02:18:12 America/Los_Angeles";
                "original_transaction_id" = 1000000415925712;
                "product_id" = "com.facebac.MontnetsLiveCloud.number01_07";
                "purchase_date" = "2018-07-10 09:18:12 Etc/GMT";
                "purchase_date_ms" = 1531214292000;
                "purchase_date_pst" = "2018-07-10 02:18:12 America/Los_Angeles";

                quantity = 1;
                "transaction_id" = 1000000415925712;
            },
                        {
                "is_trial_period" = false;
                "original_purchase_date" = "2018-07-10 09:30:32 Etc/GMT";
                "original_purchase_date_ms" = 1531215032000;
                "original_purchase_date_pst" = "2018-07-10 02:30:32 America/Los_Angeles";
                "original_transaction_id" = 1000000415937014;
                "product_id" = "com.facebac.MontnetsLiveCloud.number02_07";
                "purchase_date" = "2018-07-10 09:30:32 Etc/GMT";
                "purchase_date_ms" = 1531215032000;
                "purchase_date_pst" = "2018-07-10 02:30:32 America/Los_Angeles";
                quantity = 1;
                "transaction_id" = 1000000415937014;
            }
        );
        "original_application_version" = "1.0";
        "original_purchase_date" = "2013-08-01 07:00:00 Etc/GMT";
        "original_purchase_date_ms" = 1375340400000;
        "original_purchase_date_pst" = "2013-08-01 00:00:00 America/Los_Angeles";
        "receipt_creation_date" = "2018-07-10 09:30:33 Etc/GMT";
        "receipt_creation_date_ms" = 1531215033000;
        "receipt_creation_date_pst" = "2018-07-10 02:30:33 America/Los_Angeles";
        "receipt_type" = ProductionSandbox;
        "request_date" = "2018-07-10 09:30:36 Etc/GMT";
        "request_date_ms" = 1531215036424;
        "request_date_pst" = "2018-07-10 02:30:36 America/Los_Angeles";
        "version_external_identifier" = 0;
    };
    status = 0;
}
```

