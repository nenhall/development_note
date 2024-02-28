越狱开发学习历程 - Tweak文件编写

[TOC]

# 越狱开发学习历程 - Tweak文件编写

## 创建tweak项目：



1. 终端执行：`nic.pl` > 选择`11` > 如下
    ```
    NIC 2.0 - New Instance Creator
    ------------------------------
    [1.] iphone/activator_event
    [2.] iphone/application_modern
    [3.] iphone/cydget
    [4.] iphone/flipswitch_switch
    [5.] iphone/framework
    [6.] iphone/ios7_notification_center_widget
    [7.] iphone/library
    [8.] iphone/notification_center_widget
    [9.] iphone/preference_bundle_modern
    [10.] iphone/tool
    [11.] iphone/tweak
    [12.] iphone/xpc_service
    Choose a Template (required): 11 //选择11
    Project Name (required): nhwechat //项目名字，随便取，不要用中文
    Package Name [com.yourcompany.nhwechat]: //deb安装包的名称
    Author/Maintainer Name [neghao]: //作者
    
    [iphone/tweak] MobileSubstrate Bundle filter [com.apple.springboard]: com.tencent.xin //你要hook的APP的Bundle Identifier 可通过[NSBundle mainBundle].bundleIdentifier查看
    
    [iphone/tweak] List of applications to terminate upon installation (space-separated, '-' for none) 
    
    [SpringBoard]://SpringBoard需要干什么
    ```



2. 执行完上面的命令后，会在你当前执行命令所在的路径下生成文件名为刚取的`Project Name`文件；

3. 开始写hook代码：用xcode或者其实代码编辑器打开刚创建的文件夹：

    1. 配置tweak与手机的环境变量，在`Makefile`文件前面添加：

         

        > export THEOS_DEVICE_IP=127.0.0.1

        > export THEOS_DEVICE_PORT=10010

        因为我用的是usb进行连接的，ip即是本地ip，端口是22映射到了10010端口，所以按如上写法。

        如果是直接通过wifi连接的，则改成如下：

        > export THEOS_DEVICE_IP=你手机的ip

        > export THEOS_DEVICE_PORT=22

        

        如果不希望每个项目都配Makefile的IP和端口，则添加到用户配置文件(.bash_profile)中:

        ```
        vim ~/.bash_profile
        //添加如下代码,如果你是用wifi连接的，则按上面说一样进行修改
        export THEOS_DEVICE_IP=127.0.0.1
        export THEOS_DEVICE_PORT=10010
        source ~/.bash_profile
        ```

         

    2. 在Tweak.xm文件中写具体hook的代码：

        ```
        //%hook开头 %end结束，要hook的方法写在中间
        %hook MMTableViewInfo //你要hook的类
        //下面要hook的方法
        -(long long)tableView:(id)arg1 numberOfRowsInSection:(long long)arg2 {
         return %orig;
        }
        
        -(long long)numberOfSectionsInTableView:(id)arg1 {
         return %ogin+1;
        }
        
        -(id)tableView:(id)arg1 cellForRowAtIndexPath:(id)arg2 {
           return %orig;
        }
        
        %end
        ```

    3. 编译、安装：`make` > `make package` > `make install`，每次编译前最好先清理下:`make clean`



## 语法说明

### 常用语法

#### 参考链接

http://www.cycript.org/manual/#4dd1ca6b-2bd5-48e4-a0ee-06b0947880f5

#### 示例

##### 查看bundle Identifier

    cy# [[NSBundle mainBundle] bundleIdentifier]

##### 可执行文件位置

    cy# [[NSFileManager defaultManager] URLsForDirectory:NSDocumentDirectory inDomains:NSUserDomainMask][0]

##### UIApplication

    cy# [UIApp description]

##### 通过内存地址查看所有(属性名)ivar

    cy# [#0x4870560 _ivarDescription].toString()

##### 打印出当前界面的view层级

    cy# UIApp.keyWindow.recursiveDescription().toString()

##### 通过view的nextResponder方法，找出它所属的视图控制器

    cy# [#0x181009f0 nextResponder]

##### 打印所有UIView对象

    cy# [[UIApp keyWindow] recursiveDescription].toString()

##### recursiveDescription的简化版，去掉了UIView的一些描述 

    cy# [[UIApp keyWindow] _autolayoutTrace].toString()

##### 打印某个对象的属性，实例和类方法

    cy# [choose(SBApplicationController)[0] _methodDescription].toString()

##### 获取bundle info

    cy# [[NSBundle mainBundle] infoDictionary].toString()

##### 设置frame ：x y w h转成你要设置的值

    cy# #0x181009f0.frame={0:(0:x,1:y), 1:(0:w,1:h)}

##### 判定某对象是否属于某个类 instanceof

    cy# @"hello" instanceof String
    
    cy# @[2, 4, 5] instanceof Array
    
    cy# @{"thing": 9, "field": 10} instanceof Object
    
    cy# @YES instanceof Boolean
    
    cy# @5 instanceof Number​

##### 获取bundle info





