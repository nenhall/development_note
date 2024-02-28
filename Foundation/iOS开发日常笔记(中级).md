# iOS开发日常笔记(中级)
1. Other Linker Flags常用的修饰词说明：
   1. `-ObjC`：对于`ObjC`，官方解释如下：

      ```kotlin
      This flag causes the linker to load every object file in the library that defines an Objective-C class or category. While this option will typically result in a larger executable (due to additional object code loaded into the application), it will allow the successful creation of effective Objective-C static libraries that contain categories on existing classes.
      简单的说就是ObjC会把静态库中所有的类和分类都链接进可执行文件，但是它不会去检查此类是否重复了，所以才会出现duplicate symbols的错误
      ```

   2. `-force_load`：这个的作用其实跟`ObjC`是类似的。它是只指定某一个静态库才执行链接所有类和分类的操作，而`ObjC`表示的是所有静态库

   3. 开发中会遇到`duplicate symbols`的问题，所以理想的状态就是不使用ObjC，只有包含分类的静态库才使用`-force_load`，这样既能避免一些不必要的文件使包变大，又能使出现`duplicate symbols`的概率减小。

      为什么只是减小呢？因为如果你这个静态库里即有重复文件又存在分类，那只能使用-force_load，同时那个重复的类也链接进来了，这样`duplicate symbols`就还会出现

2. 符号表和编译链：

   1. 一个程序从代码到可执行文件要经历以下步骤：源代码 > 预处理器 > 编译器 > 汇编器 > 机器码 > 链接器 > 可执行文件

   2. 源代码经过一系列处理以后，会生成对应的`.o`文件，然后一个项目必然会有许多`.o`文件，并且这些文件之间会有各种各样的联系，例如函数调用。链接器做的事就是把这些目标文件和所用的一些库链接在一起形成一个完整的可执行文件；通俗的讲，我们在源码中写的`全局变量名`、`函数名`或`类名`在生成的`*.o`对象文件中都叫做符号，存在一个叫做符号表的地方。

   3. 举个例子：

      1. `a.c`文件中写了一个函数叫`foo()`，然后在`main.c`文件中调用了`foo()`函数，在将源码编译(生成)的对象文件`a.o`时，对象文件中的符号表里保存着`foo()`函数符号，并通过该符号可以定位到`a.o`文件中关于`foo()`方法的具体实现代码。
      2. 链接器在链接生成最终的二进制程序的时候会发现main.o对象文件中引用了符号foo()，而foo()符号并没有在main.o文件中定义，所以不会存在与main.o对象文件的符号表中，于是链接器就开始检查其他对象文件，当检查到a.o文件中定义了符号foo()，于是就将a.o对象文件链接进来。这样就确保了在main.c中能够正常调用a.c中实现的foo()方法了。

   4. 导出库里面的符号表：`nm`命令，查看库的函数符号信息

      1. 语法：nm [ -A ] [ -C ] [ -X {32|64|32_64}] [ -f ] [ -h ] [ -l ] [ -p ] [ -r ] [ -T ] [ -v ] [ -B | -P ] [ -e | -g | -u ] [ -d | -o | -x | -t Format ] File ...

      2. 语法说明请参考这个链接，我看写得挺详细的：https://blog.csdn.net/jingcheng345413/article/details/54969811

      3. 示例，列出 `a.out` 对象文件的静态和外部符号，请输入： 

         ```
         nm -e a.out
         ```
   
3. Target Conditionals 

   1. The system header `TargetConditionals.h` defines several macros which you can use from C and Objective-C to determine which platform you're using.
   2. The values of the macros are: 7.0 When using the iOS 9.1, tvOS 9.0, watchOS 2.0, OS X 10.11 or newer SDKs:

   | Macro                     | Mac   | iOS   | iOS simulator | Watch | Watch simulator | TV    | TV simulator |
   | ------------------------- | ----- | ----- | ------------- | ----- | --------------- | ----- | ------------ |
   | `TARGET_OS_MAC`           | **1** | **1** | **1**         | **1** | **1**           | **1** | **1**        |
   | `TARGET_OS_IPHONE`        | 0     | **1** | **1**         | **1** | **1**           | **1** | **1**        |
   | `TARGET_OS_IOS`           | 0     | **1** | **1**         | 0     | 0               | 0     | 0            |
   | `TARGET_OS_WATCH`         | 0     | 0     | 0             | **1** | **1**           | 0     | 0            |
   | `TARGET_OS_TV`            | 0     | 0     | 0             | 0     | 0               | **1** | **1**        |
   | `TARGET_OS_SIMULATOR`     | 0     | 0     | **1**         | 0     | **1**           | 0     | **1**        |
   | `TARGET_OS_EMBEDDED`      | 0     | **1** | 0             | **1** | 0               | **1** | 0            |
   | `TARGET_IPHONE_SIMULATOR` | 0     | 0     | **1**         | 0     | **1**           | 0     |              |

4. 通过符号表还原崩溃调用堆信息：

   1. 首先得有对应的`xxx.dSYM`文件

   2. 找到`plCrashReporter.crashlog`文件，名字可能不一样，这个文件源自appstore或其它第三方工具

   3. 导出开发工具，等下需要用到，终端执行：`export DEVELOPER_DIR=/Applications/Xcode.app/Contents/Developer`

   4. 找到`symbolicatecrash`可执行文件：路径：`/Applications/Xcode.app/Contents/SharedFrameworks/DVTFoundation.framework/Versions/A/Resources/`，复制到某文件夹内，把第一步和第二步两文件也一起复制到同一文件下

   5. 当然上面那一步也可以写入终端脚本文件内：

      ```shell
      // 如我终端默认脚本是：`.zshrc`，把下面内容追加到`.zshrc`中，这样下次就可以省略第四步操作了
      export SYMBOLICATECRASH="/Applications/Xcode.app/Contents/SharedFrameworks/DVTFoundation.framework/Versions/A/Resources/"
      export PATH=$SYMBOLICATECRASH:$PATH
      ```

   6. 还原崩溃信息：

      ```shell
      symbolicatecrash plCrashReporter.crashlog xxx.dSYM > appName.log
      // > 代表输出，指定输出的文件：appName.log
      ```

   7. 常见 Exception Type 分析：

      * EXC_BAD_ACCESS：此类型的Excpetion是我们最长碰到的Crash，通常用于访问了不改访问的内存导致。一般EXC_BAD_ACCESS后面的"()"还会带有补充信息。

      * SIGSEGV: 通常由于重复释放对象导致，这种类型在切换了ARC以后应该已经很少见到了。

      * SIGABRT:  收到Abort信号退出，通常Foundation库中的容器为了保护状态正常会做一些检测，例如插入nil到数组中等会遇到此类错误。

      * SEGV:（Segmentation  Violation），代表无效内存地址，比如空指针，未初始化指针，栈溢出等；

      * SIGBUS：总线错误，与 SIGSEGV 不同的是，SIGSEGV 访问的是无效地址，而 SIGBUS 访问的是有效地址，但总线访问异常(如地址对齐问题)

      * SIGILL：尝试执行非法的指令，可能不被识别或者没有权限
      * EXC_BAD_INSTRUCTION：此类异常通常由于线程执行非法指令导致
      * EXC_ARITHMETIC：除零错误会抛出此类异常

   8. 常见 Exception Code 分析：

      * 0xbaaaaaad  此种类型的log意味着该Crash log并非一个真正的Crash，它仅仅只是包含了整个系统某一时刻的运行状态。通常可以通过同时按Home键和音量键，可能由于用户不小心触发
      * 0xbad22222  当VOIP程序在后台太过频繁的激活时，系统可能会终止此类程序
      * 0x8badf00d 程序启动或者恢复时间过长被watch dog终止
      * 0xc00010ff  程序执行大量耗费CPU和GPU的运算，导致设备过热，触发系统过热保护被系统终止
      * 0xdead10cc  程序退到后台时还占用系统资源，如通讯录被系统终止
      * 0xdeadfa11  前面也提到过，程序无响应用户强制关闭
   
5. 多语言文件的正确性验证：

   1. 我们在开发中可能会出现多语言文件中少了；号，但一时以难以发现，此时可以借助命令行来分析：

   2. ````shell
      plutil "多语言文件路径"
      // 用这个命令用终端可以验证多语言文件的正确性，有错误会指出在哪一行
      eg.: 
      ➜  ~ plutil /Users/ws/Studio/Project/it.lproj/InfoPlist.strings
      /Users/ws/Studio/Project/it.lproj/InfoPlist.string: OK
      ➜  ~ 
      ````

6. UIView和CALayer
   1. 关系：在每个UIView实例中，都有个默认的layer图层，UIView负责创建并且管理这个图层。UIView之所以能够显示,就是因为它里面有这个一个层，才具有显示的功能。
   2. 区别：
      1. 首先UIView可以响应事件，CALayer不可以响应事件
      2. 一个 Layer 的 frame 是由它的 anchorPoint,position,bounds,和 transform 共同决定的，而一个 View 的 frame 只是简单的返回 Layer的 frame
      3. UIView主要是对显示内容的管理而 CALayer 主要侧重显示内容的绘制
      4. 在做 iOS 动画的时候，修改非 RootLayer的属性（譬如位置、背景色等）会默认产生隐式动画，而修改UIView则不会。
   
7. swift 与 OC 的区别：

   1. swift和OC的共同点：

      * \- `OC`出现过的绝大多数概念，比如**引用计数**、**ARC**（自动引用计数）、**属性**、**协议**、**接口**、**初始化**、**扩展类**、**命名参数**、**匿名函数**等，在`Swift`中继续有效（可能最多换个术语）。

      *  \- `Swift`和`Objective-C`共用一套运行时环境，`Swift`的类型可以桥接到`Objective-C`（下面我简称OC），反之亦然
   2. swift的优点：

      *  \- swift注重安全，`OC`注重灵活
      *  swift注重值类型，`OC`注重指针和引用
      *  swift是静态类型语言，`OC`是动态类型语言
      *  swift注重面向协议编程、函数式编程、面向对象编程，`OC`注重面向对象编程
      *  swift容易阅读，文件结构和大部分语法简易化，只有.swift文件，结尾不需要分号
      *   swift中的可选类型，是用于所有数据类型，而不仅仅局限于类。相比于`OC`中的`nil`更加安全和简明
      *  swift中的[泛型类型]更加方便和通用，而非`OC`中只能为集合类型添加泛型
      *  swift中各种方便快捷的[高阶函数]（`函数式编程`） (**Swift的标准数组支持三个高阶函数：`map`，`filter`和`reduce`,以及map的扩展`flatMap`**)
      *  swift新增了两种权限，细化权限。**`open` > `public` > `internal(默认)` > `fileprivate` > `private`**
      *  swift中独有的元组类型(`tuples`)，把多个值组合成复合值。元组内的值可以是任何类型，并不要求是相同类型的。

8. Swift 值类型和引用类型深度对比：

   1. 定义值类型和引用类型

      1. `值类型`存储在栈区。 每个值类型变量都有其自己的数据副本，并且对一个变量的操作不会影响另一个变量。
      2. `引用类型`存储在其他位置（堆区），我们在内存中有一个指向该位置的引用。 引用类型的变量可以指向相同类型的数据。 因此，对一个变量进行的操作会影响另一变量所指向的数据
      
   2. 从性能出发
        导致`Swift`结构体（和枚举）与类的性能差异的三个维度是：
   
        1. 复制消耗的成本；
        2. 创建和销毁时花费成本；
        3. 引用计数造成的成本；
   3. 复制的成本
      1. 值类型都分配在栈上的，复制它们需要花费固定的时间。 复制操作速度快的原因是整数和浮点数等基本数据类型存储在CPU寄存器中，复制它们时无需访问RAM内存。 Swift的大多数可扩展类型（例如字符串，数组，集合和字典）都在写入时被复制了（ copied on write）。 这意味着复制操作消耗很小。
      2. 由于引用类型不会直接存储其数据，因此我们在复制它们时只会产生引用计数成本。 引用计数的增加和减少不像整数变化那么简单，还需要额外的花销。因为堆可能同时被多个线程共享，为了保持原子性也需要额外花销。
   
9. Swift 跟 JavaScript 有什么相同和不同点？

   1. 一. 层次结构 
      * 两者都可以在 全局 执行语句和表达式，相比Java和C#只能在类的方法（或初始化及匿名代码块？）里执行语句要人性化很多。
      * 两者都有相应的 依赖机制，但Swift是基于 文件无关的 Module（类似于C#那样层次化的命名空间）的；而 JavaScript 是基于 文件路径 的。因此对于相同复杂程度的依赖，JavaScript 往往需要更多的 import 关键字（恰好两者关键字都是 import ）。
   2. 类型系统
      1. Swift 为 强类型、静态类型；
      2. JavaScript 为 弱类型、动态类型；
      3. Swift 具有一个可选的 **显式类型** 声明语法，也可依靠类型推理使用 **隐式类型**。
   3. 继承方面，
      1. Swift 支持 **协议**（Protocol）类型（可以声明式多继承）；
      2.  JavaScript 并没有接口类型（只能单继承）。