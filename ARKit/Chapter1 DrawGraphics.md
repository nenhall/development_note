# Chapter1 图形绘制

## 图形绘制的几种方式

1. 使用Core Animation框架，因为Core Animation框架不仅提供了动画类，还提供了显示内容的图层
2. 使用 OpenGL ES框架，因为这个框架提供了一套开放标准的图形绘制库，主要面向游戏开发或者需要高频绘制的App
3. 使用UIWebView累展示基于Web人图形界面
4. 使用系统提供的标准视图，如(多平台)：lists , collections , alerts, images, progress bars, tables
5. 还有一种就是绕过core animation，直接使用core graphics。core graphics是一套强大的绘图引擎，是基于C 语言的一套API，在iOS框架的大部分工作是使用core graphice来完成的

## 绘制几何图层

1. 方式一：使用Core Graphics

   ```swift
   let ctx = UIGraphicsGetCurrentContext()
   // create path
   let path = CGMutablePath.init()
   // 画圆
   path.addArc(center: CGPoint(x: 130, y: 260), radius: 50, startAngle: 0, endAngle: 360,
   clockwise: false)
   // 正方形
   path.move(to: CGPoint(x: 230, y: 250))
   path.addLine(to: CGPoint(x: 280, y: 250))
   path.addLine(to: CGPoint(x: 280, y: 300))
   path.addLine(to: CGPoint(x: 230, y: 300))
   path.addLine(to: CGPoint(x: 230, y: 250))
   // 三角形
   path.move(to: CGPoint(x: 280, y: 160))
   path.addLine(to: CGPoint(x: 220, y: 220))
   path.addLine(to: CGPoint(x: 340, y: 220))
   ctx?.addPath(path)
   ctx?.setFillColor(UIColor.orange.cgColor)
   ctx?.setStrokeColor(UIColor.black.cgColor)
   ctx?.setLineWidth(1.0)
   ctx?.drawPath(using: .fillStroke)
   ```

2. 方式二：使用Core Animation

   ```swift
   let path = UIBezierPath.init()
   // 画圆
   path.addArc(withCenter: CGPoint(x: 130, y: 150), radius: 50, startAngle: 0, endAngle: 360,
   clockwise: true)
   // 四方形
   path.move(to: CGPoint(x: 230, y: 150))
   path.addLine(to: CGPoint(x: 280, y: 150))
   path.addLine(to: CGPoint(x: 280, y: 200))
   path.addLine(to: CGPoint(x: 230, y: 200))
   path.addLine(to: CGPoint(x: 230, y: 150))
   // 三角形
   path.move(to: CGPoint(x: 280, y: 60))
   path.addLine(to: CGPoint(x: 220, y: 120))
   path.addLine(to: CGPoint(x: 340, y: 120))
   // 关闭路径
   path.close()
   let shapeLayer = CAShapeLayer.init()
   shapeLayer.path = path.cgPath
   shapeLayer.lineWidth = 1.0
   shapeLayer.strokeColor = UIColor.black.cgColor
   shapeLayer.fillColor = UIColor.orange.cgColor
   self.layer.addSublayer(shapeLayer)
   ```

## CPU 与 GPU交互

> 任何程序在动时生成的数据都会先存放在堆内存中，然后再依据具体的情况往其它地方存放，而这样同样如此，我们要调用图形框架生成好的图形数据，也需要首先存放在内存中，人硬件的角度来看存放在RAM（Random Access Memory）中，然后CPU不去直接计算渲染它，因为有一个更独立，更强大的计算单元来完成这件事件，就是GPU
>
> CPU运行app过程中生成的图形数据是要向GPU发送并渲染的，而GPU中有一个内存单元专门用来存放图形数据叫：VRAM（Video Random Access Memory），也称显存，从CPU发送来的数据先存放在VRAM中，然后再由GPU独立计算配合CPU完成渲染工作。

1. 装备状态：

   把所有要渲染的数据，可能包含方字、几何图形，此外还有诸如点、曲线、填充区域，如果是在3D建模的情况下，则还可能包含一些复杂的模型数据，如游戏中某个英雄，模型数据就是英雄的属性：肤色、服饰等。当这些数据全部传递到VRAM后，CPU还需要配合GPU来进行一些操作，首先CPU把数据传过去，然后设置好数据的属性，包括颜色、线宽等，接着就可以告诉GPU开始渲染了，GPU收到接令就开始下一步操作

2. 坐标转换：

   GPU在接收到图形数据的同时也接收到渲染指令，这时它会对每个图形的每个坐标点进行转换，从虚拟空间转到屏幕空间，坐标点数据就是我们在编写代码时定义的CGPoint类型数据，因为我们编写代码时把屏幕的左上方为原点，但在openGL转换中，默认屏幕的坐标原点在左下角；描述过程中并不是以屏幕的像素为单位的，而是以点为单位的，坐标系各有不同，GPU在屏幕上渲染自然要转换。

3. 像素计算：

   1. 转换成屏幕坐标之后，我们依据所有坐标点来计算图形每条边的像素坐标，然后以此得出所要显示的图形边界，得出边界后，会逐个查看屏幕上每个像素是否被这个图形覆盖，遍历后会输出整个覆盖区域，此进再把所有覆盖区域所有信息保存下来：

   ![像素计算](https://blog-1257063273.cos.ap-chengdu.myqcloud.com/2020/IMG_5180.JPG)

   

   2. 得到所有覆盖区域的所有信息以后，我们就可以计算覆盖区域内每个像素的纹理信息，并汇总每个像素的颜色值，每个纹理都是一个包含RGBA值的正方形，每一个正方形就是一个像素，在CoreAnimation框架中，一个覆盖区域就一个CAShapeLayer。以这种方式理解，每一个覆盖区域可能会被重叠，也可能会被彼此孤立，此时对于屏幕上的每一个像素，GPU都需要计算在混合情况下的色值。计算结束后就剩下最把所有值放入一个缓冲区，供屏幕显示使用。

   3. 硬件显示：
   
      首先假设我们使用的显示器是基于阴极射线管（CRT）设计的，之前的老式大头显示器就是基于它设计的，这种显示器原理其实很简单，它通过一个叫作电子枪的部件来发射出电子，经过处理后变成电子束，然后用电子束去轰击屏幕表面涂抹的荧光粉，最后利用荧光粉被轰击会变亮的特性来显示的图像，当把所有的色值放入缓冲区后电子枪发射出来的电子束在屏幕上按点移动时，从缓冲区中取出刚放入的色值和其他信息。当然如果是LED、LCD这种非CRT技术的屏幕，那无非就是显示原理不一样，本质上还是不断地从缓冲中取值，不断刷新显示状态，最后呈现出来的就是一幅动态图



