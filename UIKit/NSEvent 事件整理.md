# NSEvent 事件整理

## class NSEvent : NSObject

### 相关属性

* var type: enum NSEvent.EventType   // 响应者对象处理的事件的类型。
  * case leftMouseDown   // 用户按下鼠标左键。
  * case leftMouseUp   // 用户释放了鼠标左键。
  * case rightMouseDown   // 用户按下鼠标右键。
  * case rightMouseUp   // 用户释放了鼠标右键。
  * case mouseMoved   // 用户以导致光标在屏幕上移动的方式移动了鼠标。
  * case leftMouseDragged   // 用户在按住鼠标左键的同时移动了鼠标。
  * case rightMouseDragged   // 用户在按住鼠标右键的同时移动了鼠标。
  * case mouseEntered   // 光标进入定义明确的区域，例如视图。
  * case mouseExited   // 光标退出定义明确的区域，例如视图。
  * case keyDown   // 用户按下键盘上的一个键。
  * case keyUp   // 用户释放了键盘上的键。
  * case flagsChanged   // 事件标志已更改。
  * case appKitDefined   // 发生与AppKit相关的事件。
  * case systemDefined   // 发生了与系统相关的事件。
  * case applicationDefined   // 发生了应用定义的事件。
  * case periodic   // 为定期任务提供执行时间的事件。
  * case cursorUpdate   // 一个更新游标的事件。
  * case scrollWheel   // 滚轮位置已更改。
  * case tabletPoint   // 用户触摸了平板电脑上的一个点。
  * case tabletProximity   // 指示设备靠近但未触摸关联的平板电脑。
  * case otherMouseDown   // 用户按下了第三级鼠标按钮。
  * case otherMouseUp   // 用户释放了第三级鼠标按钮。
  * case otherMouseDragged   // 用户在按住第三级鼠标按钮的同时移动了鼠标。
  * case gesture   // 用户执行了非特定类型的手势。
  * case magnify   // 用户执行捏开或捏合手势。
  * case swipe   // 用户执行了轻扫手势。
  * case rotate   // 用户执行了旋转手势。
  * case beginGesture   // 标记手势开始的事件。
  * case endGesture   // 标记手势结束的事件。
  * case smartMagnify   // 用户执行了智能缩放手势。
  * case pressure   // 报告压敏设备上压力变化的事件。
  * case directTouch   // 用户触摸了触摸条的一部分。
  * case quickLook   // 引发快速查找请求的事件。
  * case changeMode   // 用户更改了所连接设备的模式。
* var subtype: enum NSEvent.EventSubtype  // 各种事件的子类型。
  * case windowExposed // 窗口曝露
  * case applicationActivated  // 应用程序已激活
  * case applicationDeactivated  // 应用停用（退到后台等）
  * case windowMoved  // 窗口移动
  * case screenChanged  // 屏幕已更改
  * case touch  // 触摸
  * static var mouseEvent: NSEvent.EventSubtype  // 鼠标事件
  * static var powerOff: NSEvent.EventSubtype  // 电源关闭
  * static var tabletPoint: NSEvent.EventSubtype  // 平板电脑点
  * static var tabletProximity: NSEvent.EventSubtype  // 平板电脑接近度
* var modifierFlags: NSEvent.ModifierFlags   // 一个整数位字段，指示事件的修改键。
* struct NSEvent.EventTypeMask   // 用于从传入事件流中过滤出特定事件类型的常量，内部有NSEvent.EventType 的所有常量属性
* var locationInWindow: NSPoint   // 接收者在相关窗口的基本坐标系中的位置。
* var timestamp: TimeInterval   // 自系统启动以来，事件发生的时间（以秒为单位）。
* var window: NSWindow?   // 与事件关联的窗口对象。
* var windowNumber: Int   // 与事件关联的窗口设备的标识符。

* var eventRef: UnsafeRawPointer?   // 与该事件关联的不透明Carbon类型。

* var cgEvent: CGEvent?   // 与此事件对应的Core Graphics事件对象。

#### 获取关键事件信息

* class var modifierFlags: NSEvent.ModifierFlags   // 返回当前按下的修饰符标志。
  * public static var capsLock: NSEvent.ModifierFlags { get } // Set if Caps Lock key is pressed.
  * public static var shift: NSEvent.ModifierFlags { get } // Set if Shift key is pressed.
  * public static var control: NSEvent.ModifierFlags { get } // Set if Control key is pressed.
  *  public static var option: NSEvent.ModifierFlags { get } // Set if Option or Alternate key is pressed.
  * public static var command: NSEvent.ModifierFlags { get } // Set if Command key is pressed.
  * public static var numericPad: NSEvent.ModifierFlags { get } // Set if any key in the numeric keypad is pressed.
  * public static var help: NSEvent.ModifierFlags { get } // Set if the Help key is pressed.
  * public static var function: NSEvent.ModifierFlags { get } // Set if any function key is pressed.
  * public static var deviceIndependentFlagsMask: NSEvent.ModifierFlags { get }

* struct NSEvent.ModifierFlags   // 表示事件对象中关键状态的标志。

* class var keyRepeatDelay: TimeInterval   // 返回按住键重复事件时长，第一个键重复事件的时间长度。

* class var keyRepeatInterval: TimeInterval   // 返回发布的后续按键重复事件之间的长度。

* var characters: String?   // 与向上或向下事件关联的字符。

* var charactersIgnoringModifiers: String?   // 按键事件生成的字符，就好像没有修饰键（Shift键除外）一样适用。

* var isARepeat: Bool   // 一个布尔值，指示键事件是否为重复事件。

* var keyCode: UInt16   // 与按键事件关联的键盘按键的虚拟按键代码。

* class var pressedMouseButtons: Int   // 返回当前按下的鼠标按钮的索引。

* class var doubleClickInterval: TimeInterval   // 返回以秒为单位的时间，在此时间内必须进行第二次鼠标单击才能被视为双击。

* class var mouseLocation: NSPoint   // 报告当前鼠标在屏幕坐标中的位置。

* var buttonNumber: Int   // 鼠标事件的按钮号。

* var clickCount: Int   // 与鼠标向下或鼠标向上事件关联的鼠标单击次数。

* var associatedEventsMask: NSEvent.EventTypeMask   // 鼠标事件的关联事件掩码。

* var eventNumber: Int   // 最新鼠标或跟踪矩形事件对象的计数器值；每个系统生成的鼠标和跟踪矩形事件都会递增此计数器。

* var trackingNumber: Int   // 鼠标跟踪事件的标识符。

* var trackingArea: NSTrackingArea?   // 跟踪区域，NSTrackingArea 事件的对象。

* var userData: UnsafeMutableRawPointer?   // 与鼠标跟踪事件关联的数据。

* var data1: Int   // 与该事件关联的其他数据。

* var data2: Int   // 与该事件关联的其他数据。

* var deltaX: CGFloat   // 鼠标移动，鼠标拖动和滑动事件的x坐标更改。

* var deltaY: CGFloat   // 鼠标移动，鼠标拖动和滑动事件的y坐标更改。

* var deltaZ: CGFloat   // 滚轮，鼠标移动或鼠标拖动事件的z坐标更改。

#### 获取压力信息

* var pressure: Float   // 从到的值表示施加到适当输入设备的压力程度。0.0 - 1.0

* var stage: Int    // 值0，1或者2，指示类型的手势事件的阶段。NSEvent.EventType.pressure

* var stageTransition: CGFloat   // 类型为的压力手势事件的阶段的过渡值。NSEvent.EventType.pressure

* var pressureBehavior: NSEvent.PressureBehavior   // 事件的压力行为和级数。NSEvent.EventType.pressure

* enum NSEvent.PressureBehavior   // 这些常数描述了压力手势的行为和进程。



