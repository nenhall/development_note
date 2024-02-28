



## iOS super调用详解

本文分两部份：

1. 主要讲解下 isMemberOfClass 、isKindOfClass 两个方法的内部原理
2. 以一个示例来讲解有关函数调用堆栈的问题

### Objective-C isKindOfClass 详解

#### 实例对象：

先看一则我们经常用到的方法：

```objective-c
 id person = [[Person alloc] init];
 NSLog(@"%d", [person isMemberOfClass:[Person class]]);
 NSLog(@"%d", [person isMemberOfClass:[NSObject class]]);
 
 NSLog(@"%d", [person isKindOfClass:[Person class]]);
 NSLog(@"%d", [person isKindOfClass:[NSObject class]]);
 
 NSLog(@"%d", [Person isKindOfClass:[NSObject class]]);
 
 //上面的打印结果分别是什么？
 //相信这几个大家心中的结果都是对的： 1  1  1  1  1
```

对于上面这则案例相信大家都没什么疑问：

这几个方法在oc底层源代码是开源的，NSObject.mm源代码如下：

```objective-c
// NSObject.mm
- (Class)class {
    return object_getClass(self);
}

- (Class)superclass {
    // class_getSuperclass
    return [self class]->superclass;
}

- (BOOL)isMemberOfClass:(Class)cls {
    return [self class] == cls;
}

- (BOOL)isKindOfClass:(Class)cls {
    for (Class tcls = [self class]; tcls; tcls = tcls->superclass) {
        if (tcls == cls) return YES;
    }
    return NO;
}
```

- class ：拿到对象的isa指针，而实例对象的isa指向的类对象，然后返回类对象；关于对象 isa 的指针问题，我的《NSObject的本看》文章有讲到。
- superclass ： 拿到isa指向的类对象，对象的isa指向的类对象，再通过类对象找到类对象的父类返回(内部调用的是这个上方法:`class_getSuperclass(...)`)
- isMemberOfClass：判定当某个类是不是等于另外一个类： 把传进来的类型判定是否等于当前类型
- isKindOfClass： 判定某个类是不是属于另一个类或者是另一个类的子类，如果传进来的类对象等于自己的类对象，直接返回，内部会拿到`superclass`一层层的往上遍历，直到遍历到NSObject，

#### 类对象

```objective-c
// 下面的打印结果是什么
NSLog(@"%d", [[NSObject class] isKindOfClass:[NSObject class]]);
NSLog(@"%d", [[NSObject class] isMemberOfClass:[NSObject class]]);
NSLog(@"%d", [[Person class] isKindOfClass:[Person class]]);
NSLog(@"%d", [[Person class] isMemberOfClass:[Person class]]);

//实际打印结果：1 0 0 0
```

这里是不是跟你猜想中的不一样呢！下面我们来看看相关类方法源码部份：
```objective-c
// NSObject.mm
+ (Class)class {
    return self;
}
+ (Class)superclass {
    return self->superclass;
}
+ (BOOL)isMemberOfClass:(Class)cls {
    return object_getClass((id)self) == cls;
}
+ (BOOL)isKindOfClass:(Class)cls {
    for (Class tcls = object_getClass((id)self); tcls; tcls = tcls->superclass) {
        if (tcls == cls) return YES;
    }
    return NO;
}
+ (BOOL)isSubclassOfClass:(Class)cls {
    for (Class tcls = self; tcls; tcls = tcls->superclass) {
        if (tcls == cls) return YES;
    }
    return NO;
}
```

* class 、superclass这里就没什么好说的，相信都懂
* isMemberOfClass ：当前 self 是一个类，那类对象的isa指向的是元类对象，也就是这个类方法是在拿元类进行比较的(`object_getClass(self)`得到就是 NSObject类对象) 。
* isKindOfClass：也是拿元类要进行比较的，先判定当前类的元类对象是不是等于传过来的类，如果不是再去元类的父类里面找，判定当前元类的父类是不是等于传进来的类；也正因为如此所以我们在调用这个类方法时，正确的应该是传元类进去；
* isSubclassOfClass：同理

**第一部份总结：**

* 如果是要判定那个实例对象是不是属于某个类，那右边应该传类对象
* 如果是要判定一个类是否属于别一个类的或者子类，那右边应该传元类对象，如下示例：

```objective-c
@interface Person : NSObject
@end

// 错误：
[[Person class] isMemberOfClass:[Person class]];

// 正确：
[Person isMemberOfClass:object_getClass([Person class])];
[Person isKindOfClass:object_getClass([NSObject class])];
// 其实上面这句代码，不管是哪个类调用，只要是继承自NSObject体系下的，都返回yes
```



### 第二部份，函数调用堆栈问题：

如下代码：

1. 请问它能运行吗?
2. 如果能成功运行，那它的打印结果是什么？

```objective-c
@interface ViewController : UIViewController
@end
    
@implementation ViewController
- (void)viewDidLoad {
    [super viewDidLoad];
    
    NSString *test = @"123";
    
    // 如下三句
	id cls = [Person class];
	void *obj = &cls;
	[(__bridge id)obj print];
    
    Person *person = [[Person alloc] init];
    [person print];
}
@end
    
@implementation Person
- (void)print {
    NSLog(@"my name is %@",self.name);
}
@end
    
// 运行结果：成功的调用并且name的打印既然是viewConteroller
// 2019-02-02 15:30:02.728856+0800 demo[10042:321696] my name is 123
```

#### 首先分析为何能运行成功：

1. 整理指针指向关系增加一个person实例对象，如下图：
2. obj 指向 cls ，cls指向`[Person class]`;
3. 同时创建的person，person指向了一个person实例对象；一个oc的实例对象在本质上其实就是一个结构体，里面至少有一个isa指针变量，而isa指针是person实例对象的首地址，所以这个isa指针的地址就是实例对象本身的地址；那 isa 指针变量又指向`[Person class]`，关于NSObject的本质这时不做细说，请参阅我的另一遍文章：《NSObject的本质》
4. 通过上面的分析，也就是说cls 和 person实例对象的isa其实都是指向了同一个对象：`[Person class]`

![](https://blogimage-1257063273.cos.ap-guangzhou.myqcloud.com/20190302154241.png)

结论1：因为obj 刚好指向了cls的前8个字节，而cls的前8个字节指向的就是`[Person class]`，而对象在调用方法时会以前8个字节为准取出isa，isa再`[Person class]`找方法列表或者方法缓存里面找对应实现，所以它能成功调用。

#### 

####它的打印结果为什么是ViewController：

> 其实这个关系到一个函数调用栈内存分配的问题，在解决这个问题之前，需要先来大概的讲下这个问题：

我们把刚对那段代码它的内存栈如下图：

![](https://blogimage-1257063273.cos.ap-guangzhou.myqcloud.com/20190302173445.png)

1. 函数调用栈内存分配都是从高内存地址往底内存地址的：test 在最高位，接下来是cls；

2. 那它是怎么通过cls找到name，并打印的呢？

   1. 我们上面已经知道cls 其实也是一个指向类对象的isa；

   2. 正常访问是通过对象去找一个 _name 的属性，那 _name 这个属性在内存地址中的什么位置呢？

      Person_IMPL 它的底层结构体如下(关于这部份知道还得先去参阅《NSObject的本质》这遍文章)：

      结构体里面有两个成员：isa、name，它访问name的时候，其实是通过访问 Person_IMPL 的内存地址，然后跳过前8个字节(前8个字节固定是isa的地址)，访问到的就是name；

      ```objective-c
      struct Person_IMPL
      {
          Class isa;
          NSString *_name;
      };
      ```

   3. 我们回到上面那个问题：

      ```objective-cobj
      @interface ViewController : UIViewController
      @end
      
      @implementation ViewController
      - (void)viewDidLoad {
          [super viewDidLoad];
          
          NSString *test = @"123";
          
      	id cls = [Person class];
      	void *obj = &cls;
      	[(__bridge id)obj print];
      }
      @end
          
      @implementation Person
      - (void)print {
          NSLog(@"my name is %@",self.name);
      }
      @end
      ```

      这里面打印的是self.name，从本质上讲主是去访问self的_name属性：`self->_name`，那当前的self是谁？就是obj，那也就是obj在访问_name，而obj又指向cls，再回看上面那个内存堆栈图，cls相邻且更高地址存放的是 test ，那obj在访问\_name的时候，跳过cls指针的8个字节，访问相邻更高位的内存地址，那刚好是 test 。

   4. 如果把代码改好如下这样：打印结果又不一样：

      ```objective-c
      @implementation ViewController
      - (void)viewDidLoad {
          [super viewDidLoad];
          
          NSString *first = @"first";
      
          NSString *test = @"123";
          
      	id cls = [Person class];
      	void *obj = &cls;
      	[(__bridge id)obj print];
      }
      @end
      // 打印结果是 frist
      ```

      ```objective-c 
      @implementation ViewController
      - (void)viewDidLoad {
      
      	id cls = [Person class];
      	void *obj = &cls;
      	[(__bridge id)obj print];
      }
      @end
      // 把cls前面的变量全部删除，这种情况下运行会直接崩溃，报访问坏内存地址，因为当跳进cls的isa 8个字节去访问相邻的时候，已经没有对象了，不知道访问到那里去了，所以报访问坏内存地址。
      ```

   5. 我们也可能通过lldb指令来直观性的确认下：

      1. 在obj 调用print的那个地方打个断点；

      2. 在lldb调试终端做如下打印：

         ```
         (lldb) p obj
         (Person *) $0 = 0x00007ffeed4ff9a8
         (lldb) p/x obj
         (Person *) $1 = 0x00007ffeed4ff9a8
         (lldb) x 0x00007ffeed4ff9a8
         0x7ffeed4ff9a8: d8 ff 6f 02 01 00 00 00 00 bc c1 64 83 7f 00 00  ..o........d....
         0x7ffeed4ff9b8: 10 ff 6f 02 01 00 00 00 47 1c e8 06 01 00 00 00  ..o.....G.......
         // x/4g 指令释义：从0x00007ffeed4ff9a8开始打印四个内存地址，至于你要打印几个随你自己
         (lldb) x/4g 0x00007ffeed4ff9a8 
         0x7ffeed4ff9a8: 0x00000001026fffd8 0x00007f8364c1bc00
         0x7ffeed4ff9b8: 0x00000001026fff10 0x0000000106e81c47
         (lldb) p (Class)0x00000001026fffd8
         (Class) $2 = Person
         (lldb) po 0x00007f8364c1bc00
         <ViewController: 0x7f8364c1bc00>
         // 因这是个对象，所以前面要强转，不强转打印结果无法识别
         (lldb) p (Class)0x00007f8364c1bc00  
         (Class) $4 = 0x00007f8364c1bc00
         (lldb) p (Class)0x00000001026fff10
         (Class) $5 = ViewController
         (lldb) 
         ```

**不过我们转成的底层c++代码只是一个种本质的参考，真正底层的汇编肯定还是有差别的**

