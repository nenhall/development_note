# iOS/MacOS手动还原Crash 符号表

1. 创建一个单独的文件夹，并进入文件夹目录；

2. 导出`symbolicatecrash`可执行文件：

   ``` shell
   // 执行下面这句命令后，可打印出多个对应文件路径，选择其中一个对应平台的即可
   find /Applications/Xcode.app -name symbolicatecrash -type f
   
   // 再执行如下命令（就是把symbolicatecrash文件拷贝到当前目录下）
   cp /Applications/Xcode.app/Contents/SharedFrameworks/DVTFoundation.framework/Versions/A/Resources/symbolicatecrash ./
   ```

3. 把对应的 `.dSYM`，`.app`，`.crash`，`symbolicatecrash`放同一目录(就第一步创建的那个文件目录下);

   **dSYM**文件：在 xcode 打包的时候生成的，此文件与.app 一定要是一起编译出来的，否则还原不了。每改次代码 dSYM 文件都会跟着代码变的，具体的自行搜索了解下。

4. 设置环境变量(为了方便也可以把这个环境变量直接加到你的 shell 脚本中，就省去了每次临时设置这个)：

   ```shell
   export DEVELOPER_DIR=/Applications/Xcode.app/Contents/Developer
   ```

5. 还原符号表并导出崩溃日志：

   ```shell
   // plCrashReporter.crashlog与.dSYM 都换成你的对应日志文件名，最后app.log为导出文件名
   ./symbolicatecrash plCrashReporter.crashlog xxxx.dSYM > app.log
   ```

6. 还原前后对比：

   ![](https://blog-1257063273.cos.ap-chengdu.myqcloud.com/2020/20201008111459.png)

![](https://blog-1257063273.cos.ap-chengdu.myqcloud.com/2020/20201008105619.png)

![](https://blog-1257063273.cos.ap-chengdu.myqcloud.com/2020/20201008110136.png)