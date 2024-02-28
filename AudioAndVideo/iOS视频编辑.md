1. 创建AVURLAsset 对象获取音频或者视频资源

2. 创建AVMutableComposition实例，用来合成视频的工程文件

   ```
   AVMutableComposition *mixComposition = [[AVMutableComposition alloc] init];
   ```

3. 创建视频通道：音频轨或者视频轨

   ```
   // 视频通道
   AVMutableCompositionTrack *videoTrack = [mixComposition addMutableTrackWithMediaType:AVMediaTypeVideo preferredTrackID:kCMPersistentTrackID_Invalid];
   
   // 获取视频轨道，里面可以插入各种对应的素材                                     
   [videoTrack insertTimeRange:CMTimeRangeFromTimeToTime(startTime, endTime) ofTrack:[[videoAsset tracksWithMediaType:AVMediaTypeVideo] objectAtIndex:0] atTime:kCMTimeZero error:nil];
                            
   //音频通道
   AVMutableCompositionTrack * audioTrack = [mixComposition addMutableTrackWithMediaType:AVMediaTypeAudio preferredTrackID:kCMPersistentTrackID_Invalid];
   
   // 获取音频资源
   AVAssetTrack * audioAssetTrack = [[audioAsset tracksWithMediaType:AVMediaTypeAudio] firstObject];
   
   // 播放音频资源
   [audioTrack insertTimeRange:CMTimeRangeFromTimeToTime(startTime, endTime) ofTrack:audioAssetTrack atTime:kCMTimeZero
   error:nil];
   
   
   ```

4. 
