# HTML5 学习笔记：

### 选择器

/* 通配符：* 代表所有标签
​        作用：设置一些全局的公共属性 字体大小...
​        缺陷：
​            1，性能不好（内部递归做法）；
​            2，优先级别最低
​            3，不利于SEO(搜索引擎)优化，会把引擎标签全部标注*
​        */
​        /* * {
​            font-size: 100px;
​        } */

​        /* 标签选择器 */
​        p {
​            color: royalblue;
​        }

​        /* 类选择器 */
​        .test1 {
​            color: blueviolet;
​        }

​        /* ID选择器 */
​        #main {
​            font-size: 30px;
​        }

​        /* 并列选择器：只要满足其中一个就生效，如下 只要是#main 或者 test1 */
​        #main,
​        .test1 {
​            border: 10px solid yellow;
​        }

​        /* 复合选择器：只有满足了所有条件时才生效 */
​        div.test2 {
​            color: brown;
​        }

​        /* 后代选择器 */
​        div p {
​            border: 5px solid black;
​        }

​        /* 直接后代选择器 */
​        div>p>a {
​            border: 5px solid red;
​        }

​        /* 相邻兄弟选择器 */
​        div+p {
​            border: 5px solid black;
​        }

​        /* 属性选择器 */
​        div[name] {
​            border: 5px solid royalblue;
​        }

​        /* 需要name = label1的 */
​        div[name="label1"] {
​            border: 5px solid royalblue;
​        }

​        /* 属性多元选择器 */
​        div[name][age] {
​            border: 5px solid royalblue;
​        }

​        /* 伪类选择器，先拿到选择器：input  :focus代表动作*/
​        input:focus {
​            /* 去除外边线 */
​            outline: none;
​            border-color: red;
​            width: 200px;
​            height: 50px;
​        }

​        /* 悬浮效果 :hover */
​        div#main:hover {
​            background-color: brown;
​            width: 200px;
​            height: 50px;
​        }

​        /* 伪元素选择器 */
​        p.word:first-letter {
​            font-size: 50px !important;
​        }
​        /* 

​        选择器的优先级别：
​        1，相同类型的选择器：a，就近原则 b，叠加原则
​        2，不相同类型的选择器：
​         important > 内联 > ID类型选择器 > 类选择器 | 伪类 | 属性 | 伪元素 > 标签选择器 > 通配符 > 继承

​           选择器的权值：
​           通配符 0
​           标签   1
​           类     10
​           属性   10
​           伪类   10
​           伪元素 10
​           ID    100
​           important 1000

​           原则：选择器的权值加到一起，大的优行；如果权值相同，后定义的优先
​        */



### 标签

* 块级标签(div)：独占一行
* 行内标签(span、a、label...): 宽高度取决于内容
* 行内块级标签：

### css 属性
* 可继承属性: 文字控制

* 不可继承属性：区块控制，如背景颜色

* display: 
  * none隐藏 
  * block：让其变块级标签
  * inline：让其变为行内标签
  * inline-block：行内块级标签
  
* overflow: scroll;  /* 超出的内容滚动 */

* float: left; /* 脱离标准流 */

* position: relative; /* 定位 在父盒子的任意位置定位，它会往上找，直到找不到`position不是为static的父级盒子`，然后相对它去进行定位*/  

* transform: translate(-50%, -50%) /* 平移 */

* 水平居中：

  * 行内标签和行内块级标签：直接在父标签中设置 text-align：center
  * 块级标签：在自身设置 margin: 0 auto
  
* 垂直居中：

  * 通过设置行高和高度结合定位及设置偏移来达到效果：

    ```css
    height: 100px;
    line-height: 100px;
    position: absolute;
    left: 50%;
    top: 50%;
    /* 平移 */
    transform: translate(-50%, -50%)
    ```

    

  

