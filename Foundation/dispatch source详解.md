## dispatch源（dispatch source）和RunLoop源概念上有些类似的地方，而且使用起来更简单。要很好地理解dispatch源，其实把它看成一种特别的生产消费模式。dispatch源好比生产的数据，当有新数据时，会自动在dispatch指定的队列（即消费队列）上运行相应地block，生产和消费同步是dispatch源会自动管理的。

dispatch源的使用基本为以下步骤：

**1. dispatch_source_t source = dispatch_source_create(dispatch_source_type, handler, mask, dispatch_queue****);** //创建dispatch源，这里使用加法来合并dispatch源数据，最后一个参数是指定dispatch队列

**2. dispatch_source_set_event_handler(source, ^{ //设置响应dispatch源事件的block，在dispatch源指定的队列上运行**

　　**//可以通过dispatch_source_get_data(source)来得到dispatch源数据**

**});**

**3. dispatch_resume****(source);** //dispatch源创建后处于suspend状态，所以需要启动dispatch源

**4. dispatch_source_merge_data****(source, value);** //合并dispatch源数据，在dispatch源的block中，dispatch_source_get_data(source)就会得到value。

是不是很简单？而且完全不必编写同步的代码。比如网络请求数据的模式，就可以这样来写：

​    **\*dispatch_source_t source = dispatch_source_create(DISPATCH_SOURCE_TYPE_DATA_ADD, 0, 0, dispatch_get_global_queue(0, 0));***

​    **\*dispatch_source_set_event_handler(source, ^{***

​        **\*dispatch_sync(dispatch_get_main_queue(), ^{***

　　　　**\*//更新UI***

​        **\*});***

​    **\*});***

​    **\*dispatch_resume(source);***

​    **\*dispatch_async(dispatch_get_global_queue(0, 0), ^{***

　　　**\*//网络请求***

​        **\*dispatch_source_merge_data(source, 1); //通知队列***

​    **\*});***

dispatch源还支持其它一些系统源，包括定时器、监控文件的读写、监控文件系统、监控信号或进程等，基本上调用的方式原理和上面相同，只是有可能是系统自动触发事件。比如dispatch定时器：

**\*dispatch_source_t timer = dispatch_source_create(DISPATCH_SOURCE_TYPE_TIMER, 0, 0, queue);***

**\*dispatch_source_set_timer(timer, dispatch_walltime(NULL, 0), 10\*NSEC_PER_SEC, 1*NSEC_PER_SEC); //每10秒触发timer，误差1秒***

**\*dispatch_source_set_event_handler(timer, ^{***

　　**\*//定时处理***

**\*});***

**\*dispatch_resume(timer);***

其它情况的dispatch源就不再一一举例，可参看官网有具体文档: <https://developer.apple.com/library/ios/documentation/General/Conceptual/ConcurrencyProgrammingGuide/GCDWorkQueues/GCDWorkQueues.html#//apple_ref/doc/uid/TP40008091-CH103-SW1>

 

最后，dispatch源的其它一些函数大致罗列如下：

**uintptr_t dispatch_source_get_handle(dispatch_source_t source);** //得到dispatch源创建，即调用dispatch_source_create的第二个参数

**unsignedlong dispatch_source_get_mask(dispatch_source_t source);** //得到dispatch源创建，即调用dispatch_source_create的第三个参数

**void dispatch_source_cancel(dispatch_source_t source);** //取消dispatch源的事件处理--即不再调用block。如果调用dispatch_suspend只是暂停dispatch源。

**long dispatch_source_testcancel(dispatch_source_t source);** //检测是否dispatch源被取消，如果返回非0值则表明dispatch源已经被取消

**void dispatch_source_set_cancel_handler(dispatch_source_t source, dispatch_block_t cancel_handler);** //dispatch源取消时调用的block，一般用于关闭文件或socket等，释放相关资源

**void dispatch_source_set_registration_handler(dispatch_source_t source, dispatch_block_t registration_handler);** //可用于设置dispatch源启动时调用block，调用完成后即释放这个block。也可在dispatch源运行当中随时调用这个函数。



# GCD

### GCD_after与performSelector的区别：

1. 用于延迟操作的情况gcd的误差要大；
2. gcd不可以取消；
3. perfromSelector在主线程的runloop是默认开启的，而子线程需手动开启；
4. 如把perfromSelector放到子线程是无法执行的，必须在子线程中加入`[[NSRunLoop currentRunLoop] run]`才会执行
5. 用于定时操作的时候，误差比NSTimer误差要小。

