[toc]

## 认识NSLayoutConstraint

**定义**：基于约束的布局系统必须满足的两个用户界面对象之间的关系，每个约束都是线性方程。



### 品赏Autolayout

### 代码设置约束

![image-20210330112523525](/Users/nenhall/Library/Application Support/typora-user-images/image-20210330112523525.png)

***添加约束之前，一定要先把 `view`添加到 `spuerview`上，否则无效，如果是iOS平台上还会直接崩溃***

### Autolayout解读

| 属性             | 含义                                                         |
| ---------------- | ------------------------------------------------------------ |
| top              | 顶部                                                         |
| bottom           | 底部                                                         |
| leading (left)   | 左边：代表左边公限于当前语言中文字的阅读顺序，习惯从左到右，从右到左阅读语言中表示右边 |
| trailing (right) | 右边：代表右边公限于当前语言中文字的阅读顺序，习惯从左到右，从右到左阅读语言中表示左边 |
| width            | 宽                                                           |
| height           | 高                                                           |
| center X         | 水平方向的中心                                               |
| center Y         | 垂直方向的中心                                               |
| baseline         | 文字的基线位置：只用在Text 相关控件，用来确定文字底部位置    |

### Xib中设置约束

<img src="https://developer.apple.com/library/archive/documentation/UserExperience/Conceptual/AutolayoutPG/Art/view_formula_2x.png" alt="image: ../Art/view_formula.pdf" style="zoom:67%;" />

| Equation     | Symbol                  |
| ------------ | ----------------------- |
| Item 1       | myView                  |
| Attribute 1  | leadingAnchor           |
| Relationship | constraintEqualToAnchor |
| Multiplier   | None (defaults to 1.0)  |
| Item 2       | margins                 |
| Attribute 2  | leadingAnchor           |
| Constant     | None (defaults to 0.0)  |

#### 制造约束方法

```swift
class func constraints(withVisualFormat: String, options: NSLayoutConstraint.FormatOptions, metrics: [String : Any]?, views: [String : Any]) -> [NSLayoutConstraint]
```

#### 约束相关属性

```swift

var firstItem: AnyObject?
第一个参与约束的对象。
var firstAttribute: NSLayoutConstraint.Attribute
参与约束的第一个对象的属性。
var relation: NSLayoutConstraint.Relation
约束中两个属性之间的关系。
var secondItem: AnyObject?
第二个参与约束的对象。
var secondAttribute: NSLayoutConstraint.Attribute
参与约束的第二个对象的属性。
var multiplier: CGFloat
应用于参与约束的第二个属性的乘数。
var constant: CGFloat
常量添加到参与约束的乘法第二个属性中。
var firstAnchor: NSLayoutAnchor<AnyObject>
定义约束的第一个锚。
var secondAnchor: NSLayoutAnchor<AnyObject>?
定义约束的第二个锚。
```
#### 获得布局优先级
```swift
var priority: UILayoutPriority
约束的优先级。
struct UILayoutPriority
布局优先级用于向基于约束的布局系统指示哪些约束更重要，允许系统在满足整个系统的约束时做出适当的权衡。
static let required: UILayoutPriority
必要的约束。
static let defaultHigh: UILayoutPriority
按钮拒绝压缩其内容的优先级。
static let defaultLow: UILayoutPriority
按钮水平拥抱其内容的优先级。
static let fittingSizeLevel: UILayoutPriority
视图希望符合该计算中目标大小的优先级。
struct NSLayoutConstraint.Priority
布局优先级用于指示约束的相对重要性，允许Auto Layout在满足整个系统的约束时做出适当的权衡。
```

#### 常量

```swift
enum NSLayoutConstraint.Relation
约束中第一个属性和修改后第二个属性之间的关系。
enum NSLayoutConstraint.Attribute
对象视觉表示中应用于获取约束值的部分。
struct NSLayoutConstraint.FormatOptions
一个位掩码，它既指定要对齐的接口元素的一部分，也指定两个接口元素之间对齐的方向。
enum NSLayoutConstraint.Orientation
约束用于在对象之间强制布局的布局约束方向，无论是水平还是垂直。
enum NSLayoutConstraint.Axis
指定对象之间水平或垂直布局约束的键。
struct NSEdgeInsets
描述两个矩形边缘之间的距离。
```

#### 安全区域

<img src="https://docs-assets.developer.apple.com/published/dbcc36bfb3/e5aca39a-f9a2-4ab8-9f45-08fd95fb845c.png" alt="日历应用程序中的安全区域" style="zoom:67%;" />



## 创建约束方式
#### Layout Anchors

```swift
// Creating layout anchors
// Get the superview's layout
let margins = view.layoutMarginsGuide
 
// Pin the leading edge of myView to the margin's leading edge
myView.leadingAnchor.constraint(equalTo: margins.leadingAnchor).isActive = true
 
// Pin the trailing edge of myView to the margin's trailing edge
myView.trailingAnchor.constraint(equalTo: margins.trailingAnchor).isActive = true
 
// Give myView a 1:2 aspect ratio
myView.heightAnchor.constraint(equalTo: myView.widthAnchor, multiplier: 2.0).isActive = true
```

#### NSLayoutConstraint Class
```swift
// Directly Instantiating Constraints
NSLayoutConstraint(item: myView, attribute: .leading, relatedBy: .equal, toItem: view, attribute: .leadingMargin, multiplier: 1.0, constant: 0.0).isActive = true
 
NSLayoutConstraint(item: myView, attribute: .trailing, relatedBy: .equal, toItem: view, attribute: .trailingMargin, multiplier: 1.0, constant: 0.0).isActive = true
 
NSLayoutConstraint(item: myView, attribute: .height, relatedBy: .equal, toItem: myView, attribute:.width, multiplier: 2.0, constant:0.0).isActive = true
```


#### Visual Format Language
```swift
// Creating constraints with the Visual Format Language
let views = ["myView" : myView]
let formatString = "|-[myView]-|"
 
let constraints = NSLayoutConstraint.constraints(withVisualFormat: formatString, options: .alignAllTop, metrics: nil, views: views)
 
NSLayoutConstraint.activate(constraints)
```

[Visual Format Language 官方文档](https://developer.apple.com/library/archive/documentation/UserExperience/Conceptual/AutolayoutPG/VisualFormatLanguage.html#//apple_ref/doc/uid/TP40010853-CH27-SW1)



## 基本控件的内容收缩及扩张

#### 收缩及扩张

基本控件的内容，有两个特定的约束，即内容收缩(content hugging)约束和内容扩张(content compression)约束，这两个约束简称CHCR。

* 内容收缩(content hugging)：当控件装载的内容小于控件本身的预设值时，控件是否跟据内容自动收缩；
* 内容扩张(content compression)：当控件装载的内容大于于控件本身的预设值时，控件是否跟据内容自动扩张；
* 当有多个控件的约束相互依赖时，由以上两属性的优先级值来决定优化收缩或者扩张那个控件；

#### 内容控件

内容控件即用来承载文字、图片等具体内容的控件，如NSLabel、NSButton、NSTextField、NSImageView

内容控件可以省去设置其宽和高，系统会自动根据其内容的大小来收缩和扩张

IntrinsicContentSize：对于文本/图片等一些视图控件，可以通过其内在的内容推算出控件的大小，但并不是所有的控件都有这个属性。`Button`、`Label`、`TextField`、`TextView`、`ImageView`都可以根据内在的内容计算控件的大小。



## 实战

#### 内容控件相互依赖约束，以`NSLabel`为例:

<img src="/Users/nenhall/Library/Application Support/typora-user-images/image-20210330163239947.png" alt="image-20210330163239947" style="zoom:50%;" />

<img src="/Users/nenhall/Library/Application Support/typora-user-images/image-20210330163257902.png" alt="image-20210330163257902" style="zoom:50%;" />

#### 多view等宽/高

<img src="/Users/nenhall/Library/Application Support/typora-user-images/image-20210330115827022.png" alt="image-20210330115827022" style="zoom:60%;" />

<img src="/Users/nenhall/Library/Application Support/typora-user-images/image-20210330115132671.png" alt="image-20210330115132671" style="zoom:60%;" />

<img src="/Users/nenhall/Library/Application Support/typora-user-images/image-20210330114905166.png" alt="image-20210330114905166" style="zoom:50%;" />

<img src="/Users/nenhall/Library/Application Support/typora-user-images/image-20210330122815384.png" alt="image-20210330122815384" style="zoom:60%;" />

## 高级约束实战

#### 等间隙约束

1. 确定主按钮Y位置：
   1. 设置最左边按钮上、左间隙；
   2. 设置中间按钮的垂直居中等于最左边按钮；
   3. 设置最右边按钮上、右间隙。
   4. 按钮的宽和高可以给固定值也可让其自动伸缩
2. 拖入占位辅助空视图：
   1. 在两按钮中间拖入空视图；
   2. 确认占位视图的左右分别约束两边按钮的左右，按需设定间隙值；
   3. 确认占位视图的高及Y；
   4. 设置两占位视图等宽。
3. 如果是垂直排序布的，一样的方式，把Y变成X，占位视图的等宽变成等高

![image-20210330160224375](/Users/nenhall/Library/Application Support/typora-user-images/image-20210330160224375.png)

![image-20210330161832074](/Users/nenhall/Library/Application Support/typora-user-images/image-20210330161832074.png)

![image-20210330161852654](/Users/nenhall/Library/Application Support/typora-user-images/image-20210330161852654.png)



#### 滚动视图自动约束

滚动视图与其它视图不一样，滚动视图本身的宽或高是固定的，只有内部的内容视图的大小是根据内容自动变化的，所以宽或者高是不能固定的；

如下设置垂直方向滚动的约束，下图中标红的即是内部的内容视图：

* 首先确定`ScrollView`本身的 X、Y、Width、Height

* 垂直方向滚动：设置内容视图的X和Y及宽度，高度不要设置，再分别设置内容控件（下图是个label）的上、下、左、右距离内容控件的间隙值；
* 水平方向滚动：设置内容视图的X和Y及高度，宽度不要设置，再分别设置内容控件（下图是个label）的上、下、左、右距离内容控件的间隙值；
* 双向滚动：设置内容视图的X和Y，宽度、高度不要设置，再分别设置内容控件（下图是个label）的上、下、左、右距离内容控件的间隙值；

![image-20210330170309622](/Users/nenhall/Library/Application Support/typora-user-images/image-20210330170309622.png)



## NSStackView比例布局控件

#### 特性：

* 不需要写任何代码，就能实现等宽/高/间隙自动布局
* 支持子级视图相邻的视图隐藏后自动填充补位
* 专为等比例约束而生，支持横向、竖向



## 网络常用的第三方布局框架 Masonry/SnapKit

都是基于Autolayout的封装

Masonry也支持等宽高/间隙自动布局；

SnapKit本身不支持，但网络上有开发者对它二次此功能扩展；

```objective-c
/**
 ``* distribute with fixed spacing
 ``*
 ``* @param axisType   横排还是竖排
 ``* @param fixedSpacing 两个控件间隔
 ``* @param leadSpacing 第一个控件与边缘的间隔
 ``* @param tailSpacing 最后一个控件与边缘的间隔
 ``*/
- (void)mas_distributeViewsAlongAxis:(MASAxisType)axisType withFixedSpacing:(CGFloat)fixedSpacing leadSpacing:(CGFloat)leadSpacing tailSpacing:(CGFloat)tailSpacing;
/**
 ``* distribute with fixed item size
 ``*
 ``* @param axisType    横排还是竖排
 ``* @param fixedItemLength 控件的宽或高
 ``* @param leadSpacing   第一个控件与边缘的间隔
 ``* @param tailSpacing   最后一个控件与边缘的间隔
 ``*/
- (void)mas_distributeViewsAlongAxis:(MASAxisType)axisType withFixedItemLength:(CGFloat)fixedItemLength leadSpacing:(CGFloat)leadSpacing tailSpacing:(CGFloat)tailSpacing;
```