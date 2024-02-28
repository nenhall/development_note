# NSObject的本质

### NSObject的编译过程：

> 1. 我们平时编写的oc代码，底层实现其实都是C/C++代码：
>    Objective-C > C/C++ > 汇编语言 > 机器语言代码
> 2. Objective-C对象主要是基于C/C++的结构体来实现的.

### OC代码转成C++代码：

**我们需要借助转换后的代码来分析**

>  终端命令：`xcrun -sdk iphoneos clang -arch arm64 -rewrite-obj xxx.m -o xxx.cpp`
>  不指定平台与架构:`clang -rewrite-obj xxx.m -o xxx.cpp`
>  iphoneos代表要编译的平台，-arch arm64代表平台架构 xxx代表文件名，-o为输出文件；如果报缺少某个库，加参数：-framework 库名

### NSObject的Implementation：

参考：[苹果源码](https://opensource.apple.com/tarballs) 、[刚才转换出来的xxx.cpp文件](xxx.cpp) 、[GNU源码](http://www.gnu.org/software/libc/)

#### 借助转换后后C++代码来分析，以下转换后的关键代码：

```objective-c
//NSObject 定义:也就是说NSObject其实是一个isa指针，指向Class，
//而Class又指向了一个`objc_class`，`objc_class`就是一个结构体，如下:
@interface NSobject {
    Class isa;
}
@end

//NSObject Implementation
struct NSObject_IMPL {
    Class isa; // isa是一个指针，在64位环境下占8个字节
};
typedef struct objc_class *Class;//`objc_class`就是一个结构体

/// Represents an instance of a class.
struct objc_object {
    Class _Nonnull isa  OBJC_ISA_AVAILABILITY;
};

NSObject *obj = [[NSObject alloc] init];
        
// 获得NSObject实例对象的成员变量所占用的大小 >> 8
NSLog(@"%zd", class_getInstanceSize([NSObject class]));
        
// 获得obj指针所指向内存的大小 >> 16
NSLog(@"%zd", malloc_size((__bridge const void *)obj));
```

### 相关问题一：NSObject占用多少个字节

> 从上面的打印可知，Class isa本身占用8个字节，NSObject *obj占16个字节，为什么占16个呢？

- 进一步分析证明：创建一个继承自`NSObject`带有成员变量的`Student`对象，如下：
  - 猜想：按上面的结论一个`NSObject`实例对象占用16个字节，那下面可能这个`Student`实例对象应该占用4+4+16 = 24；
  - 实际：是占用了16个字节， 因为8+4+4=16，没有超过16，也就相当于NSObject的16个字节，有8个其实像是预留的，具体看下面的代码结论。

```objective-c
//申明一个继承自NSObject对象的Student
@interface Student : NSObject {	
    @public
    int _sex;
    int _age;
}
@end
@implementation Student
@end

//转换成c++后的代码：
struct Student_IMPL {
    struct NSObject_IMPL NSObject_IVARS;//8个字节
    int _grade;//4个字节
    int _age;  //4个字节
};

struct NSObject_IMPL {
    Class isa;//8个字节
};

//实例化一个Student对象，查看其占用的内存大小
int main(int argc, const char * argv[]) {
    @autoreleasepool {
        Student *stu = [[Student alloc] init];
        stu->_grade = 3;
        stu->_age = 5;
        
        NSLog(@"%zd", class_getInstanceSize([Student class]));
        // >>这里打印出来的结果是 16
        
        NSLog(@"%zd", malloc_size((__bridge const void *)stu));
        // >>这里打印出来的结果是 16
        
        //用这个方法可以证明stu其实就是`Student_IMPL`这个结构体
        //下方面打印出来的结果跟上面赋值的结果一致
        struct Student_IMPL *stuImpl = (__bridge struct Student_IMPL *)stu;
        NSLog(@"grade is %d, age is %d", stuImpl->_grade, stuImpl->_age);
    }
    return 0;
}
```

- 从NSObject源码分析：

```c++
// objc-runtime-new.h
/* NSObject在调用alloc初始化的时候，底层是调用：
 * _objc_rootAllocWithZone() > class_createInstance() > calloc(size_t, size_t)
 * class_createInstance() 最终会调用如下方法来分配内存
 */
size_t instanceSize(size_t extraBytes) {
        size_t size = alignedInstanceSize() + extraBytes;
        // CF requires all objects be at least 16 bytes.
     	// 所有CF对象都必须最少16个字节
        if (size < 16) size = 16;
        return size;
    }
```

> ##### 结论：
>
> 1. 系统分配了16个字节给NSObject对象(通过`malloc_size()`函数获得对应对象所指向内存中的大小)
> 2. 在64bit环境下，NSObject对象内部只使用了8个字节的空间(通过`class_getInstanceSize()`函数获得实例对象的大小)，还有8个字节是预留的；
> 3. 如果结构体大小小于16个字节时，底层会最小按16来分配，超过16个字节后， 结构体的大小必须是最大成员大小的倍数(如Student结构体中最大为8，如Student超过16后，则会按8的倍数去分配)；
> 4. 但在最终分配大小的时候iOS系统做了内存分配的优化，及内存对齐机制，注意这里所说的内存对齐不是指结构体的那个内存对齐，最终分配给结构体的大小为16的倍数(可以从苹果的libmalloc源码中查看，如下图)；
> 5. 整体逻辑跟iOS本身的内存对齐机制、及运行机制有关；
>    ![](https://blogimage-1257063273.cos.ap-guangzhou.myqcloud.com/NSObject_libmalloc1.png)
>    ![](https://blogimage-1257063273.cos.ap-guangzhou.myqcloud.com/NSObject_libmalloc2.png)
>    ![](https://blogimage-1257063273.cos.ap-guangzhou.myqcloud.com/NSObject_libmalloc3.png)

###### 其它查看内存大小的方式

> 1. 通过xcode的工具来辅助性的查看：Xcode > Debug > Debug Workflew > View Memory > 然后在内存地址栏中输入你要查询的内存地址；
> 2. 通过LLDB命令来打印内存地址：`(lldb) memory read 0x10331a920` 也可以简写成：`x 0x10331a920`;
> 3. 指定的格式来打印内存地址：`(lldb) x/3xg 0x10331a920`，命令释义：以0x10331a920这个内存地址去读取3串内存地址，每串8个字节数以16进制的格式打印；
>
> ```
> (lldb) memory read/数量+格式+字节数  内存地址
> (lldb) x/数量+格式+字节数  内存地址
> 数量：你想读取的个数
> 格式：x是16进制，f是浮点，d是10进制
> 字节数：
> b：byte 1字节，h：half word 2字节
> w：word 4字节，g：giant word 8字节
> ```

![](https://blogimage-1257063273.cos.ap-guangzhou.myqcloud.com/NSObject_lldb_cmd.png)

###### 写内存

> 写内存：`(lldb) memory write 0x10331a928 9`
> 释义：改`0x10331a920`内存起的第28个字节为9，如下图

![](https://blogimage-1257063273.cos.ap-guangzhou.myqcloud.com/NSObject_memory_write.png)

注意点：

> sizeof( )它是个运算符，不是函数，在编译的时候就确定，编译之后就是个常量，如：
>
> Student *stu = [[Student alloc] init];
>
> NSLog(@"%zd",sizeof( stu )); 打印出来并不是stu占用多少空间，而是*stu这个指针占用的内存空间，结果：8；
>
> `class_getInstanceSize()`函数返回的仅仅是对象需要占用的内存大小，并非最终系统分配的内存大小。

### 相关问题二：对象的isa指针指向哪里？

![](https://blogimage-1257063273.cos.ap-guangzhou.myqcloud.com/20180816214104.png)

### 相关问题三：OC的类信息存放在哪里？

实例对象里面只存放成员变量，方法不是放在实例对象里的，因为同一类可能实例化成多个对象，但会有相同的方法，这样就只需要创建一份方法，不必须每个实例对象各存一份，每个实例对象里面只存放自已特有的东西。