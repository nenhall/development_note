## OC对象分类

> OC对象分类：instance对象(实例对象)、class对象(类对象)、meta-class对象(元类对象)；

### instance对象

**定义：**通过类alloc出来的对象，就是instance对象，每次调用alloc都会产生新的instance对象；
**instance对象在内存中存储的信息包括：**

1. isa指针；
2. 其它成员变量；



### class对象

注意：每个类对象在内存中都是唯一的(只有一份)，每个类只有一个元类

获取类对象：[obj class]、object_getClass(obj);

**class对象在内存中存储的信息包括：**

> meta-class对象和class对象的内存结构是一样的，但是用途不一样，在内存中包括的信息主要如下：

1. ias指针；
2. superclass指针；
3. 类的属性信息(@property)、类的对象方法信息(instance method)
4. 类的协议信息(protocol)、类的成员变量信息(ivar)。
5. 类方法也是放在元类里面的；

### meta-class对象

获得元类对象：

```
//将类对象当做参数传入，获得元类对象：
Class metaClass = object_getClass([NSObject class]);
```

判定一个对象是不是元类：

```
class_isMetaClass(obj);
```

**获取类对象、元类对象的三个方法的区别：**

1. object_getClass([NSObject class]);
   1. 传入字符串类名，返因对应的类对象；
2. object_getClass(obj)
   1. 传入的obj可能是instance对象，class对象、meta-class对象；
   2. 返回：
      - 如果传的是instance对象，返回class对象；
      - 如果传的是class对象，返回meta-class对象；
      - 如果传的是meta-class对象，返回的是NSObject(基类)的meta-class对象；
3. -(Class)class 、+(Class)class
   返回的就是类对象

### isa指针：存放那些信息？

1. 实例对象(instance)：

   isa、其它成员变量；

2. 类对象(class)：

   isa、superclass、属性、对象方法、协议、成员变量；

3. meta-class：

   isa、superclass、类方法

**instance的isa指向class(类对象)：**当调用对象方法时，通过instance的isa找到class，最后找到对象的方法实现进行调用；

**class的isa指向meta-class：**当调用类方法时，通过class的isa找到meta-class，最后找到类方法的实现进行调用；**

**meta-class的isa指向基类有meta-class**

**class的superclass指向父类的class，如果没有父类，superclass指针为nil；**

**meta-class的superclass指向的是父类的meta-class**

**注意：**由于NSObject没有superclass，但也不是为空，而是：基类的meta-class对象的superclass指向的是基类的class(类对象)(如下图三)，

![class 对象的 superclass 指针](https://blogimage-1257063273.cos.ap-guangzhou.myqcloud.com/20190306193101.png)

![isa指针](https://blogimage-1257063273.cos.ap-guangzhou.myqcloud.com/20190306192815.png)

![](https://blogimage-1257063273.cos.ap-guangzhou.myqcloud.com/20190306193409.png)

按上面的流程，举个例分析说明：

```objective-c
Student *stu = [[Student alloc] init];
[stu test];//调用对象方法
[stu classTest];//调用类方法
```

> stu调用`test`的调用流程：首先test是对象方法，而对象方法是放在class里面，
> 流程：通过stu通过自己的isa找到他自己的class，在class对象中找test方法，如果有就展开调用,如果没有class通过isa去superclass的里面找(一层层的往上找，直到继承关系走完)，如果还没有，superclass通过自己的isa去meta-class里面找，最后走到NSObject，如果还是没有找到就会报错："unreconized selector sent to instance"
> 注意：如果在调用类方法`classTest`，因找不到方法崩溃之前，其实还有最后一步流程，也是我们一般没注意的地方，在基类的meta-class对象找不到`classTest`方法时，它还会去基类的类对象里面找(即上图右上角转角那个箭头)，如果有则调用，否则才报错。
>
> 可能你觉得很奇怪，meta-class里面存放的是类方法，类里面存放的是对象方法，为什么meta-class类对象里面找不到方法时会找到类的对象方法里面去？具体这个根runtime机制有关，后面有时间我会写runtime相关的问题；

可以进行如下测试：

```objective-c
@implementation NSObject (Test)
- (void)test {
    NSLog(@"-[NSObject test] - %p", self);
}
@end

// Person
@interface Person : NSObject
- (void)test;
@end

@implementation Person
- (void)test {
    NSLog(@"+[Person test] - %p", self);
}
@end

int main(int argc, const char * argv[]) {
    @autoreleasepool {
        NSLog(@"[Person class] - %p", [Person class]);
        NSLog(@"[NSObject class] - %p", [NSObject class]);
        [Person test];
        [NSObject test];
    }
    return 0;
}
```

上面我们在NSObject类扩展中申明和实现了一个实例方法，调用的时候调用的是类方法，但你会神奇的发现不会崩溃，而且调用者分别是Person、NSObject；其实就是我们上面所说到的**在基类的meta-class对象找不到`test`方法时，它还会去基类的类对象里面找**.

### 结论：

#### 对象的isa指针指向哪里？

- **instance对象的isa指向class对象；**
- **class对象的isa指向meta-class对象；**
- **meta-class对象的isa指向基类的meta-class对象**

#### OC的类信息存放在哪里？

- **对象方法、属性、成员变量、协议信息，存放在class对象中；**
- **类方法，存放在meta-class对象中；**
- **成员变量的具体值，存放在instance对象；**

#### 窥探struct objc_class结构体

从objc_runtime源码得到如下结构图：

![](https://blogimage-1257063273.cos.ap-guangzhou.myqcloud.com/20180801223018.png)

已经编译好一个从objc_runtime源码抽出来工程，可以清楚看到objc_class的内部结构：

代码地址：[窥struct objc_class结构体](https://github.com/ruqibazao/struct-objc_class)

https://github.com/nenhall/struct-objc_class)