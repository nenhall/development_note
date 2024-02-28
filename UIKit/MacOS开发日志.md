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
   3. 也可以根据系统的函数来判定：`NSApplication.shared.userInterfaceLayoutDirection`
   
3. macOS 10.15 Catalina xxx.app已损坏，无法打开，你应该将它移到废纸篓解决方法:

   1. `sudo spctl --master-disable`在终端使用此命令打开允许来自任何安装来源

   2. 如果使用上面命令后还不行，则再执行如下命令：xxxxxx代表你要执行的app

      ```bash
      sudo xattr -rd com.apple.quarantine /Applications/xxxxxx.app
      ```

