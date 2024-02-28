## 动态调试任意APP：

### ldid的使用

* ldid帮助，电脑终端输入：

  ```
  $ldid
  usage: ldid -S[entitlements.xml] <binary>
     ldid -e MobileSafari //导入现有的权限
     ldid -S cat //将权限文件进行签名
     ldid -Stfp.xml gdb //将权限文件进行签名
  ```


* 导出某文件的权限：
  ```
  e.g.: 导出debugserver文件的权限：
  $ ldid -e /Users/xxxxx/debugserver > debugserver.entitlements
  // `/Users/xxxxx/debugserver`是你的文件路径 `>`是代表导出 箭头后面是导出后的文件名，会导出到你当前执行命令所在目录下
  ```

* 赋予新权限后，重签：

  > 这里以给debugserver文件添加调试权限为例，需要添加两个权限：get-task-allow、task_for_pid-allow，类型BOOL为YES。
  >
  > debugserver文件在手机的`Device/Developer/usr/bin`目录下

     * 方法一：
        打开刚导出的debugserver.entitlements文件，在文件内添加你要添加的权限，添加完后使用ldid命令进行重新签名，如下，
        ```
        Desktop$ ldid -Sdebugserver.entitlements debugserver
        //指令说明：ldid -S紧跟着权限文件名 要签入权限文件的文件名
        //注意，-S后面不要有空格，S是大写的
        ```

     * 方法二：
         1. 新创建一个`.entitlements`(.plist文件也可以)文件；
         2. 然后把要添加的权限属性添加到文件中，如下图；
         3. 把刚新建的权限文件添加到debugserver文件中；
         ![](https://blogimage-1257063273.cos.ap-guangzhou.myqcloud.com/20180711220112.png)
         ```
         Desktop$ codesign -s - --entitlements ent.plist -f debugserver
         //命令说明：ent.plist就是刚我们新建的权限文件，debugserver：要签入的文件
         ```

     * 签完后，可以使用权限文件导出命令，导出来看下是否成功了；

     * 然后将重签好的文件复制到手机的`Device/usr/bin`目录下，因为原目录下的`debugserver`文件是只读的，无法替换，而且此目录在环境变量下，可以直接使用；

     * 最后将给`debugserver`赋予可执行权限；

         ```
         chmod +x /usr/bin/debugserver
         ```
     * 然后可以开始使用了。

  ### 将debugserver文件进行瘦身后再放入设备

  原本debugserver文件是包含了所有架构，但我们现在是固定在某种构架指令下使用，所以只要把对应构架的可执行文件导出来使用就行：

  ```
  Desktop$ lipo -thin arm64 debugserver -output debugserver2
  //指令说明：lipo -thin 对应构架指令 对应文件 -output 输出的文件名/路径
  ```
  ### 动态调试app

  #### 启动LLDB、debugserver

  * 让debugserver附加到某个app进程，连接手机，在手机上输入：

    ```
    $ debugserver *:端口号 -a 进程
    ```

    ***\*:端口号：***\*是代表主机地址，因为调试的是当前主机，所以用\*代替，使用iPhone的某个端口号启动debugserver服务(只要不是保留端口号就行了)；

    ***-a：***app的进程id或者进程名称

    ***使用debugserver启动App***

    ```
    iPad:~ root# debugserver -x auto *:端口号 app可执行文件的路径
    e.g.:
    iPad:~ root# debugserver -x auto *:10011 /var/mobile/Containers/Bundle/Application/457F9034-6CE9-4381-B483-C3091C0FA176/WeChat.app/WeChat
    ```

  * 在mac电脑上启动LLDB，远程连接iPhone上的debugserver服务：

    * 启动LLDB

      ```
      $ lldb
      (lldb)
      ```

    * 连接debugserver服务

      ```
      (lldb) process connect connect://手机IP地址:debugserver服务端口号
      e.g.:
      (lldb) process connect connect://10.10.202.31:10010
      or:
      (lldb) process connect connect://localhost:10011
      ```
      > 因为我使用的是python脚本usb映射连接，所以如下,相当于手机的10011端口映射到电脑的10011端口：
      > 启动映射进程改成如下命令：
      > mac $ python tcprelay.py -t 22:10010 10011:10011
      > lldb连接命令：
      >
      > (lldb) process connect connect://localhost:10011

    * 使用LLDB的c命令让程序继续运行：

      ```
      //刚连接成功后，app上是无法点击的，因为处于断点状态，需要在电脑上输入`c`
      (lldb) c
      ```

    * 接下可以使用LLDB命令调试app

  #### 开始调试app



更多lldb命令请参考：[iOS开发断点调试高级技巧](https://blog.csdn.net/zhouzhoujianquan/article/details/54949464)