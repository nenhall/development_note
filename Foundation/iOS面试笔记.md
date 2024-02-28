## iOS面试笔记

1. weak的内部实现原理

	1. 将弱引用（weak指针）存储到一个hash表内，当前对象的地址值作为Key，Value是weak指针的地址数组，也叫弱引用表
	2. 被weak修饰的对象是弱引用，所引用对象的计数器不会+1，在引用对象dealloc的时候，会去弱引用表里面取出当前对象的所有弱引用指针进行清除，将其值设置成 nil

2. ARC都帮我们做了什么？

   利用LLVM编译特性 + runtime运行系统动态的管理我们创建的对象，两者相互协作来完成的

3. @autoreleasePool

   1. 数据结构：简单说是双向链表，每张链表头尾相接，有parent、child指针，每创建一个池子，会在首部创建一个 哨兵 对象,作为标记，最外层池子的顶端会有一个next指针。当链表容量满了，就会在链表的顶端，并指向下一张表。
   2. 释放时机：
      1. 如果是直接被@autoreleasePool{}包括在大括号内的，那就是执行完大括号就进会释放，
      2. 如果没有被@autoreleasePool{}包括，只是在某个函数内，那由runloop来控制，可能是在所属的某次runloop循环中，runloop休眠之前之前调用release

4. 代理设计模式：

   1. 主要作用：通过引入代理对象的方式来间接访问目标对象。在某些情况下，一个对象不适合或者不能直接引用另一个对象，而代理对象可以在客户端和目标对象之间起到中介的作用；防止直接访问目标对象带来的不必要复杂性；
   2. 代理包括三部分：代理，委托，协议
   3. 优点：
   		* 职责清晰：被代理对象只负责自己实际的业务逻辑，不关心其他非本身的职责。并将其他事务可以通过代理类处理。
      * 高扩展性：无论被代理对象如何改变，只要代理类和被代理类都实现了统一接口，都不用修改代理类，而且即使扩展了新的被代理类，代理类也可以使用。
      * 降低耦合度：能够协调调用者和被调用者，在一定程度上降低了系统的耦合度
      * 保护代理：可以控制对一个对象的某些访问权限，及隐藏委托类的实现
   4. 缺点：
      * 只能一对一

   

5. Block回调

   1. block是一种轻量级的回调，可以直接访问上下文；
   2. 优点：
      * block的代码可读性更好，因为block只要实现就可以了
      * 由于block的代码是内联的，运行效率更高
      * 可以把block当做一个成员变量、属性、参数使用，使用起来非常灵活
   3. 缺点：
      * blcok的运行成本高。block出栈需要将使用的数据从栈内存拷贝到堆内存
      * block容易造成循环引用，而且不易察觉。因为为了blcok不被系统回收，所以我们都用copy关键字修饰，实行强引用。block对捕获的变量也都是强引用，所以就会造成循环引用
   4. 代理与Block的选择：

   	 * 如果回调的状态过多、多个消息传递，使用代理
   	 * 如回调的次数很频繁，使用代理
   	 * delegate运行成本低，block成本很高的；delegate只是保存了一个对象指针，没有额外消耗，只多做了一个查表动作(相对C的函数指针)

   

6. 观察者模式

   涉及多个对象都对一个特殊对象中的数据变化感兴趣，而且这多个对象都希望跟踪那个特殊对象中的数据变化，在这样的情况下就可以使用观察者模式

   1. 优点：

      1. 观察者和被观察之间是抽象耦合，容易扩展
   2. 缺点：

      1. 运行效率较低，一个被观察者，多个观察者时，开发代码和调试会比较复杂
      2. 通知虽然耦合低但不能被滥用，通知还考虑多线程调用(如果对观察者的通知是通过另外的线程进行异步投递的话，必须保证投递是以自恰的方式进行的)


7. 通知和代理跟有什么区别？
   * 通知是观察者模式，适合一对多的场景
   * 代理模式适合一对一的反向传值
   * 通知耦合度低，代理的耦合度高
   * delegate运行成本低，block的运行成本高
   * block出栈需要将使用的数据从栈内存拷贝到堆内存，当然对象的话就是加计数，使用完或者block置nil后才消除。delegate只是保存了一个对象指针，直接回调，没有额外消耗。就像C的函数指针，只多做了一个查表动作。
   * delegate更适用于多个回调方法（3个以上），block则适用于1，2个回调时

   

8. 单例模式

   为了确保程序运行期某个类，只有一份实例，用于进行资源共享控制。可用于跨模块传值
   
   1. 优点：
         1. 提供了对唯一实例的受控访问
         2. 由于在系统内存中只存在一个对象，因此可以节约系统资源，对于一些需要频繁创建和销毁的对象单例模式无疑可以提高系统的性能
   
   2. 缺点：
          1. 单例类的职责过重，在一定程度上违背了“单一职责原则”
          2. 由于单利模式中没有抽象层，因此单例类的扩展有很大的困难
          3. 不可过多的创建单例，因为单例从创建后到彻底关闭程序前都会一直存在，如果过多的创建单例无疑浪费系统资源和影响系统效率
     4. 可能会导致共享连接池对象的程序过多而出现连接池溢出；如果实例化的对象长时间不被利用，系统会认为是垃圾而被回收，这将导致对象状态的丢失
   
9. 图像和流媒体 -- I 帧,B帧,P帧,IDR帧的区别


   1. 序列的说明：

      在H264中图像以序列为单位进行组织，一个序列是一段图像编码后的数据流，以 I 帧开始，到下一个 I 帧结束。
      一个序列的第一个图像叫做 IDR 图像（立即刷新图像），IDR 图像都是 I 帧图像。H.264 引入 IDR 图像是为了解码的重同步

   2. 三种帧的说明：


      1. I 帧：
    
         1. 描述：帧内编码帧 ，I 帧表示关键帧，你可以理解为这一帧画面的完整保留；解码时只需要本帧数据就可以完成（因为包含完整画面）
    
         2. I 帧特点: 
    
            * 它是一个全帧压缩编码帧。它将全帧图像信息进行 JPEG 压缩编码及传输;
            * 解码时仅用 I 帧的数据就可重构完整图像;
            * I 帧描述了图像背景和运动主体的详情;
            * I 帧不需要参考其他画面而生成;
            * I 帧是 P 帧和 B 帧的参考帧(其质量直接影响到同组中以后各帧的质量);
            * I 帧是帧组 GOP 的基础帧(第一帧),在一组中只有一个 I 帧;
            * I 帧不需要考虑运动矢量;
            * I 帧所占数据的信息量比较大。
    
         3. P 帧：
    
            1. 描述：前向预测编码帧。P 帧表示的是这一帧跟之前的一个关键帧（或P 帧）的差别，解码时需要用之前缓存的画面叠加上本帧定义的差别，生成最终画面。（也就是差别帧，P 帧没有完整画面数据，只有与前一帧的画面差别的数据）；P帧的预测与重构：P 帧是以 I 帧为参考帧,在 I 帧中找出 P 帧“某点”的预测值和运动矢量，取预测差值和运动矢量一起传送。在接收端根据运动矢量从 I 帧中找出 P 帧“某点”的预测值并与差值相加以得到 P 帧“某点”样值,从而可得到完整的 P 帧。
    
            2. P帧特点:
    
               * P 帧是 I 帧后面相隔 1~2 帧的编码帧;
               * P 帧采用运动补偿的方法传送它与前面的 I 或 P 帧的差值及运动矢量(预测误差);
               * 解码时必须将 I 帧中的预测值与预测误差求和后才能重构完整的 P 帧图像;
               * P 帧属于前向预测的帧间编码。它只参考前面最靠近它的 I 帧或 P 帧;
               * P 帧可以是其后面 P 帧的参考帧,也可以是其前后的 B 帧的参考帧;
               * 由于 P 帧是参考帧,它可能造成解码错误的扩散;
               * 由于是差值传送，P 帧的压缩比较高。
    
            3. B 帧：
    
               1. 描述：双向预测内插编码帧。B 帧是双向差别帧，也就是 B 帧记录的是本帧与前后帧的差别（具体比较复杂，有 4 种情况，但我这样说简单些），换言之，要解码 B 帧，不仅要取得之前的缓存画面，还要解码之后的画面，通过前后画面的与本帧数据的叠加取得最终的画面。B 帧压缩率高，但是解码时 CPU 会比较累。B 帧的预测与重构：B 帧以前面的 I 或 P 帧和后面的 P 帧为参考帧,“找出”B 帧“某点”的预测值和两个运动矢量,并取预测差值和运动矢量传送。接收端根据运动矢量在两个参考帧中“找出(算出)”预测值并与差值求和,得到 B帧“某点”样值,从而可得到完整的 B 帧。
    
               2. B 帧特点：
    
                  * B 帧是由前面的 I 或 P 帧和后面的 P 帧来进行预测的;
                  * B 帧传送的是它与前面的 I 或 P 帧和后面的 P 帧之间的预测误差及运动矢量;
                  * B 帧是双向预测编码帧;
                  * B 帧压缩比最高，因为它只反映丙参考帧间运动主体的变化情况,预测比较准确;
                  * B 帧不是参考帧,不会造成解码错误的扩散。
    
               3. 为什么需要B帧：
    
                  因为B帧记录的是前后帧的差别，比P帧能节约更多的空间，但这样一来，文件小了，解码器就麻烦了，因为在解码时，不仅要用之前缓存的画面，还要知道下一个I或者P的画面（也就是说要预读预解码），而且，B帧不能简单地丢掉，因为B帧其实也包含了画面信息，如果简单丢掉，并用之前的画面简单重复，就会造成画面卡（其实就是丢帧了）

   3. PTS和DTS


      1. P帧需要参考前面的I帧或P帧才可以生成一张完整的图片，而B帧则需要参考前面I帧或P帧及其后面的一个P帧才可以生成一张完整的图片。这样就带来了一个问题：在视频流中，先到来的 B 帧无法立即解码，需要等待它依赖的后面的 I、P 帧先解码完成，这样一来播放时间与解码时间不一致了，顺序打乱了，那这些帧该如何播放呢？这时就引入了另外两个概念：DTS 和 PTS。
      2. DTS（Decoding Time Stamp）：即**解码时间戳**，这个时间戳的意义在于告诉播放器该在什么时候解码这一帧的数据。
      3. PTS（Presentation Time Stamp）：即**显示时间戳**，这个时间戳用来告诉播放器该在什么时候显示这一帧的数据。
      4. 注：I、B、P 各帧是根据压缩算法的需要，是人为定义的,它们都是实实在在的物理帧。一般来说，I 帧的压缩率是7（跟JPG差不多），P 帧是20，B 帧可以达到50
      5. 原文链接：https://blog.csdn.net/qq_29350001/article/details/73770702
         原文链接：https://www.cnblogs.com/yongdaimi/p/10676309.html

   






10. 堆、栈和队列 分别是什么？


   1. 堆：
      * 堆是一种经过排序的树形数据结构，每个节点都有一个值，通常我们所说的堆的数据结构是指二叉树
      * 堆常用来实现优先队列，堆的存取是随意的，这就如同我们在图书馆的书架上取书，虽然书的摆放是有顺序的，但是我们想取任意一本时不必像栈一样，先取出前面所有的书，书架这种机制不同于箱子，我们可以直接取出我们想要的书。
   2. 栈：
      * 栈是限定仅在表尾进行插入和删除操作的线性表。我们把允许插入和删除的一端称为栈顶，另一端称为栈底，不含任何数据元素的栈称为空栈。栈的特殊之处在于它限制了这个线性表的插入和删除位置，它始终只在栈顶进行。
      * 栈是一种具有后进先出的数据结构，又称为后进先出的线性表，简称 LIFO（Last In First Out）结构。也就是说后存放的先取，先存放的后取，这就类似于我们要在取放在箱子底部的东西（放进去比较早的物体），我们首先要移开压在它上面的物体（放进去比较晚的物体）。
      * 栈的应用—递归
   3. 队列：队列是一种先进先出的数据结构，又称为先进先出的线性表，简称 FIFO（First In First Out）结构。也就是说先放的先取，后放的后取

   

11. 选择排序、冒泡排序、插入排序三种排序算法可以总结为如下：

    都将数组分为已排序部分和未排序部分。

    1. 选择排序将已排序部分定义在左端，然后选择未排序部分的最小元素和未排序部分的第一个元素交换。
    2. 冒泡排序将已排序部分定义在右端，在遍历未排序部分的过程执行交换，将最大元素交换到最右端。
    3. 插入排序将已排序部分定义在左端，将未排序部分元的第一个元素插入到已排序部分合适的位置。

    

12. 深拷贝与浅拷贝分别是什么？
    1. 浅拷贝只是对指针的拷贝，拷贝后两个指针指向同一个内存空间
    2. 深拷贝不但对指针进行拷贝，而且对指针指向的内容进行拷贝，经深拷贝后的指针是指向两个不同地址的指针。


13. copy与strong的区别
    1. 对象属性描述copy与strong的区别
       在用@property声明NSString是经常要使用copy而不是strong的原因：
       1. 父类指针可以指向子类对象（如上代码中NSMutableString是NSString的子类），使用copy的目的是为了对象有更好的封装性，不受外部影响。无论外部传入可变或不可变对象，本身持有一个不可变的副本。
       2. 使用strong，属性可能指向可变对象，如果这个对象被外部更改，则该属性也会受到影
       3. 同样和NSString有可变子类的NSArray，NSDictionary，在声明时也应该使用copy描述
    2. 对象的copy与mutablecopy：
       1. 在非集合类对象中：
          1. 对 immutable(不可变) 对象进行 copy 操作，是指针复制(浅拷贝)，mutableCopy 操作时内容复制
          2. 对 mutable（可变） 对象进行 copy 和 mutableCopy 都是内容复制(深拷贝)
       2. 集合对象中：
          1. 对 immutable(不可变) 对象进行 copy 操作，是指针复制，mutableCopy 操作时单层内容复制，但需要强调的是：此处的内容拷贝，仅仅是拷贝 array 这个对象，array 集合内部的元素仍然是指针拷贝
          2. 对 mutable（可变） 对象进行 copy 和 mutableCopy 都是内容复制，仅仅是拷贝 array 这个对象，array 集合内部的元素仍然是指针拷贝
          3. copy方法返回的对象是不可变对象
          4. 集合对象的完全复制的方法：
    
             ```objective-c
             NSMutableArray *array1 = [NSMutableArray arrayWithObjects:@"a",@"b",@"c", nil];
                 NSArray *array2 = [[NSArray alloc] initWithArray:array1 copyItems:YES];
                 NSLog(@"%p %p",array1,array2);
                 NSLog(@"%p %p",array1[0],array2[0]);
             // 或者先将集合进行归档，然后再解档，这样也可实现集合对象完全拷贝
             ```
    
          5. copy拷贝NSSting字符串时，拷贝指针，既浅拷贝；
    
          6. copy拷贝NSMutableString字符串时是重新生成一个新对象，即深拷贝。
    
          7. copy修饰的可变字符串属性类型始终是NSString，而不是NSMutableString，如果想让拷贝过来的对象是可变的，就要使用mutableCopy。(所有copy修饰的NSSting字符串不会被外界影响)
    
          8. strong表示强指向，对可变和不可变字符串都只有浅拷贝。对NSMutableString不存在深拷贝。


​    

14. @property的本质是什么？ivar、getter、setter是如何生成并添加到这个类中的


    1. @property 的本质是实例变量（ivar）+存取方法（access method ＝ getter + setter）,即 @property = ivar + getter + setter;
    2. ivar、getter、setter 是自动合成这个类中的，完成属性定义后，编译器会自动编写访问这些属性所需的方法，此过程叫做“自动合成”(autosynthesis)。需要强调的是，这个过程由编译 器在编译期执行，除了生成方法代码 getter、setter 之外，编译器还要自动向类中添加适当类型的实例变量，并且在属性名前面加下划线，以此作为实例变量的名字



## iOS内存管理方式

1. Tagged Pointer（小对象）：
   * Tagged Pointer 专门用来存储小的对象，例如 NSNumber 和 NSDate
   * Tagged Pointer 指针的值不再是地址了，而是真正的值。所以，实际上它不再是一个对象了，它只是一个披着对象皮的普通变量而已。所以，它的内存并不存储在堆中，也不需要 malloc 和 free，在内存读取上有着高效率
   
2. NONPOINTER_ISA （指针中存放与该对象内存相关的信息） 苹果将 isa 设计成了联合体，在 isa 中存储了与该对象相关的一些内存的信息

3. 内存管理内容如下:

   - 内存布局
   - 内存管理
   - 数据结构
   - ARC/MRC
   - 引用计数
   - 弱引用
   - 自动释放池
   - 循环引用

## ARC 的 retainCount 怎么存储的？

##### 散列表（引用计数表、weak表）

- SideTables 表在 非嵌入式的64位系统中，有 64张 SideTable 表
- 每一张 SideTable 主要是由三部分组成。自旋锁、引用计数表、弱引用表。
- 全局的 引用计数 之所以不存在同一张表中，是为了避免资源竞争，解决效率的问题。
- 引用计数表 中引入了 分离锁的概念，将一张表分拆成多个部分，对他们分别加锁，可以实现并发操作



## KVC的实现原理

KVC，键-值编码，使用字符串直接访问对象的属性。

* 当一个对象调用setValue方法时，方法内部会做以下操作：
  1. 检查是否存在相应key的set方法，如果存在，就调用set方法
  2. 如果set方法不存在，就会先顺序查找：setKey > _setKey 方法，如果找到就调用，否则调用`+(B00L)accessInstanceVariablesDirectly`，如果这个函数返回YES，就会按: _key > _isKey > key > isKey 顺序查找对应的成员变量，
  3. 如果找到就赋值，则调用``valueForUndefinedKey：``和``setValue：forUndefinedKey：``方法，如果没有实现这两方法，就抛异常

* 当一个对象调用getValue方法时，方法内部会做以下操作：
  1. 先检查是否有对应的方法，如果没有就按顺序查找：getKey > key > isKey > _key 方法，如果找到就调用，否则调用`+(B00L)accessInstanceVariablesDirectly`，如果这个函数返回YES，就会按: _key > _isKey > key > isKey 顺序查找对应的成员变量，

## KVO的实现原理

- 1.当给A类添加KVO的时候，runtime动态的生成了一个子类NSKVONotifying_A，让A类的isa指针指向NSKVONotifying_A类，重写class方法，隐藏对象真实类信息
- 2.重写监听属性的setter方法，在setter方法内部调用了Foundation 的 _NSSetObjectValueAndNotify 函数
- 3._NSSetObjectValueAndNotify函数内部
  * 首先会调用 willChangeValueForKey
  * 然后给属性赋值
  * 最后调用 didChangeValueForKey
  * 最后调用 observer 的 observeValueForKeyPath 去告诉监听器属性值发生了改变 .
- 4.重写了dealloc做一些 KVO 内存释放
- 直接修改成员变量不会触发KVO，必须调用set方法才会触发；但通过KVC修改属性也会触发KVO

## Block 本质

* 变量捕获机制：

  * 访问局部 auto 变量：block内部也会生成一个对应的成员变量，但它是值传递
  * 访问局部 static 变量：block内部也会捕获变量，但捕获的不是具体值，而是变量的指针
  * 访问全局变量：block内部不会捕获，是在使用时直接访问的
  * 访问对象的成员变量：不管是用_age 还是self.age访问，都会捕获self，调用者是谁，self就是谁，访问的时候block内部是直接通过self去访问的，内部不侍捕获age

* 为什么局部变量要捕获，而全局变量不用：

  因为局部变量的作用域是函数范围内，出了函数变量就会销毁，但block存在跨函数调用，这种情况下就无法访问对应变量了；而全局变量在任何位置都可以访问，没必须捕获

* Block 种类：

  | **block 类型**                             | **环境**                   |
  | :----------------------------------------- | :------------------------- |
  | _*NSGlobalBlock*_ (_NSConcreteGlobalBlock) | 没有访问auto变量           |
  | _*NSStackBlock*_ (_NSConcreteStackBlock)   | 访问了auto变量             |
  | _*NSMallocBlock*_ (_NSConcreteMallocBlock) | __NSStackBlock__调用了copy |

  | **block 类型**    | **环境**                         | **复制效果** |
  | :---------------- | :------------------------------- | :----------- |
  | _*NSGlobalBlock*_ | 没有访问auto变量(数据区域)       | 从栈复制到堆 |
  | _*NSStackBlock*_  | 访问了auto变量(栈区)             | 什么也不做   |
  | _*NSMallocBlock*_ | __NSStackBlock__调用了copy(堆区) | 引用计数增加 |

* 栈自动拷贝的情况：

  ARC 环境下如下情况，编译器会根据情况自动将栈上的block复制到堆上：

  * block 作为函数返回值时
  * 将block赋值给 __strong 指针时
  * blcok 作为GCD API 的方法参数时
  * block 作为 cocoa API 中方法名含有 usingBlock 的方法参数时

* 局部block默认是globalBlock，存放在数据区，如果访问了auto变量，block会自动调用copy操作，变成MallocBlock存放在堆区，待block出了作用域后，会自动销毁

  MRC 环境下：

  * 局部block默认是globalBlock，存放在数据区，如果访问了auto变量，则变成 stackBlock 存放在栈区，需要手动调用 block copy 操作才会成为 MallocBlock

* __block 

  * 不能修饰全局变量、静态变量，
  * \___block 修饰 auto 变量，编译器会将 \__block变量包装成一个结构体，且结构体内就有对应的成员变量，并把\__block拷贝到堆区，此时结构体可以对变量进行修改
  * \__block只能修饰自动变量，block内访问auto变量时，正常是值拷贝，内部无法修改__
  * 在ARC下，当block使用完后，会自动把刚拷贝的\__block进行释放

## Http 和 Https 的区别？Https为什么更加安全？

1. 区别

   * HTTPS 需要向机构申请 CA 证书，极少免费。
   * HTTP 属于明文传输，HTTPS基于 SSL 进行加密传输。
   * HTTP 端口号为 80，HTTPS 端口号为 443 。
   * HTTPS 是加密传输，有身份验证的环节，更加安全。

2. 安全

   * SSL(安全套接层)    TLS(传输层安全)
   * 以上两者在传输层之上，对网络连接进行加密处理，保障数据的完整性，更加的安全。

3. 优缺点

   https的优点：

   * 使用https协议可认证用户和服务器，确保数据发送到正确的客户机和服务器
   * 可防止数据在传输过程中不被窃取、改变，确保数据的完整性
   * 在搜索引擎中一些关于排名的问题

   https的坏处：

   * https协议握手阶段比较费时，对网站的响应速度有负面影响，使页面的加载时间延长(打开速度问题完全可以通过CDN加速解决)
   * https协议还会影响缓存，增加数据开销和功耗
   * https协议的加密范围也比较有限，在黑客攻击、拒绝服务攻击、服务器劫持等方案几乎起不到什么作用
   * https连接缓存不如http高效，大流量网站如非必要也不会采用，流量成本太高

   

## get 和 post 主要区别

- get请求无消息体，只能携带少量数据
- post请求有消息体，可以携带大量数据
- get请求将数据放在url地址中
- post请求将数据放在消息体中
- GET方式提交的数据最多只能有1024字节，而POST则没有此限制



## 解释一下 三次握手 和 四次挥手

1. 三次握手

   1. 由客户端向服务端发送 SYN 同步报文。
   2. 当服务端收到 SYN 同步报文之后，会返回给客户端 SYN 同步报文和 ACK 确认报文。
   3. 客户端会向服务端发送 ACK 确认报文，此时客户端和服务端的连接正式建立。

2. 建立连接

   1. 这个时候客户端就可以通过 Http 请求报文，向服务端发送请求
   2. 服务端接收到客户端的请求之后，向客户端回复 Http 响应报文。

3. 四次挥手

   当客户端和服务端的连接想要断开的时候，要经历四次挥手的过程，步骤如下：

   1. 先由客户端向服务端发送 FIN 结束报文。
   2. 服务端会返回给客户端 ACK 确认报文 。此时，由客户端发起的断开连接已经完成。
   3. 服务端会发送给客户端 FIN 结束报文 和 ACK 确认报文。
   4. 客户端会返回 ACK 确认报文到服务端，至此，由服务端方向的断开连接已经完成



## TCP 和 UDP的区别

- TCP：面向连接、传输可靠(保证数据正确性,保证数据顺序)、用于传输大量数据(流模式)、速度慢，建立连接需要开销较多(时间，系统资源)。
- UDP：面向非连接、传输不可靠、用于传输少量数据(数据包模式)、速度快。



## Cookie和Session

1. cookie主要是用来记录用户状态，区分用户，状态保存在客户端。cookie功能需要浏览器的支持。如果浏览器不支持cookie（如大部分手机中的浏览器）或者把cookie禁用了，cookie功能就会失效。

2. Session

   - Session是服务器端使用的一种记录客户端状态的机制，使用上比Cookie简单一些，相应的也增加了服务器的存储压力。
   - Session是另一种记录客户状态的机制，不同的是Cookie保存在客户端浏览器中，而Session保存在服务器上。 客户端浏览器访问服务器的时候，服务器把客户端信息以某种形式记录在服务器上。这就是Session。客户端浏览器再次访问时只需要从该Session中查找该客户的状态就可以了。



## 多线程的 并行 和 并发 有什么区别？

- 串行和并行描述的是任务和任务之间的执行方式
- 串行：是任务顺序执行的往下执行
- 并行：是任务与任务之间可以同时执行，多核cpu在多个线程同时执行，如果单核cpu就只有并发了
- 并发：在一条线程上通过快速切换任务来回执行
- 线程从开始到结束的状态：
  - 新建状态 -> 就绪状态 ->运行状态 ->阻塞状态(重复回到就绪状态) ->死亡状态(正常死亡、非正常死亡)

## iOS中实现多线程的几种方案，各自有什么特点？
* pthread：是一种纯 C 语言跨平台的API，提供的多线程方案,使用 pthread 非常麻烦
* NSThread 面向对象的，需要程序员手动创建线程。子线程间通信很难，还得程序员手动管理线程，所以用的不多
* GCD c语言，充分利用了设备的多核，自动管理线程生命周期。比NSOperation效率更高。
* NSOperation 基于gcd封装，更加面向对象，比 GCD 多了一些功能。
* 多线程的优缺点

  1. 优点

     1. 能适当提高程序的执行效率
     2. 能适当提高资源的利用率(CPU、内存利用率)

  2. 缺点
      1. 开启线程需要占用一定的内存空间（默认情况下，每一条线程都占用512KB），如果开启大量的线程，会占用大量的内存空间，从而降低程序的性能。
     2. 线程越多，CPU在调度线程上的开销就越大。
     3. 线程越多，程序设计就会更复杂：比如 线程间通讯、多线程的数据共享等 

## 多个网络请求完成后执行下一步，有哪些方案：

* 使用GCD的dispatch_group_t

  每次网络请求前先dispatch_group_enter,请求回调后再dispatch_group_leave，enter和leave必须配合使用，有几次enter就要有几次leave，否则group会一直存在。

  当所有enter的block都leave后，会执行dispatch_group_notify的block。

* 使用GCD的信号量dispatch_semaphore_t

  dispatch_semaphore信号量为基于计数器的一种多线程同步机制。如果semaphore计数大于等于1，计数-1，返回，程序继续运行。如果计数为0，则等待。



## Category 的实现原理

* Category 实际上是 Category_t的结构体，在运行时，新添加的方法，都被以倒序插入到原有方法列表的最前面，所以不同的Category，添加了同一个方法，执行的实际上是最后一个。

* Category 在刚刚编译完的时候，和原来的类是分开的，只有在程序运行起来后，通过 Runtime ，Category 和原来的类才会合并到一起



## Objective-C 如何实现多重继承？

Object-c的类没有多继承,只支持单继承,如果要实现多继承的话，可使用如下几种方式间接实现

- 通过组合实现

  A和B组合，作为C类的组件

- 通过协议实现

  C类实现A和B类的协议方法

- 消息转发实现

  forwardInvocation:方法



## 简述下Objective-C中调用方法的过程

Objective-C是动态语言，每个方法在运行时会被动态转为消息发送，即：objc_msgSend(receiver, selector)，整个过程介绍如下：

- objc在向一个对象发送消息时，runtime 会根据对象的isa指针找到该对象实际所属的类
- 然后在该类中的方法列表以及其父类方法列表中寻找方法运行
- 如果，在最顶层的父类（一般也就NSObject）中依然找不到相应的方法时，程序在运行时会挂掉并抛出异常unrecognized selector sent to XXX
- 但是在这之前，objc的运行时会给出三次拯救程序崩溃的机会，这三次拯救程序奔溃的说明见问题《什么时候会报unrecognized selector的异常》中的说明。



## 讲一下 OC 的消息机制

* OC中的方法调用其实都是转成了objc_msgSend函数的调用，给receiver（方法调用者）发送了一条消息（selector方法名）

* objc_msgSend底层有3大阶段：

  * 消息发送：当前类 > 父类中查找方法，查找到方法还会缓存方法到缓存方法列表中

  * 动态方法解析：动态方法解析前还会判定方法是否解析过，如果解析过进入消息转发，如果没有就调用动态方法解析方法，解析后重走`消息发送`流程

  * 消息转发：如果`动态方法`解析阶段没有做处理，就会进入消息转发阶段

    * 在- (id)forwardingTargetForSelector:方法还是没有处理，那接下会来到下面的返回方法签名方法

    * 返回方法签名：- (NSMethodSignature *)methodSignatureForSelector:(SEL)aSelector，返回了正常方法签名后，必须实现- (void)forwardInvocation:方法

    * 方法转发处理：- (void)forwardInvocation:(NSInvocation *)anInvocation

      在这个方法中，我们就可以随意处理了，随意处理即：你可以什么事都不做，只实现这个方法就可以了



## runtime具体应用

* 利用关联对象（AssociatedObject）给分类添加属性

* 遍历类的所有成员变量（修改textfield的占位文字颜色、字典转模型、自动归档解档）

* 交换方法实现（交换系统的方法）

* 利用消息转发机制解决方法找不到的异常问题

* KVC 字典转模型



## load和initialize的区别

两者都会自动调用父类的，不需要super操作，且仅会调用一次（不包括外部显示调用).

- load和initialize方法都会在实例化对象之前调用，以main函数为分水岭，前者在main函数之前调用，后者在之后调用。这两个方法会被自动调用，不能手动调用它们。
- load和initialize方法都不用显示的调用父类的方法而是自动调用，即使子类没有initialize方法也会调用父类的方法，而load方法则不会调用父类。
- load方法通常用来进行Method Swizzle，initialize方法一般用于初始化全局变量或静态变量。
- load和initialize方法内部使用了锁，因此它们是线程安全的。实现时要尽可能保持简单，避免阻塞线程，不要再使用锁。


## RunLoop

1. Runloop 和线程的关系？

   * 一个线程对应一个 Runloop。
   * 主线程的默认就有了 Runloop。
   * 子线程的 Runloop 以懒加载的形式创建。
   * Runloop 存储在一个全局的可变字典里，线程是 key ，Runloop 是 value。

2. RunLoop的运行模式

   RunLoop的运行模式共有5种，RunLoop只会运行在一个模式下，要切换模式，就要暂停当前模式，重写启动一个运行模式

   ```objectivec
   - kCFRunLoopDefaultMode, App的默认运行模式，通常主线程是在这个运行模式下运行
   - UITrackingRunLoopMode, 跟踪用户交互事件（用于 ScrollView 追踪触摸滑动，保证界面滑动时不受其他Mode影响）
   - kCFRunLoopCommonModes, 伪模式，不是一种真正的运行模式
   - UIInitializationRunLoopMode：在刚启动App时第进入的第一个Mode，启动完成后就不再使用
   - GSEventReceiveRunLoopMode：接受系统内部事件，通常用不到
   ```

3. runloop内部逻辑？

   * 实际上 RunLoop 就是这样一个函数，其内部是一个 do-while 循环。当你调用 CFRunLoopRun() 时，线程就会一直停留在这个循环里；直到超时或被手动停止，该函数才会返回。
   * 内部逻辑：
     1. 通知 Observer 已经进入了 RunLoop
     2. 通知 Observer 即将处理 Timer
     3. 通知 Observer 即将处理非基于端口的输入源（即将处理 Source0）
     4. 处理那些准备好的非基于端口的输入源（处理 Source0）
     5. 如果基于端口的输入源准备就绪并等待处理，请立刻处理该事件。转到第 9 步（处理 Source1）
     6. 通知 Observer 线程即将休眠
     7. 将线程置于休眠状态，直到发生以下事件之一
        - 事件到达基于端口的输入源（port-based input sources）(也就是 Source0)
        - Timer 到时间执行
        - 外部手动唤醒
        - 为 RunLoop 设定的时间超时
     8. 通知 Observer 线程刚被唤醒（还没处理事件）
     9. 处理待处理事件
        - 如果是 Timer 事件，处理 Timer 并重新启动循环，跳到第 2 步
        - 如果输入源被触发，处理该事件（文档上是 deliver the event）
        - 如果 RunLoop 被手动唤醒但尚未超时，重新启动循环，跳到第 2 步

4. autoreleasePool 在何时被释放？

   。。。。

5. GCD 在Runloop中的使用？

   - GCD由 子线程 返回到 主线程,只有在这种情况下才会触发 RunLoop。会触发 RunLoop 的 Source 1 事件。

6. AFNetworking 中如何运用 Runloop

   1. AFNetworking 单独创建了一个线程，并在这个线程中启动了一个 RunLoop
   2. RunLoop 启动前内部必须要有至少一个 Timer/Observer/Source，所以 AFNetworking 在 [runLoop run] 之前先创建了一个新的 NSMachPort 添加进去了
   3. 通常情况下，调用者需要持有这个 NSMachPort (mach_port) 并在外部线程通过这个 port 发送消息到 loop 内；但此处添加 port 只是为了让 RunLoop 不至于退出，并没有用于实际的发送消息
   4. 当需要这个后台线程执行任务时，AFNetworking 通过调用 [NSObject performSelector:onThread:..] 将这个任务扔到了后台线程的 RunLoop 中

7. PerformSelector 的实现原理

   - 当调用 NSObject 的 performSelecter:afterDelay: 后，实际上其内部会创建一个 Timer 并添加到当前线程的 RunLoop 中。所以如果当前线程没有 RunLoop，则这个方法会失效。
   - 当调用 performSelector:onThread: 时，实际上其会创建一个 Timer 加到对应的线程去，同样的，如果对应线程没有 RunLoop 该方法也会失效。

8. PerformSelector:afterDelay:这个方法在子线程中是否起作用？

   * 不起作用，子线程默认没有 Runloop，也就没有 Timer。可以使用 GCD的dispatch_after来实现



## 事件响应的过程

* 苹果注册了一个 Source1 (基于 mach port 的) 用来接收系统事件，其回调函数为 __IOHIDEventSystemClientQueueCallback()。

* 当一个硬件事件(触摸/锁屏/摇晃等)发生后，首先由 IOKit.framework 生成一个 IOHIDEvent 事件并由 SpringBoard 接收。SpringBoard 只接收按键(锁屏/静音等)，触摸，加速，接近传感器等几种 Event，随后用 mach port 转发给需要的 App 进程。随后苹果注册的那个 Source1 就会触发回调，并调用 _UIApplicationHandleEventQueue() 进行应用内部的分发。

* _UIApplicationHandleEventQueue() 会把 IOHIDEvent 处理并包装成 UIEvent 进行处理或分发，其中包括识别 UIGesture/处理屏幕旋转/发送给 UIWindow 等。通常事件比如 UIButton 点击、touchesBegin/Move/End/Cancel 事件都是在这个回调中完成的。

## 手势识别的过程？

* 当 _UIApplicationHandleEventQueue() 识别了一个手势时，其首先会调用 Cancel 将当前的 touchesBegin/Move/End 系列回调打断。随后系统将对应的 UIGestureRecognizer 标记为待处理。

* 苹果注册了一个 Observer 监测 BeforeWaiting (Loop即将进入休眠) 事件，这个 Observer 的回调函数是 _UIGestureRecognizerUpdateObserver()，其内部会获取所有刚被标记为待处理的 GestureRecognizer，并执行GestureRecognizer 的回调。

* 当有 UIGestureRecognizer 的变化(创建/销毁/状态改变)时，这个回调都会进行相应处理。



## CADispalyTimer和Timer

CADisplayLink 更精确

* iOS设备的屏幕刷新频率是固定的，CADisplayLink在正常情况下会在每次刷新结束都被调用，精确度相当高。

* NSTimer的精确度就显得低了点，比如NSTimer的触发时间到的时候，runloop如果在阻塞状态，触发时间就会推迟到下一个runloop周期。并且 NSTimer新增了tolerance属性，让用户可以设置可以容忍的触发的时间的延迟范围。

* CADisplayLink使用场合相对专一，适合做UI的不停重绘，比如自定义动画引擎或者视频播放的渲染。NSTimer的使用范围要广泛的多，各种需要单次或者循环定时处理的任务都可以使用。在UI相关的动画或者显示内容使用 CADisplayLink比起用NSTimer的好处就是我们不需要在格外关心屏幕的刷新频率了，因为它本身就是跟屏幕刷新同步的。



## MVC、MVP、MVVM模式

1. MVC

   * MVC是比较直观的架构模式，最核心的就是通过Controller层来进行调控
   * Model和View永远不能相互通信，只能通过Controller传递
   * Controller可以直接与Model对话（读写调用Model），Model通过NOtification和KVO机制与Controller间接通信
   * **优点**：对于混乱的项目组织方式，有了一个明确的组织方式。通过Controller来掌控全局，同时将View展示和Model的变化分开
   * **缺点**：愈发笨重的Controller，随着业务逻辑的增加，大量的代码放进Controller，导致Controller越来越臃肿，堆积成千上万行代码，后期维护起来费时费力

2. MVP

   MVP模式是MVC模式的一个演化版本

   * Model与MVC模式中Model层没有太大区别，主要提供数据存储功能，一般都是用来封装网络获取的json数据；

   * View与MVC中的View层有一些差别，MVP中的View层可以是viewController、view等控件

   * Presenter层则是作为Model和View的中介，从Model层获取数据之后传给View
   * **优点**： 模型和视图完全分离，可以做到修改视图而不影响模型；更高效的使用模型，View不依赖Model，可以说VIew能做到对业务逻辑完全分离
   * **缺点**： Presenter中除了处理业务逻辑以外，还要处理View-Model两层的协调，也会导致Presenter层的臃肿

3. MVVM

   view和ViewCOntroller联系在一起，我们把它们视为一个组件，view和ViewController都不能直接引用model，而是引用是视图模型即ViewModel，viewModel是一个用来放置用户输入验证逻辑、视图显示逻辑、网络请求等业务逻辑的地方

   - **优点**： VIew可以独立于Model的变化和修改，一个ViewModel可以绑定到不同的View上，降低耦合，增加重用
   - **缺点**：过于简单的项目不适用、大型的项目视图状态较多时构建和维护成本太大



## 编程中的六大设计原则

1. 单一职责原则

   通俗地讲就是一个类只做一件事

   CALayer：动画和视图的显示。

   UIView：只负责事件传递、事件响应。

2. 开闭原则

   对修改关闭，对扩展开放。 要考虑到后续的扩展性，而不是在原有的基础上来回修改

3. 接口隔离原则

   使用多个专门的协议、而不是一个庞大臃肿的协议，如 UITableviewDelegate + UITableViewDataSource

4. 依赖倒置原则

   抽象不应该依赖于具体实现、具体实现可以依赖于抽象。 调用接口感觉不到内部是如何操作的

5. 里氏替换原则

   父类可以被子类无缝替换，且原有的功能不受任何影响 如：KVO

6. 迪米特法则

   一个对象应当对其他对象尽可能少的了解，实现高聚合、低耦合



## 如何设计一个图片缓存框架

* 构成：Manager、内存缓存、磁盘缓存、网络下载、Code Manager

* 图片解码：图片的存储是以图片的单向 hash 值为 Key

  * 应用 策略模式，针对 jpg、png、gif 等不同的图片格式进行解码

  * 图片解码的时机

    * 在 子线程 图片刚下载完时

    * 在 子线程 刚从磁盘读取完时

    * 避免在主线程解压缩、解码，避免卡顿

  * 内存设计需要考虑的问题，存储的 Size，因为内存的空间有限，我们针对不同尺寸的图片，给出不同的方案

* 淘汰的策略：内存的淘汰策略 采取 LRU（最近最少使用算法），触发淘汰策略的时机有三种

  1. 定期检查（不建议，耗性能）

  2. 提高检查触发频率（一定要注意开销）

     * 前后台切换的时候
  * 每次读写的时候
  3. 磁盘设计需要考虑的问题：存储方式、大小限制（有固定的大小）、移除策略（可以设置为7天或者15天）

  4. 网络设计需要考虑的问题：图片请求的最大并发量、请求超时策略、请求优先级



  ## 如何计算图片加载内存中所占的大小

图片内存大小的计算公式 宽度 * 高度 * bytesPerPixel/8。

* bytesPerPixel : 每个像素所占的字节数。

* RGBA颜色空间下 每个颜色分量由32位组成

所以一般图片的计算公式是 wxhx4



## 对称加密和非对称加密的区别？

1.  对称加密又称公开密钥加密，加密和解密都会用到同一个密钥，如果密钥被攻击者获得，此时加密就失去了意义。常见的对称加密算法有DES、3DES、AES、Blowfish、IDEA、RC5、RC6。

2. 非对称加密又称共享密钥加密，使用一对非对称的密钥，一把叫做私有密钥，另一把叫做公有密钥；公钥加密只能用私钥来解密，私钥加密只能用公钥来解密。

3. 常见的公钥加密算法有：RSA、ElGamal、背包算法、Rabin（RSA的特例）、迪菲－赫尔曼密钥交换协议中的公钥加密算法、椭圆曲线加密算法）。  



## 简述 SSL 加密的过程用了哪些加密方法，为何这么做？

SSL 加密，在过程中实际使用了 对称加密 和 非对称加密 的结合。主要的考虑是先使用 非对称加密 进行连接，这样做是为了避免中间人攻击秘钥被劫持，但是 非对称加密 的效率比较低。
所以一旦建立了安全的连接之后，就可以使用轻量的 对称加密



## iOS的签名机制是怎么样的

* 签名机制：

  1. 先将应用内容通过摘要算法，得到摘要

  2. 再用私钥对摘要进行加密得到密文

  3. 将源文本、密文、和私钥对应的公钥一并发布

* 验证流程：

  1. 查看公钥是否是私钥方的

  2. 然后用公钥对密文进行解密得到摘要

  3. 将APP用同样的摘要算法得到摘要，两个摘要进行比对，如果相等那么一切正常



## 组件化有什么好处？

- 业务分层、解耦，使代码变得可维护；
- 有效的拆分、组织日益庞大的工程代码，使工程目录变得可维护；
- 便于各业务功能拆分、抽离，实现真正的功能复用；
- 业务隔离，跨团队开发代码控制和版本风险控制的实现；
- 模块化对代码的封装性、合理性都有一定的要求，提升开发同学的设计能力；
- 在维护好各级组件的情况下，随意组合满足不同客户需求；（只需要将之前的多个业务组件模块在新的主App中进行组装即可快速迭代出下一个全新App）

  

## 你是如何组件化解耦的？

- 分层

  * 基础功能组件：按功能分库，不涉及产品业务需求，跟库Library类似，通过良好的接口拱上层业务组件调用；不写入产品定制逻辑，通过扩展接口完成定制；

  * 基础UI组件：各个业务模块依赖使用，但需要保持好定制扩展的设计

  * 业务组件：业务功能间相对独立，相互间没有Model共享的依赖；业务之间的页面调用只能通过UIBus进行跳转；业务之间的逻辑Action调用只能通过服务提供；

- 中间件：target-action，url-block，protocol-class



## 断点调试

- 条件断点

  打上断点之后，对断点进行编辑，设置相应过滤条件。下面简单的介绍一下条件设置：

  Condition：返回一个布尔值，当布尔值为真触发断点，一般里面我们可以写一个表达式。

  Ignore：忽略前N次断点，到N+1次再触发断点。

  Action：断点触发事件，分为六种：

  - AppleScript：执行脚本。
  - Capture GPU Frame：用于OpenGL ES调试，捕获断点处GPU当前绘制帧。
  - Debugger Command：和控制台中输入LLDB调试命令一致。
  - Log Message：输出自定义格式信息至控制台。
  - Shell Command：接收命令文件及相应参数列表，Shell Command是异步执行的，只有勾选“Wait until done”才会等待Shell命令执行完在执行调试。
  - Sound：断点触发时播放声音。

  Options(Automatically continue after evaluating actions选项)：选中后，表示断点不会终止程序的运行。

- 异常断点

  异常断点可以快速定位不满足特定条件的异常，比如常见的数组越界，这时候很难通过异常信息定位到错误所在位置。这个时候异常断点就可以发挥作用了。

  Exception：可以选择抛出异常对象类型：OC或C++。

  Break：选择断点接收的抛出异常来源是Throw还是Catch语句。

- 符号断点

  符号断点的创建方式和异常断点一样一样的，在符号断点中可以指定要中断执行的方法：

  Symbol:[类名 方法名]可以执行到指定类的指定方法中开始断点。



## 造成tableView卡顿的原因有哪些？

- 1.最常用的就是cell的重用， 注册重用标识符

  如果不重用cell时，每当一个cell显示到屏幕上时，就会重新创建一个新的cell

  如果有很多数据的时候，就会堆积很多cell。

  如果重用cell，为cell创建一个ID，每当需要显示cell 的时候，都会先去缓冲池中寻找可循环利用的cell，如果没有再重新创建cell

- 2.避免cell的重新布局

  cell的布局填充等操作 比较耗时，一般创建时就布局好

  如可以将cell单独放到一个自定义类，初始化时就布局好

- 3.提前计算并缓存cell的属性及内容

  当我们创建cell的数据源方法时，编译器并不是先创建cell 再定cell的高度

  而是先根据内容一次确定每一个cell的高度，高度确定后，再创建要显示的cell，滚动时，每当cell进入凭虚都会计算高度，提前估算高度告诉编译器，编译器知道高度后，紧接着就会创建cell，这时再调用高度的具体计算方法，这样可以方式浪费时间去计算显示以外的cell

- 4.减少cell中控件的数量

  尽量使cell得布局大致相同，不同风格的cell可以使用不用的重用标识符，初始化时添加控件，

  不适用的可以先隐藏

- 5.不要使用ClearColor，无背景色，透明度也不要设置为0

  渲染耗时比较长

- 6.使用局部更新

  如果只是更新某组的话，使用reloadSection进行局部更

- 7.加载网络数据，下载图片，使用异步加载，并缓存

- 8.少使用addView 给cell动态添加view

- 9.按需加载cell，cell滚动很快时，只加载范围内的cell

- 10.不要实现无用的代理方法，tableView只遵守两个协议

- 11.缓存行高：estimatedHeightForRow不能和HeightForRow里面的layoutIfNeed同时存在，这两者同时存在才会出现“窜动”的bug。所以我的建议是：只要是固定行高就写预估行高来减少行高调用次数提升性能。如果是动态行高就不要写预估方法了，用一个行高的缓存字典来减少代码的调用次数即可

- 12.不要做多余的绘制工作。在实现drawRect:的时候，它的rect参数就是需要绘制的区域，这个区域之外的不需要进行绘制。例如上例中，就可以用CGRectIntersectsRect、CGRectIntersection或CGRectContainsRect判断是否需要绘制image和text，然后再调用绘制方法。

- 13.预渲染图像。当新的图像出现时，仍然会有短暂的停顿现象。解决的办法就是在bitmap context里先将其画一遍，导出成UIImage对象，然后再绘制到屏幕；

- 14.使用正确的数据结构来存储数据。



## 如何提升 tableview 的流畅度？

- 本质上是降低 CPU、GPU 的工作，从这两个大的方面去提升性能。

  CPU：对象的创建和销毁、对象属性的调整、布局计算、文本的计算和排版、图片的格式转换和解码、图像的绘制

  GPU：纹理的渲染

  

- 卡顿优化在 CPU 层面
   - 尽量用轻量级的对象，比如用不到事件处理的地方，可以考虑使用CALayer取代UIView
   - 不要频繁地调用UIView的相关属性，比如frame、bounds、transform等属性，尽量减少不必要的修改
   - 尽量提前计算好布局，在有需要时一次性调整对应的属性，不要多次修改属性
   - Autolayout会比直接设置frame消耗更多的CPU资源
   - 图片的size最好刚好跟UIImageView的size保持一致
   - 控制一下线程的最大并发数量
   - 尽量把耗时的操作放到子线程:
      - 文本处理（尺寸计算、绘制）
   - 图片处理（解码、绘制）

- 卡顿优化在 GPU层面
  
  - 尽量避免短时间内大量图片的显示，尽可能将多张图片合成一张进行显示
  - GPU能处理的最大纹理尺寸是 4096x4096，一旦超过这个尺寸，就会占用 CPU 资源进行处理，所以纹理尽量不要超过这个尺寸
  - 尽量减少视图数量和层次
  - 减少透明的视图（alpha<1），不透明的就设置 opaque 为 YES尽量避免出现离屏渲染
  
- 离屏渲染:

  - n在OpenGL中，GPU有2种渲染方式:

    * pOn-Screen Rendering：当前屏幕渲染，在当前用于显示的屏幕缓冲区进行渲染操作

    * pOff-Screen Rendering：离屏渲染，在当前屏幕缓冲区以外新开辟一个缓冲区进行渲染操作

  - n离屏渲染消耗性能的原因:

    * p需要创建新的缓冲区

    * p离屏渲染的整个过程，需要多次切换上下文环境，先是从当前屏幕（On-Screen）切换到离屏（Off-Screen）；等到离屏渲染结束以后，将离屏缓冲区的渲染结果显示到屏幕上，又需要将上下文环境从离屏切换到当前屏幕

  - 哪些操作会触发离屏渲染：光栅化、遮罩、圆角、阴影

  

- iOS 保持界面流畅的技巧

  1. 预排版，提前计算

     在接收到服务端返回的数据后，尽量将 CoreText 排版的结果、单个控件的高度、cell 整体的高度提前计算好，将其存储在模型的属性中。需要使用时，直接从模型中往外取，避免了计算的过程。尽量少用 UILabel，可以使用 CALayer 。避免使用 AutoLayout 的自动布局技术，采取纯代码的方式

  2. 预渲染，提前绘制

     例如圆形的图标可以提前在，在接收到网络返回数据时，在后台线程进行处理，直接存储在模型数据里，回到主线程后直接调用就可以了

     避免使用 CALayer 的 Border、corner、shadow、mask 等技术，这些都会触发离屏渲染。

  3. 异步绘制

  4. 全局并发线程

  5. 高效的图片异步加载



## APP启动时间应从哪些方面优化？

App启动时间可以通过xcode提供的工具来度量，在Xcode的Product->Scheme-->Edit Scheme->Run->Auguments中，将环境变量DYLD_PRINT_STATISTICS设为YES，优化需以下方面入手

- dylib loading time

  核心思想是减少dylibs的引用

  合并现有的dylibs（最好是6个以内）

  使用静态库

- rebase/binding time

  核心思想是减少DATA块内的指针

  减少Object C元数据量，减少Objc类数量，减少实例变量和函数（与面向对象设计思想冲突）

  减少c++虚函数

  多使用Swift结构体（推荐使用swift）

- ObjC setup time

  核心思想同上，这部分内容基本上在上一阶段优化过后就不会太过耗时

  initializer time

- 使用initialize替代load方法

  减少使用c/c++的attribute((constructor))；推荐使用dispatch_once() pthread_once() std:once()等方法

  不要在初始化中调用dlopen()方法，因为加载过程是单线程，无锁，如果调用dlopen则会变成多线程，会开启锁的消耗，同时有可能死锁

  不要在初始化中创建线程



## 如何降低APP包的大小

> 安装包（IPA）主要由可执行文件、资源组成

* 资源（图片、音频、视频等）
  * 采取无损压缩
  * 去除没有用到的资源
* 可执行文件瘦身:
  * 编译器优化：Strip Linked Product、Make Strings Read-Only、Symbols Hidden by Default设置为YES；去掉异常支持，Enable C++ Exceptions、Enable Objective-C Exceptions设置为NO， Other C Flags添加-fno-exceptions
* 利用AppCode，检测未使用的代码
* 编写LLVM插件检测出重复代码、未被调用的代码



降低包大小需要从两方面着手

- 可执行文件

  编译器优化：Strip Linked Product、Make Strings Read-Only、Symbols Hidden by Default 设置为 YES，去掉异常支持，Enable C++ Exceptions、Enable Objective-C Exceptions 设置为 NO， Other C Flags 添加 -fno-exceptions 利用 AppCode 检测未使用的代码：菜单栏 -> Code -> Inspect Code

  编写LLVM插件检测出重复代码、未被调用的代码

- 资源（图片、音频、视频 等）

  优化的方式可以对资源进行无损的压缩

  去除没有用到的资源： [https://github.com/tinymind/LSUnusedResources](https://links.jianshu.com/go?to=https%3A%2F%2Fgithub.com%2Ftinymind%2FLSUnusedResources)



## 如何优化 APP 的电量？

- 程序的耗电主要在以下四个方面：

  CPU 处理
  定位
  网络
  图像

- 优化的途径主要体现在以下几个方面：

  尽可能降低 CPU、GPU 的功耗。
  尽量少用 定时器。
  优化 I/O 操作。

  不要频繁写入小数据，而是积攒到一定数量再写入
  读写大量的数据可以使用 Dispatch_io ，GCD 内部已经做了优化。
  数据量比较大时，建议使用数据库

- 网络方面的优化

  减少压缩网络数据 （XML -> JSON -> ProtoBuf），如果可能建议使用 ProtoBuf。
  如果请求的返回数据相同，可以使用 NSCache 进行缓存
  使用断点续传，避免因网络失败后要重新下载。
  网络不可用的时候，不尝试进行网络请求
  长时间的网络请求，要提供可以取消的操作
  采取批量传输。下载视频流的时候，尽量一大块一大块的进行下载，广告可以一次下载多个

- 定位层面的优化

  如果只是需要快速确定用户位置，最好用 CLLocationManager 的 requestLocation 方法。定位完成后，会自动让定位硬件断电
  如果不是导航应用，尽量不要实时更新位置，定位完毕就关掉定位服务
  尽量降低定位精度，比如尽量不要使用精度最高的 kCLLocationAccuracyBest
  需要后台定位时，尽量设置 pausesLocationUpdatesAutomatically 为 YES，如果用户不太可能移动的时候系统会自动暂停位置更新
  尽量不要使用 startMonitoringSignificantLocationChanges，优先考虑 startMonitoringForRegion:

- 硬件检测优化

  用户移动、摇晃、倾斜设备时，会产生动作(motion)事件，这些事件由加速度计、陀螺仪、磁力计等硬件检测。在不需要检测的场合，应该及时关闭这些硬件



## SDWebImage加载图片过程

- 0、首先显示占位图
- 1、在webimagecache中寻找图片对应的缓存，它是以url为数据索引先在内存中查找是否有缓存；
- 2、如果没有缓存，就通过md5处理过的key来在磁盘中查找对应的数据，如果找到就会把磁盘中的数据加到内存中，并显示出来；
- 3、如果内存和磁盘中都没有找到，就会向远程服务器发送请求，开始下载图片；
- 4、下载完的图片加入缓存中，并写入到磁盘中；
- 5、整个获取图片的过程是在子线程中进行，在主线程中显示。



## AFNetworking 底层原理分析

- AFNetworking是封装的NSURLSession的网络请求，由五个模块组成：分别由NSURLSession,Security,Reachability,Serialization,UIKit五部分组成
- NSURLSession：网络通信模块（核心模块） 对应 AFNetworking中的 AFURLSessionManager和对HTTP协议进行特化处理的AFHTTPSessionManager,AFHTTPSessionManager是继承于AFURLSessionmanager的
- Security：网络通讯安全策略模块 对应 AFSecurityPolicy
- Reachability：网络状态监听模块 对应AFNetworkReachabilityManager
- Seriaalization：网络通信信息序列化、反序列化模块 对应 AFURLResponseSerialization
- UIKit：对于iOS UIKit的扩展库



## SVN与Git优缺点比较

1．SVN优缺点

- 优点：

  1、管理方便，逻辑明确，符合一般人思维习惯。

  2、易于管理，集中式服务器更能保证安全性。

  3、代码一致性非常高。

  4、 适合开发人数不多的项目开发。

- 缺点：

  1、服务器压力太大，数据库容量暴增。

  2、如果不能连接到服务器上，基本上不可以工作，看上面第二步，如果服务器不能连接上，就不能提交，还原，对比等等。

  3、不适合开源开发（开发人数非常非常多，但是Google app engine就是用svn的）。但是一般集中式管理的有非常明确的权限管理机制（例如分支访问限制），可以实现分层管理，从而很好的解决开发人数众多的问题。

2．Git优缺点

- 优点：

  1、适合分布式开发，强调个体。

  2、公共服务器压力和数据量都不会太大。

  3、速度快、灵活。

  4、任意两个开发者之间可以很容易的解决冲突。

  5、离线工作。

- 缺点：

  1、学习周期相对而言比较长。

  2、不符合常规思维。

  3、代码保密性差，一旦开发者把整个库克隆下来就可以完全公开所有代码和版本信息。



## atomic 修饰的属性是绝对安全的吗？为什么？

不一定安全，所谓的安全只是局限于 Setter、Getter 的访问器方法而言的，你对它做 Release 的操作是不会受影响的。这个时候就容易崩溃了。



## id 和 instanceType 有什么区别？

- 相同点

  instancetype 和 id 都是万能指针，指向对象。

- 不同点：

  1.id 在编译的时候不能判断对象的真实类型，instancetype 在编译的时候可以判断对象的真实类型。

  2.id 可以用来定义变量，可以作为返回值类型，可以作为形参类型；instancetype 只能作为返回值类型。



## self和super的区别

- self调用自己方法，super调用父类方法

- self是类，super是预编译指令

- [self class] 和 [super class] 输出是一样的

- self和super底层实现原理

  1. 当使用 self 调用方法时，会从当前类的方法列表中开始找，如果没有，就从父类中再找；

  2. 而当使用 super 时，则从父类的方法列表中开始找，然后调用父类的这个方法

  3. 当使用 self 调用时，会使用 objc_msgSend 函数：

		```objectivec
id objc_msgSend(id theReceiver, SEL theSelector, ...)
		```

		第一个参数是消息接收者，第二个参数是调用的具体类方法的 selector，后面是 selector 方法的可变参数。以 [self setName:] 为例，编译器会替换成调用 objc_msgSend 的函数调用，其中 theReceiver 是 self，theSelector 是 @selector(setName:)，这个 selector 是从当前 self 的 class 的方法列表开始找的 setName，当找到后把对应的 selector 传递过去。

  4. 当使用 super 调用时，会使用 objc_msgSendSuper 函数：

		```objectivec
  	id objc_msgSendSuper(struct objc_super *super, SEL op, ...)
		```

		第一个参数是个objc_super的结构体，第二个参数还是类似上面的类方法的selector

		```cpp
		struct objc_super {
    id receiver;
    Class superClass;
		};
		```



## @synthesize和@dynamic分别有什么作用？

- @property有两个对应的词，一个是 @synthesize，一个是 @dynamic。如果 @synthesize和 @dynamic都没写，那么默认的就是@syntheszie var = _var;
- @synthesize 的语义是如果你没有手动实现 setter 方法和 getter 方法，那么编译器会自动为你加上这两个方法。
- @dynamic 告诉编译器：属性的 setter 与 getter 方法由用户自己实现，不自动生成。（当然对于 readonly 的属性只需提供 getter 即可）。

假如一个属性被声明为 @dynamic var，然后你没有提供 @setter方法和 @getter 方法，编译的时候没问题，但是当程序运行到 instance.var = someVar，由于缺 setter 方法会导致程序崩溃；或者当运行到 someVar = var 时，由于缺 getter 方法同样会导致崩溃。编译时没问题，运行时才执行相应的方法，这就是所谓的动态绑定。



## struct和class的区别

- 类： 引用类型（位于栈上面的指针（引用）和位于堆上的实体对象）
- 结构：值类型（实例直接位于栈中）



## 分类、扩展、代理（Delegate）

一、分类

- 1.分类的作用？
  声明私有方法，分解体积大的类文件，把framework的私有方法公开
- 2.分类的特点
  运行时决议，可以为系统类添加分类 。
  说得详细些，在运行时时期，将 Category 中的实例方法列表、协议列表、属性列表添加到主类中后（所以Category中的方法在方法列表中的位置是在主类的同名方法之前的），然后会递归调用所有类的 load 方法，这一切都是在main函数之前执行的。
- 3.分类可以添加哪些内容？
  实例方法，类方法，协议，属性（添加getter和setter方法，并没有实例变量，添加实例变量需要用关联对象）；
- 4.如果工程里有两个分类A和B，两个分类中有一个同名的方法，哪个方法最终生效？
  取决于分类的编译顺序，最后编译的那个分类的同名方法最终生效，而之前的都会被覆盖掉(这里并不是真正的覆盖，因为其余方法仍然存在，只是访问不到，因为在动态添加类的方法的时候是倒序遍历方法列表的，而最后编译的分类的方法会放在方法列表前面，访问的时候就会先被访问到，同理如果声明了一个和原类方法同名的方法，也会覆盖掉原类的方法)。
- 5.如果声明了两个同名的分类会怎样？
  会报错，所以第三方的分类，一般都带有命名前缀
- 6.分类能添加成员变量吗？
  不能，category 是无法添加实例变量的（因为在运行期，对象的内存布局已经确定，如果添加实例变量就会破坏类的内部布局，这对编译型语言来说是灾难性的），只能通过关联对象(objc_setAssociatedObject)来模拟实现成员变量，但其实质是关联内容，所有对象的关联内容都放在同一个全局容器哈希表中:AssociationsHashMap,由AssociationsManager统一管理。
- 在类和 category 中都可以有 `+load`方法，那么有两个问题：
  - 1. 在类的 `+load`方法调用的时候，我们可以调用 category 中声明的方法么？答案是：可以调用，因为附加 category 到类的工作会先于 `+load`方法的执行。
  - 1. 这么些个`+load`方法，调用顺序是咋样的呢？答案是：`+load`的执行顺序是先类，后 category，而 category 的`+load` 执行顺序是根据编译顺序决定的。虽然对于 `+load`的执行顺序是这样，但是对于「覆盖」掉的方法，则会先找到最后一个编译的 category 里的对应方法。

二、扩展

- 一般用扩展做什么？
  声明私有属性，声明方法（没什么意义），声明私有成员变量
- 扩展的特点
  编译时决议，只能以声明的形式存在，多数情况下寄生在宿主类的.m中，不能为系统类添加扩展。
  三、代理（Delegate）

三、代理：是一种设计模式，以@protocol形式体现，一般是一对一传递。
一般以weak关键词以规避循环引用。

