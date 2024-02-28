在c语言中，其实每一个局部变量的前都带有auto修饰符，但国为是默认的，所以可以省略，如：

 `int a = 10;  <<等价于>>  auto int a = 10;`

数据段一般用来放全局变量之类的； 
 程序段一般用来放我们写的代码； 
 堆：动态分配内存，需要程序员来申请内存和管理内存； 
 栈：局部变量；

在mrc情况下，栈空间不会持有外部的变量，但如果在堆空间会持有block内部的外部对象； 
 mrc下，_Blcok_object_assign 会自动决定是否需要强引用外部自动变量； 
 arm下，_Blcok_object_assign 会根据当时访问时的修饰的情况一致的引用外部变量，也就是说如果当时访问外部自动变量时用以strong修饰，那么blcok内部也是strong； 
 block的内部是看block里面的整体有没在访问外部自动变量，再确定是不是访问的外部自动变量什么时候释放，如下情况，obj对象是在第三个block释放后再释放obj：

 `NSObject *obj = [[NSObject alloc] init];``__weak NSObject *weakObj = obj;``^(){``NSLog(@"%@",weakObj);``    ^(){``    NSLog(@"%@",obj);``    };``};`