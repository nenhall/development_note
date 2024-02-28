AVFoundation框架学习之视频录制基础版

## AVFoundation框架简介
### 在系统中的位置：
![img](https://upload-images.jianshu.io/upload_images/3691932-3a17b0a605bdfa18.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/565)

### 框架中主要得类和功能：
```
AVAnimation  //动画类
AVAsset  //资产类可通过这个类获得图片、文件、媒体库
AssetDownloadTask  //资源下载任务
AVAssetExportSession  //资源导出会话:是一个通过资源文件对象去创建一个指定预设的输出的转码内容会话
AVAssetImageGenerator  //用于截取视频某帧的画面
AVAssetReader  //从资源读取音视频数据
AVAssetReaderOutput  //读取资源文件输出类
AVAssetResourceLoader  //资源文件的加载器会从AVURLAsset和代理方法得到加载的内容
AVAssetTrack  //资源的分轨
AVAssetTrackGroup  //这里面封装了一组资源的分轨
AVAssetTrackSegment  //表示资源分轨的一段
AVAssetWriter  //资源文件写入类
AVAssetWriterInput  //写入文件的输入类
AVAssetDownloadTask  //资源文件下载任务
AVCaptureDevice  //硬件捕获设备类
AVCaptureInput  //从硬件捕获设备获得输入的数据
AVCaptureOutput  //获得输出的数据
AVCaptureSession  //用于调配音视频输入与输出之间的数据流
AVCaptureVideoPreviewLayer  //捕获的视频数据的预览图层
AVMetadataObject  //音视频元数据是一个基类里面包含面部检测的元数据和二维码的元数据
AVPlayer  //音视频播放器
AVPlayerItem  //音视频播放的元素
AVPlayerItemMediaDataCollector  //音视频播放器元素媒体数据收集器
AVPlayerItemOutput  //播放器元素输出类
AVPlayerItemTrack  //播放器元素的分轨
AVPlayerLayer  //播放器的图层
AVPlayerMediaSelectionCriteria  //播放器媒体选择的规范
AVSampleBufferDisplayLayer  //用来显示压缩或解压的视频帧
AVSynchronizedLayer  //同步动画图层
AVTextStyleRule  //文本样式的规范
AVVideoCompositing  //视频合成的协议
AVAudioSettings  //音频的配置信息
AVAudioEngine  //音频引擎
AVAudioNode  //音频节点
AVAudioTime  //音频时间类
AVMIDIPlayer  //MIDI播放器
AVAudioSession  //音频会话
```

### 主要应用场景：
```
* 音频的录制、播放
* 视频的播放
* 媒体文件检查
* 媒体捕捉
* 媒体编辑
* 媒体处理
* 媒体采样
* 媒体压缩
* 音频编解码
* 视频编解码
* 音频的音效处理
```

## 视频流处理

![img](https:////upload-images.jianshu.io/upload_images/3691932-a46c3cc54b405348.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/500)

* AVCaptureDevice  //代表了输入设备,例如摄像头与麦克风。

  > 输入设备，包括麦克风、摄像头，通过该对象可以设置物理设备的一些属性（例如相机聚焦、白平衡等）。

  

* AVCaptureDeviceInput

  > 设备输入数据管理对象，可以根据AVCaptureDevice创建对应AVCaptureDeviceInput对象，该对象将会被添加到AVCaptureSession中管理。

  

* AVCaptureInput  //代表了输入数据源

* AVCaptureOutput  //代表了输出数据源

  > 输出数据管理对象，用于接收各类输出数据，通常使用对应的子类`AVCaptureAudioDataOutput`、`AVCaptureStillImageOutput`、`AVCaptureVideoDataOutput`、`AVCaptureFileOutput`，该对象将会被添加到AVCaptureSession中管理。注意：前面几个对象的输出数据都是`NSData`类型，而AVCaptureFileOutput代表数据以文件形式输出，类似的，AVCcaptureFileOutput也不会直接创建使用，通常会使用其子类：`AVCaptureAudioFileOutput`、AVCaptureMovieFileOutput`。当把一个输入或者输出添加到AVCaptureSession之后AVCaptureSession就会在所有相符的输入、输出设备之间建立连接

  

* AVCaptureSession  //用于协调输入与输出之间的数据流

  > 媒体（音、视频）捕获会话，负责把捕获的音视频数据输出到输出设备中。一个`AVCaptureSession`可以有多个输入输出。 `AVCaptureSession`是`AVFoundation`捕捉类的中心枢纽，在视频捕获时,客户端可以实例化`AVCaptureSession`并添加适当的`AVCaptureInputs`、`AVCaptureDeviceInput`和输出，比如`AVCaptureMovieFileOutput`。通过`[AVCaptureSession startRunning]`开始数据流从输入到输出,和`[AVCaptureSession stopRunning]`停止输出输入的流动。客户端可以通过设置`sessionPreset`属性定制录制质量水平或输出的比特率。

  

* `AVCaptureVideoPreviewLayer`
  > 相机拍摄预览图层，是`CALayer`的子类，使用该对象可以实时查看拍照或视频录制效果，创建该对象需要指定对应的AVCaptureSession对象

  

## 实例

* 创建捕捉会话AVCaptureSession

  ```objective-c
   //捕获会话
  _captureSession = [[AVCaptureSession alloc] init];
  //设置清晰度
  if ([self.captureSession canSetSessionPreset:AVCaptureSessionPreset1280x720]) {
      self.captureSession.sessionPreset = AVCaptureSessionPreset1280x720;
  } else {
      self.captureSession.sessionPreset = AVCaptureSessionPreset640x480;
  }
  ```

* 获取视频设备

  ```objective-c
  //方法一：选择广角前置摄像头，因为默认的就是广角摄像头
  AVCaptureDevice *device = [AVCaptureDevice defaultDeviceWithDeviceType:AVCaptureDeviceTypeBuiltInWideAngleCamera mediaType:AVMediaTypeVideo position:AVCaptureDevicePositionFront];
  
  //方法二：从系统的媒体设备组中查找设备，这种方式得到的设备类型即是：
  //       `deviceType:AVCaptureDeviceTypeBuiltInWideAngleCamera`
  - (AVCaptureDevice *)deviceWithMediaType:(AVMediaType)mediaType position:(AVCaptureDevicePosition)position
  {
      NSArray *devices = [AVCaptureDevice devicesWithMediaType:mediaType];
      AVCaptureDevice *captureDevice = devices.firstObject;
  
      for (AVCaptureDevice *dinput in devices) {
          if (dinput.position == position) {
              captureDevice = dinput;
          }
      }
      return captureDevice;
  }
  
  ```

* 创建媒体视频输入源

  ```objective-c
  NSError *getDeviceInputErr;
  AVCaptureDeviceInput *deviceInput = [[AVCaptureDeviceInput alloc] initWithDevice:device error:&getDeviceInputErr];
  if (getDeviceInputErr) {
      NSLog(@"获取输入设备失败");
      return;
  }
  
  ```

* 创建媒体音频输入源

  ```objective-c
  NSError *getaudioInputErr;
  AVCaptureDevice *audio = [AVCaptureDevice defaultDeviceWithMediaType:AVMediaTypeAudio];
  AVCaptureDeviceInput *audioInput = [AVCaptureDeviceInput deviceInputWithDevice:audio error:&getaudioInputErr];
  if (getaudioInputErr) {
      NSLog(@"获取音频输入设备失败");
  }
  ```

* 创建视频输出对象

  ```ob
  AVCaptureMovieFileOutput *movieFileOutput = [[AVCaptureMovieFileOutput alloc] init];
  
  ////////////////////////以下设置为可选////////////////////////
  //设置链接管理对象
  AVCaptureConnection *captureConnection = [movieFileOutput connectionWithMediaType:AVMediaTypeVideo];
  //设置连接管理对象
  //视频稳定设置
  if ([captureConnection isVideoStabilizationSupported]) {
       captureConnection.preferredVideoStabilizationMode = AVCaptureVideoStabilizationModeAuto;
   }
  //视频旋转方向的设置
  captureConnection.videoScaleAndCropFactor = captureConnection.videoMaxScaleAndCropFactor;
  ```

* 将创建的所有输入、输出源添加到视频捕捉会话对象

  ```objective-c
  [_captureSession beginConfiguration];
  
  if ([_captureSession canAddInput:deviceInp
      [_captureSession addInput:deviceInput]
  }
  
  if ([_captureSession canAddInput:audioInpu
      [_captureSession addInput:audioInput];
  }
  
  if ([_captureSession canAddOutput:movieFileOutput]) {
      [_captureSession addOutput:movieFileOutput];
  }
  
  [_captureSession commitConfiguration];
  ```

* 创建视频预览层

  ```objective-c
  //创建预览层
  AVCaptureVideoPreviewLayer *previewLayer = [[AVCaptureVideoPreviewLayer alloc] initWithSession:_captureSession];
  previewLayer.frame = CGRectMake(0, 0, self.playerLayer.bounds.size.width, self.playerLayer.bounds.size.height);
  previewLayer.connection.videoOrientation = [movieFileOutput connectionWithMediaType:AVMediaTypeVideo].videoOrientation;
  [self.playerLayer.layer addSublayer:previewLayer];
  ```

* 开始预览视频

  ```objective-c
  [_captureSession startRunning];
  ```

* 开始录制

  ```objective-c
  //设置视频存放路径
  NSURL *url = [NSURL fileURLWithPath:[NSString stringWithFormat:@"%@move.mov",NSTemporaryDirectory()]];
  //开始录制视频
  [_movieFileOutput startRecordingToOutputFileURL:url recordingDelegate:self];
  ```

* 停止录制

  ```
  [_movieFileOutput stopRecording];
  ```

### 请求相机权限

```objective-c
- (void)getCameraAuthorization {
    
    AVAuthorizationStatus status = [AVCaptureDevice authorizationStatusForMediaType:AVMediaTypeVideo];
    switch (status) {
        case AVAuthorizationStatusNotDetermined:
        {
            NSLog(@"相机权限：未设置过权限");
            [AVCaptureDevice requestAccessForMediaType:AVMediaTypeVideo completionHandler:^(BOOL granted) {
                
                if (granted) {
                    NSLog(@"请求授权成功");
                    [self createAV];
                } else {
                    NSLog(@"请求授权失败");
                }
            }];
        }
            break;
        case AVAuthorizationStatusRestricted:
        {
            NSLog(@"相机权限：无权访问这项权限");
        }
            break;
        case AVAuthorizationStatusDenied:
        {
            NSLog(@"相机权限：拒绝");
        }
            break;
        case AVAuthorizationStatusAuthorized:
        {
            [self createAV];
            NSLog(@"相机权限：已授权");
        }
            break;
    }
}
```



demo地址：https://github.com/ruqibazao/AVFoundation_note.git