# Raw 图片加载方案



## LibRaw API 解析

## 介绍

LibRaw 是一个用于从数码相机读取 RAW 文件的库（CRW/CR2、NEF、RAF、DNG、MOS、KDC、DCR 等；几乎支持所有 RAW 格式）

- RAW数据（*像素值）*
- 处理 RAW 所需的元数据（*几何信息、颜色滤波阵列/拜耳阵列、黑电平、白平衡等）*
- 嵌入预览/缩略图。
- 图片转换

> `几何（geometry）`：一般指的是与相机捕获图像有关的各种几何参数和设置。这些参数决定了相机如何在二维平面上记录三维世界，涉及以下几个方面：视场、焦距、光圈、传感器尺寸、焦点距离、相机位置与姿态、投影模式。
>
> [CFA(color filter array)颜色滤波阵列](https://baike.baidu.com/item/CFA/15918447?fr=ge_ala)：是一种颜色滤波的综合体，它可以去除光谱中的一些成分，使每个像素只保留一个颜色成分。
>
> [黑电平（Black Level）](https://deepinout.com/qcom-camera-tuning/qcom-camera-tuning-black-level-analysis.html)：也称作：Optical Black，很多人也称呼为OB，指的是光学暗区矫正，也就是黑色的最低点，指在经过一定校准的显示装置上，没有一行光亮输出的视频信号电平。
>
> [拜耳阵列（Bayer Pattern）](https://zhuanlan.zhihu.com/p/667045838)：1974年，柯达公司的工程师Bryce Bayer提出了一个全新方案，在图像传感器前面，设置一层彩色滤光片阵列**（Color Filter Array，CFA）** ，有间隔的在每个像素上放置单一颜色的滤镜。 这样，每个通道能得到一个部分值空缺的图片，然后通过各种插值手段填充空缺的值，进而得到彩色图像。
>
> `白平衡`: 就是针对不同色温条件下，通过调整摄像机内部的色彩电路使拍摄出来的影像抵消偏色，更接近人眼的视觉习惯。

## 许可

LibRaw 库免费分发，其开源代码受两个许可证约束：

1. GNU 通用公共许可证版本 2.1
2. 通用开发和分发许可证 (CDDL) 1.0 版



## Raw 图片解码速度优化调研

### 行业解决方案

1. [RawTherapee](https://www.rawtherapee.com/)：GNU3公共许可，raw 图片源数据编辑软件，依赖 [lensfun](https://github.com/lensfun/lensfun)、[GTK](https://www.gtk.org/)，用 [dcraw](https://www.cybercom.net/~dcoffin/dcraw/) 的修补版本来读取原始文件

2. [PhotoFlow](https://github.com/aferrero2707/PhotoFlow/)：是一款开源的 raw 图片编辑软件，也是基于 [rawtherapee](https://github.com/aferrero2707/PhotoFlow/) 、[lensfun](https://github.com/lensfun/lensfun)、[libraw](https://www.libraw.org/)、[art](https://bitbucket.org/agriggio/art/)、[vips](https://www.libvips.org/) 组合解码与渲染
   1. vips 是一个[运行速度快且占用内存少](https://github.com/libvips/libvips/wiki/Speed-and-memory-use)图像处理引擎，[LGPL 2.1+](https://www.gnu.org/licenses/old-licenses/lgpl-2.1.en.html) 许可。ysm - 注：一个非raw图读取、色域转换、基础图片处理等的函数库。
   
3. [ART](http://rawtherapee.com/)：是 rawtherapee 的衍生品，使用更简单且同时仍然保持 rawtherapee 的功能和质量。
   1. 更好的元数据处理（[exiv2](http://exiv2.org/)和[exiftool](http://exiftool.org/)），并且（可选）支持读写 XMP sidecar 文件。
   2. 支持使用 libraw 解码原始文件
   
4. 美图云修：利用 rawtherapee 与 libraw 、GTK 、lensfun 结合使用
   1. libraw 处理缩略图
   
   2. rawtherapee 到16位调色库进行调色再转档
   

rawtherapee 依赖编译：
| 项目        | 版本信息 | 备注                                                         |
| ----------- | -------- | ------------------------------------------------------------ |
| rawtherapee | 5.9      | https://www.rawtherapee.com/                                 |
| libraw      | 0.21.1   | https://www.libraw.org                                       |
| lensfun     |          | https://github.com/lensfun/lensfun Lensfun 是一个开源的镜头校正库 |
| GTK         | 3.16     | GTK是用C语言编写的一个面向对象的部件工具                     |

### 加载速度对比

在苹果 m2 设备上测试：

photoFlow、rawtherapee 、art 都能秒开，基本无感加载

下表是加载二张图片的数据：

1. Sony ARW RGB Display P3 4000 × 6000 49.2 MB 
2. Nikon Electronic RGB Display P3 4016 × 6016 30.2 MB

| 名称        | 加载时间/s | 内存占用 (张/M) | 编辑     | 导出         | 其它                                                         |
| :---------- | :--------- | :-------------- | :------- | :----------- | :----------------------------------------------------------- |
| photoFlow   | 1～2       | 520M / 480M     | 原图编辑 | 原分辨率导出 | 其它格式无差别                                               |
| rawtherapee | 1～2       | 460M / 440M     | 原图编辑 | 原分辨率导出 | 其它格式无差别                                               |
| art         | 1～2       | 1060M / 1030M   | 原图编辑 | 原分辨率导出 | 其它格式无差别                                               |
| 美图秀秀    | 3～8       | 160M / 150M     | 原图编辑 | 原分辨率导出 | 加载时间跟图片格式有关，RAF 格式都慢，支持的格式不全，部份解码失败 |
| 美图云修    | 1          | 小于100M        | 缩略图   | 缩略图       |                                                              |
| Evoto       | 1～2       | 1150M / 1100M   | 原图编辑 | 原分辨率导出 | 其它格式无差别                                               |

索尼原生的是分块加载

暂时无法在飞书文档外展示此内容

### 抉择

1. Libraw 加载缩略图 - 加载缩略图的话 libraw 是最快的，而且尺寸也满足大部份场景（maxsize：1080 × 1616）
2. photoFlow、rawtherapee 、art 开源的都没有单独封装开箱即用的核心的源码模块，需要自己从中把非UI及非app业务代码排除，抽离出核心代码，再把核心代码进行包装成一个 C++ cmake 工程。主要涉及到对 rawtherapee、lensfun(镜头校正) 的源码解读及 GTK基础，最后进行封装 - 大概耗时 40 天。
3. 以上三个开源的 app 都没有代码层面的 API 文档，需要自行探索。

建议：如果在短期内就着急使用，那先用缩略图编辑，因为我们现在的缩略图也是最大 2000，在这期间再进行开发，开发完成再对接上全新的。
