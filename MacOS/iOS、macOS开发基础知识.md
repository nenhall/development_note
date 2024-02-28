# iOS、macOS开发基础知识

## Foundation

### App main 函数执行前的启动流程

1. 在App开始启动后，系统首先加载可执行文件（自身App的所有.o文件的集合）。
2. 然后加载动态链接库dyld，dyld是一个专门用来加载动态链接库的库。
3. 然后dyld会去找到可执行文件当中所依赖的动态库，递归加载所有的依赖的动态库。
4. 由于现在使用的都是虚拟内存(ASLR，地址空间布局随机化），可执行文件和动态链接库在虚拟内
   存中的加载地址每次启动都不固定，所以需要rebase/binding这2步来修复镜像中的资源指针，来指
   向正确的地址。rebase修复的是指向当前镜像内部的资源指针；而bind指向的是镜像外部的资源指
   针。
5. Objc 运行时的初始处理，包括 Objc 相关类的注册、category 注册、selector 唯一性检查，执行
   objc的+load函数，C++的构造函数等等。然后dyld会调起main()函数开始执行。

### 静态库

* 静态库在编译时加载，链接时会完整的复制到可执行文件中。
* 静态库的可执行文件通常会比较大，因为所需的数据都会被整合到目标代码中，因此编译后的执行文件不需要外部库的支持，直接就能使用。
* 有多个app使用就会被复制多份，不能共享且占用更多冗余内存。
* 所有的函数都在库中，因此当修改函数时需要重新编译。
* 静态库导入项目时，Embed 需要设置为 Do not embed

### 动态库

* 动态库在程序运行时由系统动态加载到内存，供程序调用，如果环境缺少动态库或者库的版本不正确，就会导致程序无法运行。
* 动态库的文件会比较小，因为在编译过程中，数据并没有整合到目标代码中，只有在执行到该函数时才去调用库中的函数，所以首次加载时比较耗时。
* 多个程序可以共享内存中同一份库资源，系统只加载一次，多个程序可共用，节省内存空间。
* 库是动态的，因此修改库中函数时，不需要重新编译。

### load() 和 initialize() 的区别

两者都会自动调用父类的，不需要super操作，且仅会调用一次（不包括外部显示调用).

- load和initialize方法都会在实例化对象之前调用，以main函数为分水岭，前者在main函数之前调用，后者在之后调用。这两个方法会被自动调用，不能手动调用它们。
- load和initialize方法都不用显示的调用父类的方法而是自动调用，即使子类没有initialize方法也会调用父类的方法，而load方法则不会调用父类。
- load方法通常用来进行Method Swizzle，initialize方法一般用于初始化全局变量或静态变量。
- load和initialize方法内部使用了锁，因此它们是线程安全的。实现时要尽可能保持简单，避免阻塞线程，不要再使用锁。

### 方法的调用顺序

* Category 并不会覆盖主类的同名方法，只是 Category 的方法排在主类方法的前面。
* OC 的消息发送机制是根据方法名在 method list 中查找方法，找到第一个名字匹配的方法之后就不继续往下找了。
* 每次调用的都是 method_list 中最前面的同名方法。
* 当有多个同名方法时，由文件的编译顺序决定先调哪个，后编译的先调用。

### 原子属性能保障线程安全吗

> 不一定安全，所谓的安全只是局限于 setter、getter 的访问器方法而言的，你对它做 Release 的操作是不会受影响的。这个时候就容易崩溃了。
>
> 原子操作：(atomic operation) 指的是由多步操作组成的一个操作。如果该操作不能原子地执行，则要么执行完所有步骤，要么一步也不执行，不可能只执行所有步骤的一个子集。

原子属性并不能够保障线程安全。atomic 只是对属性的 `getter/setter`方法进行了加锁操作，这种安全仅仅能够保障属性的读\写安全。但是对于整个对象来说不一定是线程安全的。
举个例子：`count ++` 

 这个 count++ 它既有 setter 操作又有 getter 操作，首先得 getter 到 count 的值，将 count 的值减1，然后通过 setter 去给 count 赋值。如果这时出现另外一个线程也在改 count 的值，那么最终的 count 的值就和我们预期的不一样了。所以说原子属性并不能够保障线程安全。

### @synthesize和@dynamic 的作用

- @property有两个对应的词，一个是 @synthesize，一个是 @dynamic。如果 @synthesize和 @dynamic都没写，那么默认的就是@syntheszie var = _var;
- @synthesize 的语义是如果你没有手动实现 setter 方法和 getter 方法，那么编译器会自动为你加上这两个方法。
- @dynamic 告诉编译器：属性的 setter 与 getter 方法由用户自己实现，不自动生成。（当然对于 readonly 的属性只需提供 getter 即可）。

假如一个属性被声明为 @dynamic var，然后你没有提供 @setter方法和 @getter 方法，编译的时候没问题，但是当程序运行到 instance.var = someVar，由于缺 setter 方法会导致程序崩溃；或者当运行到 someVar = var 时，由于缺 getter 方法同样会导致崩溃。编译时没问题，运行时才执行相应的方法，这就是所谓的动态绑定。

### KVC的实现原理

> KVC：键-值编码，使用字符串直接访问对象的属性。

- 当一个对象调用setValue方法时，方法内部会做以下操作：
  1. 检查是否存在相应key的set方法，如果存在，就调用set方法
  2. 如果set方法不存在，就会先顺序查找：setKey > _setKey 方法，如果找到就调用，否则调用`+(B00L)accessInstanceVariablesDirectly`，如果这个函数返回YES，就会按: _key > _isKey > key > isKey 顺序查找对应的成员变量，
  3. 如果找到就赋值，则调用`valueForUndefinedKey：`和`setValue：forUndefinedKey：`方法，如果没有实现这两方法，就抛异常
- 当一个对象调用getValue方法时，方法内部会做以下操作：
  1. 先检查是否有对应的方法，如果没有就按顺序查找：getKey > key > isKey > _key 方法，如果找到就调用，否则调用`+(B00L)accessInstanceVariablesDirectly`，如果这个函数返回YES，就会按: _key > _isKey > key > isKey 顺序查找对应的成员变量。

### KVO的实现原理

#### 原理

- 当给A类添加KVO的时候，runtime动态的生成了一个子类NSKVONotifying_A，让A类的isa指针指向NSKVONotifying_A类，重写class方法，隐藏对象真实类信息
- 重写监听属性的setter方法，在setter方法内部调用了Foundation 的 _NSSetObjectValueAndNotify 函数
- _NSSetObjectValueAndNotify函数内部
  - 首先会调用 willChangeValueForKey
  - 然后给属性赋值
  - 最后调用 didChangeValueForKey
  - 最后调用 observer 的 observeValueForKeyPath 去告诉监听器属性值发生了改变 .
- 重写了dealloc做一些 KVO 内存释放
- 直接修改成员变量不会触发KVO，必须调用set方法才会触发；但通过KVC修改属性也会触发KVO

#### 手动触发

手动触发KVO：KVO通知依赖于NSObject的两个方法：

```swift
// 在一个被观察属性发生改变之前调用:
func willChangeValue(forKey key: String)
/**
两方法中间改变其值
*/
// 当改变发生后调用
func didChangeValue(forKey key: String)
```

手动关闭KVO：在类里面实现下面这个方法，返回 `false`

```swift
override class func automaticallyNotifiesObservers(forKey key: String) -> Bool {
    return false
}
```

### copy 与 strong

#### 对象属性描述 copy 与 strong 的区别

在用 @property 声明 NSString 是经常要使用 copy 而不是 strong 的原因：

1. 父类指针可以指向子类对象（如上代码中 NSMutableString 是 NSString 的子类），使用 copy 的目的是为了对象有更好的封装性，不受外部影响。无论外部传入可变或不可变对象，本身持有一个不可变的副本。
2. 使用 strong，属性可能指向可变对象，如果这个对象被外部更改，则该属性也会受到影。
3. 同样和 NSString 有可变子类的 NSArray，NSDictionary，在声明时也应该使用 copy 描述。

#### 对象的 copy 与 mutablecopy

##### 深拷贝与浅拷贝

1. 浅拷贝只是对指针的拷贝，拷贝后两个指针指向同一个内存空间。
2. 深拷贝不但对指针进行拷贝，而且对指针指向的内容进行拷贝，经深拷贝后的指针是指向两个不同地址的指针。

##### 在非集合类对象中：

1. 对 immutable(不可变) 对象进行 copy 操作，是指针复制(浅拷贝)，mutableCopy 操作时内容复制。
2. 对 mutable(可变) 对象进行 copy 和 mutableCopy 都是内容复制(深拷贝)。

##### 集合对象中：

1. 对 immutable(不可变) 对象进行 copy 操作，是指针复制，mutableCopy 操作时单层内容复制，但需要强调的是：此处的内容拷贝，仅仅是拷贝 array 这个对象，array 集合内部的元素仍然是指针拷贝。

2. 对 mutable（可变） 对象进行 copy 和 mutableCopy 都是内容复制，仅仅是拷贝 array 这个对象，array 集合内部的元素仍然是指针拷贝。

3. copy 方法返回的对象是不可变对象。

4. 集合对象的完全复制的方法：

   ```objective-c
   NSMutableArray *array1 = [NSMutableArray arrayWithObjects:@"a",@"b",@"c", nil];
       NSArray *array2 = [[NSArray alloc] initWithArray:array1 copyItems:YES];
       NSLog(@"%p %p",array1,array2);
       NSLog(@"%p %p",array1[0],array2[0]);
   // 或者先将集合进行归档，然后再解档，这样也可实现集合对象完全拷贝
   ```

5. copy 拷贝 NSSting 字符串时，拷贝指针，既浅拷贝。

6. copy 拷贝 NSMutableString 字符串时是重新生成一个新对象，即深拷贝。

7. copy 修饰的可变字符串属性类型始终是 NSString，而不是 NSMutableString，如果想让拷贝过来的对象是可变的，就要使用mutableCopy。(所有 copy 修饰的 NSSting 字符串不会被外界影响)。

8. strong 表示强指向，对可变和不可变字符串都只有浅拷贝。对 NSMutableString 不存在深拷贝。

#### 为什么block要用copy修饰？

因为 block 在创建的时候，它的内存是分配在栈区，而不是在堆区。栈区的特点是：对象随时有可能被销毁，一旦被销毁，在调用的时候，就会造成系统的崩溃。所以我们要使用 copy 把它拷贝到堆区。

#### @property 

##### 本质

@property 的本质是实例变量（ivar）+存取方法（access method ＝ getter + setter）,即 @property = ivar + getter + setter。

##### ivar、getter、setter 是如何生成并添加到这个类中的

ivar、getter、setter 是自动合成这个类中的，完成属性定义后，编译器会自动编写访问这些属性所需的方法，此过程叫做“自动合成” (autosynthesis)。需要强调的是，这个过程由编译 器在编译期执行，除了生成方法代码 getter、setter 之外，编译器还要自动向类中添加适当类型的实例变量，并且在属性名前面加下划线，以此作为实例变量的名字。

### 堆、栈和队列

#### 堆

- 堆是一种经过排序的树形数据结构，每个节点都有一个值，通常我们所说的堆的数据结构是指二叉树
- 堆常用来实现优先队列，堆的存取是随意的，这就如同我们在图书馆的书架上取书，虽然书的摆放是有顺序的，但是我们想取任意一本时不必像栈一样，先取出前面所有的书，书架这种机制不同于箱子，我们可以直接取出我们想要的书。

#### 栈

- 栈是限定仅在表尾进行插入和删除操作的线性表。我们把允许插入和删除的一端称为栈顶，另一端称为栈底，不含任何数据元素的栈称为空栈。栈的特殊之处在于它限制了这个线性表的插入和删除位置，它始终只在栈顶进行。
- 栈是一种具有后进先出的数据结构，又称为后进先出的线性表，简称 LIFO（Last In First Out）结构。也就是说后存放的先取，先存放的后取，这就类似于我们要在取放在箱子底部的东西（放进去比较早的物体），我们首先要移开压在它上面的物体（放进去比较晚的物体）。
- 栈的应用—递归

#### 队列

队列是一种先进先出的数据结构，又称为先进先出的线性表，简称 FIFO（First In First Out）结构。也就是说先放的先取，后放的后取

### 多线程

#### 并行 和 并发的区别

- 串行和并行描述的是任务和任务之间的执行方式
- 串行：是任务顺序执行的往下执行
- 并行：是任务与任务之间可以同时执行，多核cpu在多个线程同时执行，如果单核cpu就只有并发了
- 并发：在一条线程上通过快速切换任务来回执行
- 线程从开始到结束的状态：新建状态 -> 就绪状态 -> 运行状态 -> 阻塞状态(重复回到就绪状态) -> 死亡状态(正常死亡、非正常死亡)

#### 多线程的几种方案

- pthread：是一种纯 C 语言跨平台的API，提供的多线程方案,使用 pthread 非常麻烦
- NSThread 面向对象的，需要程序员手动创建线程。子线程间通信很难，还得程序员手动管理线程，所以用的不多
- GCD c语言，充分利用了设备的多核，自动管理线程生命周期。比NSOperation效率更高。
- NSOperation 基于gcd封装，更加面向对象，比 GCD 多了一些功能。

**多线程的优缺点：**

1. 优点
   1. 能适当提高程序的执行效率
   2. 能适当提高资源的利用率(CPU、内存利用率)
2. 缺点
   1. 开启线程需要占用一定的内存空间（默认情况下，每一条线程都占用512KB），如果开启大量的线程，会占用大量的内存空间，从而降低程序的性能。
   2. 线程越多，CPU在调度线程上的开销就越大。
   3. 线程越多，程序设计就会更复杂：比如 线程间通讯、多线程的数据共享等

#### 多个网络请求完成后执行下一步，有哪些方案

- GCD的 dispatch_group_t

  每次网络请求前先 dispatch_group_enter，请求回调后再 dispatch_group_leave，enter 和 leave 必须配合使用，有几次 enter 就要有几次 leave，否则 group 会一直存在。

  当所有 enter 的 block 都 leave 后，会执行 dispatch_group_notify 的 block。

- GCD的信号量 dispatch_semaphore_t

  dispatch_semaphore 信号量为基于计数器的一种多线程同步机制。如果 semaphore 计数大于等于1，计数-1，返回，程序继续运行。如果计数为0，则等待。

- NSOperation

### 设计模式

####  代理设计模式

##### 应用场景：通过引入代理对象的方式来间接访问目标对象。在某些情况下，一个对象不适合或者不能直接引用另一个对象，而代理对象可以在客户端和目标对象之间起到中介的作用；防止直接访问目标对象带来的不必要复杂性。

##### 代理包括三部分：代理、委托、协议。

##### 优点：

- 职责清晰：被代理对象只负责自己实际的业务逻辑，不关心其他非本身的职责。并将其他事务可以通过代理类处理。

- 高扩展性：无论被代理对象如何改变，只要代理类和被代理类都实现了统一接口，都不用修改代理类，而且即使扩展了新的被代理类，代理类也可以使用。

- 降低耦合度：能够协调调用者和被调用者，在一定程度上降低了系统的耦合度。

- 保护代理：可以控制对一个对象的某些访问权限，及隐藏委托类的实现。

##### 缺点： 只能一对一。

#### Block 回调

> block是一种轻量级的回调，可以直接访问上下文；

##### 优点：

- block的代码可读性更好，因为block只要实现就可以了
- 由于block的代码是内联的，运行效率更高
- 可以把block当做一个成员变量、属性、参数使用，使用起来非常灵活

##### 缺点：

- blcok的运行成本高。block出栈需要将使用的数据从栈内存拷贝到堆内存
- block容易造成循环引用，而且不易察觉。因为为了blcok不被系统回收，所以我们都用copy关键字修饰，实行强引用。block对捕获的变量也都是强引用，所以就会造成循环引用

##### 代理与Block的选择：

- 如果回调的状态过多、多个消息传递，使用代理
- 如回调的次数很频繁，使用代理
- delegate运行成本低，block成本很高的；delegate只是保存了一个对象指针，没有额外消耗，只多做了一个查表动作(相对C的函数指针)

#### 观察者模式

> 涉及多个对象都对一个特殊对象中的数据变化感兴趣，而且这多个对象都希望跟踪那个特殊对象中的数据变化，在这样的情况下就可以使用观察者模式

##### 优点：观察者和被观察之间是抽象耦合，容易扩展

##### 缺点：

1. 运行效率较低，一个被观察者，多个观察者时，开发代码和调试会比较复杂
2. 通知虽然耦合低但不能被滥用，通知还考虑多线程调用(如果对观察者的通知是通过另外的线程进行异步投递的话，必须保证投递是以自恰的方式进行的)

##### 通知和代理跟的区别

* 通知是观察者模式，适合一对多的场景。
* 代理模式适合一对一的反向传值。
* 通知耦合度低，代理的耦合度高。

#### 单例

> 为了确保程序运行期某个类，只有一份实例，用于进行资源共享控制。可用于跨模块传值

##### 优点：

1. 提供了对唯一实例的受控访问
2. 由于在系统内存中只存在一个对象，因此可以节约系统资源，对于一些需要频繁创建和销毁的对象单例模式无疑可以提高系统的性能

##### 缺点：

1. 由于单利模式中没有抽象层，因此单例类的扩展有很大的困难。
2. 单例类的职责过重，在一定程度上违背了“单一职责原则”。
3. 单例一旦创建，会一直保存在内存当中，直到程序退出时才由系统自动释放这部分的内存，所以过多的单例会增大内存的消耗。
4. 可能会导致共享连接池对象的程序过多而出现连接池溢出；如果实例化的对象长时间不被利用，系统会认为是垃圾而被回收，这将导致对象状态的丢失。

## UIKit

### 离屏渲染

#### 什么是离屏渲染

> 在OpenGL中，GPU有2种渲染方式:
>
> On-Screen Rendering：当前屏幕渲染，在当前用于显示的屏幕缓冲区进行渲染操作
>
> Off-Screen Rendering：离屏渲染，在当前屏幕缓冲区以外新开辟一个缓冲区进行渲染操作

如果要在显示屏上显示内容，我们至少需要一块与屏幕像素数据量一样大的 frame buffer（帧缓冲区），作为像素数据存储区域，然后由显示控制器把帧缓存区的数据显示到屏幕上。如果有时因为面临一些限制，比如说阴影，遮罩 mask 等，GPU无法把渲染结果直接写入 frame buffer，而是先暂把中间的一个临时状态存在另外的内存区域，之后再写入 frame buffer，这个过程被称之为离屏渲染。

#### 离屏渲染消耗性能的原因:

- 需要创建新的缓冲区。
- 离屏渲染的整个过程，需要多次切换上下文环境，先是从当前屏幕（On-Screen）切换到离屏（Off-Screen）；等到离屏渲染结束以后，将离屏缓冲区的渲染结果显示到屏幕上，又需要将上下文环境从离屏切换到当前屏幕。

- 哪些操作会触发离屏渲染：光栅化、遮罩、圆角、阴影。

#### 离屏渲染的影响

需要额外开辟内存，增大了内存的消耗。需要多次切换上下文，从当前屏切换到离屏，从离屏切换到当前屏，上下文的切换是耗时的，可能会引起掉帧。

### 保持界面流畅的技巧

1. 预排版，提前计算

   在接收到服务端返回的数据后，尽量将 CoreText 排版的结果、单个控件的高度、cell 整体的高度提前计算好，将其存储在模型的属性中。需要使用时，直接从模型中往外取，避免了计算的过程。尽量少用 UILabel，可以使用 CALayer 。避免使用 AutoLayout 的自动布局技术，采取纯代码的方式。

2. 预渲染，提前绘制

   例如圆形的图标可以提前在，在接收到网络返回数据时，在后台线程进行处理，直接存储在模型数据里，回到主线程后直接调用就可以了。

   避免使用 CALayer 的 Border、corner、shadow、mask 等技术，这些都会触发离屏渲染。

3. 异步绘制。

4. 全局并发线程。

5. 高效的图片异步加载。

### UITableView 重⽤原理

> 查看`UITableView`头⽂件，会找到`NSMutableArray *visiableCells`，和`NSMutableDictnery *reusableTableCells`两个结构。
>
> `visiableCells`内保存当前显示的cells，`reusableTableCells`保存可重⽤的cells。

#### visiableCells

开始的 Cell 都是通过 `UITableViewCell.initWithStyle: reuseIdentifier:` 来创建，⽽且`cellForRowAtIndexPath`只是调⽤最⼤显示 Cell 数的次数， ⽐如：有100条数据，iPhone⼀屏最多显示10个Cell，那么就会创建 10 次 Cell，并给 Cell 指定重⽤标识，并且 10 个 Cell 全部都加⼊到`visiableCells`数组，`reusableTableCells` 为空。

#### reusableTableCells

tableView 显示之初，`reusableTableCells` 为空，通过上面的方式创建的；

当向下拖动 tableView，当 Cell 1 完全移出屏幕，并且 Cell 11  (它也是alloc出来的，原因同上)完全显示出来的时候。Cell 11 加⼊到 `visiableCells`，Cell 1 移出 `visiableCells`，Cell 1 加⼊到 `reusableTableCells`。

接着向下拖动 tableView，因为 `reusableTableCells` 中已经有值，所以，当需要显示新的 Cell，`cellForRowAtIndexPath` 再次被调⽤的时候，`tableView.dequeueReusableCellWithIdentifier:` ，返回 Cell 1；Cell 1 从`reusableTableCells` 移出，Cell 1 加⼊到 visiableCells。以此类推达到重用的效果，所以我们需要在每次返回的 Cell 进行重新赋值。 

### 提升 tableview 的流畅度

- 本质上是降低 CPU、GPU 的工作，从这两个大的方面去提升性能。

  CPU：对象的创建和销毁、对象属性的调整、布局计算、文本的计算和排版、图片的格式转换和解码、图像的绘制。

  GPU：纹理的渲染。

- 卡顿优化在 CPU 层面

  - 尽量用轻量级的对象，比如用不到事件处理的地方，可以考虑使用 CALayer 取代 UIView。
  - 不要频繁地调用UIView的相关属性，比如 frame、bounds、transform 等属性，尽量减少不必要的修改。
  - 尽量提前计算好布局，在有需要时一次性调整对应的属性，不要多次修改属性。
  - Autolayout 会比直接设置 frame 消耗更多的CPU资源。
  - 图片的size最好刚好跟 UIImageView 的size保持一致。
  - 控制一下线程的最大并发数量。
  - 尽量把耗时的操作放到子线程：文本处理（尺寸计算、绘制）。
  - 图片处理（解码、绘制）。

- 卡顿优化在 GPU层面

  - 尽量避免短时间内大量图片的显示，尽可能将多张图片合成一张进行显示。
  - GPU能处理的最大纹理尺寸是 4096x4096，一旦超过这个尺寸，就会占用 CPU 资源进行处理，所以纹理尽量不要超过这个尺寸。
  - 尽量减少视图数量和层次。
  - 减少透明的视图（alpha<1），不透明的就设置 opaque 为 YES尽量避免出现离屏渲染。

### 事件响应的过程

- 苹果注册了一个 Source1 (基于 mach port 的) 用来接收系统事件，其回调函数为 __IOHIDEventSystemClientQueueCallback()。
- 当一个硬件事件(触摸/锁屏/摇晃等)发生后，首先由 IOKit.framework 生成一个 IOHIDEvent 事件并由 SpringBoard 接收。SpringBoard 只接收按键(锁屏/静音等)，触摸，加速，接近传感器等几种 Event，随后用 mach port 转发给需要的 App 进程。随后苹果注册的那个 Source1 就会触发回调，并调用 _UIApplicationHandleEventQueue() 进行应用内部的分发。
- _UIApplicationHandleEventQueue() 会把 IOHIDEvent 处理并包装成 UIEvent 进行处理或分发，其中包括识别 UIGesture/处理屏幕旋转/发送给 UIWindow 等。通常事件比如 UIButton 点击、touchesBegin/Move/End/Cancel 事件都是在这个回调中完成的。

### 手势识别的过程

- 当 _UIApplicationHandleEventQueue() 识别了一个手势时，其首先会调用 Cancel 将当前的 touchesBegin/Move/End 系列回调打断。随后系统将对应的 UIGestureRecognizer 标记为待处理。
- 苹果注册了一个 Observer 监测 BeforeWaiting (Loop即将进入休眠) 事件，这个 Observer 的回调函数是 _UIGestureRecognizerUpdateObserver()，其内部会获取所有刚被标记为待处理的 GestureRecognizer，并执行GestureRecognizer 的回调。
- 当有 UIGestureRecognizer 的变化(创建/销毁/状态改变)时，这个回调都会进行相应处理。

### CADispalyTimer和Timer

- iOS设备的屏幕刷新频率是固定的，CADisplayLink在正常情况下会在每次刷新结束都被调用，精确度相当高。
- NSTimer的精确度就显得低了点，比如NSTimer的触发时间到的时候，runloop如果在阻塞状态，触发时间就会推迟到下一个runloop周期。并且 NSTimer新增了tolerance属性，让用户可以设置可以容忍的触发的时间的延迟范围。
- CADisplayLink使用场合相对专一，适合做UI的不停重绘，比如自定义动画引擎或者视频播放的渲染。NSTimer的使用范围要广泛的多，各种需要单次或者循环定时处理的任务都可以使用。在UI相关的动画或者显示内容使用 CADisplayLink比起用NSTimer的好处就是我们不需要在格外关心屏幕的刷新频率了，因为它本身就是跟屏幕刷新同步的。

### id 和 instanceType

- 相同点

  instancetype 和 id 都是万能指针，指向对象。

- 不同点：

  1. 在编译的时候不能判断对象的真实类型，instancetype 在编译的时候可以判断对象的真实类型。

  2. 可以用来定义变量，可以作为返回值类型，可以作为形参类型；instancetype 只能作为返回值类型。

### UI 操作与线程

UI 操作为什么要放在主线程：因为 UIKit 的方法不是线程安全的，保证线程安全需要极大的开销。另外即使是在主线程中执行的代码，也很可能不是运行在主队列中。

[参考链接](http://t.zoukankan.com/LiLihongqiang-p-7662739.html)

## NSObject 底层原理

### isa 指针储存的信息

#### 关于 isa 的定义（ arm64 架构）

定义了 union 联合体，使用位域来存储更多的信息，比如定义一个bool值变量需要8个字节，其实仅仅使用这8个字节中的一个位就可以表达是或否的情况了，使用 union 就是充分利用每个字节的每一个位，节约空间

```c
union isa_t {
    Class cls;
    uintptr_t bits;
    struct {
      ISA_BITFIELD  // 这里arm64和x86有所不同
    };
};

#   define ISA_MASK        0x0000000ffffffff8ULL
#   define ISA_MAGIC_MASK  0x000003f000000001ULL
#   define ISA_MAGIC_VALUE 0x000001a000000001ULL
#   define ISA_BITFIELD                                                      \
      uintptr_t nonpointer        : 1;                                       \
      uintptr_t has_assoc         : 1;                                       \
      uintptr_t has_cxx_dtor      : 1;                                       \
      uintptr_t shiftcls          : 33; /*MACH_VM_MAX_ADDRESS 0x1000000000*/ \
      uintptr_t magic             : 6;                                       \
      uintptr_t weakly_referenced : 1;                                       \
      uintptr_t deallocating      : 1;                                       \
      uintptr_t has_sidetable_rc  : 1;                                       \
      uintptr_t extra_rc          : 19
#   define RC_ONE   (1ULL<<45)
#   define RC_HALF  (1ULL<<18)
```

#### 字段释义

 -   `nonpointer`：isa 的第0位，占1位，
     -   0：代表普通的指针，存储着Class、Meta-Class对象的内存地址，
     -   1：代表优化过，使用不同位域存储了不同信息。

-   `has_assoc`：isa 的第1位，占1位，记录这个对象是否是关联对象，没有的话，释放更快。
-   `has_cxx_dtor`：isa 的第2位，占1位，记录是否有c++的析构函数，没有的话，释放更快。
-   `shiftcls`：isa 的第3-35位，占33位，存储着Class、Meta-Class对象的内存地址，这里也解释了`&ISA_MASK`才能取到Class、Meta-Class 对象的内存地址。
-   `magic`：isa 的第36-41位，占6位，用于在调试时分辨对象是否完成初始化。
-   `weakly_referenced`：isa 的第42位，占1位，用于记录该对象是否被弱引用或曾经被弱引用过，没有被弱引用过的对象可以更快释放。
-   `deallocating`：isa 的第43位，占1位，标志对象是否正在释放内存。
-   `has_sidetable_rc`：isa 的第44位，占1位
    -   0：引用计数存储在isa的extra_r所占用位域中
    -   1：引用计数过大无法存储在isa中，引用计数存储在一个叫 SideTable 的类的属性中

-   `extra_rc`：isa 的第45-63位，占19位，表示该对象的引用计数值，实际上是引用计数值减 1（比如引用计数是5的话这里记录的就是4）。这里总共是19位，如果引用计数很大，19位存不下的话就会采用 SideTable 来协助存储，规则如下：当19位存满时，会将19位的一半（也就是上面定义的 RC_HALF ）存入 SideTable 中，如果此时引用计数又+1，那么是加在 extra_rc 上，当 extra_rc 又存满时，继续拿出 RC_HALF 的大小放入 SideTable。当引用计数减少时，如果 extra_rc 的值减少到了0，那就从 SideTable 中取出RC_HALF 大小放入 extra_rc 中。
-   综上所述，引用计数不管是增加还是减少都是在 extra_rc上 进行的，而不会直接去操作 SideTable，这是因为 SideTable 中有个自旋锁，而引用计数的增加和减少操作是非常频繁的，如果直接去操作 sideTable 会非常影响性能，所以这样设计来尽量减少对 SideTable 的访问。

#### isa的指向

1. isa的内存地址就是对象的内存地址
2. 实例对象的isa的值是类对象地址
3. 类对象的isa的值是元类对象地址
4. 元类对象的isa值是基类的元类对象地址，基类的元类对象isa的值是它自己的地址
5. 基类的元类对象的 superclass 是基类的类对象

#### OC的类信息存放在哪里

* 对象方法、属性、成员变量、协议信息，存放在 class 对象；

* 类方法，存放在 meta-class 对象；
* 成员变量的具体值，存放在 instance 对象。

#### 一个 NSObject 对象占用多少内存

系统分配了16个字节给 NSObject 对象（通过malloc_size函数获得），但 NSObject 对象内部只使用了8个字节的空间（64bit环境下，可以通过class_getInstanceSize函数获得)，实际分配了16个是为了内存对齐。

### Category 的实现原理

- Category 实际上是 Category_t的结构体，在运行时，新添加的方法，都被以倒序插入到原有方法列表的最前面，所以不同的Category，添加了同一个方法，执行的实际上是最后一个。
- Category 在刚刚编译完的时候，和原来的类是分开的，只有在程序运行起来后，通过 Runtime ，Category 和原来的类才会合并到一起

### Objective-C 中调用方法的过程

> Objective-C是动态语言，每个方法在运行时会被动态转为消息发送，即：objc_msgSend(receiver, selector)，整个过程介绍如下：

- objc在向一个对象发送消息时，runtime 会根据对象的isa指针找到该对象实际所属的类。
- 然后在该类中的方法列表以及其父类方法列表中寻找方法运行。
- 如果，在最顶层的父类（一般也就NSObject）中依然找不到相应的方法时，程序在运行时会挂掉并抛出异常unrecognized selector sent to xxx。
- 但是在这之前，objc的运行时会给出三次拯救程序崩溃的机会，这三次拯救程序奔溃的说明见问题《什么时候会报unrecognized selector的异常》中的说明。

### RunLoop

#### Runloop 和线程的关系

- 一个线程对应一个 Runloop。
- 主线程的默认就有了 Runloop。
- 子线程的 Runloop 以懒加载的形式创建。
- Runloop 存储在一个全局的可变字典里，线程是 key ，Runloop 是 value。

#### RunLoop的运行模式

RunLoop的运行模式共有5种，RunLoop只会运行在一个模式下，要切换模式，就要暂停当前模式，重写启动一个运行模式。

```
- kCFRunLoopDefaultMode, App的默认运行模式，通常主线程是在这个运行模式下运行
- UITrackingRunLoopMode, 跟踪用户交互事件（用于 ScrollView 追踪触摸滑动，保证界面滑动时不受其他Mode影响）
- kCFRunLoopCommonModes, 伪模式，不是一种真正的运行模式
- UIInitializationRunLoopMode：在刚启动App时第进入的第一个Mode，启动完成后就不再使用
- GSEventReceiveRunLoopMode：接受系统内部事件，通常用不到
```

#### runloop内部逻辑

> 实际上 RunLoop 就是这样一个函数，其内部是一个 do-while 循环。当你调用 CFRunLoopRun() 时，线程就会一直停留在这个循环里；直到超时或被手动停止，该函数才会返回。

1. 通知 Observers 已经进入了 RunLoop；
2. 通知 Observers 即将处理 Timers；
3. 通知 Observers 即将处理 Sources（非基于端口的输入源）；
4. 处理 Blocks；
5. 处理 Sources0（可能会再次处理 Blocks）；
6. 如果存在 Source1 ，就跳转到第8步；
7. 通知 Observers 开始休眠（等待消息唤醒）；
8. 通知 Observers 结束休眠（被某个消息唤醒）：
   1. 处理 Timer
   2. 处理 GCD Async To Main Queue
   3. 处理 Source1 
9. 处理 Blocks；
10. 根据前面执行结果，决定如何操作：
    1. 回到第02步
    2. 退出 Loop
11. 通知 Observers：退出 Loop。

各节点释文：

* Source0：触摸类事件；
* Source1：基于Port的线程间通信、系统事件捕捉。
* Times：NSTimers、performSelector:withObject:afterDelay
* Observers：用于临川RunLoop的状态、刷新UI、Autorelease pool

#### runtime 具体应用

- 利用关联对象（AssociatedObject）给分类添加属性
- 遍历类的所有成员变量（修改textfield的占位文字颜色、字典转模型、自动归档解档）
- 交换方法实现（交换系统的方法）
- 利用消息转发机制解决方法找不到的异常问题
- KVC 字典转模型

#### GCD 在Runloop中的使用

GCD由 子线程 返回到 主线程,只有在这种情况下才会触发 RunLoop。会触发 RunLoop 的 Source 1 事件。

### OC 的消息机制

> OC中的方法调用其实都是转成了objc_msgSend函数的调用，给receiver（方法调用者）发送了一条消息（selector方法名）

#### objc_msgSend 底层三大阶段：

- 消息发送：当前类 > 父类中查找方法，查找到方法还会缓存方法到缓存方法列表中。

- 动态方法解析：动态方法解析前还会判定方法是否解析过，如果解析过进入消息转发，如果没有就调用动态方法解析方法，解析后重走`消息发送`流程。

- 消息转发：如果`动态方法`解析阶段没有做处理，就会进入消息转发阶段

  - 在- (id)forwardingTargetForSelector:方法还是没有处理，那接下会来到下面的返回方法签名方法。

  - 返回方法签名：- (NSMethodSignature *)methodSignatureForSelector:(SEL)aSelector，返回了正常方法签名后，必须实现- (void)forwardInvocation:方法。

  - 方法转发处理：- (void)forwardInvocation:(NSInvocation *)anInvocation

    在这个方法中，我们就可以随意处理了，随意处理即：你可以什么事都不做，只实现这个方法就可以了。

#### PerformSelector

- 当调用 NSObject 的 performSelecter:afterDelay: 后，实际上其内部会创建一个 Timer 并添加到当前线程的 RunLoop 中。所以如果当前线程没有 RunLoop，则这个方法会失效。
- 当调用 performSelector:onThread: 时，实际上其会创建一个 Timer 加到对应的线程去，同样的，如果对应线程没有 RunLoop 该方法也会失效。

`PerformSelector:afterDelay` 这个方法在子线程中是否起作用？

- 不起作用，子线程默认没有 Runloop，也就没有 Timer。可以使用 GCD的dispatch_after来实现

### 简述 dealloc 的实现机制

#### dealloc 调用流程

1. 首先调用 _objc_rootDealloc()
2. 接下来调用 rootDealloc()
3. 这时候会判断是否可以被释放，判断的依据主要有5个，判断是否有以上五种情况
   * NONPointer_ISA：普通的指针 / 优化过的指针
   * has_assoc：是否有c++的析构函数
   * has_cxx_dtor：是否有c++的析构函数
   * weakly_reference ：是否被弱引用或曾经被弱引用过
   * has_sidetable_rc：是否创建了额外的引用计数 SideTable 的类
4. 如果有以上五中任意一种，将会调用 object_dispose()方法，做下一步的处理。如果没有之前五种情况的任意一种，则可以执行释放操作， C 函数的 free()。
5. 执行完毕。

#### object_dispose() 调用流程

1. 直接调用 objc_destructInstance()。
2. 之后调用C 函数的 free()。

#### objc_destructlnstance() 调用流程

1. 先判断 hasCxxDtor， 如果有C++ 的相关内容，要调用 object_cxxDestruct()，销毁 C++ 相关的内容。

2. 再判断 hasAssocitatedObjects， 如果有的话，要调用 object_remove_associations()，销毁关联对象的一系列操作。

3. 然后调用 clear Deallocating()。

4. 执行完毕。

#### clearDeallocating()调用流程

1. 先执行 sideTable_clearDellocating()。
2. 再执行 weak_clear_no_lock，在这一步骤中，会将指向该对象的弱引用指针置为 nil。
3. 接下来执行 table.refcnts.eraser()，从引用计数表中擦除该对象的引用计数。
4. 至此为止，dealloc的执行流程结束。

### block

#### 变量捕获机制

- 访问局部 auto 变量：block内部也会生成一个对应的成员变量，但它是值传递
- 访问局部 static 变量：block内部也会捕获变量，但捕获的不是具体值，而是变量的指针
- 访问全局变量：block内部不会捕获，是在使用时直接访问的
- 访问对象的成员变量：不管是用_age 还是self.age访问，都会捕获self，调用者是谁，self就是谁，访问的时候block内部是直接通过self去访问的，内部不侍捕获age

**为什么局部变量要捕获，而全局变量不用：**

因为局部变量的作用域是函数范围内，出了函数变量就会销毁，但block存在跨函数调用，这种情况下就无法访问对应变量了；而全局变量在任何位置都可以访问，没必须捕获

#### Block 种类

| **block 类型**                             | **环境**                   |
| :----------------------------------------- | :------------------------- |
| **NSGlobalBlock** (_NSConcreteGlobalBlock) | 没有访问auto变量           |
| **NSStackBlock** (_NSConcreteStackBlock)   | 访问了auto变量             |
| **NSMallocBlock** (_NSConcreteMallocBlock) | __NSStackBlock__调用了copy |

| **block 类型**    | **环境**                         | **复制效果** |
| :---------------- | :------------------------------- | :----------- |
| **NSGlobalBlock** | 访问auto变量(数据区域)           | 从栈复制到堆 |
| **NSStackBlock**  | 访问了auto变量(栈区)             | 什么也不做   |
| **NSMallocBlock** | __NSStackBlock__调用了copy(堆区) | 引用计数增加 |

局部block默认是globalBlock，存放在数据区，如果访问了auto变量，block会自动调用copy操作，变成MallocBlock存放在堆区，待block出了作用域后，会自动销毁。

MRC 环境下：局部block默认是globalBlock，存放在数据区，如果访问了auto变量，则变成 stackBlock 存放在栈区，需要手动调用 block copy 操作才会成为 MallocBlock

**栈自动拷贝的情况：**

ARC 环境下如下情况，编译器会根据情况自动将栈上的block复制到堆上：

- block 作为函数返回值时
- 将block赋值给 __strong 指针时
- blcok 作为GCD API 的方法参数时
- block 作为 cocoa API 中方法名含有 usingBlock 的方法参数时

#### __block

- 不能修饰全局变量、静态变量，
- ___block 修饰 auto 变量，编译器会将 __block变量包装成一个结构体，且结构体内就有对应的成员变量，并把__block拷贝到堆区，此时结构体可以对变量进行修改
- _*block只能修饰自动变量，block内访问auto变量时，正常是值拷贝，内部无法修改*_
- 在ARC下，当block使用完后，会自动把刚拷贝的__block进行释放

### weak 内部的实现原理

1. 将弱引用（weak 指针）存储到一个 hash 表内，当前对象的地址值作为 Key，Value 是 weak 指针的地址数组，也叫弱引用表
2. 被 weak 修饰的对象是弱引用，所引用对象的计数器不会+1，在引用对象 dealloc 的时候，会去弱引用表里面取出当前对象的所有弱引用指针进行清除，将其值设置成 nil

### ARC

#### ARC都帮我们做了什么

利用LLVM编译特性 + runtime 运行系统动态的管理我们创建的对象，两者相互协作来完成的

#### @autoreleasePool

1. 数据结构：简单说是双向链表，每张链表头尾相接，有 parent、child 指针，每创建一个池子，会在首部创建一个 哨兵 对象,作为标记，最外层池子的顶端会有一个 next 指针。当链表容量满了，就会在链表的顶端，并指向下一张表。
2. 释放时机：
   1. 如果是直接被 @autoreleasePool{} 包括在大括号内的，那就是执行完大括号就进会释放。
   2. 如果没有被 @autoreleasePool{} 包括，只是在某个函数内，那由 runloop 来控制，可能是在所属的某次 runloop 循环中，runloop 休眠之前之前调用 release。

#### ARC 的 retainCount 怎么存储

> 散列表（引用计数表、weak表）

- SideTables 表在 非嵌入式的64位系统中，有 64张 SideTable 表
- 每一张 SideTable 主要是由三部分组成。自旋锁、引用计数表、弱引用表。
- 全局的 引用计数 之所以不存在同一张表中，是为了避免资源竞争，解决效率的问题。
- 引用计数表 中引入了 分离锁的概念，将一张表分拆成多个部分，对他们分别加锁，可以实现并发操作

### 内存管理之 Swapped Memory

#### iOS内存管理方式

1. Tagged Pointer（小对象）：

   - Tagged Pointer 专门用来存储小的对象，例如 NSNumber 和 NSDate。
   - Tagged Pointer 指针的值不再是地址了，而是真正的值。所以，实际上它不再是一个对象了，它只是一个披着对象皮的普通变量而已。所以，它的内存并不存储在堆中，也不需要 malloc 和 free，在内存读取上有着高效率。

2. NONPOINTER_ISA： （指针中存放与该对象内存相关的信息） 苹果将 isa 设计成了联合体，在 isa 中存储了与该对象相关的一些内存的信息。

3. 内存管理内容如下:

   内存布局、内存管理、数据结构、ARC/MRC、引用计数、弱引用、自动释放池、循环引用

#### Swap Space

> `Linux`、`Macos`等系统有一个`Swap space`的概念，当物理内存紧张时，系统会将`inactive`的`pages`放到Swap Space，Swap Space为磁盘上的某个区域，一般是文件形式，这样能节省出来一部分的物理内存，不过，当我们需要访问已经放到磁盘中的内存时，由于已经不在物理内存中，会引发缺页中断，需要再次从磁盘中重新读取，所以会比直接从内存获取要慢。不过`iOS`系统并没有`Swap Space`。

#### Swapped Size

在iOS里，内存分为两种：

- `clean Memory`： 指的是加载后不会发生变化的内存；
- `dirty Memory`：指的是进程运行时会发生改变的内存；

#### 两者差异：

* `clean Memory`的`page`可以换出，既磁盘中有其对应内容，系统可以在内存紧张时将 clean Memory 的 page 换出，当再次访问时，可以重新从磁盘中读取，我们使用的图片、mapped files、Framework 的数据段常量以及代码段等，这些都是 clean Memory。
* `dirty Memory` 是无法换出的，我们所有堆上的分配等都是属于`dirty Memory`，所以我们一定要尽可能的减少`dirty Memory`的使用
* 从`iOS7`开始，iOS 引入了`Compression`的概念，`Swapped Size`的指标，从 [WWDC 2018 416](https://devstreaming-cdn.apple.com/videos/wwdc/2018/416n2fmzz0fz88f/416/416_ios_memory_deep_dive.pdf) 得知，该指标的含义为`Compression size`，参考：[WWDC 2018 416](https://devstreaming-cdn.apple.com/videos/wwdc/2018/416n2fmzz0fz88f/416/416_ios_memory_deep_dive.pdf)

由于 iOS 不使用 `Swap Space`，因此 dirty Memory 在 iOS 里的代价很大。

dirty Memory 比 clean Memory 更昂贵，由于前者一经使用就必须一直存在。（昂贵指的是性价比，一样容量大小的内存，就造价而言，iOS 上须要花费的成本要比 Mac 上花费的成本要多得多）。

### 数据：ro、rw、rwe 的理解

* ro：数据是只读的，因此它属于`clean Memory`（它是从沙盒读取，数据在编译的时候就已经肯定了）。
* rw：数据是可读可写的，因此它属于`dirty Memory`（数据存放的是运行时动态修改的数据）。
* rwe：因为 rw 存在的那片内存属于 `dirty Memory`，而 `dirty Memory` 在iOS里的代价很大，咱们应该尽量减小 rw 里的数据存在。这时候，引出了 rwe 的概念，它是对 rw 的拓展，目的是为了优化 rw。由于并非每一个类都会在运行时改变属性、方法、协议。而 rwe 会标记处理，针对那些不须要改变内容的数据，就去 ro 读取，那些须要改变内容的就去 rw 读取。

![rorwrwe](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/1ccafefa7fa04797b5843de03dc53fe0~tplv-k3u1fbpfcp-watermark.image)

**Tip：**在类里面，前面申明的属性变量，内存地址是较低的，越往后越高，在方法内部，局部变量是从高地址到低地址的。

### 为什么要设计 metaclass

为了复用消息发送机制。我们调用方法的时候，编译器就会把方法转换为`objc_msgSend`这个函数，其实在底层就是通过`objc_msgSend`的第一个参数：消息的接收者的 isa 指针去找方法对应的实现，如果是实例对象，那通过 isa 就可以找到它的类对象，如果是类对象，那通过 isa 就可以找到它的元类对象。

如果没有元类的话，这个 objc_msgSend 起码还得加一个参数，来判断该方法是类方法还是对象方法，因为类方法和对象方法是可以重名的。而且还得判断，调用这到底是类对象还是实例对象。

通过元类就可以巧妙的解决问题，让各类各司其职，实例对象就干存储属性值的事，类对象存储实例方法列表，元类对象存储类方法列表，这样就不用去判断对象类型和方法类型，从而提升消息发送的效率，并且不同类型的方法走的都是同一套流程。

**扩展**：

>面向对象三特征：**封装、继承、多态**，而如Objective-C这种借鉴Smalltalk的，更注重的是消息传递，是动态响应消息。
>
>消息传递对于面向对象的设计，其实在于给出一种对消息的解决方案。而面向对象优点之一的复用，在这种设计里，更多在于复用解决方案，而不是单纯的类本身

### runtime 如何通过 selector 找到对应的IMP 地址

每一个类对象中都一个方法列表，方法列表中记录着方法的名称，方法实现，以及参数类型，其实 selector 本质 就是方法名称，通过这个方法名称就可以在方法列表中找到对应的方法实现。

### self 与 super

> 简述：self 是类的隐藏参数，指向当前调用方法的这个类的实例。super 本质是一个编译器标示符，和 self 指向的是同一个消息接受者。self 与 super 在调用方法时，在编译时有所不同，self 为 objc_msg Send， super 为 objc_msgSendSuper。objc_msgSend 会优先从当前类中找实现，objc_msgSendSuper 直接从父类中寻找实现。

#### 区别

- self 调用自己方法，super 调用父类方法

- self 是类，super 是预编译指令

- [self class] 和 [super class] 输出是一样的

#### 原理：

  1. 当使用 self 调用方法时，会从当前类的方法列表中开始找，如果没有，就从父类中再找；

  2. 而当使用 super 时，则从父类的方法列表中开始找，然后调用父类的这个方法

  3. 当使用 self 调用时，会使用 objc_msgSend 函数：

     ```c++
     id objc_msgSend(id theReceiver, SEL theSelector, ...)
     ```

     第一个参数是消息接收者，第二个参数是调用的具体类方法的 selector，后面是 selector 方法的可变参数。以 [self setName:] 为例，编译器会替换成调用 objc_msgSend 的函数调用，其中 theReceiver 是 self，theSelector 是 @selector(setName:)，这个 selector 是从当前 self 的 class 的方法列表开始找的 setName，当找到后把对应的 selector 传递过去。

  4. 当使用 super 调用时，会使用 objc_msgSendSuper 函数：

     ```c++
     id objc_msgSendSuper(struct objc_super *super, SEL op, ...)
     ```

     第一个参数是个objc_super的结构体，第二个参数还是类似上面的类方法的selector

     ```c++
     struct objc_super {
     id receiver;
     Class superClass;
     };
     ```

### Objective-C 如何实现多重继承

Object-c的类没有多继承,只支持单继承,如果要实现多继承的话，可使用如下几种方式间接实现

- 通过组合实现：A和B组合，作为C类的组件
- 通过协议实现：C类实现A和B类的协议方法
- 消息转发实现：forwardInvocation:方法

## GCD

### 队列与线程：

在 GCD 中，使用 `dispatch_get_main_queue()` 函数可以获取主队列。调用 `dispatch_sync()` 方法会把任务同步提交到指定的队列。

注意一下队列和线程的区别，他们之间并没有“拥有关系(ownership)”，当我们同步的提交一个任务时，首先会阻塞当前队列，然后等到下一次 runloop 时再在合适的线程中执行 block。

在执行 block 之前，首先会寻找合适的线程来执行block，然后阻塞这个线程，直到 block 执行完毕。寻找线程的规则是: 任何提交到主队列的 block 都会在主线程中执行，在不违背此规则的前提下，文档还告诉我们系统会自动进行优化，尽可能的在当前线程执行 block。

顺便补充一句，GCD 死锁的充分条件是:“向当前队列重复同步提交 block”。从原理来看，死锁的原因是提交的 block 阻塞了队列，而队列阻塞后永远无法执行完 `dispatch_sync()`，可见这里完全和代码所在的线程无关。

另一个例子也可以证明这一点，在主线程中向一个串行队列同步的派发 block，根据上文选择线程的原则，block 将在主线程中执行，但同样不会导致死锁:

```objective-c
NSLog(@"begin");
dispatch_queue_t queue = dispatch_queue_create("com.kt.deadlock", DISPATCH_QUEUE_SERIAL);
dispatch_sync(queue, ^{
    NSLog(@"current thread = %@", [NSThread currentThread]);
});
NSLog(@"end");
// 打印结果：
2022-06-09 00:45:32.287389+0800 测试[2210:66057] begin
2022-06-09 00:45:32.288173+0800 测试[2210:66057] current thread = <_NSMainThread: 0x101007d60>{number = 1, name = main}
2022-06-09 00:45:32.288781+0800 测试[2210:66057] end
```

### 常见问答

* 用一句话描述GCD发生的死锁现象：使用 sync 函数往当前串行队列中添加同步任务，会卡住当前的串行队列 - 产生死锁。
* 有几个线程池：两个。一个是主线程池，另一个是除了主线程池之外的线程池。
* 一个队列最多支持几个线程同时工作：64
* 多个队列，允许最多几个线程同时工作：64个。优先级高的队列获得的可活跃线程数多于优先级低的，但也有例外，低优先级的也能获得少量活跃线程。 参考资料：[iOS刨根问底-深入理解GCD](https://www.cnblogs.com/kenshincui/p/13272517.html)
* dispatch_semaphore_t：
  * 当创建信号量时初始化大于1，可以用来实现多线程并发。
  * 当创建信号量时初始化等于1，退化为锁。信号量锁的效率很高，仅次于OSSpinLock和os_unfair_lock。
* 有几个root队列：12个
  * 分别是：userInteractive、default、unspecified、userInitiated、utility 6个，他们的overcommit版本6个。 支持overcommit的队列在创建队列时无论系统是否有足够的资源都会重新开一个线程。
  * 优先级 userInteractive>default>unspecified>userInitiated>utility>background。
  * 全局队列是root队列。

## 安全/加解密

### 对称加密和非对称加密的区别

1. 对称加密又称公开密钥加密，加密和解密都会用到同一个密钥，如果密钥被攻击者获得，此时加密就失去了意义。常见的对称加密算法有DES、3DES、AES、Blowfish、IDEA、RC5、RC6。
2. 非对称加密又称共享密钥加密，使用一对非对称的密钥，一把叫做私有密钥，另一把叫做公有密钥；公钥加密只能用私钥来解密，私钥加密只能用公钥来解密。
3. 常见的公钥加密算法有：RSA、ElGamal、背包算法、Rabin（RSA的特例）、迪菲－赫尔曼密钥交换协议中的公钥加密算法、椭圆曲线加密算法）。

### 简述 SSL 加密的过程用了哪些加密方法，为何这么做

SSL 加密，在过程中实际使用了 对称加密 和 非对称加密 的结合。主要的考虑是先使用 非对称加密 进行连接，这样做是为了避免中间人攻击秘钥被劫持，但是 非对称加密 的效率比较低。
所以一旦建立了安全的连接之后，就可以使用轻量的 对称加密。

### iOS的签名机制是怎么样的

- 签名机制：
  1. 先将应用内容通过摘要算法，得到摘要。
  2. 再用私钥对摘要进行加密得到密文。
  3. 将源文本、密文、和私钥对应的公钥一并发布。
- 验证流程：
  1. 查看公钥是否是私钥方的。
  2. 然后用公钥对密文进行解密得到摘要。
  3. 将APP用同样的摘要算法得到摘要，两个摘要进行比对，如果相等那么一切正常。

## 网络模块

### 三次握手

**过程**：

1. 第⼀次握⼿： 客户端给服务器发送⼀个 SYN 报⽂。
2. 第⼆次握⼿： 服务器收到 SYN 报⽂之后，会应答⼀个 SYN+ACK 报⽂。
3. 第三次握⼿： 客户端收到 SYN+ACK 报⽂之后，会回应⼀个 ACK 报⽂。
4. 服务器收到 ACK 报⽂之后，三次握⼿建⽴完成。

**目的**：

1. 第⼀次握⼿： 客户端发送⽹络包，服务端收到了。这样服务端就能得出结论：客户端的发送能⼒、服务端的接收能⼒是正常的。

2. 第⼆次握⼿： 服务端发包，客户端收到了。这样客户端就能得出结论：服务端的接收、发送能⼒，客户端的接收、发送能⼒是正常的。不过此时服务器并不能确认客户端的接收能⼒是否正常。

3. 第三次握⼿： 客户端发包，服务端收到了。这样服务端就能得出结论：客户端的接收、发送能⼒正常，服务器⾃⼰的发送、接收能⼒也正常。

   因此，需要三次握⼿才能确认双⽅的接收与发送能⼒是否正常。

4. •如果建立连接只需要2次握手，可能会出现的情况
   口假设client发出的第一个连接请求报文段，因为网络延迟，在连接释放以后的某个时间才到达server
   口本来这是一个早已失效的连接请求，但server收到此失效的请求后，误认为是client再次发出的一个新的连接请求
   口于是server就向client发出确认报文段，同意建立连接
   口如果不采用 “3次握手”，那么只要server发出确认，新的连接就建立了
   口由于现在client并没有真正想连接服务器的意愿，因此不会理睬server的确认，也不会向server发送数据
   口但server却以为新的连接已经建立，并一直等待client发来数据，这样，server的很多资源就白白浪费掉了

**作用**：

1. 确认双⽅的接受能⼒、发送能⼒是否正常。
2. 指定⾃⼰的初始化序列号，为后⾯的可靠传送做准备。
3. 如果是 https 协议的话，三次握⼿这个过程，还会进⾏数字证书的验证以及加密密钥的⽣成到。

### 四次挥手

**过程**：

1.   第⼀次挥⼿： 客户端发送⼀个 FIN 报⽂，报⽂中会指定⼀个序列号。此时客户端处于 CLOSED_WAIT1状态。
2.   第⼆次握⼿： 服务端收到 FIN 之后，会发送 ACK 报⽂，且把客户端的序列号值 + 1 作为 ACK 报⽂的序列号值，表明已经收到客户端的报⽂了，此时服务端处于CLOSE_WAIT2状态。
3.   第三次挥⼿： 如果服务端也想断开连接了，和客户端的第⼀次挥⼿⼀样，发给 FIN 报⽂，且指定⼀个序列号。此时服务端处于 LAST_ACK 的状态。
4.   第四次挥⼿： 客户端收到 FIN 之后，⼀样发送⼀个 ACK 报⽂作为应答，且把服务端的序列号值 + 1 作为⾃⼰ ACK 报⽂的序列号值，此时客户端处于 TIME_WAIT 状态。需要过⼀阵⼦以确保服务端收到⾃⼰的 ACK 报⽂之后才会进⼊ CLOSED 状态
5.   服务端收到 ACK 报⽂之后，就处于关闭连接了，处于 CLOSED 状态。

**作用：**

•如果client发送ACK后马上释放了，然后又因为网络原因，server设有收到client的ACK，server就会重发FIN
口这时可能出现的情况是
@ client没有任何响应，服务器那边会干等，甚至多次重发FIN，浪费资源
② client有个新的应用程序刚好分配了同一个端口号，新的应用程序收到FIN后马上开始执行断开连接的操作，本来
它可能是想跟server建立连接的

### Http 与 Https 的区别

#### 区别

- HTTPS 需要向机构申请 CA 证书，极少免费。
- HTTP 属于明文传输，HTTPS基于 SSL 进行加密传输。
- HTTP 端口号为 80，HTTPS 端口号为 443 。
- HTTPS 是加密传输，有身份验证的环节，更加安全。

#### 安全

- SSL(安全套接层) TLS(传输层安全)。
- 以上两者在传输层之上，对网络连接进行加密处理，保障数据的完整性，更加的安全。

#### 优缺点

**https的优点：**

- 使用https协议可认证用户和服务器，确保数据发送到正确的客户机和服务器
- 可防止数据在传输过程中不被窃取、改变，确保数据的完整性。
- 在搜索引擎中一些关于排名的问题。

**https的缺点：**

- https协议握手阶段比较费时，对网站的响应速度有负面影响，使页面的加载时间延长(打开速度问题完全可以通过CDN加速解决)。
- https协议还会影响缓存，增加数据开销和功耗。
- https协议的加密范围也比较有限，在黑客攻击、拒绝服务攻击、服务器劫持等方案几乎起不到什么作用。
- https连接缓存不如http高效，大流量网站如非必要也不会采用，流量成本太高。

### get 和 post 主要区别

- get请求无消息体，只能携带少量数据。
- post请求有消息体，可以携带大量数据。
- get请求将数据放在url地址中。
- post请求将数据放在消息体中。
- GET方式提交的数据最多只能有1024字节，而POST则没有此限制。

###  TCP 和 UDP的区别

- TCP：面向连接、传输可靠(保证数据正确性,保证数据顺序)、用于传输大量数据(流模式)、速度慢，建立连接需要开销较多(时间，系统资源)。
- UDP：面向非连接、传输不可靠、用于传输少量数据(数据包模式)、速度快。

### Cookie和Session

1. cookie主要是用来记录用户状态，区分用户，状态保存在客户端。cookie功能需要浏览器的支持。如果浏览器不支持cookie（如大部分手机中的浏览器）或者把cookie禁用了，cookie功能就会失效。
2. Session
   - Session是服务器端使用的一种记录客户端状态的机制，使用上比Cookie简单一些，相应的也增加了服务器的存储压力。
   - Session是另一种记录客户状态的机制，不同的是Cookie保存在客户端浏览器中，而Session保存在服务器上。 客户端浏览器访问服务器的时候，服务器把客户端信息以某种形式记录在服务器上。这就是Session。客户端浏览器再次访问时只需要从该Session中查找该客户的状态就可以了。

### 对称加密和非对称加密的区别

1. 对称加密又称公开密钥加密，加密和解密都会用到同一个密钥，如果密钥被攻击者获得，此时加密就失去了意义。常见的对称加密算法有DES、3DES、AES、Blowfish、IDEA、RC5、RC6。
2. 非对称加密又称共享密钥加密，使用一对非对称的密钥，一把叫做私有密钥，另一把叫做公有密钥；公钥加密只能用私钥来解密，私钥加密只能用公钥来解密。
3. 常见的公钥加密算法有：RSA、ElGamal、背包算法、Rabin（RSA的特例）、迪菲－赫尔曼密钥交换协议中的公钥加密算法、椭圆曲线加密算法）。

### 简述 SSL 加密的过程用了哪些加密方法

SSL 加密，在过程中实际使用了 对称加密 和 非对称加密 的结合。主要的考虑是先使用 非对称加密 进行连接，这样做是为了避免中间人攻击秘钥被劫持，但是 非对称加密 的效率比较低。所以一旦建立了安全的连接之后，就可以使用轻量的 对称加密。

### iOS的签名机制

- 签名机制：
  1. 先将应用内容通过摘要算法，得到摘要
  2. 再用私钥对摘要进行加密得到密文
  3. 将源文本、密文、和私钥对应的公钥一并发布
- 验证流程：
  1. 查看公钥是否是私钥方的
  2. 然后用公钥对密文进行解密得到摘要
  3. 将APP用同样的摘要算法得到摘要，两个摘要进行比对，如果相等那么一切正常

### 断点续传

**实现原理**：

> 将一个文件按照一定的规则人为的分割成多个小文件，然后客户端每次只上传一个小文件，服务器接收到上传过来的小文件后根据一定的规则来组合这些小文件。如果在上传过程中出现网络中断等意外情况，下次再次上传时可以从已经上传的部分继续上传，而不是重新上传

**参数解释**：

从 HTTP1.1 协议开始就已经支出获取文件的部分内容，断点续传技术就是利用 HTTP1.1 协议的这个特点在 Header 里添加两个参数来实现的。这两个参数分别是客户端请求时发送的 Range 和服务器返回信息时返回的 `Content-Range - Range`，Range 用于指定第一个字节和最后一个字节的位置，格式如下：

`Range:(unit=first byte pos)-[last byte pos]`

Range 常用的格式有如下几种情况：

Range:bytes=0-1024 ，表示传输的是从开头到第1024字节的内容；

Range:bytes=1025-2048 ，表示传输的是从第1025到2048字节范围的内容；

Range:bytes=-2000 ，表示传输的是最后2000字节的内容；Range:bytes=1024- ，表示传输的是从第1024字节开始到文件结束部分的内容；

Range:bytes=0-0,-1 表示传输的是第一个和最后一个字节 ；

Range:bytes=1024-2048,2049-3096,3097-4096 ，表示传输的是多个字节范围。

Content-Range Content-Range 用于响应带有 Range 的请求。服务器会将 Content-Range 添加在响应的头部，格式如下：

`Content-Range:bytes(unit first byte pos)-[last byte pos]/[entity length]`

Content-Range:bytes 2048-4096/10240：2048-4096 表示当前发送的数据范围， 10240 表示文件总大小。

**Note**：

1. 如果在客户端请求报文头中，对 Range 填入了错误的范围值，服务器会返回 416 状态码。
2. 当使用断点续传的方式上传下载软件时 HTTP 响应头将会变为: HTTP/1.1 206 Partial Content。
3. 校验：当服务器端的文件发生改变时，客户端再次向服务端发送断点续传请求时，数据肯定就会发生错误。这时我们可以利用 Last-Modified 来标识最后的修改时间，这样就可以判断服务器上的文件是否发生改变。
4. 秒传：秒传利文件的MD5，首先将文件的MD5发送个服务器，服务器传输过来的MD5判断服务器上是否存在相同类型的文件，如果存在就将文件复制一份，而不是本地上传。

## 第三方库原理

### SDWebImage加载图片过程

1. 首先显示占位图
2. 在webimagecache中寻找图片对应的缓存，它是以url为数据索引先在内存中查找是否有缓存；
3. 如果没有缓存，就通过md5处理过的key来在磁盘中查找对应的数据，如果找到就会把磁盘中的数据加到内存中，并显示出来；
4. 如果内存和磁盘中都没有找到，就会向远程服务器发送请求，开始下载图片；
5. 下载完的图片加入缓存中，并写入到磁盘中；
6. 整个获取图片的过程是在子线程中进行，在主线程中显示。

### AFNetworking 底层原理分析

- AFNetworking是封装的NSURLSession的网络请求，由五个模块组成：分别由NSURLSession,Security,Reachability,Serialization,UIKit五部分组成
- NSURLSession：网络通信模块（核心模块） 对应 AFNetworking中的 AFURLSessionManager和对HTTP协议进行特化处理的AFHTTPSessionManager,AFHTTPSessionManager是继承于AFURLSessionmanager的
- Security：网络通讯安全策略模块 对应 AFSecurityPolicy
- Reachability：网络状态监听模块 对应AFNetworkReachabilityManager
- Seriaalization：网络通信信息序列化、反序列化模块 对应 AFURLResponseSerialization
- UIKit：对于iOS UIKit的扩展库

## 优化项

### MVC、MVP、MVVM 模式

#### MVC

- MVC 是比较直观的架构模式，最核心的就是通过 Controller 层来进行调控
- Model 和 View 永远不能相互通信，只能通过 Controller 传递
- Controller 可以直接与 Model 对话（读写调用 Model），Model通过 NOtification 和 KVO 机制与 Controller 间接通信
- **优点**：View、Model 之间没有相互绑定，耦合度低，可以重复利用、独立使用。
- **缺点**：随着业务逻辑的增加，大量的代码放进 Controller，导致 Controller 越来越臃肿，后期维护工作量大。

**MVC 变种**：

	* 优点：对 Controller 进行瘦身，将 View 的司令部细节封装起来，外界不知道 View 的内部实现。
	* 缺点：View 依赖于 Model

#### MVP

MVP模式是MVC模式的一个演化版本

- Model与MVC模式中Model层没有太大区别，主要提供数据存储功能，一般都是用来封装网络获取的json数据；
- View与MVC中的View层有一些差别，MVP中的View层可以是viewController、view等控件
- Presenter层则是作为Model和View的中介，从Model层获取数据之后传给View
- **优点**： 模型和视图完全分离，可以做到修改视图而不影响模型；更高效的使用模型，View不依赖Model，可以说VIew能做到对业务逻辑完全分离
- **缺点**： Presenter中除了处理业务逻辑以外，还要处理View-Model两层的协调，也会导致Presenter层的臃肿

#### MVVM

view和ViewCOntroller联系在一起，我们把它们视为一个组件，view和ViewController都不能直接引用model，而是引用是视图模型即ViewModel，viewModel是一个用来放置用户输入验证逻辑、视图显示逻辑、网络请求等业务逻辑的地方

- **优点**： VIew可以独立于Model的变化和修改，一个ViewModel可以绑定到不同的View上，降低耦合，增加重用
- **缺点**：过于简单的项目不适用、大型的项目视图状态较多时构建和维护成本太大

### 六大设计原则

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

### 如何设计一个图片缓存框架

- 构成：Manager、内存缓存、磁盘缓存、网络下载、Code Manager

- 图片解码：图片的存储是以图片的单向 hash 值为 Key

  - 应用 策略模式，针对 jpg、png、gif 等不同的图片格式进行解码
  - 图片解码的时机
    - 在 子线程 图片刚下载完时
    - 在 子线程 刚从磁盘读取完时
    - 避免在主线程解压缩、解码，避免卡顿
  - 内存设计需要考虑的问题，存储的 Size，因为内存的空间有限，我们针对不同尺寸的图片，给出不同的方案

- 淘汰的策略：内存的淘汰策略 采取 LRU（最近最少使用算法），触发淘汰策略的时机有三种

  1. 定期检查（不建议，耗性能）
  2. 提高检查触发频率（一定要注意开销）
     - 前后台切换的时候

  - 每次读写的时候

  1. 磁盘设计需要考虑的问题：存储方式、大小限制（有固定的大小）、移除策略（可以设置为7天或者15天）
  2. 网络设计需要考虑的问题：图片请求的最大并发量、请求超时策略、请求优先级

### 如何计算图片加载内存中所占的大小

> 图片内存大小的计算公式 宽度 * 高度 * bytesPerPixel / 8。

- bytesPerPixel : 每个像素所占的字节数。
- RGBA 颜色空间下 每个颜色分量由32位组成

所以一般图片的计算公式是 wxhx4

### 组件化有什么好处？

- 业务分层、解耦，使代码变得可维护。
- 有效的拆分、组织日益庞大的工程代码，使工程目录变得可维护。
- 便于各业务功能拆分、抽离，实现真正的功能复用。
- 业务隔离，跨团队开发代码控制和版本风险控制的实现。
- 模块化对代码的封装性、合理性都有一定的要求，提升开发同学的设计能力。
- 在维护好各级组件的情况下，随意组合满足不同客户需求；（只需要将之前的多个业务组件模块在新的主App中进行组装即可快速迭代出下一个全新App）。

### 如何组件化解耦

- 分层
  - 基础功能组件：按功能分库，不涉及产品业务需求，跟库Library类似，通过良好的接口拱上层业务组件调用；不写入产品定制逻辑，通过扩展接口完成定制。
  - 基础UI组件：各个业务模块依赖使用，但需要保持好定制扩展的设计。
  - 业务组件：业务功能间相对独立，相互间没有Model共享的依赖；业务之间的页面调用只能通过UIBus进行跳转；业务之间的逻辑Action调用只能通过服务提供。
- 中间件：target-action，url-block，protocol-class。

### 动画性能优化
- 减少非必要 `Layer` 层的数量，图层实际上是由很多小物体组成的特别重量级的对象，太多的图层就会引起CPU的瓶颈；
- 减少复杂的布局就复杂的布局计算；
- 图片不要超过绘制支持的最大尺寸：如果视图绘制超出GPU支持的4096x4096尺寸的纹理，则会降低性能；
- 减少不必要的重绘：
  - 减少非必要的 layer 层的重绘（如`needsDisplay、setNeedsDisplay`的调用）；
  - 由重叠的半透明图层引起重绘，避免每一帧用相同的像素填充多次的发生；
- 防止离屏渲染：比如圆角，图层遮罩，阴影或者是图层光栅化都会强制Core Animation提前渲染图层的离屏绘制，这不意味着你需要避免使用这些效果，但要明白它带来性能的负面影响。

### 如何优化 APP 的电量

- 程序的耗电主要在以下四个方面：

  CPU 处理、定位、网络、图像

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

### 如何降低APP包的大小

> 安装包（IPA）主要由可执行文件、资源组成

- 资源（图片、音频、视频等）
  - 采取无损压缩
  - 去除没有用到的资源
- 可执行文件瘦身:
  - 编译器优化：Strip Linked Product、Make Strings Read-Only、Symbols Hidden by Default设置为YES；去掉异常支持，Enable C++ Exceptions、Enable Objective-C Exceptions设置为NO， Other C Flags添加-fno-exceptions
- 利用AppCode，检测未使用的代码
- 编写LLVM插件检测出重复代码、未被调用的代码

降低包大小需要从两方面着手

- 可执行文件

  编译器优化：Strip Linked Product、Make Strings Read-Only、Symbols Hidden by Default 设置为 YES，去掉异常支持，Enable C++ Exceptions、Enable Objective-C Exceptions 设置为 NO， Other C Flags 添加 -fno-exceptions 利用 AppCode 检测未使用的代码：菜单栏 -> Code -> Inspect Code

  编写LLVM插件检测出重复代码、未被调用的代码

- 资源（图片、音频、视频 等）

  优化的方式可以对资源进行无损的压缩

  去除没有用到的资源： [https://github.com/tinymind/LSUnusedResources](https://links.jianshu.com/go?to=https%3A%2F%2Fgithub.com%2Ftinymind%2FLSUnusedResources)

### App 启动时间优化

#### 启动时间的计算

t(App 总启动时间) = t1 (main 调⽤之前的加载时间) + t2 (main 调⽤之后的加载时间)。

t1 = 系统 dylib(动态链接库)和⾃身 App 可执⾏⽂件的加载。

t2 = main ⽅法执⾏之后到 AppDelegate 类中的 `application:didFinishLaunchingWithOptions:`⽅法执⾏结束前这段时间，主要是构建第⼀个界⾯，并完成渲染展示。

也可通过Xcode来度量：在Xcode的Product->Scheme-->Edit Scheme->Run->Auguments中，将环境变量`DYLD_PRINT_STATISTICS`设为 `YES`，

#### 启动速度优化

##### 在 t1 阶段

dylib loading time：

*  核心思想是减少 dylibs 的引用，动态链接⽐较耗时。
*  如果要⽤动态库，尽量将多个 dylib 动态库合并成⼀个。
*  使用静态库。
*  尽量避免对系统库使⽤ optional linking，如果 App ⽤到的系统库在你所有⽀持的系统版本上都有，就设置为 required，因为 optional 会有些额外的检查。

rebase/binding time：

* 核心思想是减少 DATA 块内的指针。
* 减少Object C元数据量，减少Objc类数量，减少实例变量和函数（与面向对象设计思想冲突）。
* 减少c++虚函数。
* 多使用Swift结构体（推荐使用swift）。

ObjC setup time

* 核心思想同上，这部分内容基本上在上一阶段优化过后就不会太过耗时。

* initializer time：将不必须在 +load 中做的事情尽量挪到`+initialize`中，`+initialize` 是在第⼀次初始化这个类之前被调⽤，`+load` 在加载类的时候就被调⽤；尽量将`+load`⾥的代码延后调⽤。

使用initialize替代load方法

* 减少使用 c/c++的 `attribute((constructor))`；推荐使用 `dispatch_once()、pthread_once()、std:once()` 等方法。
* 不要在初始化中调用dlopen()方法，因为加载过程是单线程，无锁，如果调用dlopen则会变成多线程，会开启锁的消耗，同时有可能死锁。
* 不要在初始化中创建线程。

##### 在 t2 阶段： 

* 尽量不要使⽤ xib/storyboard，⽽是⽤纯代码作为⾸⻚ UI。 

* 如果要⽤ xib/storyboard，不要在 xib/storyboard 中存放太多的视图。

* 对`application:didFinishLaunchingWithOptions:`⾥的任务尽量延迟加载或懒加载。 

* 不要在 `NSUserDefaults` 中存放太多的数据，`NSUserDefaults` 是⼀个 plist ⽂件，plist ⽂件被反序列化⼀次。

* 避免在启动时打印过多的 log； 少⽤ NSLog，因为每⼀次 NSLog 的调⽤都会创建⼀个新的 `NSCalendar` 实例。 

* 不要在主线程执⾏磁盘、⽹络、Lock 或者 `dispatch_sync`、发送消息给其他线程等操作。 

* 为了防⽌使⽤ GCD 创建过多的线程，解决⽅法是创建串⾏队列, 或者使⽤带有最⼤并发数限制的 `NSOperationQueue`。 线程安全：UIKit只能在主线程执⾏，除了 `UIGraphics、UIBezierPath` 之外，`UIImage、CG、CA、Foundation` 都不能从两个线程同时访问。 

* 每⼀段 SQLite 语句都是⼀个段被编译的程序，调⽤ sqlite 3 _prepare 将编译 SQLite 查询到字节码，使⽤ sqlite_bind_int 绑定参数到 SQLite 语句。 

## 算法

### 选择排序、冒泡排序、插入排序三种排序算法总结：

都将数组分为已排序部分和未排序部分。

1. 选择排序将已排序部分定义在左端，然后选择未排序部分的最小元素和未排序部分的第一个元素交换。
2. 冒泡排序将已排序部分定义在右端，在遍历未排序部分的过程执行交换，将最大元素交换到最右端。
3. 插入排序将已排序部分定义在左端，将未排序部分元的第一个元素插入到已排序部分合适的位置。

## 图像和流媒体
### OpenGL

#### 渲染流程

1. 3D 坐标与 2D 的屏幕坐标转换；

2. 设置顶点数据和其他参数；

3. 细分着色器、几何着色器，不可自定义 - 跳过；

4. 图元设置，根据设置构成点、线、三角形；

5. 裁剪，裁剪掉超出显示区域的部分；

6. 光栅化：根据顶点信息对每经过的像素做光栅化, 将图源栅格化为一个个的像素点，参考🔗[什么是光栅化](https://blog.csdn.net/linuxheik/article/details/53884132)；

7. 片元着色器，将对应的栅格(像素)填充为具体的颜色；

8. 渲染图像。

#### 释义：

1. 顶点着色器（Vertex Shader）作为第一个阶段，将单独的顶点作为输入。顶点着色器运行对顶点属性进行一些基本处理；
2. 图元装配（Primitive Assembly）将顶点着色器输出的所有顶点作为输入，并将所有的点装配为指定图元的形状；
3. 几何着色器（Geometry Shader）将图元的顶点集合作为输入，可以通过产生新顶点构造出新的图元来生成其他形状；
4. 光栅格化（Rasterization Stage）将图元映射为最终屏幕上相应的像素，生成供片元着色器使用的片元（Fragment）；在片元着色器运行之前会进行裁剪（Clipping），将超出视图范围外的像素丢弃，以提升效率；
5. 片元着色器（Fragment Shader）用于计算每个像素的最终颜色；
6. 测试和混合（Test and blending）阶段，用来检测片元的深度，判断物体的前后位置关系。

#### 问答：

像素片段该如何着色?

> 最简单的是按照顶点设置的颜色指输出，或者按照绑定的纹理图片和顶点对应的纹理坐标从纹理中采样输出。

如何解决遮挡和颜色混合的问题?

> 这个就是我们测试和混合阶段的任务。测试分为深度测试、模板测试、Alpha测试，遮挡问题就是深度测试解决的。由于3d坐标都有 z 值，代表了我们离摄像机的远近。那么距离越远的深度值将会越高。那么片段的遮挡就是通过深度测试过程中将距离远的片段丢弃绘制深度值小的片段实现遮挡。颜色混合的问题，就是通过我们设置的混合方案，将两个片段根据透明度结合运算出一个混合的颜色。由于颜色混合过程没有深度信息的处理，是通过后渲染的片段去和之前渲染的片段融合，<u>所以为了正常的混合结果，我们渲染的时候会先渲染不透明的物品，然后透明的物体安装由远及近的顺序绘制。</u>

### H.264

#### 编码过程与原理

大体可以归纳为以下几个主要步骤：

- 划分帧类型
- 帧内 / 帧间编码
  - I帧 采用的是帧内（Intra Frame）编码，处理的是空间冗余。
  - P帧、B帧 采用的是帧间（Inter Frame）编码，处理的是时间冗余。
- 变换 + 量化：对残差值进行DCT变换（Discrete Cosine Transform，译为离散余弦变换）。
- 滤波：是将信号中特定波段频率滤除的操作，是抑制和防止干扰的一项重要措施，滤波分为经典滤波和现代滤波。
- 熵编码：它是信息量的度量单位。不要再去想不确定性，抽象的，可认为它是信息的多样性。

#### GOP中的帧类型

* I帧（I Picture、I Frame、Intra Coded Picture），译为：`帧内编码图像`，也叫做关键帧（Keyframe）

  - 是视频的第一帧，也是 GOP 的第一帧，一个 GOP 只有一个 I帧；

  - 编码：对整帧图像数据进行编码；

  - 解码：仅用当前 I帧 的编码数据就可以解码出完整的图像；

  - 是一种自带全部信息的独立帧，无需参考其他图像便可独立进行解码，可以简单理解为一张静态图像。

* P帧（P Picture、P Frame、Predictive Coded Picture），译为：`预测编码图像`

  - 编码：并不会对整帧图像数据进行编码，以前面的 I帧 或 P帧 作为参考帧，只编码当前 P帧 与参考帧的差异数据；

  - 解码：需要先解码出前面的参考帧，再结合差异数据解码出当前 P帧 完整的图像。

* B帧（B Picture、B Frame、Bipredictive Coded Picture），译为：`前后预测编码图像`

  - 编码
    - 并不会对整帧图像数据进行编码；
    - 同时以前面、后面的 I帧 或 P帧 作为参考帧，只编码当前 B帧 与前后参考帧的差异数据；
    - 因为可参考的帧变多了，所以只需要存储更少的差异数据；

  - 解码：需要先解码出前后的参考帧，再结合差异数据解码出当前 B帧 完整的图像。

**编码后的数据大小：I帧 > P帧 > B帧。**

#### GOP的类型

> GOP又可以分为开放（Open）、封闭（Closed）两种.

- Open：前一个GOP的B帧可以参考下一个GOP的I帧；
- Closed：前一个GOP的B帧不能参考下一个GOP的I帧，GOP不能以B帧结尾。

#### YUV

> YUV，是一种颜色编码方法，跟[RGB](https://www.cnblogs.com/mjios/p/14661561.html#toc_title_1)是同一个级别的概念，广泛应用于多媒体领域中。
>
> 也就是说，图像中每1个像素的颜色信息，除了可以用RGB的方式表示，也可以用YUV的方式表示。

1. 对比RGB，YUV有哪些不同和优势

* 体积更小：

  * 使用RGB：比如 RGB888（R、G、B每个分量都是 8bit），1个像素占用 24bit（3字节）；

  * 使用YUV：1个像素可以减小至平均只占用12bit（1.5字节），体积为 RGB888 的一半。

* 组成

  * RGB 数据由 R、G、B 三个分量组成；
  * YUV 数据由 Y、U、V 三个分量组成，现在通常说的 YUV 指的是 YCbCr。

* 兼容性：彩色电视有 Y、U、V 分量，如果去掉 UV 分量，剩下的 Y 分量和黑白电视相同。

[参考链接](https://www.cnblogs.com/mjios/p/14768991.html)

## 常用开发工具特性

### SVN与Git优缺点比较

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