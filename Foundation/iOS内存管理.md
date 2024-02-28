@interface Person : NSObject 
@property (strong, nonatomic) NSArray *bookArray1; @property (copy, nonatomic) NSArray* bookArray2; 
@end

@implementation Person 
//省略setter方法 
@end

//Person调用 
main(){ 
NSMutableArray *books = [@[@"book1"] mutableCopy]; Person* person = [[Person alloc] init]; 
person.bookArray1 = books; 
person.bookArray2 = books; 
[books addObject:@"book2"]; 
NSLog(@"bookArray1:%@",person.bookArray1); 
NSLog(@"bookArray2:%@",person.bookArray2); 
} 
我们看到，使用strong修饰的person.bookArray1输出是[book1,book2]，而使用copy修饰的person.bookArray2输出是[book1]。这下可以看出来区别了吧。 
备注：使用strong，则person.bookArray1与可变数组books指向同一块内存区域，books内容改变，导致person.bookArray1的内容改变，因为两者是同一个东西；而使用copy，person.bookArray2在赋值之前，将books内容复制，创建一个新的内存区域，所以两者不是一回事，books的改变不会导致person.bookArray2的改变。 
说到底，其实就是不同的修饰符，对应不同的setter方法，

1. strong对应的setter方法，是将_property先release（_property release），然后将参数retain（property retain），最后是_property = property。
2. copy对应的setter方法，是将_property先release（_property release），然后拷贝参数内容（property copy），创建一块新的内存地址，最后_property = property。

深拷贝拷贝到是对象，浅拷贝拷贝的是指针