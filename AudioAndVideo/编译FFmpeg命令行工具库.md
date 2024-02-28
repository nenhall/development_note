

1. 下载脚本，脚本地址：[**FFmpeg-iOS-build-script**](https://github.com/kewlbear/FFmpeg-iOS-build-script)，这个脚本一直在维护

2. 进入下载后的文件夹内：

   1. 打**build-ffmpeg.sh**文件，更改ffmpeg要下载的版本号，找到如下代码，把FF_VERSION改成你要下载的版本号：

   ```
   ...
   FF_VERSION="4.2.0" //改这里
   if [[ $FFMPEG_VERSION != "" ]]; then
   ...
   ```

3. 执行脚本内的编译命令：

   ```
   sh build-ffmpeg.sh
   ```

4. 编译好后：

   1. 

