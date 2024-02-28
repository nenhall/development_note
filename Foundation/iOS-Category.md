## iOS-Category与 Class Extension

## Category( 分类 )

### 概念

分类（Category）是OC中的特有语法，它是表示一个指向分类的结构体的指针。原则上它只能增加方法，不能增加成员（实例）变量。

1. 分类中的可以写@property, 但不会生成`setter/getter`方法, 也不会生成实现以及私有的成员变量（编译时会报警告）;
2. 可以在分类中访问原有类中.h中的属性;
3. 如果分类中有和原有类同名的方法, 会优先调用分类中的方法, 就是说会忽略原有类的方法。所以同名方法调用的优先级为 `分类 > 本类 > 父类`。
4. 如果多个分类中都有和原有类中同名的方法, 那么调用该方法的时候执行谁由编译器决定；编译器会执行最后一个参与编译的分类中的方法。

### 分类里面的方法存放
* 对象方法：不管写了多少个分类，分类里面写了多少对象方法，最终(注意)都是存放在唯一的一个类对象里面，调用的时候都是通过instance的isa指针去(class)那里面找。
* 类方法：不管写了多少个分类，分类里面写了多少类方法，最终都是存放在唯一的一个元类(meta-class)里面，调用的时候都是通过class的isa指针去(meta-class)那里面找。

![](https://blogimage-1257063273.cos.ap-guangzhou.myqcloud.com/20180816214104.png)

* 最终通过runtime动态将分类的方法合并到类对象、元类对象中，并不是在编译的时候合并的；

  > 分类在编译完后，其实是转成了如下结构体，把结构体的信息存放在这个结构体下，并申明了一个_category_t的成员变量，并进行赋值：

通过代码进行证明：
```objective-c
#import "Person+Test.h"
@implementation Person (Test)
- (void)run { NSLog(@"Person (Test) - run"); }

- (void)test { NSLog(@"test"); }

+ (void)test2 {  }
@end
```
通过clang将其转c++代码查看，如下图1，可以看到，有一个大的结构体：`_category_t `，大的结构体内又包含了好多结构体，每个结构体里面存放着不同的属性：类基本信息、实例方法、类方法、遵守的协议列表、属性列表。

![](https://blogimage-1257063273.cos.ap-guangzhou.myqcloud.com/20180816221520.png)

再看看结构体赋值，是一一对应赋值的：
如果有多个分类，它生成的结构体名字都是`_category_t `，只不过后面的名字不一样：` _OBJC_$_CATEGORY_Person_$_Test`
![](https://blogimage-1257063273.cos.ap-guangzhou.myqcloud.com/20180816222138.png)



### Category的实现原理：

1. Category编译之后的底层结构是struct category_t，里面存储着分类的对象方法、类方法、属性、协议信息；
2. 在程序运行的时候，runtime会将Category的数据，合并到类信息中(类对象、元类对象)；

### Category和Class Extension的区别

1. Class Extension在编译的时候，它的数据就已经包含在类信息中；
2. Category是在运行时，才会将数据合并到类信息中；

### Category的加载过程

1. 通过Runtime加载某个类的所有Category数据

2. 把所有Category的方法、属性、协议数据，合并到一个大数组中

   * 后面参与编译的Category数据，会在数组的前面

3. 将合并后的分类数据（方法、属性、协议），插入到类原来数据的前面


**源码解读顺序，按以下顺序阅读源码可能更容易懂：**

- objc-os.mm
  - _objc_init
  - map_images
  - map_images_nolock

- objc-runtime-new.mm
  - _read_images

  - remethodizeClass

  - attachCategories

  - attachLists

  - realloc、memmove、 memcpy


### +(void)load为什么里不会覆盖？

* 去runtime的源码里面看，有如下函数：

* 系统会自动先加载类的`+(void)load`方法，然后再加载分类的`+(void)load`方法，而且都是直接通过内存地址直接加载的，与加载正常方法不一样，正常我们执行一个方法是：去类或者元类里面找，找到然后通过**消息机制**进行调用，这里则不是使用**消息机制**。

* `+(void)load`方法是先编译的类，就会先调用，不管是在类还是分类中的`+(void)load`方法；但在分类中的对于我们自己定义的方法，加载顺序则相反：当类和分类都实现了同一个方法时，我们调用对应方法时，执行的是分类里面的方法；因为如前面所说分类的方法会在程序运行时合并到类信息内，越往后加载的分类，分类的信息存放在越前面，调用时依次查找，找到后就不再往后查找。

![](https://blogimage-1257063273.cos.ap-guangzhou.myqcloud.com/20181010213330.png)

* 假如存在继承的时候，会优先调用父类的`+(void)load`方法，而且每个类的`+(void)load`只会调用一次，源码的实现如下：

![](https://blogimage-1257063273.cos.ap-guangzhou.myqcloud.com/20181010215509.png)



### +(void)load方法什么时候调用(总结)：

- runtime加载类、分类的时候调用，即使你还没有初始化那有类，也会调用；
- 每个类、分类的+load方法，在程序运行过程中只调用一次

**调用顺序：**

- 先调用类的+load
  - 按照编译先后顺序调用（先编译，先调用）
  - 调用子类的+load之前会先调用父类的+load

* 再调用分类的+load
  * 按照编译先后顺序调用（先编译，先调用）
* +(void)load方法可以继承，但一般不会这么做，因为这个方法都是由系统主动调用的，我们主动调用时其实就相当于使用了`objc_msgSend()`消息机制，发送了一个消息。

#### objc4源码解读过程：objc-os.mm

- _objc_init

- load_images

- prepare_load_methods
  - schedule_class_load
  - add_class_to_loadable_list
  - add_category_to_loadable_list

- call_load_methods
  - call_class_loads
  - call_category_loads
  - (*load_method)(cls, SEL_load)

- +load方法是根据方法地址直接调用，并不是经过objc_msgSend函数调用


### +(void)initialize与+(void)load方法的区别：

* `+(void)initialize`方法是通过objc_msgSend进行调用的，而`+(void)load`方法是直接对应的方法地址进行调用用的；
* `+(void)initialize`方法会在类第一次接收到消息时调用(如下图一runtime源码)；
* 调用顺序：对应的类存在继承时，会先调用父类`+(void)initialize`方法，再调用子类的`+(void)initialize`方法(如下图二runtime源码)；
* 也正因为`+(void)initialize`方法是通过objc_msgSend进行调用，所以存在以两种情况：

  * 所以如果分类实现了`+(void)initialize`，就会覆盖类本身的`+(void)initialize`方法；
  * 如果子类没有实现`+(void)initialize`方法，会调用父类的`+(void)initialize`方法，所以父类的`+(void)initialize`有可能会被调用多次；

![判定是否初始化](https://blogimage-1257063273.cos.ap-guangzhou.myqcloud.com/20181011214333.png)

![递归调用initialize](https://blogimage-1257063273.cos.ap-guangzhou.myqcloud.com/20181011214956.png)

#### objc4源码解读过程

- objc-msg-arm64.s
  - objc_msgSend

- objc-runtime-new.mm
  - class_getInstanceMethod
  - lookUpImpOrNil
  - lookUpImpOrForward
  - _class_initialize
  - callInitialize
  - objc_msgSend(cls, SEL_initialize)



### 关联对象：

Category无法添加成员变量，但可以通过关联对象的api来间接的实现给对象添加属性；

**原理：**

- 关联对象并不是存储在被关联对象本身内存中
- 关联对象存储在全局的统一的一个AssociationsManager中
- 设置关联对象为nil，就相当于是移除关联对象

- 实现关联对象技术的核心对象有
  - AssociationsManager
  - AssociationsHashMap
  - ObjectAssociationMap
  - ObjcAssociation

- objc4源码解读：objc-references.mm

说明：通过一个`AssociationManager`的类来管理关联的对象，`AssociationManager`里面存放着一个`AssociationsHashMap`对象，里面以类似字典的形式来存放所关联的对象，及关联信息，所关联的对象作为key(disguised_ptr_t)，关联信息为值(ObjectAssociationMap)；`ObjectAssociationMap`又是以字典形式存放对应信息的：`const void *key`作为key，值即包装成了一个`ObjectAssociation`，里面存着：关联策略、关联的值。(如下图)

![](https://blogimage-1257063273.cos.ap-guangzhou.myqcloud.com/20181016203113.png)



## 扩展（Class Extension）

### 概念

Extension是Category的一个特例。类扩展与分类相比只少了分类的名称，所以称之为“匿名分类”。
其实开发当中经常使用，示例代码如下：

```objective-c
@interface Student ()
@property (nonatomic, copy) NSString *name;
@end

@implementation Student
@end
```

### 作用：

* 为一个类添加额外的原来没有变量，方法和属性
* 正常情况类扩展写到`.m`文件中
* 一般私有的属性写到`.m`文件中的类扩展中



## 类别与类扩展的区别：

1. 类别中原则上只能增加方法（能添加属性的的原因只是通过`runtime`解决无`setter/getter`的问题而已）；
2. 类扩展不仅可以增加方法，还可以增加实例变量（或者属性），只是该实例变量默认是@private类型的（用范围只能在自身类，而不是子类或其他地方）；
3. 类扩展中声明的方法没被实现，编译器会报警，但是类别中的方法没被实现编译器是不会有任何警告的。这是因为**类扩展是在编译阶段被添加到类中，而类别是在运行时添加到类中**。
4. 类扩展不能像类别那样拥有独立的实现部分（@implementation部分），也就是说，类扩展所声明的方法必须依托对应类的实现部分来实现。定义在 .m 文件中的类扩展方法为私有的，定义在 .h 文件（头文件）中的类扩展方法为公有的。类扩展是在 .m 文件中声明私有方法的非常好的方式。