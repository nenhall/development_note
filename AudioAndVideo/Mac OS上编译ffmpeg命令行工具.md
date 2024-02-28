## 在 Mac OS 上编译ffmpeg命令行工具

### 编译ffmpeg命令行工具

1. 下载源码：

   去官网下载源码：http://ffmpeg.org/download.html

2. 进入下到的ffmpeg文件夹内

3. 在终端执行配置编译命令：

   ```
   $ ./configure --prefix=/usr/local --enable-gpl --enable-nonfree --enable-libass \
   --enable-libfdk-aac --enable-libfreetype --enable-libmp3lame --enable-libopus \
   --enable-libtheora --enable-libvorbis --enable-libvpx --enable-libx264 --enable-libxvid --extra-ldflags=-L/usr/local/lib
   ```

4. 根据配置进行安装

   ```
   $ make && make install
   ```

5. 在第3步进行配置编译的时候可能会因为缺少依赖库而报错，以下错误是比较常见的错误：

   1. yasm/nasm not found or too old. Use --disable-yasm for a crippledbuild：

      yasm是汇编编译器，ffmpeg为了提高效率使用了汇编指令，如MMX和SSE等。所以系统中未安装yasm时，就会报上面错误。

      解决方法：

      安装yasm编译器；提供三种安装方法如下(那一种都可以，使用无区别)：

      * 通过brew安装：brew install yasm

      * 通过apt-get安装：sudo apt-get install yasm

      * 如果不需要yasm也可以：./configure --disable-x86asm

      * 手动下载安装包进行安装：

        ```
          //在http://www.tortall.net/projects/yasm/releases下面找到适合自己平台的yasm版本。然后进行安装。举例如下：
          1）下载：wget http://www.tortall.net/projects/yasm/releases/yasm-1.3.0.tar.gz
          2）解压：tar zxvf yasm-1.3.0.tar.gz
          3）切换路径： cd yasm-1.3.0
          4）执行配置： ./configure
          5）编译：make
          6）安装：make install   
        ```

   2. ERROR: libass not found using pkg-config

      需要安装 [libass](http://www.linuxfromscratch.org/blfs/view/svn/multimedia/libass.html) (字幕渲染库和滤镜也用到了这个库)

      方式一：手动安装；方式二：brew 安装(往下看)

      1. clong libass的代码：

         `$ git clone https://github.com/libass/libass.git`

      2. 下载 libass 相关的依懒包：

         * [FreeType](http://www.linuxfromscratch.org/blfs/view/svn/general/freetype2.html)2下载后解压，进入解压包，执行：

           ```
           // 在`Mac OS上`不要指定安装目录，即如下：
           $ ./configure --disable-static && make
           // 上面是在`ubuntu`下指定安装目录，即如下：
           // $ ./configure --prefix=/usr --enable-freetype-config --disable-static && make
           $ make check
           // 上面这一步主要是检查能否正确安装，注意看终端的打印，有问题都会报出来，
           // 没有问题就继续执行下一步：
           $ sudo make install
           ```

         * [fribidi](http://www.linuxfromscratch.org/blfs/view/svn/general/fribidi.html)下载后解压，进入解压包，执行：

           ```
           // 在`Mac OS上`不要指定安装目录，即如下：
           $ ./configure --disable-static && make
           // 上面是在`ubuntu`下指定安装目录，即如下：
           //$ ./configure --prefix=/usr --disable-docs && make
           $ make check
           // 上面这一步主要是检查能否正确安装，注意看终端的打印，有问题都会报出来，
           // 没有问题就继续执行下一步：
           $ sudo make install
           ```

         * [fontconfig](http://www.linuxfromscratch.org/blfs/view/svn/general/fontconfig.html)下载后解压，进入解压包，执行：

           ```
           $ rm -f src/fcobjshash.h
           $ ./configure --prefix=/usr \
                 --sysconfdir=/etc \
                 --localstatedir=/var \
                 --disable-docs \
                 --docdir=/usr/share/doc/fontconfig-2.13.0 && make
           $ sudo make install
           ```

         * [NASM](http://www.linuxfromscratch.org/blfs/view/svn/general/nasm.html) 下载压缩包后，进入对应目录，执行：

           ```
           $ tar -xf ../nasm-2.14.02-xdoc.tar.xz --strip-components=1
           $ ./configure --prefix=/usr && make
           $ make install
           ```

           

      3. 然后回到刚clone libass目录，执行：

         ```
         $ sh autogen.sh
         $ ./configure --prefix=/usr --disable-static && make
         // 上面是在`ubuntu`上的，在`Mac OS上`不要指定安装目录，即如下：
         $ ./configure --disable-static && make
         $ sudo make install
         // 到此libass安装完成
         ```

      4. 最后配置下环境路径：

         `export PKG_CONFIG_PATH=/usr/local/ass/lib/pkgconfig:$PKG_CONFIG_PATH`

   3. libaacplus无法通过brew来安装，安装方式如下：

      [FFmpeg libaacplus官方链接](https://trac.ffmpeg.org/wiki/How%20to%20quickly%20compile%20libaacplus)

      ```
      # apt-get install libfftw3-dev pkg-config autoconf automake libtool unzip
      $ wget http://tipok.org.ua/downloads/media/aacplus/libaacplus/libaacplus-2.0.2.tar.gz
      $ tar -xzf libaacplus-2.0.2.tar.gz
      $ cd libaacplus-2.0.2
      $ ./autogen.sh --enable-shared --enable-static
      $ make
      $ sudo make install
      $ sudo ldconfig
      ```

   4. libtool 安装

      ```
      curl -OL http://ftpmirror.gnu.org/libtool/libtool-2.4.2.tar.gz
      tar -xzf libtool-2.4.2.tar.gz
      cd libtool-2.4.2
      ./configure && make && sudo make install
      ```

   5. brew 安装相关依赖包：

            参考 ffmpeg 官方文档：https://trac.ffmpeg.org/wiki/CompilationGuide/macOS
          
            ```
            $ brew install automake fdk-aac lame libass libtool libvorbis libvpx libvo-aacenc opencore-amr openjpeg opus sdl schroedinger shtool speex texi2html theora wget x264 xvid yasm
            ```

6. 所有的相关依懒库我们都安装完成了，接下回到ffmpeg编译操作

   ```
   $ ./configure --prefix=/usr/local --enable-gpl --enable-nonfree --enable-libass \
   --enable-libfdk-aac --enable-libfreetype --enable-libmp3lame --enable-libopus \
   --enable-libtheora --enable-libvorbis --enable-libvpx --enable-libx264 --enable-libxvid --extra-ldflags=-L/usr/local/lib
   
   $ make && make install
   ```

   以上额外依赖库说明：以下仅仅只是举例，具体参数请输入`./configure --help`查看:

   > **x264** 编码H.264视频，编译参数--enable-gpl --enable-libx264
   > **fdk-aac** 编码AAC音频，编译参数--enable-libfdk-aac
   > **libvpx** VP8/VP9视频编码器，编译参数--enable-libvpx
   > **libvorbis** 编码Vorbis音频，需要libogg。编译参数--enable-libvorbis
   > **libopus** 编码Opus音频。
   > **LAME** 编码MP3音频，编译参数--enable-libmp3lame
   > **libass** 字幕渲染器，编译参数--enable-libass

7. 至此我们已经编译出了ffmpeg的命令行工具：

   ![](https://blogimage-1257063273.cos.ap-guangzhou.myqcloud.com/20190228111234.png)

提示：在安装依赖库时，在不同Mac OS系统上，可能会报错，特别是新系统版本，我编译过的最高系统版本是：Mac OS 10.14.1



### 附：brew install ffmpeg

```
// 下面的每一个with都是可选的，看你需要那些
$ brew install ffmpeg \
--with-chromaprint \
--with-fdk-aac \
--with-libass \
--with-librsvg \
--with-libsoxr \
--with-libssh \
--with-libbluray \
--with-libbs2b \
--with-libcaca \
--with-libgsm \
--with-libmodplug \
--with-libvidstab \
--with-opencore-amr \
--with-openh264 \
--with-openjpeg \
--with-openssl \
--with-tesseract \
--with-rtmpdump \
--with-rubberband \
--with-sdl2 \
--with-snappy \
--with-tools \
--with-webp \
--with-x265 \
--with-xz \
--with-zeromq \
--with-zimg \
--with-frei0r \
--with-game-music-emu \
--with-filter=drawtext \
--with-freetype \
--with-fontconfig \
--with-tools \
--with-fdk-aac \
--with-freetype \
--with-fontconfig \
--with-libass \
--with-libvorbis \
--with-libvpx \
--with-opus
```

