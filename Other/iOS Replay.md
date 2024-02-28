# iOS Replay屏幕录制的学习与实践

## 实现方式：

1.  iOS 8 及以前系统：

   * 实现：iOS8系统不提供相关SDK，开发者只能通过一些trick的方式（例如通过破解苹果用于无线传输的airplay协议，使用协议的私有api相关功能），实现屏幕共享的直播。

   * 问题：私有 api，无法上架

2. iOS 9系统：

   * 实现：iOS9系统考虑到开发者在屏幕录制共享方面需求，禁用了之前被开发者使用的实现屏幕共享的私有api，提供了ReplayKit SDK，并且通过这个SDK, 开发者可以将当前app中的操作屏幕画面录制下来，完成后可以进行查看、编辑、通过指定方式分享出去。

   * 问题：该方案只能将当前app（而不是整个手机上）的屏幕内容录制下来，并且无法将实时的音视频流提供出来，只能将最终录制完毕的整个mp4文件提供给开发者，所以实际上并非真正的屏幕的直播共享，无法保证实时性。

     ```swift
     if replayStatus {// 停止
         RPScreenRecorder.shared().stopRecording { (previewViewController, error) in
             if let previewViewController = previewViewController {
                 self.present(previewViewController, animated: true, completion: nil)
             }
         }
         replayStatus = false
     } else { // 开始
         RPScreenRecorder.shared().delegate = self
         RPScreenRecorder.shared().startRecording(withMicrophoneEnabled: true) { (error) in
             debugPrint("startRecording1.error:", error.debugDescription)
         }
         replayStatus = true
     }
     ```

3. iOS 10系统：

   * 实现：iOS 10系统推出了用户屏幕共享内容广播的`extension`，分别是`broadcast upload extension`和`broadcast setup UI extension`两个`extensions`。添加之后开发者可将内容广播的功能作为系统的扩展添加到列表中，供其他app使用，其他app在请求内容广播时，系统会弹出支持这些extensions的列表，选择某个extension后将由这个extension来处理后续对音视频流的处理和分发。
   * 问题：虽然iOS 10 系统解决了之前系统的一系列弊病，但是仍然没能解决只能录制当前app的屏幕内容的问题，这样会限制一些应用的使用场景。

4. iOS 11系统：

   * 实现：针对之前提供的ReplayKit存在的一些问题，iOS提供了ReplayKit2这个升级的SDK,最重大的升级就是解决了iOS10系统中无法录制整个手机屏幕内容（只录制当前app）的弊病，并且进一步提高集成sdk和实现屏幕录制直播的可用性。
   * 问题：虽然ReplayKit2已经可以满足开发者的多数需求，但是对于用户来说操作太复杂，对手机系统操作不熟悉的用户来说，门槛很高。需要用户提前在手机设置中配置出屏幕录制的访问控制权限，使屏幕录制按钮显示在系统的上拉管理菜单中，并且在录制时，上拉底部菜单调出快捷管理菜单，并且长按屏幕录制圆形按钮才能开始录制和直播。

5. iOS12系统：

   * 实现：苹果WWDC 2018全球开发者大会宣布iOS12系统将要正式发布，大会在“ live screen broadcast with replaykit ”主题演讲中对iOS12系统将升级的ReplayKit2 SDK做了重点描述，其中提到将对iOS11系统中的ReplayKit2问题进行优化，使开发者可以通过接口直接启动屏幕录制，并完成直播
   * 问题：完美解决

