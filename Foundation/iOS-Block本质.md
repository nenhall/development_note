## iOS-Block本质

### 本质：

- block本质上也是一个OC对象，它内部也有个isa指针

- block是封装了函数调用以及函数调用环境的OC对象

- block的底层结构如下图二所示

  ![底层结构](https://blogimage-1257063273.cos.ap-guangzhou.myqcloud.com/20181016212502.png)

#### 底层数据结构(底层代码分析)：

  * block底层是一个**\__main_block_impl_0**这样一个结构体，结构体的第一个成员又是一个结构体**\__block_impl**，这个结构体里面有个isa指针，还有个**FuncPtr**，**FuncPtr**其实就是存放block块里面的代码实现，注意**\__block_impl  impl**这个不是一个用指针指向**\__block_impl**，所以相当于**\__main_block_impl_0**直接拥有**\__block_impl**这个结构体，**\__block_impl**里面的每个成员都可看成是**\__main_block_impl_0**的成员

  ![底层数据结构](https://blogimage-1257063273.cos.ap-guangzhou.myqcloud.com/20181017205811.png)

### 变量捕获机制

> 为了保证block内部能够正常访问外部的变量，block有个变量捕获机制

- 访问局部 auto 变量

  > blcok内部会自动捕获auto变量当时的值(值传递)，block内部也会生成一个age的成员变量，如下它捕获时是10，所以你后面再改age也不会跟着变化，因为它也是值传递

  ```objective-c
  int age = 10;
  block = ^{
      // age的值捕获进来（capture）
      NSLog(@"age is %d", age);//结果是10
  };
  age = 20;
  ```

- 访问局部 static 变量

  > static修饰局部变量时， 变量生命周期变了(直到程序结束才销毁)，在这种情况下block内部也会捕获变量，但他捕获的不是具体的值了，而是变量的指针

  ```objective-c
  static int height = 10;
  block = ^{
      // age的值捕获进来（capture）
      NSLog(@"height is %d", height);//结果是20
  };
  height = 20;
  ```

- 访问全局变量

  > block内部不会捕获，是在使用时直接访问的

  ```objective-c
  int age_ = 10;
  static int height_ = 10;
  
  int main(int argc, const char * argv[]) {
      @autoreleasepool {
          
          void (^block)(void) = ^{
              //打印结果：20, 20
              NSLog(@"age is %d, height is %d", age_, height_);
          };
  
          age_ = 20;
          height_ = 20;
  
          block();
      }
      return 0;
  }
  ```

* 访问对象的成员变量时

  > 不管是下面那种方式访问，都会捕获self，self他是每个方法都有的隐匿参数，调用者是谁，self就是谁，访问的时候block内部是直接通过self去访问的，内部不会捕获age

  ```objective-c
  - (void)test
     block = ^{
         NSLog(@"age is %d", _age);
         NSLog(@"age is %d", [self age]);//self.age
     };
  }
  ```

**变量捕获机制总结，如下图：**

> 也可以自己通过终端 xcrun命令将.m文件转成c++文件，就能看到底层实现

![](https://blogimage-1257063273.cos.ap-guangzhou.myqcloud.com/20181016212641.png)

  **联想问题：为什么要局部变量时要捕获，全局则不捕获？**

  >1. 首先局部变量的作用域是函数范围内，出了函数，变量就会被销毁，但block存在夸函数调用，这种情况下就无法访问到对应变量了，所以只能把对应变量捕获到block内部；
  >2. 全局变量在任何位置都可以访问，所以没必要捕获到block内部，直接访问就可以了。



### Block的种类

- _NSConcreteMallocBlock

> block有3种类型，可以通过调用class方法或者isa指针查看具体类型，最终都是继承自NSBlock类型

| **block 类型**                                 | **环境**                    |
| :--------------------------------------------- | :-------------------------- |
| \__NSGlobalBlock__   (\_NSConcreteGlobalBlock) | 没有访问auto变量            |
| \__NSStackBlock__    (\_NSConcreteStackBlock)  | 访问了auto变量              |
| \__NSMallocBlock__   (\_NSConcreteMallocBlock) | \__NSStackBlock__调用了copy |

![](https://blogimage-1257063273.cos.ap-guangzhou.myqcloud.com/20181016212911.png)

> 每一种类型的block调用copy后的结果如下所示

![](https://blogimage-1257063273.cos.ap-guangzhou.myqcloud.com/20181016213450.png)

### Block 对象类型、auto变量

* ARC环境下：

  > 在ARC环境下，编译器会根据情况自动将栈上的block复制到堆上，比如以下情况

  - block作为函数返回值时

  - 将block赋值给__strong指针时

  - block作为Cocoa API中方法名含有usingBlock的方法参数时(如NSArray的`enumerateObjectsUsingBlock:`方法)

  - block作为GCD API的方法参数时，如下对象Person不会立即释放，而是等3秒后才释放

    ```objective-c
    -(void)touchesBegan:(NSSet<UITouch *> *)touches withEvent:(UIEvent *)event {
        Person *p = [[Person alloc] init];
        dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(3.0*NSEC_PER_SEC)), dispatch_get_main_queue(), ^{
            NSLog(@"%@",p);
        })
    }
    ```

  - block内如果访问了对象类型的的自动变量，内部会默认对其强引用，使用修饰符除外

  - 局部的block默认是globalBock，存放在数据区，如果访问了auto变量，block会自动调用copy操作，则变成MallocBlock存放在堆区，待Block出了作用域后，会自动销毁(内部会release操作)

* MRC环境下：

  * 局部的block默认是globalBock，存放在数据区，如果访问了auto变量，则变成stackBlock存放在栈区，需要手动调用block copy操作才会成为MallocBlock

* Block内部结构变化

  * Block内部如果没有访问对象类型，Block的Desc的结构如下：

    ```c++
    static struct __main_block_desc_0 {
        size_t reserved;
        size_t Block_size;
    }
    ```

  * Block内部如果访问对象类型，Block的Desc的结构如下：

    > 因为你访问了一个对象，肯定得对对象进行内存管理操作(copy、return、release)，所以需要以下两个函数进行内管理

    ```c++
    static struct __main_block_desc_0 {
        size_t reserved;
        size_t Block_size;
        void (*copy)(struct __main_block_impl_0*, struct __main_block_impl_0*);
        void (*dispose)(struct __main_block_impl_0*);
    }
    ```

* 当block内部访问了对象类型的auto变量时

  * 如果block是在栈上，将不会对auto变量产生强引用

* 如果block被拷贝到堆上

  * 会调用block内部的copy函数
  * copy函数内部会调用_Block_object_assign函数
  * _Block_object_assign函数会根据auto变量的修饰符（__strong、__weak、__unsafe_unretained）做出相应的操作，形成强引用（retain）或者弱引用，用\__\_block修饰的对象/变量，block内部会强引用他

* 如果block从堆上移除

  - 会调用block内部的dispose函数
  - dispose函数内部会调用_Block_object_dispose函数
  - _Block_object_dispose函数会自动释放引用的auto变量（release）

### \__block修饰符

- __block可以用于解决block内部无法修改auto变量值的问题
- __block不能修饰全局变量、静态变量（static）
- 编译器会将__block变量包装成一个对象

\__block修饰auto变量：会进行包装成结构体，而对应的block内部会含有一个`__Block_byref_age_0`成员，其指针指向那个结构体，结构体如下，下对`age`变量为例：

```c++
struct __Block_byref_age_0 {
	void *_.isa;
	__Block_byref__age__0 *__forwarding;
	int __flags ;
	int __size;
	int age;//这个就是被修饰的auto变量
};
//这个__forwarding其实指的是自己，目的是为了block从栈拷贝到堆上后跨域访问变量的问题，block从栈拷贝到堆后，就是在栈和堆中各有一块内存，再通过__forwarding去访问，不管变量是在栈还是堆，__forwarding永远者能访问到，如下图：
```

![](https://blogimage-1257063273.cos.ap-guangzhou.myqcloud.com/20181219221215.png)

\__block修饰对象类型：会进行包装成结构体，而对应的block内部会含有一个`__Block_byref_age_0`成员，其指针指向那个结构体，结构体如下，下面以`Persion`对象为例：

```c++
struct __Block_byref_age_0 {
	void *_.isa;
	__Block_byref__age__0 *__forwarding;
	int __flags ;
	int __size;
	void (*__Block_byref_id_object_copy)(void*, void*);
	void (*__Block_byref_id_object_dispose)(void*);
	Persion*__strong person;//这个就是被修饰的对象
};
```



没有直接修改变量或者对象的内存地址时，是不需要添加__block修饰符的，如下只是在使用array的指针，并没有修改，这种情况下加了反而是一种负担**：

```objective-c
NSMutableArray *array = [NSMutableArray array];
Block block = ^{
	[array add0bject :@"123"];
	[array add0bject :@"123"];
}
```



### Block的内存管理

- 如果block被拷贝到堆上
  - 会调用block内部的copy函数
  - copy函数内部会调用_Block_object_assign函数
  - _Block_object_assign函数会根据auto变量的修饰符（__strong、__weak、__unsafe_unretained）做出相应的操作，形成强引用（retain）或者弱引用，用\__\_block修饰的对象/变量，block内部会强引用他
- 如果block从堆上移除
  - 会调用block内部的dispose函数
  - dispose函数内部会调用_Block_object_dispose函数
  - _Block_object_dispose函数会自动释放引用的auto变量（release）

### **解决循环引用问题** **- ARC**

- 用__weak、__unsafe_unretained解决
- 用__block解决（必须要调用block，然后block内部把对象置nil）

### **解决循环引用问题** **- MRC**
- 用__unsafe_unretained解决
- 用__block解决(在MRC下block用了\_\_block修饰的对象，block内部不会对其对象做return操作)
