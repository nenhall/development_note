## KVC实现原理
KVC的全称是Key-Value Coding，俗称“键值编码”，可以通过一个key来访问某个属性

#### 常见的API有

- \- (void)setValue:(id)value forKeyPath:(NSString *)keyPath;
- \- (void)setValue:(id)value forKey:(NSString *)key;
- \- (id)valueForKeyPath:(NSString *)keyPath;
- \- (id)valueForKey:(NSString *)key; 

#### set时的查找顺序：

先查找`setkey： _setkey`如果找到就调用，否则调用`+(BOOL)accessInstanceVariablesDirectly `，如果这个函数返回的是YES，就会按`_key 、_isKey 、key 、isKey`的顺序查找对应的成员变量，如果找到，就调用，否则抛异常，如下图。

![](https://blogimage-1257063273.cos.ap-guangzhou.myqcloud.com/20180816210006.png)
![](https://blogimage-1257063273.cos.ap-guangzhou.myqcloud.com/20180816210104.png)

联想到的相关问答：

1. 通过KVC修改属性会触发KVO吗？

   会触发KVO，KVO内部是会调用willChangeValueForKey:、 didChangeVlaueForKey:方法的，口说无凭，我们去重写下对象的这两个方法，看它会不会来；

   ![重写下对象的这两个方法](https://blogimage-1257063273.cos.ap-guangzhou.myqcloud.com/20180816211239.png)

   //断点后

   ![断点后](https://blogimage-1257063273.cos.ap-guangzhou.myqcloud.com/20180816211311.png)

   

### get时的查找顺序：getKey >> key >> isKey >> _key

```objective-c
@interface Person : NSObject
{//申明对应的成员变量，便于验证
    @public
    int age;
}
@end

@implementation Person
//给对应的成员变量赋予不同的值，便于验证识别
- (int)getAge {
    return 11;
}

- (int)age {
    return 12;
}

- (int)isAge {
    return 13;
}

- (int)_age {
    return 14;
}

int main(int argc, const char * argv[]) {
    @autoreleasepool {
        Person *person = [[Person alloc] init];
        person->_age = 10;
        //依次注释Person类的另外三个方法，再运行项目，你会发现它会依次进行查找，知道四个方法都没有的时候然后抛异常
        NSLog(@"%@", [person valueForKey:@"age"]);
    }
    return 0;
}
```

![](https://blogimage-1257063273.cos.ap-guangzhou.myqcloud.com/20180816212048.png)

Please <a class="github-button" href="https://github.com/nenhall/NHHUDExtend" data-icon="octicon-star" aria-label="Star nenhall/NHHUDExtend on GitHub">Star</a> this Project if you like it! <a class="github-button" href="https://github.com/nenhall" aria-label="Follow @nenhall on GitHub">Follow</a> would also be appreciated!
Peace!

