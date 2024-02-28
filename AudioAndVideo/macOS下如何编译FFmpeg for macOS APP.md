# macOS下如何编译FFmpeg for macOS APP

1. ```sh
   git clone https://git.ffmpeg.org/ffmpeg.git
   ```

2. 配置./configure选项

   ```sh
   --extra-cflags=-mmacosx-version-min=10.14 --extra-ldflags=-mmacosx-version-min=10.14
   ```

3. 执行./configure内容如下：

   ```sh
   ./configure --target-os=darwin --enable-static --enable-swscale --disable-x86asm --enable-nonfree  --enable-gpl --enable-version3 --enable-nonfree --disable-programs  --libdir=/Users/nenhall/Downloads/ffmpeg5/video/code/FFMPEG_iOS/ffmpegbuild/Lib --incdir=/Users/nenhall/Downloads/ffmpeg5/video/code/FFMPEG_iOS/ffmpegbuild/include --enable-shared --extra-cflags=-mmacosx-version-min=10.14 --extra-ldflags=-mmacosx-version-min=10.14
   ```

4. 执行编译和安装

   ```sh
   make && sudo make install
   ```

5. 编译aac

   ```sh
   git clone https://github.com/mstorsjo/fdk-aac.git
   cd fdk-aac
   ./autogen.sh /* 执行这个一步的需要automake，如果没有可以直接brew install automake */
   ./configure --enable-shared --enable-static --prefix=/Users/nenhall/Desktop/Library/Demo/FFmpeg/FFmpeg/ffmpeg/aacLib
   make && make install
   ```

6. 下载和编译x264库

   ```bash
   git clone http://git.videolan.org/git/x264.git.
   cd x264
   ./configure --disable-asm --enable-shared --enable-static --prefix=/Users/nenhall/Desktop/Library/Demo/FFmpeg/FFmpeg/ffmpeg/h264
   make && sudo make install
   ```