1. 将一个View放在最上层：

   ```swift
   /**
   positioned: 相对位置，是个枚举
   relativeTo: 相对那个View
   */
   func addSubview(_ view: NSView, positioned place: NSWindow.OrderingMode, relativeTo otherView: NSView?)
   
   /* ways to order windows */
     public enum OrderingMode : Int {
           case above = 1
           case below = -1
           case out = 0
     }
   ```

2. 多语言本地化，文字对齐方式问题：

   如`NSTextView`、`NSTextField`，中文英文都是左边为起点，但阿拉伯一些语言是右边为起点：

   1. 如果使用`Xib`、`storyboard`布局，会默认跟根据语言进行适合对齐方式；
   2. 如果使用代码创建控件并使用``Frame`或手写的`autolayout`布局，如是`NSTextField`则需要我们手动为设置对齐方式为：`NSTextAlignment.natural`，就会自动根据
   
3. macOS 10.15 Catalina xxx.app已损坏，无法打开，你应该将它移到废纸篓解决方法:

   1. `sudo spctl --master-disable`在终端使用此命令打开允许来自任何安装来源

   2. 如果使用上面命令后还不行，则再执行如下命令：xxxxxx代表你要执行的app

      ```bash
      sudo xattr -rd com.apple.quarantine /Applications/xxxxxx.app
      ```
   
4. 条件编译：

   1.  判断当前运行的系统和平台

      ```
      // 代码运行在32位的 Windows
      public var TARGET_OS_MAC: Int32 { get }
      // 代码运行在 Mac OS X
      public var TARGET_OS_WIN32: Int32 { get }
      // 代码运行在某些 Unix(不是OSX)
      public var TARGET_OS_UNIX: Int32 { get }
      // 代码运行在 OS X 下的设备
      public var TARGET_OS_OSX: Int32 { get }
      // 代码运行在 iphone，包括设备和模拟器
      public var TARGET_OS_IPHONE: Int32 { get }
      // 代码运行在 iOS系统
      public var TARGET_OS_IOS: Int32 { get }
      // 代码运行在 Watch OS
      public var TARGET_OS_WATCH: Int32 { get }
      // 代码运行在桥接的设备下
      public var TARGET_OS_BRIDGE: Int32 { get }
      // 代码运行在 TV OS
      public var TARGET_OS_TV: Int32 { get }
      // 代码运行在所有的模拟器下
      public var TARGET_OS_SIMULATOR: Int32 { get }
      // 代码运行在固件下
      public var TARGET_OS_EMBEDDED: Int32 { get }
      // 由32位 PowerPC 指令集编译生成
      public var TARGET_CPU_PPC: Int32 { get }
      // 由64位 PowerPC 指令集编译生成
      public var TARGET_CPU_PPC64: Int32 { get }
      // 由 680 x0 指令指令集编译生成
      public var TARGET_CPU_68K: Int32 { get }
      // 由 x86 指令集编译生成
      public var TARGET_CPU_X86: Int32 { get }
      // 由64位 X86 指令集编译生成
      public var TARGET_CPU_X86_64: Int32 { get }
      // 由 ARM 指令集编译生成
      public var TARGET_CPU_ARM: Int32 { get }
      // 由64位 ARM 指令集编译生成
      public var TARGET_CPU_ARM64: Int32 { get }
      // 由 MIPS 指令集编译生成
      public var TARGET_CPU_MIPS: Int32 { get }
      // 由 Sparc 指令集编译生成
      public var TARGET_CPU_SPARC: Int32 { get }
      // 由 Dec Alpha 指令集编译生成
      public var TARGET_CPU_ALPHA: Int32 { get }
      #if defined(WIN32) || defined(_WIN32) || defined(__WIN32__) || defined(__NT__)
         //define something for Windows (32-bit and 64-bit, this part is common)
         #ifdef _WIN64
            //define something for Windows (64-bit only)
         #else
            //define something for Windows (32-bit only)
         #endif
      #elif __APPLE__
          #include <TargetConditionals.h>
          #if TARGET_IPHONE_SIMULATOR
               // iOS Simulator
          #elif TARGET_OS_IPHONE
              // iOS device
          #elif TARGET_OS_MAC
              // Other kinds of Mac OS
          #else
          #   error "Unknown Apple platform"
          #endif
      #elif __linux__
          // linux
      #elif __unix__ // all unices not caught above
          // Unix
      #elif defined(_POSIX_VERSION)
          // POSIX
      #else
      #   error "Unknown compiler"
      #endif
      ```

   2. 条件编译

      | 平台条件              | 有效参数                                   |
      | :-------------------- | :----------------------------------------- |
      | `os()`                | `macOS`，`iOS`，`watchOS`，`tvOS`，`Linux` |
      | `arch()`              | `i386`，`x86_64`，`arm`，`arm64`           |
      | `swift()`             | `>=` 后跟版本号                            |
      | `compiler()`          | `>=` 后跟版本号                            |
      | `canImport()`         | 模块名称                                   |
      | `targetEnvironment()` | `simulator`                                |

      ```
      条件可以包括true和false布尔文字，与使用的标识符-D命令行标记，或者任何在下面的表中列出的平台的条件。
      // 模拟器 swift版
      // #if targetEnvironment(macCatalyst)
      #if targetEnvironment(simulator)
        // Simulator!
      #endif
      #if (arch(x86_64) || arch(i386)) && os(iOS)
        // Simulator!
      #endif
      // oc版
      #if !TARGET_OS_MACCATALYST
      // Code to exclude from Mac.
      #endif
      // 导入判定
      #if canImport(AppKit) && !targetEnvironment(macCatalyst)
      
      #if compiler(>=4.2)
      print("Compiled with the Swift 4.2 compiler or later")
      #endif
      #if swift(>=4.2)
      print("Compiled in Swift 4.2 mode or later")
      #endif
      #if swift(>=3.0)
      print("Compiled in Swift 3.0 mode or later")
      #endif
      ```

   3. 编译时诊断语句

      ```
      #error("error message")
      #warning("warning message")
      ```

   4. 行控制语句

      ```
      #sourceLocation(file: filename, line: line number)
      #sourceLocation()
      ```

   5. mac 打开系统偏好设备面板：

      1. 方法一：

         ```
         NSWorkspace.shared.openFile("/System/Library/PreferencePanes/Security.prefPane")
         // 其实这就是一个文件路径，路径下有一个对面设置界面的`xxx.prefPane`启动文件，可cd 到对应目录看一下
         偏好设置面板文件一般存储在以下路径：
         # system preference panes
         /System/Library/PreferencePanes/
         # user-installed system-wided preference panes
         /Library/PreferencePanes/
         # user-installed user-wided preference panes
         ~/Library/PreferencePanes/
         ```

      2.  方法二:

          使用`AppleScript`的方式打开：参考 https://objccn.io/issue-14-2/ 文章

      3.  方法三：使用 `URL Scheme`打开指定面板

          ```swift
          // 如 安全性与隐私 → 辅助功能 → 键盘 的 URL Scheme 为：
          NSWorkspace.shared.open(URL(string: "x-apple.systempreferences:com.apple.preference.universalaccess?Keyboard")!)
          ```

          URL 的构成：前缀 + 面板的 Bundle Identifier + 锚点

          解释：

          	* 控制面板的 URL Scheme 格式是 `x-apple.systempreferences:` 即上面所说的前缀
          	* 面板ID：`com.apple.preference.universalaccess`
          	* 锚点：`Keyboard`

          查询锚点的方法：

          使用 Script Editor 执行如下代码，结果如下图：

          ```javascript
          tell application "System Preferences"
          	anchors of pane "com.apple.preference.security"
          end tell
          ```

          ![锚点](https://blog-1257063273.cos.ap-chengdu.myqcloud.com/2020/20200911114858.png)

          查看应用是否支持`URL Scheme`，在各个控制面板对应的 `.prefpane` 文件里的 `Info.plist` 中，如果有下面这一键值对，则表示这个面板支持 URL Scheme:

          ```
          <key>NSPrefPaneAllowsXAppleSystemPreferencesURLScheme</key>
          <true/>
          ```

      4. 方法四：

         1. 导入：ScriptingBridge.framework

         2. 在Terminal中使用类似以下命令的命令来生成要使用的SBSystemPreferences.h头文件：
             `sdef "/Applications/System Preferences.app" | sdp -fh --basename SBSystemPreferences -o ~/Desktop/SBSystemPreferences.h`

         3. 将该`SBSystemPreferences.h`标头添加到您的项目

         4. 写代码：

           ```objective-c
           - (IBAction)openSpeechPrefs:(id)sender {
               SBSystemPreferencesApplication *systemPrefs = 
               [SBApplication applicationWithBundleIdentifier:@"com.apple.systempreferences"];
               [systemPrefs activate];
               SBElementArray *panes = [systemPrefs panes];
               SBSystemPreferencesPane *speechPane = nil;
               for (SBSystemPreferencesPane *pane in panes) {
                   if ([[pane id] isEqualToString:@"com.apple.preference.speech"]) {
                       speechPane = pane;
                       break;
                   }
               }
               [systemPrefs setCurrentPane:speechPane];
               SBElementArray *anchors = [speechPane anchors];
               for (SBSystemPreferencesAnchor *anchor in anchors) {
                   if ([anchor.name isEqualToString:@"TTS"]) {
                       [anchor reveal];
                   }
               }
           }
           ```

   6.  清除mac 电脑应用访问的隐私权限：

       ``` shell
       //  com.apple.QuickTimePlayerX为对应应用的bunldeID
       tccutil reset Camera com.apple.QuickTimePlayerX  //摄像头
       tccutil reset Microphone com.apple.QuickTimePlayerX //麦克风
tccutil reset Photos com.apple.QuickTimePlayerX // 照片
       ```
       
       

