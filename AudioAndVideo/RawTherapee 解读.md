# RawTherapee 解读

## Scope

RawTherapee 是一款功能强大的跨平台原始图像处理程序，根据 GNU 通用公共许可证版本 3 发布。由 Gábor Horváth 于 2005 年启动，于 2010 年作为开源软件发布，此后一直由国际团队开发。RawTherapee 拥有一套广泛的工具，专门用于处理照片。它与光栅图形编辑器（例如 Photoshop 或 GIMP）以及数字资产管理器（例如 digiKam）配合使用效果非常好。

## 内存要求

要在编辑器中打开图像，RawTherapee 5.6 大约需要这么多 RAM（以字节为单位）：

- Non-raw
  - 8 位：`(width * height * 3) + (width * height * 4) + (previewWidth * previewHeight * 28)`
  - 16 位：`(width * height * 3 * 2) + (width * height * 4) + (previewWidth * previewHeight * 28)`
  - 32 位：`(width * height * 3 * 4) + (width * height * 4) + (previewWidth * previewHeight * 28)`
- Raw
  - `(width * height * 4) + (width * height * 4) + (width * height * 12) + (previewWidth * previewHeight * 28)`

另外还需要一些开销内存，例如用于生成驻留在打开的图像文件夹中的其他图像的缩略图。

处理和保存图像的内存要求取决于您使用的工具，并且可能与上述内容有很大不同 - 上述内容仅适用于打开图像。

## RGB 和 L\*a\*b*

> L\*a\*b\*(或简称“*Lab*”）是两种不同的[颜色空间](https://en.wikipedia.org/wiki/Color_space)或描述颜色的方式。

想知道调整 RGB 色彩空间中的亮度、对比度和饱和度，或调整 Lab 色彩空间中的亮度、对比度和色度之间有什么区别。RGB 在三个通道上运行：红色、绿色和蓝色。Lab 是将相同信息转换为亮度分量 L* 和两个颜色分量 - a* 和 b*。亮度与颜色分开，这样您就可以调整其中一个而不影响另一个。“亮度”旨在近似人类视觉，人类视觉对绿色非常敏感，但对蓝色不太敏感。如果您在实验室空间中进行提亮，则结果在颜色方面通常看起来更正确。一般来说，我们可以说，当在 Lab 空间中使用正值饱和度滑块时，颜色会显得更加“新鲜”，而在 RGB 中使用相同量的饱和度会使颜色看起来“更温暖”。



**下图是 RGB 和 Lab 调整的比较**

![image-20240105155224357](/Users/meitu/Library/Application Support/typora-user-images/image-20240105155224357.png)

结论：

1. *[“曝光”](http://rawpedia.rawtherapee.com/Exposure)*部分（在 RGB 空间中）中的*“亮度”*滑块与*[“Lab”](http://rawpedia.rawtherapee.com/Lab_Adjustments)*部分中的*“亮度”*滑块之间的差异很细微。RGB*亮度设置为 +30 所生成的图像总体上比使用 Lab **亮**设置为 +30时要亮一些。*Lab Lightness*中的颜色更加饱和。
2. 对比度: 对比度滑块则相反；当使用 +45 的 RGB*对比度*时，颜色明显比使用 +45 的 Lab*对比度*时更暖。两种设置的对比度本身大致相同。
3. *“饱和度*/*色度”*滑块，将“RGB*饱和度”*滑块设置为 -100 会渲染似乎应用了红色滤镜的黑白图像，而“Lab*色度*”滑块则会渲染更加中性的黑白图像。正的 RGB*饱和度*值将导致色调偏移（值越大，偏移越明显），而正的Lab*色度*值将在保持色调正确的同时增强颜色，呈现清晰干净的结果。



## 代码结构

1. **核心处理引擎（Core Engine）：** 这是RawTherapee的主要部分，负责处理RAW图像文件。它包含了图像处理的核心算法，例如去噪、锐化、色彩校正等。这些功能模块被组织在一起，以确保高质量的图像处理。
2. **用户界面（User Interface）：** 这个模块负责与用户的交互，包括图形用户界面（GUI）和命令行界面（CLI）。用户可以通过这个模块调整图像参数、导入和导出图像，以及执行其他与用户交互相关的操作。
3. **文件读写（File I/O）：** 这个模块负责处理各种图像文件格式，特别是RAW格式。它包含读取RAW图像文件、导出处理后的图像等功能。
4. **配置管理（Configuration Management）：** 这个模块用于管理用户的配置和设置，确保用户的首选项和选项在不同会话中得到保留。
5. **图像显示（Image Display）：** 负责将处理后的图像显示在用户界面上，以便用户可以实时查看他们的调整效果。
6. **批处理（Batch Processing）：** 允许用户对一组图像进行相同的处理，提高效率。
7. **图像参数调整（Image Adjustments）：** 这包括对图像进行各种调整的模块，如曝光、对比度、饱和度、色温等。
8. **局部调整（Local Adjustments）：** 允许用户对图像的特定区域进行局部调整，例如局部曝光、局部对比度等。



相关链接：http://rawpedia.rawtherapee.com/Main_Page