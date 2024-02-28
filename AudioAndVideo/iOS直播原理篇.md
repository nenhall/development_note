![](https://upload-images.jianshu.io/upload_images/5210585-fdd31a3fcbc965dd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000)

* rtmp协议 ：实时消息传输协议,Adobe Systems公司为Flash播放器和服务器之间音频、视频和数据传输开发的开  放协议，因为是开放协议所以都可以使用了。RTMP协议用于对象、视频、音频的传输。这个协议建立在TCP协议或者轮询HTTP协议之上。RTMP协议就像一个用来装数据包的容器，这些数据可以是FLV中的视音频数据

  

* HLS:由Apple公司定义的用于实时流传输的协议,HLS基于HTTP协议实现，传输内容包括两部分，一是M3U8描述文件，二是TS媒体文件。可实现流媒体的直播和点播，主要应用在iOS系统，HLS是以点播的技术方式来实现直播,HLS是自适应码率流播，客户端会根据网络状况自动选择不同码率的视频流，条件允许的情况下使用高码率，网络繁忙的时候使用低码率，并且自动在二者间随意切换。这对移动设备网络状况不稳定的情况下保障流畅播放非常有帮助。

  实现方法是服务器端提供多码率视频流，并且在列表文件中注明，播放器根据播放进度和下载速度自动调整

* 

* HLS与RTMP对比:HLS主要是延时比较大，RTMP主要优势在于延时低

  HLS协议的小切片方式会生成大量的文件，存储或处理这些文件会造成大量资源浪费

  相比使用RTSP协议的好处在于，一旦切分完成，之后的分发过程完全不需要额外使用任何专门软件，普通的网络服务器即可，大大降低了CDN边缘服务器的配置要求，可以使用任何现成的CDN

  

* HTTP-FLV:基于HTTP协议流式的传输媒体内容。

  相对于RTMP，HTTP更简单和广为人知，内容延迟同样可以做到1~3秒，打开速度更快，因为HTTP本身没有复杂的状态交互。所以从延迟角度来看，HTTP-FLV要优于RTMP

* RTSP:实时流传输协议,定义了一对多应用程序如何有效地通过IP网络传送多媒体数据.

* RTP:实时传输协议,RTP是建立在UDP协议上的，常与RTCP一起使用，其本身并没有提供按时发送机制或其它服务质量（QoS）保证，它依赖于低层服务去实现这一过程。

* RTCP:RTP的配套协议,主要功能是为RTP所提供的服务质量（QoS）提供反馈，收集相关媒体连接的统计信息，例如传输字节数，传输分组数，丢失分组数，单向和双向网络延迟等等

* 关于协议的选择方面：即时性要求较高或有互动需求的可以采用RTMP,RTSP；对于有回放或跨平台需求的，推荐使用HLS



视频封装格式：

TS: 一种流媒体封装格式，流媒体封装有一个好处，就是不需要加载索引再播放，大大减少了首次载入的延迟，如果片子比较长，mp4文件的索引相当大，影响用户体验

为什么要用TS:这是因为两个TS片段可以无缝拼接，播放器能连续播放

FLV: 一种流媒体封装格式,由于它形成的文件极小、加载速度极快，使得网络观看视频文件成为可能,因此FLV格式成为了当今主流视频格式





```objective-c
AVBufferRef *buf;  //缓冲包数据存储位置。
int64_t pts;       //显示时间戳，以AVStream->time_base为单位。
int64_t dts;       //解码时间戳，以AVStream->time_base为单位。
uint8_t *data;     //压缩编码的数据。
int   size;        //data的大小。
int   stream_index;//给出所属媒体流的索引。
int   flags;       //标识域，其中，最低位置1表示该数据是一个关键帧。
AVPacketSideData *side_data;  //容器提供的额外数据包。
int64_t duration;   //Packet持续的时间，以AVStream->time_base为单位。
int64_t pos;        //表示该数据在媒体流中的字节偏移量。
```

```
输入文件操作：
avformat_open_input()：打开输入文件，初始化输入视频码流的AVFormatContext。
av_read_frame()：从输入文件中读取一个AVPacket。
 
输出文件操作：
avformat_alloc_output_context2()：初始化输出视频码流的AVFormatContext。
avformat_new_stream()：创建输出码流的AVStream。
avcodec_copy_context()：拷贝输入视频码流的AVCodecContex的数值t到输出视频的AVCodecContext。
avio_open()：打开输出文件。
avformat_write_header()：写文件头（对于某些没有文件头的封装格式，不需要此函数。比如说MPEG2TS）。
av_interleaved_write_frame()：将AVPacket（存储视频压缩码流数据）写入文件。
av_write_trailer()：写文件尾（对于某些没有文件头的封装格式，不需要此函数。比如说MPEG2TS）。
```

