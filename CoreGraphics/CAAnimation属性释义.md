CAAnimation属性释义： 
首先要明白动画有四个阶段： 
t1(动画被加到layer上的一刻) > t2(动画开始的一刻) > t3(动画结束的一刻) > t4(画从layer上移除的一刻) 
fillMode：

1 
kCAFillModeForwards 
2 
//向前填充：让动画在结束到动画从layer上移除这一段时间的效果，保持动画最后的状态，即toValue这种状态。 
3 
 
4 
kCAFillModeBackwards 
5 
//向后填充：让动画在添加到layer后到动画开始这一段时间的效果，保持动画开始的状态，即fromValue这种状态。 
6 
 
7 
kCAFillModeBoth 
8 
// 既想向前填充又想向后填充，那么使用：kCAFillModeBoth 
9 
 
10 
kCAFillModeRemoved 
11 
//Defaults 模式，