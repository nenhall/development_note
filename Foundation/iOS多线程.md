## 概念

### 进程：首先了解下进程这个概念

> 进程是指在系统中正在进行的一个应用程序
>
> 每个进程之间是独立的，每个进行均运行在其专用且受保护的内存空间；进程之间可以相互通讯。

如同时打开的QQ、Xcode，系统会分别启动2个进程

### 线程：

> 一个进程要想执行任务，必须得有线程，每个进程至少要有1条线程；一个进程的所胡任务都在线程中执行的

#### 线程的串行

> 线程中任务的执行是串行的，如果要在一个线程中执行多个任务，那么只能一个一个地按顺序执行这些任务，也就是说一个线程同一时间内只能执行一个任务

```objective-c
//创建一个串行线程，我们正常写的代码就是串行的，以下做利用GCD做了处理的
dispatch_queue_t queue = dispatch_queue_create("testQueue", DISPATCH_QUEUE_SERIAL);
dispatch_async(queue, ^{
    //do someting sth
    NSLog(@"111111111");
    for (NSInteger i = 0; i<50; i++) {
        NSLog(@"------buttonClick---%zd", i);
    }
});
dispatch_async(queue, ^{
    //do someting sth
    NSLog(@"222222222");
});
//这段代码的结果就是，执行完第一个async.block内的任务后才会执行第二个async.block
```

#### 线程的并行

> 同时执行多件事件

```objective-c
//创建一个并发线程
dispatch_queue_t queue = dispatch_queue_create("testQueue", DISPATCH_QUEUE_CONCURRENT);
dispatch_async(queue, ^{
    //do someting sth
});
```

#### 主线程

> 定义：一个iOS程序运行后，默认会开启一条线程，称为主线程(UI线程)
> 作用：主线程的主要作用显示刷新UI界面、处理UI事件(比如点击事件、滚动事件、拖拽事件等)；所以一些耗时操作且非必须在主线程中完成的事件，不要放在主线程，如下载操作

#### 子线程

> 自己创建，自己定义做某些事

#### Objective-C下可用和几种技术方案：

![](https://blogimage-1257063273.cos.ap-guangzhou.myqcloud.com/20190224165230.png)

**GCD：Grand Central Dispatch是Apple开发的一个多核编程的解决方法**

**NSOperation是个抽象类，使用时必须用它的子类，可以实现它或者定义好两个子类：NSInvocationOperation和NSBlocOperation**

####Objective-C 下创建NSThread线程的几种方法：

```objective-c
//以下的方式创建线程：简单，但无法结线程进行更详细的设置
- (void)thread {
    // 创建线程，任务放在block内，需要手机启动
    NSThread *thread = [[NSThread alloc] initWithBlock:^{
        
    }];
    // 线程名称
    thread.name = @"线程名称";
    //启动线程
    [thread start];
    
    // 创建线程，任务放在block内，需要手机启动
    thread = [[NSThread alloc] initWithTarget:self selector:@selector(test:) object:@"参数"];
    
    // 创建线程后自动启动线程
    [NSThread detachNewThreadWithBlock:^{
        
    }];
    
    // 创建线程后自动启动线程
    [NSThread detachNewThreadSelector:@selector(test:) toTarget:self withObject:@"参数"];
    
    //隐式的创建并启动线程
    [self performSelectorInBackground:@selector(test:) withObject:@"参数"];
}

- (void)test:(NSString *)str { 
    //让线程睡眠2秒
	[NSThread sleepForTimeInterval:2.0];
	//让线程睡眠到某个时间
	[NSThread sleepUntilDate:[NSDate distantFuture]];
    //退出当前线程
    [NSThread exit];
}
```

#### Objective-C 下创建GCD线程的几种常用的方法：

```objective-c
//用于全局并发队列
//DISPATCH_QUEUE_PRIORITY_DEFAULT 队列优先级
dispatch_queue_t qGlobal = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0);
// 获得主队列
dispatch_queue_t qMain = dispatch_get_main_queue();
dispatch_async(qGlobal, ^{
    
 });
//创建一个串行/并行线程
/**
 "testQueue": 线程名
 DISPATCH_QUEUE_SERIAL: 串行 DISPATCH_QUEUE_CONCURRENT:并行
 */
dispatch_queue_t qCreate = dispatch_queue_create("testQueue", DISPATCH_QUEUE_SERIAL);
dispatch_sync(qCreate, ^{
    
});
```

#### Objective-C 下创建NSOperation线程的几种方法：

```objective-c
operation
```

### 线程状态

* 就绪(Runnable)：等待CPU调度
* 运行(Running)：运行中
* 阻塞(Blocked)：调用了sleep方法或者等待同步锁
* 死亡(Dead)：线程任务执行完毕、异常、强制退出，线程死亡后不能再次开启任务

### 多线程的安全隐患

资源共享：1块资源可能会被多个线程共享，也就是多个线程可能会访问同一块资源
比如多个线程访问同一个对象、同一个变量、同一个文件，很容易引发数据错乱和数据安全问题

##### 同步锁(线程同步)：

​      这种锁很耗性能，后面我会写一篇文章专门讲iOS下的各种锁的性能分析及适应场景

```
// key:这个相当于一个锁的标识，他不能是局部变量的；
// 如果是局部对象，那也就是说在每次访问的时候都不是同一对象，就没有启到作用
@synchronized (key) {
    //要执行的代码（如修改数据的操作需要在这里面）
    break
}
```

##GCD

> GCD：Grand Central Dispatch是Apple开发的一个多核编程的解决方法
>
> GCD会自动将队列中的任务取出，放到对应的线程中执行
>
> 任务的取出遵循队列的FIFO原则：先进先出，后进后出

#### GCD中的几种执行概念：

* 同步：只能在当前线程中执行任务，不具备开启新线程的能力；

* 异步：可以在新的线程中执行任务，具备开新线程的能力；

* 并发：多个任务并发(同时)执行；

* 串行：一个任务执行完毕后，再执行下一个任务；

常用方法：

```
//用于全局并发队列
//DISPATCH_QUEUE_PRIORITY_DEFAULT 队列优先级
dispatch_queue_t qGlobal = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0);
/** 创建一个串行/并行线程
 "testQueue": 线程名
 DISPATCH_QUEUE_SERIAL: 串行 DISPATCH_QUEUE_CONCURRENT:并行
 */
dispatch_queue_t qCreate = dispatch_queue_create("testQueue", DISPATCH_QUEUE_SERIAL);
// 获得主队列
dispatch_queue_t qMain = dispatch_get_main_queue();
// 异步任务，qGlobal：执行任务的队列：子队列或者主队列
dispatch_async(qGlobal, ^{
    
 });
 //同步任务
 dispatch_sync(qGlobal, ^{
    
 });
```



