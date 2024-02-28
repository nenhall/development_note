## Mac OS上编译ffmpegiOS平台库.md

### 制定编译脚本

这里我们直接使用别人写好编译脚本，编译脚本自己写的话，估计还得研究好久；重要的这个编译脚本的作者还有在维护这个脚本

1. 下载脚本，脚本地址：[**FFmpeg-iOS-build-script**](https://github.com/kewlbear/FFmpeg-iOS-build-script)

2. 打**`build-ffmpeg.sh`**文件，更改ffmpeg要下载的版本号，找到如下代码，把FF_VERSION改成你要下载的版本号：

   ```
   ...
   FF_VERSION="4.1.0"
   if [[ $FFMPEG_VERSION != "" ]]; then
   FF_VERSION=$FFMPEG_VERSION
   fi
   SOURCE="ffmpeg-$FF_VERSION"
   ...
   ```

3. 执行脚本内的编译命令：

   ```
   sh build-ffmpeg.sh
   ```


### 编译完成：
	脚本则会自动从github中把ffmpeg源码下到本地并开始编译，编译好后在当前目录生成了FFmpeg-iOS文件夹，所以架构的.a库和头文件都在里面：

![](https://blogimage-1257063273.cos.ap-guangzhou.myqcloud.com/20190228112843.png)

### 编译FFmpeg iOS平台上使用编译命令行工具：

1. 由于我们在编译的时候使用了--disable-programs编译选项，因此并不会编译命令行相关的工具。需要自己编译相关文件来支持FFmpeg命令行的解析。如下所示：

   ```
   CONFIGURE_FLAGS="--enable-cross-compile --disable-debug --disable-programs --disable-doc --enable-pic"
   ```

2. 在编译命令解析相关的库文件的时候，我们需要用到了一下几个源文件：

   ![](https://blogimage-1257063273.cos.ap-guangzhou.myqcloud.com/20190228113912.png)

   > 以上文件在FFmpeg的源文件中的如下目录中找到：
   > **ffmpeg-3.4.2** > **fftools/...**
   > **config.h** 在 **scratch**目录下选在你要编译的构架目录下的config.h
   > 如果你要编译所有构架，那就依次选择每一个config.h到你的工程下面进行编译，再把每次编译出来的.a拿出来，然后进行库合并

3. 修改ffmpeg.c的main函数

   在编译的时候我们需要修改ffmpeg.c的main函数，因为一个程序不能有两个main函数，在这里我们改成ffmpeg_main，如下所示：

   ![](https://blogimage-1257063273.cos.ap-guangzhou.myqcloud.com/20190228114202.png)

4. 修改cmdutils.c中的exit_program函数

   修改cmdutils.c中的exit_program函数，注释掉原来的函数，改成如下，如果不修改，在FFmpeg命令执行完成后，程序会退出

   ```c++
   void exit_program(int ret)
   {
   //    if (program_exit)
   //        program_exit(ret);
   //    exit(ret);
     program_exit(NULL);
   }
   ```

5. 接下来创建一个xcode静态库工程，将刚我们找出来的的源文件拖入项目中:

6. 配置工程头文件搜索路径，我们主要是在FFmpeg-iOS及ffmpeg-4.1中搜索：

   ```
   - ($(SRCROOT)/../FFmpeg-iOS/include)
   - ($(SRCROOT)/../ffmpeg-4.1)
   ```

   之所以要在源文件中搜索，是因为编译出来的FFmpeg-iOS文件夹中并没有拷贝所有头文件，只有必要的头文件。在这里我们不需要链接之前编译的库文件，因为静态库本来就只是编译(clang -c)和打包(ar -r)的产物，并不需要链接。

7. （可选操作项）编译完成后通过lipo -create 命令生成模拟器和真机架构的通用库：

   ```
   $ lipo -create ....(文件路径)/libffmpeg.a  ....(文件路径)/libffmpeg.a -output /Users/xxx/Desktop/libfmpeg.a
   ```

8. 至此已经制作完毕了，可以使用了。

#### 各个模块功能简要说明：

> **libavformat**：用于各种音视频封装格式的生成和解析；
> **libavcodec**：用于各种类型声音、图像编解码；
> **libavutil**：包含一些公共的工具函数；
> **libswscale**：用于视频场景比例缩放、色彩映射转换；
> **libpostproc**：用于后期效果处理；
> **ffmpeg**：该项目提供的一个工具，可用于格式转换、解码或电视卡即时编码等；
> **ffsever**：一个 HTTP 多媒体即时广播串流服务器；
> **ffplay**：是一个简单的播放器，使用ffmpeg 库解析和解码，通过SDL显示；



最后呈上[DEMO](https://github.com/nenhall/NHFFmpeg)

![音视频开发交流群](http://upload-images.jianshu.io/upload_images/2443108-dd53439eb3250dd1?imageMogr2/auto-orient/strip%7CimageView2/2/w/240)