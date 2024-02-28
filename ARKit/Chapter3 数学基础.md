

# 数学基础

## 向量

### 定义：

在编程过程中，我们经常会用（0，0）来表示屏幕上的一个坐标，这个坐标的 X 值和 Y 值都为0，此时这个坐标就称为屏幕空间里的一个点，这个点是空间的维数来决定的，如果是二维空间，那一个点可能是（100，100），但如果是三维空间，那么一个点可能是（100，100，100）。

![向量的基础](https://blog-1257063273.cos.ap-chengdu.myqcloud.com/2020/%E5%90%91%E9%87%8F%E7%9A%84%E5%9F%BA%E7%A1%80.png)

一个坐标仅代表所在空间的位置，没有方向和大小之分，表示点的数值代表这个点距离数轴的大小，并且大小是相对于原点的

向量是一个有方向、有大小、但没有位置的量，在表现方式上和点类似，都是通过多个数值组成的一组数据来表示的：

$$
\begin{pmatrix}
100\\
100\\
\end{pmatrix}
和
\begin{pmatrix}
100\\
100\\
100
\end{pmatrix} \\
排列形式二 \\
\begin{pmatrix}
100, 100 \\
\end{pmatrix}
\begin{pmatrix}
100, 100, 100 \\
\end{pmatrix}
$$

一个是横排列，一个竖提成列，其实就里向量只是书写形式不一样，如果写成横排列形式，向量就叫作“行向量”，竖排形式叫作列向量。在向理的每组数据中，单个数据称为分量，通常用分量的个数来代表向量的维度：二维和三维向量。

### 向量的图形表示

向量在同何中可以表示为带箭头的线段，箭头代表向量的方向，线段的长度代表向量的大小，注意：**点的坐标是相对原点的，但向量的坐标是相对起始点的**，如下图所示，左侧是单个向量，包含起始点和方向，右侧是把多个向量的起始点固定在一个位置，而方向都不同。

![向量表示](https://blog-1257063273.cos.ap-chengdu.myqcloud.com/2020/%E5%90%91%E9%87%8F%E8%A1%A8%E7%A4%BA.png)

在物理学中也称为矢量，一个运行物体的位移、速度、加速度都可以使用矢量来描述，比如，一个人骑自行车向北10km/h 的速度骑行，此时10km/h 就代表一个矢量，既有方向又有大小。

### 向量的模

向量的大小就是它自身的长度，也称为模，向量的标记作 | 𝛎 |，假设我们要求二维向量（100，100）和三维向量（100，100，100）的模，方法如下：

$|(100,100)| =\sqrt{100^2+100^2}\approx 141.42 \\ |(100,100,100)| =\sqrt{100^2+100^2+100^2}\approx 173.21$

通过计算方法也可以看出规律，如果要求向量的 𝛎 的模，则公式为：

$ 𝛎 = |(x_1 \cdots x_n)| = \sqrt{x_1^2+ ⋯ + x_n^2} $

### 向量的运算

1. 加减法：

$$
\begin{pmatrix} x_1 \\ \vdots \\ x_n\end{pmatrix} + \begin{pmatrix} y_1 \\ \vdots \\ y_n\end{pmatrix}=\begin{pmatrix} x_1+y_1 \\ \vdots \\ x_n+y_n\end{pmatrix} \quad\quad\quad\quad\quad\quad\quad
\begin{pmatrix} x_1 \\ \vdots \\ x_n\end{pmatrix} - \begin{pmatrix} y_1 \\ \vdots \\ y_n\end{pmatrix}=\begin{pmatrix} x_1-y_1 \\ \vdots \\ x_n-y_n\end{pmatrix}
$$



上面说的是列向量的运算，如是行向量，则也是相同的运算方法，行向量的加减法如下：
$$
\begin{pmatrix} x_1 \cdots x_n \end{pmatrix}+
\begin{pmatrix} y_1 \cdots y_n \end{pmatrix}=
\begin{pmatrix} x_1 + y_1 \cdots x_n + y_n \end{pmatrix} \\
\begin{pmatrix} x_1 \cdots x_n \end{pmatrix}-
\begin{pmatrix} y_1 \cdots y_n \end{pmatrix}=
\begin{pmatrix} x_1 - y_1 \cdots x_n - y_n \end{pmatrix}
$$

2. 标量的乘除法：

   标量的乘除法的规则为：
   $$
   k \begin{pmatrix} x_1 \\ \vdots \\ x_n \end{pmatrix}=
   \begin{pmatrix}kx_1 \\ \vdots \\ kx_n\end{pmatrix}
   \quad\quad\quad\quad\quad\quad\quad
   \frac{1}{k} 
   \begin{pmatrix} x_1 \\ \vdots \\ x_n \end{pmatrix}=
   \begin{pmatrix} \frac{x_1}{k} \\ \vdots \\ \frac{x_n}{k} \end{pmatrix}
   $$
   行向量的标量乘除法规则为：
   $$
   k\begin{pmatrix} x_1 \cdots x_n \end{pmatrix}=
   \begin{pmatrix} kx_1 \cdots kx_n \end{pmatrix}\\
   \frac{1}{k}\begin{pmatrix} x_1 \cdots x_n \end{pmatrix}=
   \begin{pmatrix} \frac{x_1}{k} \cdots \frac{x_n}{k} \end{pmatrix}\\
   $$
   因为x1、x2、x3…….xn众数为n，而众数kx1、kx2、kx3……. kxn这一组数据中，每一个数都扩大了k倍，故其众数为kn

3. 点积：

   向量之间也可以进行乘法运算，但是和普通标量的乘法运算区别较大，向量的乘法运算分为两种：**点积**和**叉积**。

   **点积（内积）**：又称数量积也称内积，点积有两个计算公式，先看第一个公式：
   $$
   a \cdot b = (a_1, a_2,\cdots, a_n)\cdot(b_1, b_2, \cdots, b_n) = a_1b_1+a_2b_2+\cdots+a_nb_n
   $$
   从公式中可以看到，两个向量的点积运算就是两个向量对应的每个分量相乘并把结果相加，最后会得到一个标量，例如：
   $$
   a \cdot b = (50, 50)\cdot(60, 70) = 3000+3500=6500
   $$
   再来看第二个公式，这个公式与两个向量的角度有关，有两个向量的点积就等于各自的模相乘，最后再与两个向量夹角的$$ \cos0$$值相乘，如下：
   $$
   a \cdot b = \begin{vmatrix} a\end{vmatrix}\begin{vmatrix} b\end{vmatrix}\cos0
   $$
   **叉积（外积）**：说完点积，再来看看叉积。叉积和点积不同的是，其结果还是向量。并且叉积有一个特性，就是只针对三维向量运算，其他维度的向量运算在几何上没有意义，公式为：
   $$
   a \times b = (a_x, a_y, a_z)\times(b_x, b_y, b_z) = (a_yb_z - a_zb_y, a_xb_z - a_xb_z,a_xb_y-a_yb_a)
   $$
   公式看起来复杂，仔细观察，是有规律的。在公式最后推导出来的算式中，第一组的下标是$$_y$$和$$_z$$，第二个下标是$$_z$$和$$_x$$，第三组下标是$$_x$$和$$_y$$

   ![xiangliangchaji](https://blog-1257063273.cos.ap-chengdu.myqcloud.com/2020/xiangliangchaji.png)

### 单位向量

每一个向量都包含一个单位向量，它具有确定的方向，和原向量方向相同，把一个向量转换成单位向量的过程叫**向量归一化**。

对于任意非零向量 a（起点为 A，终点为 B），需要它的单位向量，公式如下：
$$
\to \\
AB =  \frac{AB}{\begin{vmatrix} AB \end{vmatrix}}
$$
例如：
$$
\frac{(50, 50)}{\begin{vmatrix}(50, 50)\end{vmatrix}}=
\frac{(50, 50)}{\sqrt{50_2+50_2}} \approx
\frac{(50, 50)}{70.711} \approx
(\frac{50}{70.711},\frac{50}{70.711}) \approx
(0.707,0.707)
$$
长度为0的向量叫作**零向量**，记作0，零向量的始点和终点重合，所以零向量没有确定的方向，或者说零向量的方向是任意的。

## 矩阵



