## 视频捕捉

![AVCaptureMovieFileOutput](http://pbflin9sq.bkt.clouddn.com/6941baebjw1esweq67jinj20h50ae75l.jpg)

### AVFoundation 相关类

AVFoundation 框架基于以下几个类实现图像捕捉 ，通过这些类可以访问来自相机设备的原始数据并控制它的组件。

- `AVCaptureDevice` 是关于相机硬件的接口。它被用于控制硬件特性，诸如镜头的位置、曝光、闪光灯等。

- `AVCaptureDeviceInput` 提供来自设备的数据。

- `AVCaptureOutput` 是一个抽象类，描述 capture session 的结果。以下是三种关于静态图片捕捉的具体子类：
  - `AVCaptureStillImageOutput` 用于捕捉静态图片
  - `AVCaptureMetadataOutput` 启用检测人脸和二维码
  - `AVCaptureVideoOutput` 为实时预览图提供原始帧

- `AVCaptureSession` 管理输入与输出之间的数据流，以及在出现问题时生成运行时错误。

- `AVCaptureVideoPreviewLayer` 是 `CALayer` 的子类，可被用于自动显示相机产生的实时图像。它还有几个工具性质的方法，可将 layer 上的坐标转化到设备上。它看起来像输出，但其实不是。另外，它拥有session (outputs 被 session 所拥有)。





### 初始化

1. 创建数据流协调

   ```objective-c
   //创建数据流协调
   _capSession = [[AVCaptureSession alloc] init];
   //指定输出质量
   _capSession.sessionPreset = AVCaptureSessionPreset1280x720;
   ```

2. 输入设备

   ```objective-c
   //输入设备
   AVCaptureDevice *capDevice = [AVCaptureDevice defaultDeviceWithMediaType:AVMediaTypeVideo];
   ```

3. 设置帧率

   ```objective-c
   //需要先lock，直接设置会崩溃
   BOOL lock = [capDevice lockForConfiguration:&capLockError];
   if (lock) {
       AVCaptureDeviceFormat *format = capDevice.formats.firstObject;
       //设置视频帧率
       //每一帧的时长就是帧速率的倒数，帧速率干脆叫帧频率更准确了，表示摄像头在一秒时间内总共采集多少视频图片帧
       capDevice.activeVideoMaxFrameDuration = CMTimeMake(1, 10);
       //设置完后再解锁
       [capDevice unlockForConfiguration];
   }
   ```

#### 视频帧率设置

> 比如你想要高的帧率，你将需要配置具体的设备格式。一个视频捕获设备有许多设备格式，每个都带有特定的属性和功能，下面是各设备的参考值：

1. Figure 1  Supported AVCaptureDeviceFormat for the back camera.

![](http://pbflin9sq.bkt.clouddn.com/1422856744351931.png)

1. Figure 2  Supported AVCaptureDeviceFormat's for the front camera.

![](http://pbflin9sq.bkt.clouddn.com/1422856783638944.png)

```
格式 = 像素格式
FPS = 支持帧数范围
HRSI = 高像素静态图片尺寸
FOV = 视角
VIS = 该格式支持视频防抖
Upscales = 在某一个数字高标度时使用的变焦因子
AF = 自动对焦系统（1 是反差对焦，2 是相位对焦）
ISO = 支持感光度范围
SS = 支持曝光时间范围
HDR = 支持高动态范围图像
Max Zoom = 最大视频变焦因子
Figure 1  支持后置摄像头AVCaptureDeviceFormat项目
```

4. 音频输入

   ```objective-c
   AVCaptureDevice *audioDevice = [AVCaptureDevice defaultDeviceWithMediaType:AVMediaTypeAudio];
   
   AVCaptureDeviceInput *audioInput = [AVCaptureDeviceInput deviceInputWithDevice:audioDevice error:nil];
       
   AVCaptureAudioDataOutput *audioOutput = [[AVCaptureAudioDataOutput alloc] init];
       [audioOutput setSampleBufferDelegate:self queue:dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0)];
   ```

5. 输入端口管理

   ```objective-c
    //输入端口管理
    NSError *capDevError;
    AVCaptureDeviceInput *capDevInput = [[AVCaptureDeviceInput alloc] initWithDevice:[self
   cameraWithPosition:AVCaptureDevicePositionFront] error:&capDevError];;
    _capDevInput = capDevInput;
    
    // Setup the still image file output
    // 将输出流设置成JPEG的图片格式输出，这里输出流的设置参数AVVideoCodecJPEG参数表示以JPEG的图片格式输出图片
    AVCaptureStillImageOutput *stillImageOutput = [[AVCaptureStillImageOutput alloc] init];
    [stillImageOutput setOutputSettings:@{AVVideoCodecKey : AVVideoCodecJPEG}];
    _stillImageOutput = stillImageOutput;
   
   
   - (AVCaptureDevice *)cameraWithPosition:(AVCaptureDevicePosition)position {
       NSArray *devices = [AVCaptureDevice devicesWithMediaType:AVMediaTypeVideo];
       for (AVCaptureDevice *device in devices) {
           if ([device position] == position) {
               return device;
           }
       }
       return nil;
   }
       
   ```

6. 创建输出流objective-c

   ```objective-c
   AVCaptureVideoDataOutput *videoDataOut = [[AVCaptureVideoDataOutput alloc] init];
       dispatch_queue_t videoDataOutQueue_c = dispatch_queue_create("videoDataOutQueue", DISPATCH_QUEUE_SERIAL);
       [videoDataOut setSampleBufferDelegate:self queue:videoDataOutQueue_c];
       _videoDataOut = videoDataOut;
       videoDataOut.videoSettings = [NSDictionary dictionaryWithObject:
                                      [NSNumber numberWithInt:kCVPixelFormatType_32BGRA]
                                                                 forKey:(id)kCVPixelBufferPixelFormatTypeKey];
   ```

7. 把输入输出设置添加到数据流协调对象

   ```
   [_capSession beginConfiguration];
   if ([self.capSession canAddOutput:stillImageOu
         [self.capSession addOutput:stillImageOut
   }
   if ([_capSession canAddInput:capDevInput]) {
       [_capSession addInput:capDevInput];
   }
   if ([_capSession canAddInput:audioInput]) {
       [_capSession addInput:audioInput];
   }
   if ([_capSession canAddOutput:audioOutput]) {
       [_capSession addOutput:audioOutput];
   }
   if ([_capSession canAddOutput:videoDataOut]) {
       [_capSession addOutput:videoDataOut];
   }
   [_capSession commitConfiguration];
   
   //startRunning
   [_capSession startRunning];
   ```

8. 初始化预览层

   ```objective-c
   _previewLayer = [[AVCaptureVideoPreviewLayer alloc] initWithSession:_capSession];
   [_previewLayer setVideoGravity:AVLayerVideoGravityResizeAspectFill];
   [_previewLayer setFrame:_videoPreviewView.bounds];
   [_videoPreviewView.layer addSublayer:_previewLayer];
   ```

9. AVCaptureVideoDataOutputSampleBufferDelegate

   ```
   - (void)captureOutput:(AVCaptureOutput *)output
   didOutputSampleBuffer:(CMSampleBufferRef)sampleBuffer
          fromConnection:(AVCaptureConnection *)connection {
   
       NSLog(@"%ld",self.videoDataOut.connections.count);
       if (self.videoDataOut.connections.firstObject == connection) {
           //采集视频
   
       }else{
           //采集音频
       }
   }
   ```






## AVCaptureDataOutput 和 AVAssetWriter

如果你想要对影音输出有更多的操作，你可以使用 AVCaptureVideoDataOutput 和 AVCaptureAudioDataOutput 而不是我们上节讨论的 AVCaptureMovieFileOutput。

这些输出将会各自捕获视频和音频的样本缓存，接着发送到它们的代理。代理要么对采样缓冲进行处理 (比如给视频加滤镜)，要么保持原样传送。使用 AVAssetWriter 对象可以将样本缓存写入文件：

![](http://pbflin9sq.bkt.clouddn.com/6941baebjw1esweq5vbykj20h80e6jti.jpg)