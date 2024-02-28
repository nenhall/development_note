### 1. 基本语法

#### 1.1 行内公式

- 正文(inline)中的LaTeX公式用`$...$`定义
- 语句为`$\sum_{i=0}^N\int_{a}^{b}g(t,i)\text{d}t$`
- 这是行内公式: ![\sum_{i=0}^N\int_{a}^{b}g(t,i)\text{d}t](https://math.jianshu.com/math?formula=%5Csum_%7Bi%3D0%7D%5EN%5Cint_%7Ba%7D%5E%7Bb%7Dg(t%2Ci)%5Ctext%7Bd%7Dt)

#### 1.2 行间公式

- 单独显示(display)的LaTeX公式用`$$...$$`定义，此时公式居中并放大显示
- 语句为`$$\sum_{i=0}^N\int_{a}^{b}g(t,i)\text{d}t$$`
- 这是行间公式: ![\sum_{i=0}^N\int_{a}^{b}g(t,i)\text{d}t](https://math.jianshu.com/math?formula=%5Csum_%7Bi%3D0%7D%5EN%5Cint_%7Ba%7D%5E%7Bb%7Dg(t%2Ci)%5Ctext%7Bd%7Dt)

### 2. 希腊字母

| 显示 |    命令    | 显示 |   命令   |
| :--: | :--------: | :--: | :------: |
|  α   |  `\alpha`  |  β   | `\beta`  |
|  γ   |  `\gamma`  |  δ   | `\delta` |
|  ε   | `\epsilon` |  ζ   | `\zeta`  |
|  η   |   `\eta`   |  θ   | `\theta` |
|  ι   |  `\iota`   |  κ   | `\kappa` |
|  λ   | `\lambda`  |  μ   |  `\mu`   |
|  ν   |   `\nu`    |  ξ   |  `\xi`   |
|  π   |   `\pi`    |  ρ   |  `\rho`  |
|  σ   |  `\sigma`  |  τ   |  `\tau`  |
|  υ   | `\upsilon` |  φ   |  `\phi`  |
|  χ   |   `\chi`   |  ψ   |  `\psi`  |
|  ω   |  `\omega`  |      |          |

- 若需要大写希腊字母，将命令首字母大写即可。
  `\Gamma` ![\Gamma](https://math.jianshu.com/math?formula=%5CGamma)
- 若需要斜体希腊字母，将命令前加上var前缀即可。
  `\varGamma` ![\varGamma](https://math.jianshu.com/math?formula=%5CvarGamma)

### 3. 字母修饰

##### 3.1 上下标

- 上标：`^`
- 下标：`_`

举例: `C_n^2` 显示为 ![C_n^2](https://math.jianshu.com/math?formula=C_n%5E2)

##### 3.2 矢量

`\vec a` 显示为: ![\vec a](https://math.jianshu.com/math?formula=%5Cvec%20a)
`\overrightarrow{xy}` 显示为: ![\overrightarrow{xy}](https://math.jianshu.com/math?formula=%5Coverrightarrow%7Bxy%7D)

##### 3.3 字体

- Typewriter:`\mathtt{A}`显示为
  - ![\mathtt{A}\mathtt{B}\mathtt{C}\mathtt{D}\mathtt{E}\mathtt{F}\mathtt{G}\mathtt{H}\mathtt{I}\mathtt{J}\mathtt{K}\mathtt{L}\mathtt{M}\mathtt{N}\mathtt{O}\mathtt{P}\mathtt{Q}\mathtt{R}\mathtt{S}\mathtt{T}\mathtt{U}\mathtt{V}\mathtt{W}\mathtt{X}\mathtt{Y}\mathtt{Z}](https://math.jianshu.com/math?formula=%5Cmathtt%7BA%7D%5Cmathtt%7BB%7D%5Cmathtt%7BC%7D%5Cmathtt%7BD%7D%5Cmathtt%7BE%7D%5Cmathtt%7BF%7D%5Cmathtt%7BG%7D%5Cmathtt%7BH%7D%5Cmathtt%7BI%7D%5Cmathtt%7BJ%7D%5Cmathtt%7BK%7D%5Cmathtt%7BL%7D%5Cmathtt%7BM%7D%5Cmathtt%7BN%7D%5Cmathtt%7BO%7D%5Cmathtt%7BP%7D%5Cmathtt%7BQ%7D%5Cmathtt%7BR%7D%5Cmathtt%7BS%7D%5Cmathtt%7BT%7D%5Cmathtt%7BU%7D%5Cmathtt%7BV%7D%5Cmathtt%7BW%7D%5Cmathtt%7BX%7D%5Cmathtt%7BY%7D%5Cmathtt%7BZ%7D)
- Blackboard Bold:`\mathbb{A}`显示为
  - ![\mathbb{A}\mathbb{B}\mathbb{C}\mathbb{D}\mathbb{E}\mathbb{F}\mathbb{G}\mathbb{H}\mathbb{I}\mathbb{J}\mathbb{K}\mathbb{L}\mathbb{M}\mathbb{N}\mathbb{O}\mathbb{P}\mathbb{Q}\mathbb{R}\mathbb{S}\mathbb{T}\mathbb{U}\mathbb{V}\mathbb{W}\mathbb{X}\mathbb{Y}\mathbb{Z}](https://math.jianshu.com/math?formula=%5Cmathbb%7BA%7D%5Cmathbb%7BB%7D%5Cmathbb%7BC%7D%5Cmathbb%7BD%7D%5Cmathbb%7BE%7D%5Cmathbb%7BF%7D%5Cmathbb%7BG%7D%5Cmathbb%7BH%7D%5Cmathbb%7BI%7D%5Cmathbb%7BJ%7D%5Cmathbb%7BK%7D%5Cmathbb%7BL%7D%5Cmathbb%7BM%7D%5Cmathbb%7BN%7D%5Cmathbb%7BO%7D%5Cmathbb%7BP%7D%5Cmathbb%7BQ%7D%5Cmathbb%7BR%7D%5Cmathbb%7BS%7D%5Cmathbb%7BT%7D%5Cmathbb%7BU%7D%5Cmathbb%7BV%7D%5Cmathbb%7BW%7D%5Cmathbb%7BX%7D%5Cmathbb%7BY%7D%5Cmathbb%7BZ%7D)
- Sans Serif: `\mathsf{A}`显示为
  - ![\mathsf{A}\mathsf{B}\mathsf{C}\mathsf{D}\mathsf{E}\mathsf{F}\mathsf{G}\mathsf{H}\mathsf{I}\mathsf{J}\mathsf{K}\mathsf{L}\mathsf{M}\mathsf{N}\mathsf{O}\mathsf{P}\mathsf{Q}\mathsf{R}\mathsf{S}\mathsf{T}\mathsf{U}\mathsf{V}\mathsf{W}\mathsf{X}\mathsf{Y}\mathsf{Z}](https://math.jianshu.com/math?formula=%5Cmathsf%7BA%7D%5Cmathsf%7BB%7D%5Cmathsf%7BC%7D%5Cmathsf%7BD%7D%5Cmathsf%7BE%7D%5Cmathsf%7BF%7D%5Cmathsf%7BG%7D%5Cmathsf%7BH%7D%5Cmathsf%7BI%7D%5Cmathsf%7BJ%7D%5Cmathsf%7BK%7D%5Cmathsf%7BL%7D%5Cmathsf%7BM%7D%5Cmathsf%7BN%7D%5Cmathsf%7BO%7D%5Cmathsf%7BP%7D%5Cmathsf%7BQ%7D%5Cmathsf%7BR%7D%5Cmathsf%7BS%7D%5Cmathsf%7BT%7D%5Cmathsf%7BU%7D%5Cmathsf%7BV%7D%5Cmathsf%7BW%7D%5Cmathsf%7BX%7D%5Cmathsf%7BY%7D%5Cmathsf%7BZ%7D)

##### 3.4 分组

- 使用`{}`将具有相同等级的内容扩入其中，成组处理
- 举例: `10^{10}` 呈现为 ![10^{10}](https://math.jianshu.com/math?formula=10%5E%7B10%7D)，而 `10^10` 显示为 ![10^10](https://math.jianshu.com/math?formula=10%5E10)

##### 3.5 括号和分隔符

- `()`、`[]`和`|`表示符号本身。
- 当要显示大号的括号或分隔符时，要用` \left` 和` \right`命令。 使括号大小和邻近的公式相适应，适应于所有括号。
  - `(\frac{x}{y})` 显示为 ![(\frac{x}{y})](https://math.jianshu.com/math?formula=(%5Cfrac%7Bx%7D%7By%7D))
  - `\left(\frac{x}{y}\right)` 显示为 ![\left(\frac{x}{y}\right)](https://math.jianshu.com/math?formula=%5Cleft(%5Cfrac%7Bx%7D%7By%7D%5Cright))
- 一些特殊的括号：

|       命令        |   说明   |                      输入                      |                             显示                             |
| :---------------: | :------: | :--------------------------------------------: | :----------------------------------------------------------: |
| `\langle \rangle` |  尖括号  |             `\langle a+b \rangle`              | ![\langle a+b \rangle](https://math.jianshu.com/math?formula=%5Clangle%20a%2Bb%20%5Crangle) |
|  `\lceil \rceil`  | 上方括号 |              `\lceil a+b \rceil`               | ![\lceil a+b \rceil](https://math.jianshu.com/math?formula=%5Clceil%20a%2Bb%20%5Crceil) |
| `\lfloor \rfloor` | 下方括号 |             `\lfloor a+b \rfloor`              | ![\lfloor a+b \rfloor](https://math.jianshu.com/math?formula=%5Clfloor%20a%2Bb%20%5Crfloor) |
| `\lbrace \rbrace` |  大括号  |             `\lbrace a+b \rbrace`              | ![\lbrace a+b \rbrace](https://math.jianshu.com/math?formula=%5Clbrace%20a%2Bb%20%5Crbrace) |
|    `\overline`    | 连线符号 |              `\overline{a+b+c+d}`              | ![\overline{a+b+c+d}](https://math.jianshu.com/math?formula=%5Coverline%7Ba%2Bb%2Bc%2Bd%7D) |
|   `\underline`    |  下划线  |             `\underline{a+b+c+d}`              | ![\underline{a+b+c+d}](https://math.jianshu.com/math?formula=%5Cunderline%7Ba%2Bb%2Bc%2Bd%7D) |
|   `\overbrace`    | 上大括号 | `\overbrace{a+\underbrace{b+c}_{1.0}+d}^{2.0}` | ![\overbrace{a+\underbrace{b+c}_{1.0}+d}^{2.0}](https://math.jianshu.com/math?formula=%5Coverbrace%7Ba%2B%5Cunderbrace%7Bb%2Bc%7D_%7B1.0%7D%2Bd%7D%5E%7B2.0%7D) |
|   `\underbrace`   | 下大括号 |              `\underbrace{a+d}_3`              | ![\underbrace{a+d}_3](https://math.jianshu.com/math?formula=%5Cunderbrace%7Ba%2Bd%7D_3) |

##### 3.6 空格

- Latex语法本身会忽略空格的存在。
- 小空格: `a\ b` 显示为 ![a\ b](https://math.jianshu.com/math?formula=a%5C%20b)
- 大空格: `a \quad b` 显示为 ![a \quad b](https://math.jianshu.com/math?formula=a%20%5Cquad%20b)

### 4. 数学公式

##### 4.1 初等运算

|   命令   |       说明       |                     输入                      |                             显示                             |
| :------: | :--------------: | :-------------------------------------------: | :----------------------------------------------------------: |
|   `=`    |       等于       |                     `x=y`                     |     ![x=y](https://math.jianshu.com/math?formula=x%3Dy)      |
|   `+`    |        加        |                     `x+y`                     |     ![x+y](https://math.jianshu.com/math?formula=x%2By)      |
|   `-`    |        减        |                     `x−y`                     |      ![x-y](https://math.jianshu.com/math?formula=x-y)       |
|  `\ast`  |        乘        |                  `x \ast y`                   | ![x \ast y](https://math.jianshu.com/math?formula=x%20%5Cast%20y) |
|  `\div`  |        除        |                  `x \div y`                   | ![x \div y](https://math.jianshu.com/math?formula=x%20%5Cdiv%20y) |
|  `\pm`   |       加减       |                   `x \pm y`                   | ![x \pm y](https://math.jianshu.com/math?formula=x%20%5Cpm%20y) |
|  `\mp`   |       减加       |                   `x \mp y`                   | ![x \mp y](https://math.jianshu.com/math?formula=x%20%5Cmp%20y) |
| `\times` |       叉积       |                 `x \times y`                  | ![x \times y](https://math.jianshu.com/math?formula=x%20%5Ctimes%20y) |
| `\cdot`  |    点积(内积)    |                  `x \cdot y`                  | ![x \cdot y](https://math.jianshu.com/math?formula=x%20%5Ccdot%20y) |
|   `^`    |        幂        |                     `x^a`                     |     ![x^a](https://math.jianshu.com/math?formula=x%5Ea)      |
|   `^`    |       指数       |                     `a^x`                     |     ![a^x](https://math.jianshu.com/math?formula=a%5Ex)      |
|  `\log`  |       对数       |                   `\log_ax`                   | ![\log_ax](https://math.jianshu.com/math?formula=%5Clog_ax)  |
|  `\ln`   | 自然对数(e为底)  |                    `\ln x`                    |  ![\ln x](https://math.jianshu.com/math?formula=%5Cln%20x)   |
|  `\lg`   |    10为底对数    |                    `\lg x`                    |  ![\lg x](https://math.jianshu.com/math?formula=%5Clg%20x)   |
| `\frac`  |       分式       |                 `\frac{x}{y}`                 | ![\frac{x}{y}](https://math.jianshu.com/math?formula=%5Cfrac%7Bx%7D%7By%7D) |
| `\sqrt`  |   二次开方根式   |                  `\sqrt{x}`                   | ![\sqrt{x}](https://math.jianshu.com/math?formula=%5Csqrt%7Bx%7D) |
| `\sqrt`  |       根式       |                 `\sqrt[n]{x}`                 | ![\sqrt[n]{x}](https://math.jianshu.com/math?formula=%5Csqrt%5Bn%5D%7Bx%7D) |
| `\sqrt`  | 常数二次开方根式 |                  `\sqrt{a}`                   | ![\sqrt{a}](https://math.jianshu.com/math?formula=%5Csqrt%7Ba%7D) |
| `\sqrt`  |     常数根式     |                 `\sqrt[n]{a}`                 | ![\sqrt[n]{a}](https://math.jianshu.com/math?formula=%5Csqrt%5Bn%5D%7Ba%7D) |
|    ``    |      多项式      | `a_nx^n + \cdots + a_1x + a_0 \quad n \geq 0` | ![a_nx^n + \cdots + a_1x + a_0 \quad n \geq 0](https://math.jianshu.com/math?formula=a_nx%5En%20%2B%20%5Ccdots%20%2B%20a_1x%20%2B%20a_0%20%5Cquad%20n%20%5Cgeq%200) |

##### 4.2 比较运算

|    命令     |    说明    |      输入       |                             显示                             |
| :---------: | :--------: | :-------------: | :----------------------------------------------------------: |
|   `\leq`    |  小于等于  |   `x \leq y`    | ![x \leq y](https://math.jianshu.com/math?formula=x%20%5Cleq%20y) |
|   `\geq`    |  大于等于  |   `x \geq y`    | ![x \geq y](https://math.jianshu.com/math?formula=x%20%5Cgeq%20y) |
|   `\nleq`   | 不小于等于 |   `x \nleq y`   | ![x \nleq y](https://math.jianshu.com/math?formula=x%20%5Cnleq%20y) |
| `\not \leq` | 不小于等于 | `x \not \leq y` | ![x \not \leq y](https://math.jianshu.com/math?formula=x%20%5Cnot%20%5Cleq%20y) |
|   `\ngeq`   | 不大于等于 |   `x \ngeq y`   | ![x \ngeq y](https://math.jianshu.com/math?formula=x%20%5Cngeq%20y) |
| `\not \geq` | 不大于等于 | `x \not \geq y` | ![x \not \geq y](https://math.jianshu.com/math?formula=x%20%5Cnot%20%5Cgeq%20y) |
|   `\neq`    |   不等于   |   `x \neq y`    | ![x \neq y](https://math.jianshu.com/math?formula=x%20%5Cneq%20y) |
|  `\approx`  |   约等于   |  `x \approx y`  | ![x \approx y](https://math.jianshu.com/math?formula=x%20%5Capprox%20y) |
|  `\equiv`   |   恒等于   |  `x \equiv y`   | ![x \equiv y](https://math.jianshu.com/math?formula=x%20%5Cequiv%20y) |

##### 4.2 集合运算

|      命令      |  说明  |        输入        |                             显示                             |
| :------------: | :----: | :----------------: | :----------------------------------------------------------: |
|     `\in`      |  属于  |     `x \in y`      | ![x \in y](https://math.jianshu.com/math?formula=x%20%5Cin%20y) |
|    `\notin`    | 不属于 |    `x \notin y`    | ![x \notin y](https://math.jianshu.com/math?formula=x%20%5Cnotin%20y) |
|   `\subset`    | 真子集 |   `x \subset y`    | ![x \subset y](https://math.jianshu.com/math?formula=x%20%5Csubset%20y) |
| `\not \subset` | 非子集 | `x \not \subset y` | ![x \not \subset y](https://math.jianshu.com/math?formula=x%20%5Cnot%20%5Csubset%20y) |
|  `\subseteq`   |  子集  |  `x \subseteq y`   | ![x \subseteq y](https://math.jianshu.com/math?formula=x%20%5Csubseteq%20y) |
|   `\supset`    | 真超集 |   `x \supset y`    | ![x \supset y](https://math.jianshu.com/math?formula=x%20%5Csupset%20y) |
|  `\supseteq`   |  超集  |  `x \supseteq y`   | ![x \supseteq y](https://math.jianshu.com/math?formula=x%20%5Csupseteq%20y) |
|     `\cup`     |  并集  |     `x \cup y`     | ![x \cup y](https://math.jianshu.com/math?formula=x%20%5Ccup%20y) |
|     `\cap`     |  交集  |     `x \cap y`     | ![x \cap y](https://math.jianshu.com/math?formula=x%20%5Ccap%20y) |
|  `\setminus`   |  差集  |  `x \setminus y`   | ![x \setminus y](https://math.jianshu.com/math?formula=x%20%5Csetminus%20y) |
|  `\emptyset`   | 空集合 |    `\emptyset`     | ![\emptyset](https://math.jianshu.com/math?formula=%5Cemptyset) |

##### 4.3 三角函数

|  命令  | 说明 |   输入   |                            显示                             |
| :----: | :--: | :------: | :---------------------------------------------------------: |
| `\sin` | 正弦 | `\sin x` | ![\sin x](https://math.jianshu.com/math?formula=%5Csin%20x) |
| `\cos` | 余弦 | `\cos x` | ![\cos x](https://math.jianshu.com/math?formula=%5Ccos%20x) |
| `\tan` | 正切 | `\tan x` | ![\tan x](https://math.jianshu.com/math?formula=%5Ctan%20x) |
| `\cot` | 余切 | `\cot x` | ![\cot x](https://math.jianshu.com/math?formula=%5Ccot%20x) |
| `\sec` | 正割 | `\sec x` | ![\sec x](https://math.jianshu.com/math?formula=%5Csec%20x) |
| `\csc` | 余割 | `\csc x` | ![\csc x](https://math.jianshu.com/math?formula=%5Ccsc%20x) |

##### 4.4 累加与累乘

- 累加求和: ![\sum_{i=0}^{n}{a_i}](https://math.jianshu.com/math?formula=%5Csum_%7Bi%3D0%7D%5E%7Bn%7D%7Ba_i%7D)
- 累乘求积: ![\prod_{i=0}^{n}{a_i}](https://math.jianshu.com/math?formula=%5Cprod_%7Bi%3D0%7D%5E%7Bn%7D%7Ba_i%7D)
- 累计并集: ![\bigcup_{i=0}^{n}{A_i}](https://math.jianshu.com/math?formula=%5Cbigcup_%7Bi%3D0%7D%5E%7Bn%7D%7BA_i%7D)
- 累计交集: ![\bigcap_{i=0}^{n}{A_i}](https://math.jianshu.com/math?formula=%5Cbigcap_%7Bi%3D0%7D%5E%7Bn%7D%7BA_i%7D)

##### 4.5 数学分析

|     命令     |     说明     |                  输入                  |                             显示                             |
| :----------: | :----------: | :------------------------------------: | :----------------------------------------------------------: |
|    `\lim`    |     极限     |         `\lim_{0 \to \infty}`          | ![\lim_{0 \to \infty}](https://math.jianshu.com/math?formula=%5Clim_%7B0%20%5Cto%20%5Cinfty%7D) |
|   `\Delta`   |    微变量    |               `\Delta x`               | ![\Delta x](https://math.jianshu.com/math?formula=%5CDelta%20x) |
| `\mathrm{d}` |   微分算子   |            `\mathrm{d}{x}`             | ![\mathrm{d}{x}](https://math.jianshu.com/math?formula=%5Cmathrm%7Bd%7D%7Bx%7D) |
|  `\partial`  |  偏微分算子  |             `\partial{x}`              | ![\partial{x}](https://math.jianshu.com/math?formula=%5Cpartial%7Bx%7D) |
|    `\int`    |   一重积分   |             `\int_{a}{b}`              | ![\int_{a}^{b}{f(x)}\mathrm{d}{x}](https://math.jianshu.com/math?formula=%5Cint_%7Ba%7D%5E%7Bb%7D%7Bf(x)%7D%5Cmathrm%7Bd%7D%7Bx%7D) |
|   `\iint`    |   二重积分   | `\iint_{D}{f(x, y)}\mathrm{d}{\delta}` | ![\iint_{D}{f(x, y)}\mathrm{d}{\delta}](https://math.jianshu.com/math?formula=%5Ciint_%7BD%7D%7Bf(x%2C%20y)%7D%5Cmathrm%7Bd%7D%7B%5Cdelta%7D) |
|   `\iiint`   |   三重积分   |                   ``                   |  ![\iiint](https://math.jianshu.com/math?formula=%5Ciiint)   |
|   `\oint`    | 一重曲线积分 | `\oint_{L}{P\mathrm{d}x+Q\mathrm{d}y}` | ![\oint_{L}{P\mathrm{d}x+Q\mathrm{d}y}](https://math.jianshu.com/math?formula=%5Coint_%7BL%7D%7BP%5Cmathrm%7Bd%7Dx%2BQ%5Cmathrm%7Bd%7Dy%7D) |
|   `\ooint`   | 二重曲线积分 |                   ``                   | ![\def\ooint{{\bigcirc}\kern-11.5pt{\int}\kern-6.5pt{\int}} \ooint](https://math.jianshu.com/math?formula=%5Cdef%5Cooint%7B%7B%5Cbigcirc%7D%5Ckern-11.5pt%7B%5Cint%7D%5Ckern-6.5pt%7B%5Cint%7D%7D%20%5Cooint) |
|  `\oooint`   | 三重曲线积分 |                   ``                   | ![\def\oooint{{\bigcirc}\kern-12.3pt{\int}\kern-7pt{\int}\kern-7pt{\int}} \oooint](https://math.jianshu.com/math?formula=%5Cdef%5Coooint%7B%7B%5Cbigcirc%7D%5Ckern-12.3pt%7B%5Cint%7D%5Ckern-7pt%7B%5Cint%7D%5Ckern-7pt%7B%5Cint%7D%7D%20%5Coooint) |

### 5. 矩阵

##### 5.1 基本语法

起始标记`\begin{matrix}`，结束标记`\end{matrix}`
每一行末尾标记`\\`，行间元素之间以`&`分隔
举例:

```ruby
$$
\begin{matrix}
1&0&0\\
0&1&0\\
0&0&1\\
\end{matrix}
$$
```

呈现为：
$$
\begin{matrix}
1&0&0\\
0&1&0\\
0&0&1\\
\end{matrix}
$$

##### 5.2 矩阵边框

- 在起始、结束标记处用下列词替换 `matrix`
- `pmatrix` ：小括号边框
- `bmatrix` ：中括号边框
- `Bmatrix` ：大括号边框
- `vmatrix` ：单竖线边框
- `Vmatrix` ：双竖线边框

![\begin{pmatrix} 1&0&0 \\ 0&1&0 \\ 0&0&1 \\ \end{pmatrix} \begin{bmatrix} 1&0&0 \\ 0&1&0 \\ 0&0&1 \\ \end{bmatrix} \begin{Bmatrix} 1&0&0 \\ 0&1&0 \\ 0&0&1 \\ \end{Bmatrix} \begin{vmatrix} 1&0&0 \\ 0&1&0 \\ 0&0&1 \\ \end{vmatrix} \begin{Vmatrix} 1&0&0 \\ 0&1&0 \\ 0&0&1 \\ \end{Vmatrix}](https://math.jianshu.com/math?formula=%5Cbegin%7Bpmatrix%7D%201%260%260%20%5C%5C%200%261%260%20%5C%5C%200%260%261%20%5C%5C%20%5Cend%7Bpmatrix%7D%20%5Cbegin%7Bbmatrix%7D%201%260%260%20%5C%5C%200%261%260%20%5C%5C%200%260%261%20%5C%5C%20%5Cend%7Bbmatrix%7D%20%5Cbegin%7BBmatrix%7D%201%260%260%20%5C%5C%200%261%260%20%5C%5C%200%260%261%20%5C%5C%20%5Cend%7BBmatrix%7D%20%5Cbegin%7Bvmatrix%7D%201%260%260%20%5C%5C%200%261%260%20%5C%5C%200%260%261%20%5C%5C%20%5Cend%7Bvmatrix%7D%20%5Cbegin%7BVmatrix%7D%201%260%260%20%5C%5C%200%261%260%20%5C%5C%200%260%261%20%5C%5C%20%5Cend%7BVmatrix%7D)

##### 5.3 省略元素

- 横省略号：`\cdots`
- 竖省略号：`\vdots`
- 斜省略号：`\ddots`
- 底省略号: `\ldots` 效果显示为 ![1,2,\ldots,n](https://math.jianshu.com/math?formula=1%2C2%2C%5Cldots%2Cn)

举例:

```ruby
$$
\begin{bmatrix}
{a_{11}}&{a_{12}}&{\cdots}&{a_{1n}}\\
{a_{21}}&{a_{22}}&{\cdots}&{a_{2n}}\\
{\vdots}&{\vdots}&{\ddots}&{\vdots}\\
{a_{m1}}&{a_{m2}}&{\cdots}&{a_{mn}}\\
\end{bmatrix}
$$
```

呈现为：
$$
\begin{bmatrix}
{a_{11}}&{a_{12}}&{\cdots}&{a_{1n}}\\
{a_{21}}&{a_{22}}&{\cdots}&{a_{2n}}\\
{\vdots}&{\vdots}&{\ddots}&{\vdots}\\
{a_{m1}}&{a_{m2}}&{\cdots}&{a_{mn}}\\
\end{bmatrix}
$$

##### 5.4 阵列

- 需要array环境: 起始、结束处以`{array}`声明
- 对齐方式: 在`{array}`后以`{}`逐行统一声明
  - 左对齐: `l` 居中: `c` 右对齐: `r`
  - 竖直线: 在声明对齐方式时，插入`|`建立竖直线
- 插入水平线: `\hline`

举例:

```swift
$$
\begin{array}{c|lll}
{↓}&{a}&{b}&{c}\\
\hline
{R_1}&{c}&{b}&{a}\\
{R_2}&{b}&{c}&{c}\\
\end{array}
$$
```

呈现为:
$$
\begin{array}{c|lll}
{↓}&{a}&{b}&{c}\\
\hline
{R_1}&{c}&{b}&{a}\\
{R_2}&{b}&{c}&{c}\\
\end{array}
$$

##### 5.5 方程组

- 需要cases环境：起始、结束处以`{cases}`声明

举例:



```ruby
$$
\begin{cases}
a_1x+b_1y+c_1z=d_1\\
a_2x+b_2y+c_2z=d_2\\
a_3x+b_3y+c_3z=d_3\\
\end{cases}
$$
```

呈现为:
$$
\begin{cases}
a_1x+b_1y+c_1z=d_1\\
a_2x+b_2y+c_2z=d_2\\
a_3x+b_3y+c_3z=d_3\\
\end{cases}
$$

### 参考:

1. [在Markdown中输入数学公式(MathJax)](https://www.jianshu.com/p/a0aa94ef8ab2)
2. [markdown编辑器中数学公式的基本命令](https://links.jianshu.com/go?to=https%3A%2F%2Fblog.csdn.net%2Fholdrenminbi%2Farticle%2Fdetails%2F78229488)
3. [LaTeX 各种命令，符号](https://links.jianshu.com/go?to=https%3A%2F%2Fblog.csdn.net%2Fgarfielder007%2Farticle%2Fdetails%2F51646604)
4. [Latex 求和求乘积，积分微分等](https://links.jianshu.com/go?to=https%3A%2F%2Fblog.csdn.net%2Funcle_gy%2Farticle%2Fdetails%2F78772893)
5. [LaTex自定义闭合二重积分与闭合三重积分符号](https://www.jianshu.com/p/26f20abbed6b)