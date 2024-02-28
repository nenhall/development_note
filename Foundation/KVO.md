## kvo

Key Value Observe

### 基本使用：

```objective-c
@implementation ViewController
- (void)viewDidLoad {
    [super viewDidLoad];
    
	self.person1 = [[Person alloc] init];
	self.person2 = [[Person alloc] init];

// 给person1对象添加KVO监听
    NSKeyValueObservingOptions options = NSKeyValueObservingOptionNew | NSKeyValueObservingOptionOld;
    [self.person1 addObserver:self forKeyPath:@"age" options:options context:@"123"];
}

// 当监听对象的属性值发生改变时，就会调用
- (void)observeValueForKeyPath:(NSString *)keyPath ofObject:(id)object change:(NSDictionary<NSKeyValueChangeKey,id> *)change context:(void *)context
{
    NSLog(@"监听到%@的%@属性值改变了 - %@ - %@", object, keyPath, change, context);
}
```

### 验证其实现

首先我们使用点语言给对象的属性赋值，其实是调用了对象-set...方法；

> 为了验证其实现过程，我们可以现时创建两个类，并监听其中一个类的值改变，然后同时给两对象属性赋亲值，并在set方法和赋值的地址同时打断点，你会发现当第一个obj赋值完后，第二个对象还没有赋值，但监听回调方法就已经执行，说明系统是在-set方法内部做了一些特殊处理；

进一步验证其实现：

> 这一方面没有源码给我们研究，我们只有通过一些调试来猜测他的实现。

1. 先来从对象的内存地址开始，你想进一步的查看它的相关属性都需要它的内存地址、 isa 指针：分别在监听前和后打印下对象的内存地址：

   ```objective-c
   .......
   //对象的申明和初始化部份的代码我省略了，没有什么特别的，就是正常的申明和初始化实例
   //分别初始化了两上实例对象：person1 和 person2
   .......
       
   NSLog(@"person1添加KVO监听之前 - %@ - %@",
             object_getClass(self.person1),
             object_getClass(self.person2));
             
   [self.person1 addObserver:self forKeyPath:@"age" options:options context:@"123"];
                 
   NSLog(@"person1添加KVO监听之后 - %@ - %@",
             object_getClass(self.person1),
             object_getClass(self.person2));
             
   //打印结果：
   person1添加KVO监听之前 - Person  -  Person
   person1添加KVO监听之后 - NSKVONotifying_Person  -  Person
   ```
   你会发现person1添加监听的，监听后类发生了变化，但这个类并不是我们创建的，可能是系统通过runtime动态创建的.

2. 进一步确认下它的meta-class是谁？

   ```objective-c
   // 给person1对象添加KVO监听
   [self.person1 addObserver:self forKeyPath:@"age" options:options context:@"123"];
       
   NSLog(@"元类对象 - %@ - %@",
          object_getClass(object_getClass(self.person1)), // self.person1.isa.isa
          object_getClass(object_getClass(self.person2))); // self.person2.isa.isa
   
   //打印结果：
   元类对象 - NSKVONotifying_Person  -  Person
   ```

   我之前的《NSObject本质》文中讲过class的isa指向meta-class，`NSKVONotifying_Person`的元类还是它自己创建的。

3. 通过runtime的函数及lldb打印方法的实现：

   ```objective-c
   NSLog(@"person1添加KVO监听之前 - %p %p",
             [self.person1 methodForSelector:@selector(setAge:)],
             [self.person2 methodForSelector:@selector(setAge:)]);
       
   // 给person1对象添加KVO监听
   [self.person1 addObserver:self forKeyPath:@"age" options:options context:@"123"];
       
   NSLog(@"person1添加KVO监听之后 - %p %p",
             [self.person1 methodForSelector:@selector(setAge:)],
             [self.person2 methodForSelector:@selector(setAge:)]);
             
   //这里打个断点
   
   //打印结果：
   person1添加KVO监听之前 - 0x10c6535f0 0x10c6535f0
   person1添加KVO监听之后 - 0x10c9f8f8e 0x10c6535f0
   //通过lldb指使：`p (IMP)对象的IMP的内存地址` 可以打印出其set方法，
   (lldb) p (IMP)0x10c6535f0
   (IMP) $0 = 0x000000010c6535f0 (Interview01`-[MJPerson setAge:] at MJPerson.m:13)
   (lldb) p (IMP)0x10c9f8f8e
   (IMP) $1 = 0x000000010c9f8f8e (Foundation`_NSSetIntValueAndNotify)
   (lldb) 
   ```

   如上分别打印监听前后的IMP的实现，你会发现监听前的set方法还是原来的set方法，但监听后发生了改变，成了`_NSSetIntValueAndNotify`，但我们在第一点的时候确认过，不管监听还是不监听set方法都是一样调了的，但肯定还是在set方法的赋值前后做了什么，现在依然没有确确的结论

4. 我们自己申明一个`NSKVONotifying_Person`类，重写下set方法看会怎么样：

   ```objective-c
   //ViewController中的代码不变，还是我们第一步所展示的代码，
   //只是在工程新创建了一个NSKVONotifying_Person类，
   //这个类什么都不干，创建好参与编译就可以了
   
   @implementation NSKVONotifying_Person
   - (void)setAge:(int)age {
   }
   @end
   //运行后终端直接报错，如下：
   [general] KVO failed to allocate class pair for name NSKVONotifying_Person, automatic key-value observing will not work for this class
   ```

   我们自己创建这样一个类后，直接监听失败，因为系统在创建这个类的时候，发现已经创建过了，所以无法创建；到目前为此还是无法抓到更重要的信息

5. 试试能不能通过逆向的一些工具来查看`Foundation.frame`框架的基本实现：

   * 我这里通过`Hopper Disassembler v4`工具查看，你也可以通过其实的逆向工具，具体的使用请自行百度，或者查看我的逆向相关文章；
   * 从越狱手机上的目录(如下图1)：`device/System/Library/Caches/com.apple.dyld`上复制出`dyld_shared_cache_arm64`库，然后使用电脑的终端通过指令：`./dsc_extractor dyld_shared_cache_arm64 test`(指令说明：./dsc_extractor固定指令 跟上文件名 然后输出目录)，从中抽取出`Foundation.frame`框架出来。
   * 最后将其抽出的`Foundation.frame`可执行文件拖入`Hopper Disassembler v4`工具上，查找`_NSSetIntValueAndNotify()`这个函数，你会发现有很多类似的函数(如下图2)，而且逆向后的可执行文件也可看到有调用一个`_changedValurForKey:key:key:usingBlock`这样的方法，说明它在这个方法里面确实是有调用值改变时的方法；

   ![](https://blogimage-1257063273.cos.ap-guangzhou.myqcloud.com/20180802224529.png)

   ![](https://blogimage-1257063273.cos.ap-guangzhou.myqcloud.com/20180802225509.png)

   * 也可以通过终端命令指令导出符号表，从符号表里面也可能看到：

     ```objective-c
     //Foundation库下所有的跟ValueAndNotif下相关的函数。
     $nm Foundation | grep ValueAndNotify 
     ```

6. 通过遍历其类的所有方法列表，把方法打印出来，也可以判定：

   ```objective-c
   //通过runtime遍历其方法列表
   - (void)printMethodNamesOfClass:(Class)cls {
       unsigned int count;
       // 获得方法数组
       Method *methodList = class_copyMethodList(cls, &count);
       
       // 存储方法名
       NSMutableString *methodNames = [NSMutableString string];
       
       // 遍历所有的方法
       for (int i = 0; i < count; i++) {
           // 获得方法
           Method method = methodList[i];
           // 获得方法名
           NSString *methodName = NSStringFromSelector(method_getName(method));
           // 拼接方法名
           [methodNames appendString:methodName];
           [methodNames appendString:@", "];
       }
       
       // 释放
       free(methodList);
       
       // 打印方法名
       NSLog(@"%@ %@", cls, methodNames);
   }
   
   - (void)viewDidLoad {
       [super viewDidLoad];
       
       // self.person1 是初始后添加了kvo监听的
       // self.person2 是初始后没有添加监听的
       
       [self printMethodNamesOfClass:object_getClass(self.person1)];
       [self printMethodNamesOfClass:object_getClass(self.person2)];
   }
   
   //打印结果：
   2018-08-08 22:17:51.417379+0800 Interview01[1001:40651] NSKVONotifying_Person setAge:, class, dealloc, _isKVOA,
   2018-08-08 22:17:51.417532+0800 Interview01[1001:40651] Person setAge:, age,
   ```

7. 实现结论，及通过上面测试推出的伪代码，如下：

   ```objective-c
   #import "NSKVONotifying_Person.h"
   //继承自我们创建的 `Person`
   @interface NSKVONotifying_Person : Person
   @end
   
   @implementation NSKVONotifying_Person
   
   /// 伪代码
   - (void)setAge:(int)age {
       _NSSetIntValueAndNotify();
   }
   
   void _NSSetIntValueAndNotify() {
       [self willChangeValueForKey:@"age"];
       [super setAge:age];//这个方法内部会触发kvo的代理方法
       [self didChangeValueForKey:@"age"];
   }
   
   - (void)didChangeValueForKey:(NSString *)key {
       // 通知监听器，某某属性值发生了改变
       [oberser observeValueForKeyPath:key ofObject:self change:nil context:nil];
   }
   
   @end
   ```

   

相关面试题：

1. iOS用什么方式实现对一个对象的KVO（KVO的本质）：

   * 利用runtime API动态生成一个子类，并且让instance对象的isa指向这个全新的子类；

   * 当修改instance对象的属性的时候，会调用Foundation的_NSSetxxxValueAndNotify函数；
      ```objective-c
       willChangeValueForKey:
       父类原来的setter
       didChangeVlaueForKey:
       didChangeVlaueForKey:的内部会触发Oberser的监听方法；
      ```

2. 如何手动触发kvo：

   ```objective-c
   //直接手动调用如下方法：
   [obj willChangeValueForKey:@"key"];
    ...
   [obj didChangeVlaueForKey:@"key"];
   ```

3. 直接修改成员变量会触发KVO吗？

   不会，必须调用对应的set方法，才会触发KVO；

**注意：**通过 KVC 修改属性也会触发 KVO 监听，因为 KVC 中setValue:forKey:会按照setKey、_setKey的顺序查找方法