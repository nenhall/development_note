## iOS开源项目之日志框架CocoaLumberjack

https://github.com/formShare/CocoaLumberjack
CocoaLumberjack是Mac和iOS上一个集快捷、简单、强大和灵活于一身的日志框架。CocoaLumberjack类似于流行的日志框架（如log4j），但它是专为Objective-C设计的，利用了多线程、GCD（如果可用）、无锁原子操作Objective-C运行时的动态特性。

## 代码(属性...)快速自动对齐
https://github.com/tid-kijyun/XcodeSourceEditorExtension-Alignment
This Xcode source editor extension align your assignment statement.

## JWBluetoothPrinte
 iOS端蓝牙连接小票打印机进行打印
https://github.com/kafeidou1991/JWBluetoothPrinte


## RapidLogger
log管理实时显示
https://github.com/krishna706/RapidLogger
![](https://github.com/krishna706/RapidLogger/blob/master/2.png)

## GHConsole
一款可以在手机实时显示日志的工具
An elegant and easy way to show a console in your app.
https://github.com/Liaoworking/GHConsole


## CocoaLumberjack
 日志框架
https://github.com/CocoaLumberjack/CocoaLumberjack
```
[DDLog addLogger:[DDTTYLogger sharedInstance]]; // TTY = Xcode console
[DDLog addLogger:[DDASLLogger sharedInstance]]; // ASL = Apple System Logs

DDFileLogger *fileLogger = [[DDFileLogger alloc] init]; // File Logger
fileLogger.rollingFrequency = 60 * 60 * 24; // 24 hour rolling
fileLogger.logFileManager.maximumNumberOfLogFiles = 7;
[DDLog addLogger:fileLogger];

...

DDLogVerbose(@"Verbose");
DDLogDebug(@"Debug");
DDLogInfo(@"Info");
DDLogWarn(@"Warn");
DDLogError(@"Error");
```

## 演示实时浏览器展示APP的日志
https://github.com/yohunl/TestLog
![](https://camo.githubusercontent.com/590db51ed872466ac709fe972fccc59cfc74f3b4/687474703a2f2f37787173706c2e636f6d312e7a302e676c622e636c6f7564646e2e636f6d2f696d6167652f372f33622f38353739353638656261396536343761393231663530333738376331302e706e67)

## SLConsoleLog
日志自定义管理工具，可实时浏览
https://github.com/agustinslions/ManagedLog


## NSLogger
非常好的Log工具，由JSON_KIT的作者编写
https://github.com/fpillet/NSLogger
NSLogger 分为2部分，
1.服务端 NSLogger Viewer MAC端的日志分析，通过网络可以将日志传进来
2.客户端 LoggerClient 是自己的项目使用。

## LLDebugTool
LLDebugTool is a debugging tool for developers and testers that can help you analyze and manipulate data in non-xcode situations.
https://github.com/HDB-Li/LLDebugTool.git
![](https://raw.githubusercontent.com/HDB-Li/HDBImageRepository/master/LLDebugTool/ScreenShot-3.png)

## JSONExport-master
json转model的工具：
支持转：
Objective-C - iOS.
Objective-C - MAC.
Objective-C - CoreData.
Objective-C for Realm iOS.
https://github.com/Ahmed-Ali/JSONExport

## JASONETTE-iOS
通过json数据动态创建界面
https://github.com/Jasonette/JASONETTE-iOS
https://www.jasonette.com
![](https://camo.githubusercontent.com/4d9a652025bdabdda7fd084feb7b1a6470417221/687474703a2f2f692e67697068792e636f6d2f336f37544b72646d6c583575443752737a4b2e676966)

## FXAnimationEngine
An engine to display image frames in animation without causing high-memory usage.(高性能读取git图)
![](https://camo.githubusercontent.com/176e3f2fd0979b3377458e6ca1a0b1d0d1936e97/687474703a2f2f7778342e73696e61696d672e636e2f6d773639302f393136313239376367793166646d6f6b6d73796768673230396f3068377531302e676966)

## ESJsonFormatForMac
软件化ESJsonFormat插件,脱离Xcode环境运行;
https://github.com/czhen09/ESJsonFormatForMac.git
![](https://raw.githubusercontent.com/czhen09/ESJsonFormatForMac/master/image/useGuide.gif)

## NetworkEye
a iOS network debug library ,It can monitor HTTP requests within the App and displays information related to the request.(app内监听请求结果，并界面方式显示)
https://github.com/coderyi/NetworkEye.git

## JDStatusBarNotification
Show messages on top of the status bar. Customizable colors, font and animation. Supports progress display and can show an activity indicator. iOS 7/8 ready. iOS6 support. Please open a Github issue, if you think anything is missing or wrong.(状态提示框)
https://github.com/calimarkus/JDStatusBarNotification.git
![](https://github.com/calimarkus/JDStatusBarNotification/blob/master/gfx/animation.gif)

## NSBundle-OBCodeSigningInfo
检查当前签名、Sandbox状态 MacOSX
A category on NSBundle that adds method to check an app bundle's code signing and sandboxing state.
https://github.com/ole/NSBundle-OBCodeSigningInfo

![](https://github.com/ole/NSBundle-OBCodeSigningInfo)


## lottie-ios
Lottie is a mobile library for Android and iOS that parses Adobe After Effects animations exported as json with bodymovin and renders the vector animations natively on mobile and through React Native!(通过Adobe After Effects animations制作的动画直接对接到app)
https://github.com/airbnb/lottie-ios 
AE插件[bodymovin](https://github.com/bodymovin/bodymovin)
示例demo：https://github.com/jianyu7819/YCLottieTabBar

## awesome-mac

这个仓库主要是收集非常好用的Mac应用程序、软件以及工具，主要面向开发者和设计师
https://github.com/jaywcjlove/awesome-mac/blob/master/README-zh.md

## iOS-Images-Extractor
提取ipa包内的所有资源图片
https://github.com/formShare/iOS-Images-Extractor.git

## iOS-Asset-Extractor
提取ipa包内的所有资源图片，iOS-Images-Extractor也是基于这个修改的
https://github.com/Marxon13/iOS-Asset-Extractor.git

## UIDebuggingTool
iOS自带悬浮窗调试工具
https://github.com/whf5566/UIDebuggingTool.git

## restructuredtext 编辑器
https://github.com/vscode-restructuredtext/vscode-restructuredtext
另一款：
https://yanjiong.wang/articles/RSTMacOSXAtomEditor

## DebugWindow
一个在真机上测试时方便查看输出日志的小工具
https://github.com/lqcjdx/DebugWindow

## IQScreenRuler
UI设计图测量工具
https://github.com/hackiftekhar/IQScreenRuler

## awesome-mac
mac下好用的软件及插件
https://github.com/formShare/awesome-mac

## KLGenerateSpamCode
iOS 马甲应用工具
https://github.com/klaus01/KLGenerateSpamCode
他人在原基础上的修改版，编辑成一个mac软件：https://github.com/JourneyYoung/iOSMixProject?utm_source=gold_browser_extension

## JPFPSStatus
显示刷新帧率
https://github.com/joggerplus/JPFPSStatus.git

## FileBrowser
Finder-style iOS file browser written in Swift
https://github.com/marmelroy/FileBrowser

## Chameleon
Color framework for Swift & Objective-C (Gradient colors, hexcode support, colors from images & more).(各种颜色配色方案)
https://github.com/viccalexander/Chameleon.git

## GTMNSString-HTML
Dealing with NSStrings that contain HTML
https://github.com/siriusdely/GTMNSString-HTML

## KissXML
A replacement for Cocoa's NSXML cluster of classes. Based on libxml. Works on iOS.
https://github.com/robbiehanson/KissXML.git

## SvUDID
UDID for different iOS Version
https://github.com/smileEvday/SvUDID

## MGTemplateEngine
Cocoa system to generate output based on templates and data, like Smarty etc.
https://github.com/mattgemmell/MGTemplateEngine

## BeeFramework
[Experimental] A semi-hybrid framework that allows you to create mobile apps using Objective-C and XML/CSS
https://github.com/gavinkwoe/BeeFramework.git

## EasyIOS
A new generation of development framework based on Model-View-ViewModel(swift版本)
https://github.com/zhuchaowe/EasyIOS.git

## FormatterKit
收集了很多构思优秀的 NSFormatter 子类 \`stringWithFormat:` for the sophisticated hacker set
https://github.com/mattt/FormatterKit.git


## SXFontShow
iOS Font style list use native api
https://github.com/dsxNiubility/SXFontShow.git
![](https://github.com/dsxNiubility/SXFontShow/raw/master/screenshots/104.png)

## ZH
Storyboard和Xib文件生成纯手写代码,只需要将文件放在桌面,就会识别到,点击对应的生成代码到桌面,就会生成对应的Masonry纯手写代码,如果需要给控件,控制器等等设置名字,就在CustomClass里面设置就好了
https://github.com/mcgtts/ZH.git

## Small
https://github.com/wequick/Small.git
世界那么大，组件那么小。Small，做最轻巧的跨平台插件化框架。

## FLEX
An in-app debugging and exploration tool for iOS
https://github.com/Flipboard/FLEX.git
![](https://camo.githubusercontent.com/9986601c5e4306f7935032465911c0f70596e046/687474703a2f2f656e67696e656572696e672e666c6970626f6172642e636f6d2f6173736574732f666c65782f62617369632d766965772d6578706c6f726174696f6e2e676966)


## UIDevice
https://github.com/FromBlogOrNetwork/UIDevice

## WHC_DataModelFactory
高效: 自动把json或者xml字符串自动生成模型类文件内容
准确: 避免手工创建模型类的麻烦和高错误率（提高开发效率）
处理: 自动生成类名称首字符大写属性名首字母小写
兼容: 完美支持WHC_Model、SexyJson开源json解析库
强大: 支持xml/json字符串和dictionary字符串https://github.com/netyouli/WHC_DataModelFactory.git

## LewBarChart
iOS 柱状图，支持多个 Y 轴（简单版）
https://github.com/pljhonglu/LewBarChart.git

## OCRSDKClient
二维码扫描
https://github.com/stel/OCRSDKClient.git

## solarized
终端本色方案、工具
precision color scheme for multiple applications (terminal, vim, etc.) with both dark/light modes http://ethanschoonover.com/solarized

## YouCompleteMe
A code-completion engine for Vim http://valloric.github.io/YouCompleteMe/
https://github.com/Valloric/YouCompleteMe.git

## DHxls

xls生成、读取库，支持微信打开，第三方软件和电脑上打开、编辑
https://github.com/basti35/xlslib/tree/master/xlslib

## libxlsxwriter

A C library for creating Excel XLSX files

```
https://github.com/jmcnamara/libxlsxwriter.git
```

## proxyee-down
http下载工具，基于http代理，支持多连接分块下载
https://github.com/monkeyWie/proxyee-down.git
![](https://github.com/monkeyWie/proxyee-down/raw/master/.guide/common/new-task.png)

## UETool
http下载工具，基于http代理，支持多连接分块下载
https://github.com/eleme/UETool.git
![](https://github.com/eleme/UETool/raw/master/art/move_view.gif)

## Hero
Elegant transition library for iOS & tvOS (swift转场动画)
https://github.com/lkzhao/Hero.git
![](https://camo.githubusercontent.com/ad3b44a1f8c9ad51ba120b6281b03335bd78bb22/68747470733a2f2f63646e2e7261776769742e636f6d2f6c6b7a68616f2f4865726f2f656262336632632f5265736f75726365732f66656174757265732e737667)

## JavaScript 算法与数据结构
https://github.com/trekhleb/javascript-algorithms/blob/master/README.zh-CN.md

## 这里是写博客的地方 —— Halfrost-Field 冰霜之地 https://halfrost.com
https://github.com/halfrost/Halfrost-Field?utm_source=gold_browser_extension

## iOS-InterviewQuestion-collection
iOS 开发者在面试过程中，常见的一些面试题，建议尽量弄懂了原理，并且多实践。
https://github.com/liberalisman/iOS-InterviewQuestion-collection?utm_source=gold_browser_extension



## web-launch-app

launch app from webpage

https://github.com/jawidx/web-launch-app



##12306_code_server

该仓库用于构建自托管的12306验证码识别服务器 [https://12306.yinaoxiong.cn](https://12306.yinaoxiong.cn/)

https://github.com/YinAoXiong/12306_code_server



## 12306智能刷票

https://github.com/testerSunshine/12306