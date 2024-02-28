# macOS开发之事件处理

## 鼠标和键盘事件

### 事件分发过程

OSX 与用户交互的主要外设是鼠标，键盘。鼠标键盘的活动会产生底层系统事件。这个事件首先传递到IOKit框架处理后存储到队列，通知Window Server服务层处理。Window Server存储到FIFO先进先出队列中，逐一转发到当前的活动窗口或能响应这个事件的应用程序去处理。

每个应用都有自己的Main Run Loop线程，Run Loop会遍历event消息队列，逐一分发这些事件到应用中合适的对象去处理。具体来说就是调用NSApp 的 sendEvent:方法发送消息到NSWindow，NSWindow再分发到NSView视图对象，由其鼠标或键盘事件响应方法去处理

![事件分发过程](https://blog-1257063273.cos.ap-chengdu.myqcloud.com/2020/v6WsG7.jpg)

