SDWebImage ：github托管地址<https://github.com/rs/SDWebImage>

### 一、功能简介：

1. 一个添加了web图片加载和缓存管理的UIImageView分类

2. 一个异步图片下载器

3. 一个异步的内存加磁盘综合存储图片并且自动处理过期图片

4. 支持动态gif图

5. 支持webP格式的图片

6. 后台图片解压处理

7. 确保同样的图片url不会下载多次

8. 确保伪造的图片url不会重复尝试下载

9. 确保主线程不会阻塞

    

### 二、SDWebImage 加载图片的流程

1. 入口 `setImageWithURL:placeholderImage:options: `会先把 `placeholderImage` 显示，然后 `SDWebImageManager` 根据 URL 开始处理图片

2. 进入 `SDWebImageManager-downloadWithURL:delegate:options:userInfo:`，交给 `SDImageCache` 从缓存查找图片是否已经下载 `queryDiskCacheForKey:delegate:userInfo:`

   1. 先从内存图片缓存查找，如果内存中已经有图片缓存，`SDImageCacheDelegate` 回调 `imageCache:didFindImage:forKey:userInfo: `到 `SDWebImageManager`
   2. `SDWebImageManagerDelegate` 回调 `webImageManager:didFinishWithImage:` 到 UIImageView+WebCache 等前端展示图片

3. 如果内存缓存中没有，生成 `NSInvocationOperation` 添加到队列开始从硬盘查找图片是否已经缓存

   1. 根据 URLKey 在硬盘缓存目录下尝试读取图片文件。这一步是在 NSOperation 进行的操作，所以回主线程进行结果回调 `notifyDelegate:`
   2. 如果上一操作从硬盘读取到了图片，将图片添加到内存缓存中（如果空闲内存过小，会先清空内存缓存）。`SDImageCacheDelegate` 回调 `imageCache:didFindImage:forKey:userInfo:`；进而回调展示图片

4. 如果从硬盘缓存目录读取不到图片，说明所有缓存都不存在该图片，需要下载图片，回调 `imageCache:didNotFindImageForKey:userInfo:`

5. 共享或重新生成一个下载器 `SDWebImageDownloader` 开始下载图片

6. `connectionDidFinishLoading:` 数据下载完成后交给 `SDWebImageDecoder` 做图片解码处理

   1. 图片解码处理在一个 `NSOperationQueue` 完成，不会拖慢主线程 UI。如果有需要对下载的图片进行二次处理，可以在这里完成，效率会好很多。
   2. 在主线程 `notifyDelegateOnMainThreadWithInfo: `宣告解码完成，`imageDecoder:didFinishDecodingImage:userInfo:` 回调给 SDWebImageDownloader

7. `imageDownloader:didFinishWithImage:` 回调给 SDWebImageManager 告知图片下载完成

8. 通知所有的 downloadDelegates 下载完成，回调给需要的地方展示图片

9. 将图片保存到 SDImageCache 中，内存缓存和硬盘缓存同时保存。写文件到硬盘也在以单独 NSInvocationOperation 完成，避免拖慢主线程

   

### 三、图片缓存策略： (不缓存，内存缓存，沙盒缓存)

1. SDImageCache是怎么做数据管理的？

   1. SDImageCache分两个部分，一个是内存层面的，一个是硬盘层面的。

   2. 内存层面的相当是个缓存器，以Key-Value的形式存储图片。当内存不够的时候会清除所有缓存图片。

   3. 用搜索文件系统的方式做管理，文件替换方式是以时间为单位，剔除时间大于一周的图片文件

   4. 当SDWebImageManager向SDImageCache要资源时，先搜索内存层面的数据，如果有直接返回，没有的话去访问磁盘，将图片从磁盘读取出来，然后做Decoder，将图片对象放到内存层面做备份，再返回调用层。

   5. 具体代码：

      1. Memory Cache：

      ```objective-c
      @interface SDImageCache ()
      #pragma mark - Properties
      @property (strong, nonatomic, nonnull) NSCache *memCache;
      // 这里我们发现， 有一个叫做 memCache 的属性，它是一个 NSCache 对象，用于实现我们对图片的 Memory Cache。
      // NSCache 简单来说，它是一个类似于 NSDictionary 的集合类，用于在内存中存储我们要缓存的数据。详细信息大家可以参考官方文档：https://developer.apple.com/reference/foundation/nscache
      ```

      ```objective-c
      // SDWebImage 还专门实现了一个叫做 AutoPurgeCache 的类， 相比于普通的 NSCache， 它提供了一个在内存紧张时候释放缓存的能力：
      // 接受系统的内存警告通知，然后清除掉自身的图片缓存
      @interface AutoPurgeCache : NSCache
      @end
      @implementation AutoPurgeCache
      - (nonnull instancetype)init {
          self = [super init];
          if (self) {
      #if SD_UIKIT
              [[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(removeAllObjects) name:UIApplicationDidReceiveMemoryWarningNotification object:nil];
      #endif
          }
          return self;
      }
      ```

      2.  Disk Cache：

         也就是文件缓存，SDWebImage 会将图片存放到 NSCachesDirectory 目录中，然后为每一个缓存文件生成一个 md5 文件名, 存放到文件中。

         

2. Disk 缓存清理策略

   1. SDWebImage 会在每次 APP 结束和程序切到后台的时候执行清理任务。 清理缓存的规则分两步进行。 第一步先清除掉过期的缓存文件。 如果清除掉过期的缓存之后，空间还不够。 那么就继续按文件时间从早到晚排序，先清除最早的缓存文件，直到剩余空间达到要求。

   2. 具体点，SDWebImage 是怎么控制哪些缓存过期，以及剩余空间多少才够呢？ 通过两个属性：

      ```objective-c
      @interface SDImageCache : 
      NSObject@property (assign, nonatomic) NSInteger maxCacheAge;//文件缓存的时长
      @property (assign, nonatomic) NSUInteger maxCacheSize;//允许的最大缓存空间
      
      //  maxCacheAge 的默认值
      static const NSInteger kDefaultCacheMaxCacheAge = 60 * 60 * 24 * 7; // 1 week
      // maxCacheSize 的默认值
      [SDImageCache sharedImageCache].maxCacheSize = 1024 * 1024 * 50;	// 50M
      ```

      

### 四、图片Decoder

1. 为啥必须做Decoder?

   1. 首先来了解下几种图片格式的区别(png, jpg, gif ,webp,bitMap等等)：

      1. jpeg :

         1. 有损压缩格式, 将像素信息用jpeg保存成文件再读取出来，其中某些像素值会有少许变化。
         2. 没有透明信息
         3. jpeg比较适合用来存储相机拍摄出来的图片

      2. png :

         1. 是一种无损压缩格式：因为它们使用的 (主要是基于游程长度编码的) 压缩算法可以减少存储需求。这种压缩是无损的，这意味着图像质量不会被压缩过程影响
         2. 可以有透明效果
         3. 比较适合矢量图,几何图

         (矢量格式的一大优点就是缩放.矢量格式的图像其实是一组绘图命令.这些指令通常是独立于尺寸的.如果你想要扩大一个圆形,只需在绘制之前扩大他的半径就可以了)

      3. bitMap(位图):

         1. bmp格式没有压缩像素格式
         2. 存储在文件中时先有文件头、再图像头、后面就都是像素数据了，上下颠倒存储
         3. 最早接触到bitMap是在imageView的layer.shouldRasterize

   2. CALayer的shouldRasterize和离屏渲染(offscreen rendering)：

      1. 离屏渲染消耗性能原因是显卡需要另外alloc一块内存来进行渲染,渲染完毕后在绘制到当前屏幕,而且对于显卡来说,onscreen到offscreen的上下文环境切换是非常昂贵的(涉及到OpenGL的pipelines和barrier等).

      2. 开启shouldRasterize后,CALayer会被光栅化为bitmap,layer的阴影等效果也会被保存到bitmap中.

         更新已光栅化的layer,会造成大量的offscreen渲染.

      3. 当你设置图片的圆角属性的时候，会触发GPU的离屏渲染，这个过程消耗性能。当设置这个属性( shouldRasterize )后为YES后 the layer is rendered as a bitmap in its local coordinate space and then composited to the destination with any other content. rendered as a bitmap这个过程是由CPU完成的，等下次使用时不会再重新去渲染了，直接使用bitmap。如果不设置这个属性为YES，the layer is composited directly into the destination whenever possible.造成离屏渲染

      参考文章[Advanced Graphics and Animations for iOS Apps](https://link.jianshu.com/?t=https://github.com/100mango/zen/blob/master/WWDC%E5%BF%83%E5%BE%97%EF%BC%9AAdvanced%20Graphics%20and%20Animations%20for%20iOS%20Apps/Advanced%20Graphics%20and%20Animations%20for%20iOS%20Apps.md)

   3. 图片的在内存中的大小

      1. 图片在内存中占用的大小 跟图片自身的大小没有关系

      2. 内存中占用的大小 = 图片的宽度 * 图片的高度*每个像素占用的字节数

      3. 在SDWebImage中衡量大图的标准：图片的像素总数 > 60M内存所存储的像素数 ? 压缩 : 不压缩

         1. [苹果官方的加载大图demo](https://link.jianshu.com/?t=https://developer.apple.com/library/ios/samplecode/LargeImageDownsizing/Introduction/Intro.html)
         2. 这个里面的大图处理逻辑个SDWebImage 是完全一样的,介绍更详细,建议看苹果官方的

         3. 基本原理:也就是原图按照定好的大小(像素).来对原图进行切块,然后再一块块的绘制到destContext
         4. SDWebImage部份可以参考：`+ (nullable UIImage *)decodedAndScaledDownImageWithImage:(nullable UIImage *)image`函数

   4. png,jpeg格式的数据是不能直接使用的,需要将其转化为位图

   5. 当我们使用imageView显示图片的时候:
      1. 读取图片
      2. 解压图片为位图(消耗CPU)
      3. 如果位图数据不是字节对齐的，CoreAnimation会copy一份位图数据并进行字节对齐
      4. CoreAnimation渲染解压缩过的位图
      5. 这一切在IOS中都是默认发生在主线程成的并且是在`UIImageView`执行`setImage`方法的时候完成的

   6. 补充点：

      1. `+(nullableUIImage*)imageNamed:(NSString*)name`：不适合加载大的 不常用的图片.因为它会默认在程序里保存这张图片数据(不会随ImageView的移除而移除).只有经常使用图片适合这种方式加载.
      2. `+ (nullableUIImage*)imageWithContentsOfFile:(NSString*)path`：这个方法跟上面的略有不同,他不会在内存中保留一份数据.只要imageView移除,内存中的数据就会直接移除.这也就是这个方法为什么适合加载大的图片,但却不常用的图片.
      3. 相关的参考文章：[IOS异步图片加载与常用的优化](https://link.jianshu.com/?t=https://segmentfault.com/a/1190000002776279)

2. 解码部份的代码：

   ```objective-c
   // 用来说明每个像素占用内存多少个字节，在这里是占用4个字节
   static const size_t kBytesPerPixel = 4;
   /** 表示每一个组件占多少位
    比方说RGBA，其中R（红色）G（绿色）B（蓝色）A（透明度）是4个组件，每个像素由这4个组件组成，那么我们就用8位来表示着每一个组件，所以这个RGBA就是8*4 = 32位。
    */
   static const size_t kBitsPerComponent = 8;
   
   /**
    知道了kBitsPerComponent和每个像素有多少组件组成就能计算kBytesPerPixel了。计算公式是：(bitsPerComponent * number of components + 7)/8.
   */
   
   + (nullable UIImage *)decodedImageWithImage:(nullable UIImage *)image {
     	/** 判断要不要解码
     	并不是所有的image都要解码的。可以来看看shouldDecodeImage:这个函数：
     	不适合解码的条件为：
   		1. image为nil
   		2. animated images 动图不适合
   		3. 带有透明因素的图像不适合
     	*/
       if (![UIImage shouldDecodeImage:image]) {
           return image;
       }
      
       // autorelease the bitmap context and all vars to help system to free memory when there are memory warning.
       // on iOS7, do not forget to call [[SDImageCache sharedImageCache] clearMemory];
       @autoreleasepool{
           
           CGImageRef imageRef = image.CGImage;
         	// 颜色空间
           CGColorSpaceRef colorspaceRef = [UIImage colorSpaceForImageRef:imageRef];
           
           size_t width = CGImageGetWidth(imageRef);
           size_t height = CGImageGetHeight(imageRef);
         	// 计算出每行的像素数 
           size_t bytesPerRow = kBytesPerPixel * width;
   
           // kCGImageAlphaNone is not supported in CGBitmapContextCreate.
           // Since the original image here has no alpha info, use kCGImageAlphaNoneSkipLast
           // to create bitmap graphics contexts without alpha info.
         	// 创建没有透明因素的bitmap graphics contexts
           CGContextRef context = CGBitmapContextCreate(NULL,
                                                        width,
                                                        height,
                                                        kBitsPerComponent,
                                                        bytesPerRow,
                                                        colorspaceRef,
                                                        kCGBitmapByteOrderDefault|kCGImageAlphaNoneSkipLast);
           if (context == NULL) {
               return image;
           }
           
           // Draw the image into the context and retrieve the new bitmap image without alpha
         	// 绘制图像
           CGContextDrawImage(context, CGRectMake(0, 0, width, height), imageRef);
           CGImageRef imageRefWithoutAlpha = CGBitmapContextCreateImage(context);
           UIImage *imageWithoutAlpha = [UIImage imageWithCGImage:imageRefWithoutAlpha
                                                            scale:image.scale
                                                      orientation:image.imageOrientation];
           
           CGContextRelease(context);
           CGImageRelease(imageRefWithoutAlpha);
           
           return imageWithoutAlpha;
       }
   }
   ```

   

3. 总结：结合上面五点总结下为什么要解码：

   > 其实不解码也是可以使用的，假如说我们通过`imageNamed:`来加载image，系统默认会在主线程立即进行图片的解码工作。这一过程就是把image解码成可供控件直接使用的位图。
   >
   > 当在主线程调用了大量的`imageNamed:`方法后，就会产生卡顿了。为了解决这个问题我们有两种比较简单的处理方法：
   >
   > 1. 我们不使用`imageNamed:`加载图片，使用其他的方法，比如`imageWithContentsOfFile:`
   > 2. 我们自己解码图片，可以把这个解码过程放到子线程

   #### 好处：

   1. 把图片解码这个默认在主线程执行,耗损CPU的行为,放在了后台线程.

   2. 只需要在使用的时候,直接setImage,不会有太大的CPU消耗

      
   #### 弊端：

   1. 这样解码就是以空间换时间的方法,提前解压好,用的时候直接从内存读取.
   2. 如果下载的图片比较大,然后直接解码的话 这个是内存所不能承受,需要对图片进行压缩.



### 五、部份API说明：

1. 所有类的作用介绍一下(3.8.1版本)

   ```objective-c
   SDImageCache  //缓存  定义了 Disk  和  memory二级缓存（NSCache）负责管理cache  单例
   SDWebImageManager  //核心管理类 主要对缓存管理 + 下载管理进行了封装  主要接口downloadImageWithURL单利
   SDWebImageDownloader //异步图片下载管理，管理下载队列，管理operation 管理网络请求 处理结果和异常，单例
   SDWebImageCompat  //保证不同平台/版本/屏幕等兼容性的宏定义和内联 图片缩放
   SDWebImageDecoder  //图片解压缩，内部只有一个接口
   存放网络请求回调的block  //自己理解的数据结构大概是
   //  结构{"url":[{"progress":"progressBlock"},{"complete":"completeBlock"}]}
   
   SDWebImageDownloaderOperation //实现了异步下载图片的NSOperation，网络请求给予NSURLSession 代理下载,自定义的Operation任务对象，需要手动实现start cancel等方法
   
   SDWebImageOperation  //operation协议  只定义了cancel operation这一接口 上面的downloaderOperation的代理
   
   SDWebImagePrefetcher //低优先级情况下预先下载图片，对SDWebImageViewManager进行简单封装 很少用
   MKAnnotationView+WebCache  //为MKAnnotationView异步加载图片
   NSData+ImageContentType  //通过Image data判断当前图片的格式
   UIButton+WebCache  //为UIButton异步加载图片
   UIImage+GIF  //将Image data转换成指定格式图片
   UIImage+MultiFormat //将image data转换成指定格式图片
   UIImageView+HighlightedWebCache //为UIImageView异步加载图片
   UIImageView+WebCache  //为UIImageView异步加载图片
   UIView+WebCacheOperation  //保存当前MKAnnotationView / UIButton / UIImageView异步下载图片的operations
   ```

   

2. SDWebImageOptions 所有选项

```objective-c
//默认情况下，当一个url下载失败，如果URL在黑名单中那么SDWebImage库不进行重试。这个标志使黑名单失效。
SDWebImageRetryFailed = 1 << 0,

//默认情况下，在UI交互中开始图片下载，这个标志使这个特性失效，例如导致在UIScrollView减速中延迟下载
SDWebImageLowPriority = 1 << 1,

//只进行内存缓存,这个标志使硬盘缓存失效
SDWebImageCacheMemoryOnly = 1 << 2,

//这个标志可以渐进式下载,显示的图像是逐步在下载
SDWebImageProgressiveDownload = 1 << 3,

//刷新缓存
SDWebImageRefreshCached = 1 << 4,

//后台下载，如果后台任务时间过期那么操作将会被取消
SDWebImageContinueInBackground = 1 << 5,

//通过设置NSMutableURLRequest来操作cookies保存到NSHTTPCookieStore
//NSMutableURLRequest.HTTPShouldHandleCookies = YES;
SDWebImageHandleCookies = 1 << 6,

//允许使用无效的SSL证书,测试目的是有效的。在生产环境被警告
SDWebImageAllowInvalidSSLCertificates = 1 << 7,

//默认情况下，图片在队列中排队下载。这个标志移动他们到前面的队列中
SDWebImageHighPriority = 1 << 8,

//延迟占位符
SDWebImageDelayPlaceholder = 1 << 9,

//我们通常在动画图片中不调用transformDownloadedImage代理，大部分的变形代码将损坏图片。使用这个标志在任何情况下变形图片
SDWebImageTransformAnimatedImage = 1 << 10
  
//默认情况下，图片是在下载完成后加载到图片视图；使用这个标志，如果你想在下载成功后在完成块中手动设置图片。
SDWebImageAvoidAutoSetImage = 1 << 11,

// 默认情况下，图片解码为原始的大小。在iOS，这个标志会把图片缩小到与设备的受限内容相兼容的大小。如果设置了SDWebImageProgressDownload标志，那么缩小被设置为无效。
SDWebImageScaleDownLargeImages = 1 << 12,

// 默认情况下，当图像缓存在内存中时，我们不查询磁盘数据。 此掩码可以强制同时查询磁盘数据。
// *建议将此标志与`SDWebImageQueryDiskSync`一起使用，以确保图像在同一个runloop中加载。
SDWebImageQueryDataWhenInMemory = 1 << 13,

// 默认情况下，我们同步查询内存缓存，异步查询磁盘缓存。 此掩码可以强制同步查询磁盘缓存，以确保在同一个runloop中加载映像。
// *如果禁用内存缓存或在某些其他情况下，此标志可以避免在单元重用期间闪烁。
SDWebImageQueryDiskSync = 1 << 14,

// 默认情况下，当缓存丢失时，将从网络下载映像。 此标志可以阻止网络仅从缓存加载。
SDWebImageFromCacheOnly = 1 << 15,

// 默认情况下，在图像加载完成后使用“SDWebImageTransition”进行某些视图转换时，此转换仅适用于从网络下载图像。 此掩码也可以强制为内存和磁盘缓存应用视图转换。
SDWebImageForceTransition = 1 << 16
```

[结构图](https://blogimage-1257063273.cos.ap-guangzhou.myqcloud.com/SDWebImageStructureChart.jpeg)

![结构图](https://blogimage-1257063273.cos.ap-guangzhou.myqcloud.com/SDWebImageStructureChart.jpeg)

[类API](https://blogimage-1257063273.cos.ap-guangzhou.myqcloud.com/SDWebImageClassDiagram.png)

![类API](https://blogimage-1257063273.cos.ap-guangzhou.myqcloud.com/SDWebImageClassDiagram.png)





参考：[一张图片引发的深思](http://honglu.me/2016/09/02/%E4%B8%80%E5%BC%A0%E5%9B%BE%E7%89%87%E5%BC%95%E5%8F%91%E7%9A%84%E6%B7%B1%E6%80%9D/?utm_source=tuicool&utm_medium=referral)