Node.js 学习笔记

[TOC]

## 前言

> 本书首先简要介绍 Node.js，然后通过示例讲解 Node.js 的基本特性，再用案例式教学的方式讲述 如何用 Node.js 进行 Web 开发，接着探讨一些 Node.js 进阶话题，最后展示如何将一个 Node.js 应用部署到 生产环境中。

## 简介

> 本书共6章，分别讨论了 Node.js的背景、安装和配置方法、基本特性、核心模块以及一 些进阶话题。除此之外，还有2个附录，分别介绍了JavaScript 的高级特性和 Node.js 编程规 范。
>
> * Node.js 简介
> * 安装和配置Node.js
> * Node.js 快速入门
> * Node.js 核心模块
> * 使用 Node.js 进行 Web 开发
> * Node.js 进阶话题
> * 附录A JavaScript 的高级特性
> * 附录B Node.js 编程规范

## 第一章 Node.js 简介

### Node.js是什么

> Node.js， 或者 Node， 是一个可以让 JavaScript 运行在服务器端的平台。 它可以让 JavaScript 脱离浏览器的束缚运行在一般的服务器环境下，就像运行 Python、Perl、PHP、Ruby 程序一样。你可以用 Node.js 轻松地进行服务器端应用开发，Python、Perl、PHP、Ruby 能 做的事情 Node.js 几乎都能做，而且可以做得更好。
>
> Node.js 是一个为实时Web（Real-time Web）应用开发而诞生的平台，它从诞生之初就充分 考虑了在实时响应、超大规模数据要求下架构的可扩展性。这使得它摒弃了传统平台依靠多线 程来实现高并发的设计思路，而采用了单线程、异步式I/O、事件驱动式的程序设计模型。这些 特性不仅带来了巨大的性能提升，还减少了多线程程序设计的复杂性，进而提高了开发效率。
>
> Node.js 不是一种独立的语言，与 PHP、Python、Perl、Ruby 的“既是语言也是平台” 不同。Node.js 也不是一个 JavaScript 框架，不同于 CakePHP、Django、Rails。Node.js 更不 是浏览器端的库，不能与 jQuery、ExtJS 相提并论。Node.js 是一个让 JavaScript 运行在服务 端的开发平台，它让 JavaScript 成为脚本语言世界的一等公民，在服务端堪与 PHP、Python、 Perl、Ruby 平起平坐。

### Node.js能做什么

	>  正如 JavaScript 为客户端而生，Node.js 为网络而生。Node.js 能做的远不止开发一个网 站那么简单，使用 Node.js，你可以轻松地开发：
	> * 具有复杂逻辑的网站；
	> *  基于社交网络的大规模 Web 应用；
	> *  Web Socket 服务器；
	> *  TCP/UDP 套接字应用程序；
	> * 命令行工具；
	> * 交互式终端程序；
	> *  带有图形用户界面的本地应用程序；
	> * 单元测试工具；
	> * 客户端 JavaScript 编译器。
	>   Node.js 内建了 HTTP 服务器支持，也就是说你可以轻而易举地实现一个网站和服务器 的组合。这和 PHP、Perl 不一样，因为在使用 PHP 的时候，必须先搭建一个 Apache 之类的

### JavaScript 简史

> 作为 Node.js 的基础，JavaScript 是一个完全为网络而诞生的语言。在今天看来，JavaScript
> 具有其他诸多语言不具备的优势，例如速度快、开销小、容易学习等，但在一开始它却并不
> 是这样。多年以来，JavaScript 因为其低效和兼容性差而广受诟病，一直是一个被人嘲笑的 “丑小鸭”，它在成熟之前经历了无数困难和坎坷，个中究竟，还要从它的诞生讲起。
>
> JavaScript 首次出现在1995年，正如现在的 Node.js 一样，当年 JavaScript 的诞生决不是 偶然的。在1992年，一个叫 Nombas 的公司开发了“C减减”（C minus minus，Cmm）语言， 后来改名为 ScriptEase。ScriptEase 最初的设计是将一种微型脚本语言与一个叫做 Espresso Page 的工具配合，使脚本能够在浏览器中运行，因此 ScriptEase 成为了第一个客户端脚本语言。
>
> 2000年左右，也就是 ASP 蒸蒸日上的年代，很多开发者开始学习 JScript。然而 JScript 在 当时并不是很受欢迎。随着后来 LAMP 的兴起，以及Web 2.0 时代的到来，Ajax 等一系列概念的提出，JavaScript 成了前端开发的代名词，同时服务端 JavaScript 也逐渐被人 遗忘。
>
> Node.js 是目前 CommonJS 规范最热门的一个实现，它基于 CommonJS 的 Modules/1.0 规 范实现了 Node.js 的模块，同时随着 CommonJS 规范的更新，Node.js 也在不断跟进。
>
> CommonJS 规范包括了模块（modules）、包（packages）、系统（system）、二进制（binary）、 控制台（console）、编码（encodings）、文件系统（filesystems）、套接字（sockets）、单元测 试（unit testing）等部分。

* 官方 API 文档http://nodejs.org/api/。 

* CNodeJS 社区看看http://cnodejs.org/

## 第二章 安装和配置Node.js

### Mac OS X 上安装Node.js

> 在官方链接： http://nodejs.org 找到Download 链接，然后选择Macintosh Installer，下载安装包。下载完成后运行安装包， 根据提示完成安装；
>
> Node.js 和 npm 会被安装到 /usr/local/bin 目录下，在macos下源码编译那么需要安装 Xcode
>
> 安装成功后在终端执行命令，确认是正确安装：`$ node -v` //查看版本号

### Windows 上安装Node.js
> 大同小异，或者自行百度
>
> 安装程序不会询问你安装路径， Node.js 会被自动安装到 C:\Program Files\nodejs 或 C:\Program Files (x86)\nodejs（64位系统）目录下，并且会在系统的 PATH 环境变量中增加该
>
> 源码编译：Node.js 在 Windows 下只能通过 Microsoft Visual Studio 编译

### Linux 上安装Node.js
> 大同小异，或者自行百度
>
> 源码编译：Linux上需要使用 g++ 来编译 Node.js。在 Debian/Ubuntu 中，你可以 通过 apt-get install g++ 命令安装g++。在 Fedora/Redhat/CentOS 中，你可以使用 yum install gcc-c++ 安装

### 安装多版本管理器

> 迄今为止Node.js 更新速度还很快，有时候新版本还会将旧版本的一些 API 废除，以至 于写好的代码不能向下兼容。有时候你可能想要尝试一下新版本有趣的特性，但又想要保持 一个相对稳定的环境。基于这种需求，Node.js 的社区开发了多版本管理器，用于在一台机 器上维护多个版本的 Node.js 实例， 方便按需切换。
>
> Node 多版本管理器（Node Version Manager，nvm）是一个通用的叫法，它目前有许多不同的实现。通常我们说的 nvm 是指 或者n

* nvm

   https://github.com/creationix/nvm

  如果你已经安装好了 Node.js 和 npm 环境，就可以直接使用 `$ npm install -g nvm`

* n

   https://github.com/visionmedia/n

  如果你已经安装好了 Node.js 和 npm 环境，就可以直接使用 `$ npm install -g n` n不支持Windows

  安装完 n 以后，在终端中运行 n --help 即可看到它的使用说明

## 第三章 Node.js 快速入门

### Hello World

```javascript
//打开终端
$ vim hello.js

//在hello.js文件中写代码
console.log('Hello World');

//运行hello.js文件
$ node hello.js

//输出
Hello World

//我们可以把要执行的语句作为 node -e 的参数直接执行。
$ node -e "console.log('Hello World');" 
//输出
Hello World
```

### 使用 **node** 的 REPL 模式

> REPL （Read-eval-print loop），即输入—求值—输出循环，可以进入一个即 时求值的运行环境。运行无参数的 node 将会启动一个 JavaScript 的交互式 shell：

```javascript
$ node
> console.log('Hello World'); Hello World
undefined
> consol.log('Hello World');
```

### 建立 HTTP 服务器

> Node.js 是为网络而诞生的平台，但又与 ASP、PHP 有很大的不同，究竟不同在哪里呢？ 如果你有 PHP 开发经验，会知道在成功运行 PHP 之前先要配置一个功能强大而复杂的 HTTP 服务器，譬如 Apache、IIS 或 Nginx，还需要将 PHP 配置为 HTTP 服务器的模块，或者使用 FastCGI 协议调用 PHP 解释器。这种架构是“浏览器  HTTP 服务器  PHP 解释器”的组织 方式，而Node.js采用了一种不同的组织方式，如下图所示：
>
> Node.js 将“HTTP服务器”这一层抽离，直接面向浏览器用户。这种架构 从某种意义上来说是颠覆性的， 因而会让人心存疑虑：Node.js作为HTTP服务器的效率足够吗？会不会提高耦合程度？不在这里讨论这种架构的利弊

​	![](https://blogimage-1257063273.cos.ap-guangzhou.myqcloud.com/Node.js与PHP的架构.png)

 * 建立一个名为 app.js 的文件，内容为：

   ```javascript
   //app.js
   var http = require('http');
   http.createServer(function(req, res) {
    res.writeHead(200, {'Content-Type': 'text/html'});
    res.write('<h1>Node.js</h1>');
    res.end('<p>Hello World</p>');
   }).listen(3000);
   console.log("HTTP server is listening at port 3000.");
   ```

* 接下来运行 node app.js命令，打开浏览器访问 http://127.0.0.1:3000，即可看到如下图所示的内容。

* supervisor

  > supervisor 可以帮助你实现这个功能，它会监视你对代码的改动，并自动重启 Node.js。 使用方法很简单，首先使用 npm 安装 supervisor：
  >
  > `$ npm install -g supervisor`
  >
  > 接下来，使用 supervisor 命令启动 app.js：
  >
  > `$ supervisor app.js`
  >
  > 当代码被改动时，运行的脚本会被终止，然后重新启动。

### 异、同步读取文件

* 异步

  ```javascript
  // 让我们看看在 Node.js 中如何用异步的方式读取一个文件，下面是一个例子：
  //readfile.js
  var fs = require('fs');
  fs.readFile('file.txt', 'utf-8', function (err, data) {
     if (err) {
        console.error(err);
     } else {
        console.log(data);
     }
  });
  console.log('end.');
  // 运行的结果如下：
  end.
  Contents of the file.
  ```

  >fs.readFile 调用时所做的工作只是将异步式 I/O 请求发送给了操作系统，然后立即 返回并执行后面的语句，执行完以后进入事件循环监听事件。当 fs 接收到 I/O 请求完成的 事件时，事件循环会主动调用回调函数以完成后续工作
  >
  >Node.js 所有的异步 I/O 操作在完成时都会发送一个事件到事件队列。在开发者看来，事 件由 EventEmitter 对象提供。前面提到的 fs.readFile 和 http.createServer 的回 调函数都是通过 EventEmitter 来实现的

  

* 同步

  ```javascript
  //readfilesync.js
  var fs = require('fs');
  var data = fs.readFileSync('file.txt', 'utf-8');
  console.log(data);
  console.log('end.');
  //运行的结果与前面不同， 如下所示：
  $ node readfilesync.js Contents of the file.
  end.
  ```

### Node.js 的事件循环机制
> Node.js 在什么时候会进入事件循环呢？答案是 Node.js 程序由事件循环开始，到事件循 环结束，所有的逻辑都是事件的回调函数，所以 Node.js 始终在事件循环中，程序入口就是 事件循环第一个事件的回调函数。事件的回调函数在执行的过程中，可能会发出 I/O 请求或 直接发射（emit）事件，执行完毕后再返回事件循环，事件循环会检查事件队列中有没有未 处理的事件，直到程序结束。图3-5说明了事件循环的原理。

​	![](https://blogimage-1257063273.cos.ap-guangzhou.myqcloud.com/node_event_runloop.png)

### 模块和包

> 模块（Module）和包（Package）是 Node.js 最重要的支柱。开发一个具有一定规模的程 序不可能只用一个文件，通常需要把各个功能拆分、封装，然后组合起来，模块正是为了实 现这种方式而诞生的。在浏览器 JavaScript 中，脚本模块的拆分和组合通常使用 HTML 的 script 标签来实现。Node.js 提供了 require 函数来调用其他模块，而且模块都是基于文件的，机制十分简单。Node.js 的模块和包机制的实现参照了 CommonJS 的标准， 但并未完全遵循

#### 什么是模块

> 模块是 Node.js 应用程序的基本组成部分，文件和模块是一一对应的。换言之，一个 Node.js 文件就是一个模块，这个文件可能是 JavaScript 代码、JSON 或者编译过的 C/C++ 扩展。
>
> 在前面的例子中，我们曾经用到了 var http = require('http')，其中 http 是 Node.js 的一个核心模块

1. 创建模块
   在 Node.js 中，创建一个模块非常简单，因为一个文件就是一个模块，我们要关注的问 题仅仅在于如何在其他文件中获取这个模块。Node.js 提供了 exports 和 require 两个对 象，其中 exports 是模块公开的接口，require 用于从外部获取一个模块的接口，即所获 取模块的 exports 对象。

   *  让我们以一个例子来了解模块。创建一个 module.js 的文件，内容是：

     ```javascript
     //module.js
     var name;
     exports.setName = function (thyName) {
        name = thyName;
     };
     exports.sayHello = function () {
        console.log('Hello ' + name);
     };
     //在同一目录下创建 getmodule.js， 内容是：
     //getmodule.js
     var myModule = require('./module');
     myModule.setName('mynodetest'); 
     myModule.sayHello();
     //-------------------------------------------------//
     //运行
     $ node getmodule.js，
     //结果：
     Hello mynodetest
     ```

     在以上示例中，module.js 通过 exports 对象把 setName 和 sayHello 作为模块的访 问接口，在 getmodule.js 中通过 require('./module') 加载这个模块，然后就可以直接访 问 module.js 中 exports 对象的成员函数了。

2. 覆盖 exports

   *  有时候我们只是想把一个对象封装到模块中，例如：

     ```javascript
     //hello.js
     function Hello() {
        var name;
        this.setName = function (thyName) {
           name = thyName;
        };
        this.sayHello = function () {
           console.log('Hello ' + name);
        };
     };
     
     module.exports = Hello;
     
     //gethello.js
     //这样就可以直接获得这个对象了：
     var Hello = require('./hello');
     hello = new Hello();
     hello.setName('BYVoid');
     hello.sayHello();
     ```

     注意，模块接口的唯一变化是使用 module.exports = Hello 代替了 exports.Hello= Hello。在外部引用该模块时，其接口对象就是要输出的 Hello 对象本身，而不是原先的 exports。事实上，exports 本身仅仅是一个普通的空对象，即 {}，它专门用来声明接口，本 质上是通过它为模块闭包的内部建立了一个有限的访问接口

#### 什么是包

> 包是在模块基础上更深一步的抽象，Node.js 的包类似于 C/C++ 的函数库或者 Java/.Net 的类库。它将某个独立的功能封装起来，用于发布、更新、依赖管理和版本控制。Node.js 根 据 CommonJS 规范实现了包机制，开发了 npm来解决包的发布和获取需求。
>  Node.js 的包是一个目录，其中包含一个 JSON 格式的包说明文件 package.json。严格符 合 CommonJS 规范的包应该具备以下特征：
>
> * package.json 必须在包的顶层目录下；
>
> * 二进制文件应该在 bin 目录下；
>
> * JavaScript 代码应该在 lib 目录下；
>
> * 文档应该在 doc 目录下；
>
> * 单元测试应该在 test 目录下。
>
>    Node.js 对包的要求并没有这么严格，只要顶层目录下有 package.json，并符合一些规范 即可。当然为了提高兼容性，我们还是建议你在制作包的时候，严格遵守 CommonJS 规范。

* 以文件夹作为模块

  > 模块与文件是一一对应的。文件不仅可以是 JavaScript 代码或二进制代码，还可以是一 个文件夹。最简单的包，就是一个作为文件夹的模块。

  ```javascript
  //somepackage/index.js exports.hello = function() {
   console.log('Hello.');
  };
  //然后在 somepackage 之外建立 getpackage.js，内容如下：
  //getpackage.js
  var somePackage = require('./somepackage'); 
  somePackage.hello();
  //运行 
  $ node getpackage.js，
  //控制台将输出结果 Hello.。
  ```

* package.json

  在前面的例子中的somepackage文件夹下，创建一个叫package.json的文件，内容如下：

  **还可以进入somepackage目录，使用命令来生成一个package.json:`$ npm init -f `**

  ```javascript
  {
  	"main" : "./lib/interface.js"
  }
  ```

  然后将 index.js 重命名为 interface.js 并放入 lib 子文件夹下。以同样的方式再次调用这个 包，依然可以正常使用。

  因为Node.js 在调用某个包时，会首先检查包中 package.json 文件的 main 字段，将其作为 包的接口模块，如果 package.json 或 main 字段不存在，会尝试寻找 index.js 或 index.node 作 为包的接口。

  > package.json 是 CommonJS 规定的用来描述包的文件，完全符合规范的 package.json 文 件应该含有以下字段。
  >
  > * name：包的名称，必须是唯一的，由小写英文字母、数字和下划线组成，不能包含
  >    空格。
  > * description：包的简要说明。
  >
  > * version：符合语义化版本识别 规范的版本字符串。
  >
  > * keywords：关键字数组，通常用于搜索。
  >
  > * maintainers：维护者数组，每个元素要包含 name、email （可选）、web （可选）
  >    字段。
  > * contributors：贡献者数组，格式与maintainers相同。包的作者应该是贡献者
  >    数组的第一个元素。
  > * bugs：提交bug的地址，可以是网址或者电子邮件地址。
  >
  > * licenses：许可证数组，每个元素要包含 type （许可证的名称）和 url （链接到
  >    许可证文本的地址）字段。
  > * repositories：仓库托管地址数组，每个元素要包含 type（仓库的类型，如 git ）、url （仓库的地址）和 path （相对于仓库的路径，可选）字段。
  > * dependencies：包的依赖，一个关联数组，由包名称和版本号组成。

#### Node.js 包管理器

> Node.js包管理器，即npm是 Node.js 官方提供的包管理工具①，它已经成了 Node.js 包的 标准发布平台，用于 Node.js 包的发布、传播、依赖控制。npm 提供了命令行工具，使你可 以方便地下载、安装、升级、删除包，也可以让你作为开发者发布并维护包。

* 获取一个包

  ```
  //使用 npm 安装包的命令格式为：
  npm [install/i] [package_name]
  //例如你要安装 express，可以在命令行运行：
  $ npm install express
  或者：$ npm i express
  //安装成功后，放置在当前目录的 node_modules 子目录下
  ```

* 本地模式和全局模式

  > npm在默认情况下会从http://npmjs.org搜索或下载包，将包安装到当前目录的node_modules 子目录下。
  >
  > npm 还有另一种不同的安装模式被成为全局模式，使用方法为：
  >
  > $ npm [install/i] -g [package_name]
  >
  > eg.: $ npm install -g supervisor 命令，就是以全局模式安装 supervisor。

  

  * 当我们使用全局模 式安装时，npm 会将包安装到系统目录，如 /usr/local/lib/node_modules/，同时 package.json 文 件中 bin 字段包含的文件会被链接到 /usr/local/bin/

  * 使用全局模式安装的包并不能直接在 JavaScript 文件中用 require 获得，因为 require 不会搜索 /usr/local/lib/node_modules/。

    ​				**表 - 本地模式与全局模式**

    | 模式 | 可通过 require 使用 | 注册PATH |
    | ---- | ------------------- | -------- |
    | 本地模式 |  是 | 否 |
    | 全局模式 | 否 | 是 |

* 创建全局链接

  > npm 提供了一个链接命令 npm link，它的功能是在本地包和全局包之间创建符号链接。我们说过使用全局模式安装的包不能直接通过 require 使用，但通过 npm link命令 可以打破这一限制。

  例子：

  我们已经通过 npm install -g express 安装了 express， 这时在工程的目录下运行命令：

  ```
  $ npm link express
  ./node_modules/express -> /usr/local/lib/node_modules/express
  ```

  我们可以在 node_modules 子目录中发现一个指向安装到全局的包的符号链接。通过这 种方法，我们就可以把全局包当本地包来使用了。

#### 调试

* 命令行调试：在命令行下执行 node debug debug.js，将会启动调试工具

```
$ node debug debug.js
< debugger listening on port 5858 connecting... ok
break in /home/byvoid/debug.js:1
1 var a = 1;
2 var b = 'world';
3 var c = function(x) { debug>
```

​	**Node.js 调试命令**

| 命令 | 功能 |
| ---- | ---- |
| run | 执行脚本，在第一行暂停 |
| restart | 重新执行脚本 |
| cont, c | 继续执行，直到遇到下一个断点 单步执行 |
| next, n | 单步执行 |
| step, s | 单步执行并进入函数 |
| out, o | 从函数中步出 |
| setBreakpoint(), sb() | 在当前行设置断点 |
| setBreakpoint(‘f()’), sb(...) | 在函数f的第一行设置断点 |
| setBreakpoint(‘script.js’, 20), sb(...)  |  在script.js 的第20行设置断点 |
| clearBreakpoint, cb(…) | 清除所有断点 |
| backtrace, bt | 显示当前的调用栈 |
|list(5) | 显示当前执行到的前后5行代码 |
| watch(expr) | 把表达式 expr 加入监视列表 |
| unwatch(expr) | 把表达式 expr 加入监视列表 |
| watchers | 显示监视列表中所有的表达式和值 |
| repl | 在当前上下文打开即时求值环境 |
| kill | 终止当前执行的脚本 |
| scripts | 显示当前已加载的所有脚本 |
| version |  显示 V8 的版本 |

* 远程调试：V8 提供的调试功能是基于 TCP 协议的，因此 Node.js 可以轻松地实现远程调试。在命令行下使用以下两个语句之一可以打开调试服务器：

  * 打开调试服务器

    ```
    node --debug[=port] script.js 
    node --debug-brk[=port] script.js
    ```

  * 当调试服务器启动以后，可以用命令行调试工具作为调试客户端连接，例如：

    ```
    //在一个终端中
    $ node --debug-brk debug.js debugger listening on port 5858
    //在另一个终端中
    $ node debug 127.0.0.1:5858
    connecting... ok
    debug> n
    break in /home/byvoid/debug.js:2
    1 var a = 1;
    2 var b = 'world';
    3 var c = function (x) {
    4 console.log('hello ' + x + a); debug>
    ```

* 使用 node-inspector 调试 Node.js

  > 大部分基于 Node.js 的应用都是运行在浏览器中的，例如强大的调试工具 node-inspector。 node-inspector 是一个完全基于 Node.js 的开源在线调试工具，提供了强大的调试功能和友好 的用户界面，它的使用方法十分简便。

  * 首先，使用 npm install -g node-inspector 命令安装 node-inspector，然后在终 端中通过 node --debug-brk=5858 debug.js 命令连接你要除错的脚本的调试服务器， 启动 node-inspector：
  * $ node-inspector
  * 在浏览器中打开 http://127.0.0.1:8080/debug?port=5858，即可显示出优雅的 Web 调试工 具

* 使用 Eclipse 调试 Node.js(这里不做介绍)

  

## 第四章 Node.js 核心模块

> 核心模块是 Node.js 的心脏，它由一些精简而高效的库组成，为 Node.js 提供了基本的 API。本章中，我们挑选了一部分最常用的核心模块加以详细介绍，主要内容包括：
>
> * 全局对象； 
> * 常用工具；
> * 事件机制；
> * 文件系统访问；
> * HTTP 服务器与客户端。


### 全局对象
#### 全局对象

> JavaScript 中有一个特殊的对象，称为全局对象（Global Object），它及其所有属性都可 以在程序的任何地方访问，即全局变量。在浏览器 JavaScript 中，通常 window 是全局对象， 而 Node.js 中的全局对象是 global，所有全局变量（除了 global 本身以外）都是 global 对象的属性。

#### 全局变量

> global 最根本的作用是作为全局变量的宿主。按照 ECMAScript 的定义，满足以下条 件的变量是全局变量：
>
> * 在最外层定义的变量；
> * 全局对象的属性；
> *  隐式定义的变量（未定义直接赋值的变量）。

### 常用工具 util

> util 是一个 Node.js 核心模块，提供常用函数的集合，用于弥补核心 JavaScript 的功能 过于精简的不足。

#### util.inherits

> util.inherits(constructor, superConstructor) 是一个实现对象间原型继承 的函数。JavaScript 的面向对象特性是基于原型的，与常见的基于类的不同。JavaScript 没有 提供对象继承的语言级别特性，而是通过原型复制来实现的，具体细节我们在附录A中讨论， 在这里我们只介绍 util.inherits 的用法，示例如下：

```javascript
var util = require('util');

function Base(){
    this.name = 'base',
    this.base = 1991,
    this.sayHello = function(){
        console.log('hello :'+ this.name);
    };
};

Base.prototype.showName = function(){
    console.log('this.name :', this.name);
};

function Sub(){
    this.name = 'sub'
};

util.inherits(Sub, Base);

var objBase = new Base();
objBase.showName();
objBase.sayHello();
console.log(objBase);

var objSub = new Sub();
objSub.showName();
// objSub.sayHello();
console.log('objSub :', objSub);
console.log('util.inspect(obj) :', util.inspect(objBase,true,2,true));

//输入结果：
➜  http0test node module.js
this.name : base
hello :base
Base { name: 'base', base: 1991, sayHello: [Function] }
this.name : sub
objSub : Sub { name: 'sub' }
util.inspect(obj) : Base {
  name: 'base',
  base: 1991,
  sayHello:
   { [Function]
     [length]: 0,
     [name]: '',
     [arguments]: null,
     [caller]: null,
     [prototype]: { [constructor]: [Circular] } } }
```



#### util.inspect

> util.inspect(object,[showHidden],[depth],[colors])是一个将任意对象转换 为字符串的方法，通常用于调试和错误输出。它至少接受一个参数 object，即要转换的对象。
>
> * showHidden 是一个可选参数，如果值为 true，将会输出更多隐藏信息。
> * depth 表示最大递归的层数，如果对象很复杂，你可以指定层数以控制输出信息的多 少。如果不指定depth，默认会递归2层，指定为 null 表示将不限递归层数完整遍历对象。 
> * color 值如果为 true，输出格式将会以ANSI 颜色编码，通常用于在终端显示更漂亮 的效果。

### 事件驱动 events

>  events 是 Node.js 最重要的模块，没有“之一”，原因是 Node.js 本身架构就是事件式 的，而它提供了唯一的接口，所以堪称 Node.js 事件编程的基石。events 模块不仅用于用户代码与 Node.js 下层事件循环的交互，还几乎被所有的模块依赖。

#### 事件发射器（events.EventEmitter）

> events 模块只提供了一个对象： events.EventEmitter。EventEmitter 的核心就 是事件发射与事件监听器功能的封装

* 常规事件

  ```javascript
  //我们介绍一下EventEmitter常用的API。
  //为指定事件注册一个监听器，接受一个字符串 event 和一个回调函数 listener。
  EventEmitter.on(event, listener) 
  //发射 event 事件，传递若干可选参数到事件监听器的参数表。
  EventEmitter.emit(event, [arg1], [arg2], [...]) 
  //为指定事件注册一个单次监听器， 即监听器最多只会触发一次，触发后立刻解除该监听器。
  EventEmitter.once(event, listener) 
  //移除指定事件的某个监听器，listener 必须是该事件已经注册过的监听器。
  EventEmitter.removeListener(event, listener) 
  //移除所有事件的所有监听器，如果指定 event，则移除指定事件的所有监听器。
  EventEmitter.removeAllListeners([event]) 
  //更详细的 API 文档参见 http://nodejs.org/api/events.html。
  ```

   ```javascript
  var events = require('events');
  var emitter = new event.EventEmitter();
  
  emitter.on('someEvent', function (arg1, arg2) {
    console.log('listener1', arg1, arg2);
  });
  
  emitter.on('someEvent', function (arg1, arg2) {
    console.log('listener2', arg1, arg2);
  });
  
  emitter.emit('someEvent', 'byvoid', 1991);
  // 运行的结果是：
  // listener1 byvoid 1991 
  // listener2 byvoid 1991
   ```

* error事件

  > EventEmitter 定义了一个特殊的事件 error，它包含了“错误”的语义，我们在遇到 异常的时候通常会发射 error 事件。当 error 被发射时，EventEmitter 规定如果没有响 应的监听器，Node.js 会把它当作异常，退出程序并打印调用栈

  ```javascript
  var events = require('events');
  var emitter = new events.EventEmitter();
  emitter.emit('error');
  ```

### 文件系统 fs

> fs 模块是文件操作的封装，它提供了文件的读取、写入、更名、删除、遍历目录、链接等 POSIX 文件系统操作

* fs.readFile: 

  fs.readFile(filename,[encoding],[callback(err,data)]) 是最简单的读取 文件的函数。它接受一个必选参数 filename，表示要读取的文件名。第二个参数 encoding 是可选的，表示文件的字符编码（不指定编码格式，则data形式输出）。callback 是回调函数，用于接收文件的内容。

  ```javascript
  // app.js
  var fs = require('fs');
  fs.readFile('output.txt', 'utf-8', function (err, data) {
     if (err) {
        console.error(err);
     } else {
        console.log('fileData: :', data);
     }
  });
  //输出
  ➜  nodejs_note node app.js
  fileData: : nenhall 学习node.js 笔记
  ```

* fs.readFileSync

  fs.readFileSync(filename, [encoding])是 fs.readFile 同步的版本。它接受 的参数和 fs.readFile 相同，而读取到的文件内容会以函数返回值的形式返回。如果有错 误发生，fs 将会抛出异常，你需要使用 try 和 catch 捕捉并处理异常。

* fs.open

  fs.open(path, flags, [mode], [callback(err, fd)])是 POSIX open 函数的 封装，与 C 语言标准库中的 fopen 函数类似。它接受两个必选参数，path 为文件的路径， flags 可以是以下值:

  ```
  r ：以读取模式打开文件。
  r+ ：以读写模式打开文件。
  w ：以写入模式打开文件，如果文件不存在则创建。
  w+ ：以读写模式打开文件，如果文件不存在则创建。
  a ：以追加模式打开文件，如果文件不存在则创建。
  a+ ：以读取追加模式打开文件，如果文件不存在则创建。
  mode 参数用于创建文件时给文件指定权限，默认是 0666
  callback：回调函数将会传递一个文 件描述符 fd
  
  注意：文件权限指的是 POSIX 操作系统中对文件读取和访问权限的规范，通常用一个八进制数来表示。例如 0754 表示文件所有者的权限是 7 （读、写、执行），同组的用户权限是 5 （读、执行），其他用户的权限是 4（读）， 写成字符表示就是 -rwxr-xr--。
  ```

* fs.read 

  fs.read(fd, buffer, offset, length, position, [callback(err, bytesRead, buffer)])是 POSIX read 函数的封装，相比 fs.readFile 提供了更底层的接口。fs.read 的功能是从指定的文件描述符:

  ```
  fd 中读取数据并写入 
  buffer 指向的缓冲区对象。
  offset 是 buffer 的写入偏移量。
  length 是要从文件中读取的字节数。
  position 是文件读取的起始 位置， 如果 position 的值为 null ， 则会从当前文件指针的位置读取。
  回调函数传递 bytesRead 和 buffer，分别表示读取的字节数和缓冲区对象。
  ```

  相关函数：

  fs.openSync(path, flags, [mode]) 对 fs.closeSync(fd)

  fs.read(fd, buffer, offset, length, position, callback()])  对 fs.close(fd, [callback(err)])

### HTTP 服务器与客户端

> Node.js 标准库提供了 http 模块，其中封装了一个高效的 HTTP 服务器和一个简易的 HTTP 客户端。http.Server 是一个基于事件的 HTTP 服务器，它的核心由 Node.js 下层 C++ 部分实现，而接口由 JavaScript 封装，兼顾了高性能与简易性。http.request 则是一个 HTTP 客户端工具，用于向 HTTP 服务器发起请求

#### HTTP 服务

> http.Server 是 http 模块中的 HTTP 服务器对象，用 Node.js 做的所有基于 HTTP 协 议的系统，如网站、社交应用甚至代理服务器，都是基于 http.Server 实现的。它提供了 一套封装级别很低的 API，仅仅是流控制和简单的消息解析，所有的高层功能都要通过它的 接口来实现。

* 实现一个服务器

  ```javascript
  //app.js
  var http = require('http');
  // 创建了一个 http.Server 的实例
  // 将一个函数作为 HTTP 请求处理函数,这个函数接受两个参数，分别是请求对象(req)和响应对象(res)
  http.createServer(function (req, res) {
      //响应代码200（表示请求成功）,指定响应头为'Content-Type': 'text/html'
     res.writeHead(200, {
        'Content-Type': 'text/html'
     });
     // 写入响应体 '<h1>Node.js</h1>'
     res.write('<h1>Node.js</h1>');
     // 通过 res.end 结束并发送
     res.end('<p>Hello World</p>');
  }).listen(3000);//listen 函数，启动服务器并监听 3000 端口
  
  console.log("HTTP server is listening at port 3000.");
  ```

* **http.Server** 的事件

  http.Server 是一个基于事件的 HTTP 服务器，所有的请求都被封装为独立的事件， 开发者只需要对它的事件编写响应函数即可实现 HTTP 服务器的所有功能。它继承自 EventEmitter

  * request：当客户端请求到来时，该事件被触发，提供两个参数 req 和res，分别是http.ServerRequest 和 http.ServerResponse 的实例，表示请求和响应信息。

  * connection ：当 TCP 连接建立时， 该事件被触发， 提供一个参数 socket ， 为net.Socket 的实例。connection 事件的粒度要大于 request ， 因为客户端在Keep-Alive 模式下可能会在同一个连接内发送多次请求。

  * close ：当服务器关闭时，该事件被触发。注意不是在用户连接断开时。

* **http.ServerRequest**

  1. http.ServerRequest 是 HTTP 请求的信息，是后端开发者最关注的内容。它一般由 http.Server 的 request 事件发送，作为第一个参数传递，通常简称 request 或 req

  2. HTTP 请求一般可以分为两部分：请求头（Request Header）和请求体（Requset Body）。 以上内容由于长度较短都可以在请求头解析完成后立即读取。

  3. 请求体可能相对较长， 需要一定的时间传输，因此 http.ServerRequest 提供了以下3个事件用于控制请求体 传输。

     * data ：当请求体数据到来时，该事件被触发。该事件提供一个参数 chunk，表示接收到的数据。如果该事件没有被监听，那么请求体将会被抛弃。该事件可能会被调用多次。

     * end ：当请求体数据传输完成时，该事件被触发，此后将不会再有数据到来。

     * close： 用户当前请求结束时，该事件被触发。不同于 end，如果用户强制终止了传输，也还是调用close。

  4. **ServerRequest** 的属性表

     | 名称     | 含义                       |
     | -------- | -------------------------- |
     | complete | 客户端请求是否已经发送完成 |
     | httpVersion | HTTP 协议版本，通常是 1.0 或 1.1 |
     | method | HTTP 请求方法，如 GET、POST、PUT、DELETE 等 |
     | url| 原始的请求路径，例如/static/image/x.jpg 或/user?name=byvoid |
     | headers | HTTP 请求头 |
     | trailers | HTTP 请求尾（不常见）|
     | connection | 当前 HTTP 连接套接字，为 net.Socket 的实例 |
     | socket  | connection 属性的别名 |
     | client | client 属性的别名 |

  5. 获取 GET 请求内容

     由于 GET 请求直接被嵌入在路径 中，URL是完整的请求路径，包括了 ? 后面的部分，因此你可以手动解析后面的内容作为 GET 请求的参数

     ```javascript
     var http = require('http');
     var url = require('url');
     var util = require('util');
     
     http.createServer(function (req, res) {
        res.writeHead(200, {
           'Content-Type': 'text/plain'
        });
        res.end(util.inspect(url.parse(req.url, true)));
     }).listen(3000);
     
     //浏览器中输入：http://127.0.0.1:3000/user?name=byvoid&email=byvoid@byvoid.com
     //展示：
     Url {
       protocol: null,
       slashes: null,
       auth: null,
       host: null,
       port: null,
       hostname: null,
       hash: null,
       search: '?name=byvoid&email=byvoid@byvoid.com',
       query:
        [Object: null prototype] { name: 'byvoid', email: 'byvoid@byvoid.com' },
       pathname: '/user',
       path: '/user?name=byvoid&email=byvoid@byvoid.com',
       href: '/user?name=byvoid&email=byvoid@byvoid.com' }
     ```

     

  6. 获取 POST 请求内容

     HTTP 协议 1.1 版本提供了8种标准的请求方法，其中最常见的就是 GET 和 POST。相比 GET 请求把所有的内容编码到访问路径中， POST 请求的内容全部都在请求体中。 http.ServerRequest 并没有一个属性内容为请求体，原因是等待请求体传输可能是一件耗时的工作，譬如上传文件。而很多时候我们可能并不需要理会请求体的内容，恶意的POST 请求会大大消耗服务器的资源。所以 Node.js 默认是不会解析请求体的，当你需要的时候， 需要手动来做

     ```javascript
     var http = require('http');
     var url = require('url');
     var util = require('util');
     
     var querystring = require('querystring');
     http.createServer(function(req, res) {
        var post = '';
        req.on('end', function(){
           post = querystring.parse(post);
           res.end(util.inspect(post));
        });
     }).listen(3000);
     ```

     上面代码并没有在请求响应函数中向客户端返回信息，而是定义了一个 post 变量。通过 req 的 data 事件监听函数，每当接受到请求体的数据， 就累加到 post 变量中。在 end 事件触发后，通过 querystring.parse 将 post 解析为真正的 POST 请求格式，然后向客户端返回

     **注意：**不要在真正的生产应用中使用上面这种简单的方法来获取 POST 请求，因为它有严重的效率和安全问题

     

  7. **http.ServerResponse**
     http.ServerResponse 是返回给客户端的信息，决定了用户最终能看到的结果。它 也是由 http.Server 的 request 事件发送的， 作为第二个参数传递， 一般简称为 response 或 res。

     > http.ServerResponse 有三个重要的成员函数，用于返回响应头、响应内容以及结束 请求。
     >
     > * response.writeHead(statusCode, [headers])：向请求的客户端发送响应头。
     >    statusCode 是 HTTP 状态码，如 200 （请求成功）、404 （未找到）等。headers
     >    是一个类似关联数组的对象，表示响应头的每个属性。该函数在一个请求内最多只
     >    能调用一次，如果不调用，则会自动生成一个响应头。
     > * response.write(data, [encoding])：向请求的客户端发送响应内容。data 是
     >    一个 Buffer 或字符串，表示要发送的内容。如果 data 是字符串，那么需要指定
     >    encoding 来说明它的编码方式，默认是 utf-8。在 response.end 调用之前，
     >    response.write 可以被多次调用。
     > * response.end([data], [encoding])：结束响应，告知客户端所有发送已经完
     >    成。当所有要返回的内容发送完毕的时候，该函数 必须 被调用一次。它接受两个可
     >    选参数，意义和 response.write 相同。如果不调用该函数，客户端将永远处于
     >    等待状态。

#### HTTP 客户端

> http 模块提供了两个函数 http.request 和 http.get，功能是作为客户端向 HTTP 服务器发起请求。
>
> http.request(options, callback) 发起 HTTP 请求。接受两个参数，option 是
>  一个类似关联数组的对象，表示请求的参数，callback 是请求的回调函数。option
>  常用的参数如下所示。
>
> * host ：请求网站的域名或 IP 地址。
>
> * port ：请求网站的端口，默认 80。
>
> * method ：请求方法，默认是 GET。
>
> * path ：请求的相对于根的路径，默认是“/”。QueryString 应该包含在其中。例如 /search?query=byvoid。
> * headers ：一个关联数组对象，为请求头的内容。
>
> * callback 传递一个参数，为 http.ClientResponse 的实例。
>    http.request 返回一个 http.ClientRequest 的实例。

下面是一个通过 http.request 发送 POST 请求的代码：

```javascript
//httprequest.js
var http = require('http');
var querystring = require('querystring');
var contents = querystring.stringify({
   name: 'byvoid',
   email: 'byvoid@byvoid.com',
   address: 'Zijing 2#, Tsinghua University',
});
var options = {
   host: 'www.byvoid.com',
   path: '/application/node/post.php',
   method: 'POST',
   headers: {
      'Content-Type': 'application/x-www-form-urlencoded',
      'Content-Length': contents.length
   }
};
var req = http.request(options, function (res) {
   res.setEncoding('utf8');
   res.on('data', function (data) {
      console.log(data);
   });
});
req.write(contents);
req.end();
```

* http.get(options, callback) http 模块还提供了一个更加简便的方法用于处理GET请求：http.get。它是 http.request 的简化版，唯一的区别在于：http.get自动将请求方法设为了 GET 请求，同时不需要手动调用 req.end()。

```javascript
//httprequest.js
var http = require('http');
http.get({
   host: 'www.byvoid.com'
}, function (res) {
   res.setEncoding('utf8');
   res.on('data', function (data) {
      console.log(data);
   });
});
```

* http.ClientRequest 是由 http.request 或 http.get 返回产生的对象，表示一 个已经产生而且正在进行中的 HTTP 请求，它提供一个 response 事件，即 http.request 或 http.get 第二个参数指定的回调函数的绑定对象。我们也可以显式地绑定这个事件的监听函数：

```javascript
//httpresponse.js
var http = require('http');
var req = http.get({host: 'www.byvoid.com'});
req.on('response', function(res) {
 	res.setEncoding('utf8');
 	res.on('data', function (data) {
 	console.log(data);
 });
});
```

>  http.ClientRequest 像 http.ServerResponse 一样也提供了 write 和 end 函 数，用于向服务器发送请求体，通常用于 POST、PUT 等操作。所有写结束以后必须调用 end 函数以通知服务器，否则请求无效。相关函数：
>
> - request.abort()：终止正在发送的请求。
> - request.setTimeout(timeout, [callback])：设置请求超时时间，timeout 为毫秒数。当请求超时以后，callback 将会被调用

* http.clientResponse

  http.ClientResponse 与 http.ServerRequest 相似，提供了三个事件 data、end 和 close，分别在数据到达、传输结束和连接结束时触发，其中 data 事件传递一个参数 chunk，表示接收到的数据。

  http.ClientResponse 也提供了一些属性，用于表示请求的结果状态:

  > statusCode : HTTP 状态码，如 200、404、500 
  >
  > httpVersion : HTTP 协议版本，通常是 1.0 或 1.1 
  >
  > headers : HTTP 请求头
  >
  > trailers : HTTP 请求尾（不常见）
  >
  > 
  >
  > **http.ClientResponse 还提供了以下几个特殊的函数**
  >
  > response.setEncoding([encoding])：设置默认的编码，当 data 事件被触发
  >  时，数据将会以 encoding 编码。默认值是 null，即不编码，以 Buffer 的形式存
  >  储。常用编码为 utf8。
  > response.pause()：暂停接收数据和发送事件，方便实现下载功能。
  > response.resume()：从暂停的状态中恢复。
  >
  > 参考资料：http://nodejs.org/api/index.html

## 第五章 Node.js Web开发实战

### Node.js 工作原理

Node.js 实现网站的工作原理。Node.js 和 PHP、 Perl、ASP、JSP 一样，目的都是实现动态网页，也就是说由服务器动态生成 HTML 页面。 之所以要这么做，是因为静态 HTML 的可扩展性非常有限，无法与用户有效交互。同时如 果有大量相似的内容，例如产品介绍页面，那么1000个产品就要1000个静态的 HTML 页面， 维护这1000个页面简直是一场灾难，因此动态生成 HTML 页面的技术应运而生。是在以 HTML 为主的模板中插入程序代码。这种方 式在2002年前后非常流行，但它的问题是页面和程序逻辑紧密耦合，任何一个网站规模变大 以后，都会遇到结构混乱，难以处理的问题。为了解决这种问题，以 MVC 架构为基础的平台逐渐兴起；Node.js 本质上和 Perl 或 C++ 一样，都可以作为 CGI 扩展被调用，但它还可以跳过 HTTP 服务器，因为它本身就是。传统的架构中 HTTP 服务器的角色会由 Apache、Nginx、IIS 之类 的软件来担任，而 Node.js 不需要。Node.js 提供了 http 模块，它是由 C++ 实现的，性能可靠，可以直接应用到生产环境。

> MVC （Model-View-Controller，模型视图控制器）是一种软件的设计模式，它最早是 由 20 世纪 70 年代的 Smalltalk 语言提出的，即把一个复杂的软件工程分解为三个层面：模 型、视图和控制器。
>
> * 模型是对象及其数据结构的实现，通常包含数据库操作。
> * 视图表示用户界面，在网站中通常就是 HTML 的组织结构。
> * 控制器用于处理用户请求和数据流、复杂模型，将输出传递给视图。

#### 了解 **http** 模块

Node.js 由于不需要另外的 HTTP 服务器，因此减少了一层抽象，给性能带来不少提升， 但同时也因此而提高了开发难度。举例来说，下面是一个简单的HTTP服务，提交时以 POST 的方式将请求发送，然后将发送过来内容原封不动的返回给用户

```javascript
var http = require('http');
var querystring = require('querystring');
var server = http.createServer(function (req, res) {
   var post = '';
   // req.headers;
   // req.method;
   // req.url;
   // req.client;

   req.on('data', function (chunk) {
      post += chunk;
   });
   req.on('end', function () {
      post = querystring.parse(post);
      //以流的方式持续回给发送端
      res.write(post.title);
      res.write(post.text);
      res.statusCode(200);
      res.end();
   });
}).listen(3000);
```

### Express框架

#### 简介

> npm 提供了大量的第三方模块，其中不乏许多 Web 框架，我们没有必要重复发明轮子， 因而选择使用 Express 作为开发框架，因为它是目前最稳定、使用最广泛，而且 Node.js 官 方推荐的唯一一个 Web 开发框架。
>  Express （ http://expressjs.com/ ） 除了为 http 模块提供了更高层的接口外，还实现了 许多功能，其中包括：
>
> * 路由控制；
> * 模板解析支持；
>
> * 动态视图；
> * 用户会话；
>
> * CSRF 保护；
>
> * 静态文件服务；
>
> * 错误控制器；
>
> * 访问日志；
>
> * 缓存；
>
> * 插件支持。
>
>
> 需要指出的是，Express 不是一个无所不包的全能框架，像 Rails 或 Django 那样实现了 模板引擎甚至 ORM （Object Relation Model，对象关系模型）。它只是一个轻量级的 Web 框架，多数功能只是对 HTTP 协议中常用操作的封装，更多的功能需要插件或者整合其他模块 来完成。

#### 安装

* 安装`$ npm install -g express`

* 查看帮助：$ express --help

* Express 例子(重新实现前面)

  ```javascript
  var express = require('express');
  var app = express.createServer();
  app.use(express.bodyParser());
  app.all('/', function (req, res) {
     res.send(req.body.title + req.body.text);
  });
  app.listen(3000);
  ```

#### 创建工程

> Express 在初始化一个项目的时候需要指定模板引擎，默认支持Jade和ejs，为了降低学
> 习难度我们推荐使用 ejs ，同时暂时不添加 CSS 引擎和会话支持。

1. 建立网站：

   1. 新起一个文件目录

      `$ mkdir nodetest && cd nodetest`

   2. express创建ejs模板，如下右图

      `$ npm install -g express && npm install -g express-generator`

      `$ express -e nodeblog(-e 是ejs模板)`  

      //`$express -t nodeblog(-t 是jade模板引擎)`

      // express3之前 不需要另外install ，可直接创建ejs模板：$ express -t ejs nodeblog

      //原因：express3+已经把创建一个APP的功能分离出来为express-generator，没它你创建不了应用程序

   3. 进入生成的文件夹，打开package.json文件查看模板引擎，如下左图

   4.  进入nodeblog文件夹

      `$ cd nodeblog/`

   5.  安装相关依赖模块

      `$ npm install`

   6. 启动服务

      `$ npm start`

      执行npm start其实是执行package.json中的script对应的对象中的start属性所对应的命令行

   7. 可以用浏览器打开：http://localhost:3000 看到 Welcome to Express

   **注：**ejs（Embedded JavaScript）是一个标签替换引擎，其语法与 ASP、PHP 相似，易于学习，目前被广泛应用。Express 默认提供的引擎是 jade，它颠覆了传统的模板引擎，制定了一套完整的语法用来生成 HTML 的每个标签结构，功能强大但不易学习。

   | 左图                                                         | 右图                                                         |
   | ------------------------------------------------------------ | ------------------------------------------------------------ |
   | ![](https://blogimage-1257063273.cos.ap-guangzhou.myqcloud.com/20190222110947.png) | ![](https://blogimage-1257063273.cos.ap-guangzhou.myqcloud.com/20190222112735.png) |

2. 基本结构

   * 首先我们导入了 Express 模块，前面已经通过 npm 安装到了本地，在这里可以直接通过 require 获取

   * app.js文件结构说明：

     app.set 是 Express 的参数设置工具，接受一个键（key）和一个值（value），可用的参数如下所示。

     * basepath：基础地址，通常用于 res.redirect() 跳转。
     * views：视图文件的目录，存放模板文件。
     * view engine：视图模板引擎。

     * view options：全局视图参数对象。

     * view cache：启用视图缓存。

     * case sensitive routes：路径区分大小写。

     * strict routing：严格路径，启用后不会忽略路径末尾的“ / ”。

     * jsonp callback：开启透明的 JSONP 支持。

   * routes/index.js 是路由文件，相当于控制器，用于组织展示的内容;

   * index.ejs 是模板文件，即 routes/index.js 中调用的模板;

3. 工作原理

   * 当通过浏览器访问 app.js 建立的服务器时， 会看到一个简单的页面， 实际上它已经完成了许多透明的工作， 现在就让我们来解释一下它的工作机制， 以帮助理解网站的整 体架构。访问 http://localhost:3000，浏览器会向服务器发送以下请求：

   ```
   GET / HTTP/1.1
   Host: localhost:3000
   Connection: keep-alive
   Cache-Control: max-age=0
   User-Agent: Mozilla/5.0 AppleWebKit/535.19 (KHTML, like Gecko) Chrome/18.0.1025.142
    Safari/535.19
   Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
   Accept-Encoding: gzip,deflate,sdch
   Accept-Language: zh;q=0.8,en-US;q=0.6,en;q=0.4
   Accept-Charset: UTF-8,*;q=0.5
   ```

   * app 会 解析请求的路径，调用相应的逻辑。app.js 中有一行内容是 app.get('/', routes.index)， 它的作用是规定路径为“/”的 GET 请求由 routes.index 函数处理。routes.index 通 过 res.render('index', { title: 'Express' }) 调用视图模板 index，传递 title 变量。最终视图模板生成 HTML 页面，返回给浏览器

   *  浏览器在接收到内容以后，经过分析发现要获取 /stylesheets/style.css，因此会再次向服 务器发起请求。 app.js 中并没有一个路由规则指派到 /stylesheets/style.css， 但 app 通过 app.use(express.static(__dirname + '/public')) 配置了静态文件服务器，因此 /stylesheets/style.css 会定向到 app.js 所在目录的子目录中的文件 public/stylesheets/style.css；由 Express 创建的网站架构如下图所示。

     ![](https://blogimage-1257063273.cos.ap-guangzhou.myqcloud.com/20190222124034.png)

4. 创建路由规则

   > 我们在浏览器中访问譬如 http://localhost:3000/abc 这样不存在的页面时，服务器会在 响应头中返回 404 Not Found 错误，这是因为 /abc 是一个不存在的路由规则，而且它也不是一个 public 目录下的文件，所以 Express返回了404 Not Found的错误

   * 路径匹配

     Express 还支持更高级的路径匹配模式。例 如我们想要展示一个用户的个人页面，路径为 /user/[username]，可以用下面的方法定义路由 规则：

     将routes/index.js文件改成如下：

     ```javascript
     var express = require('express');
     var router = express.Router();
     
     /* GET home page. */
     router.get('/', function(req, res, next) {
       res.render('index', { title: 'Express' });
     });
     //增加的代码
     router.get('/user/:username', function(req, res, next) {
       res.render('index', { title: 'Express'+ " " + req.params.username });
     });
     
     module.exports = router;
     ```

     然后重启app.js，访问：http://localhost:3000/user/nenhall

     ![](https://blogimage-1257063273.cos.ap-guangzhou.myqcloud.com/20190222140808.png)

     路径规则 /user/:username 会被自动编译为正则表达式，类似于`\/user\/(\[^\/]+)\/?` 这样的形式。路径参数可以在响应函数中通过 req.params 的属性访问。

5. 控制权转移

   * Express 支持同一路径绑定多个路由响应函数，例如：

     ```javascript
     //router/index.js
     
     router.all('/user/:username', function(req, res, next) {
       res.send('all methods captured');
     });
     
     router.get('/user/:username', function(req, res, next) {
       res.render('index', { title: 'Express'+ " " + req.params.username });
     });
     ```

     当你访问任何被两条同样的规则匹配到的路径时，会发现请求总是被前一条路由规则捕获，后面的规则会被忽略，原因是Express在处理路由规则时，会优先匹配定义的路由规则，因此后面相同的规则被屏蔽。

   * Express 提供了路由控制权转移的方法， 通过调用 next()，会将路由控制权转移给后面的规则

     ```javascript
     //router/index.js
     router.all('/user/:username', function(req, res, next) {
       console.log('all methods captured');
       next();
         //这个路由规则实际上起到了中间件的作用，我们还可以改成下面这样，在这进行检查用户名的合法性，eg.
         if (req.params.username == xxxxx) {
             //do something
             next();
         } else {
             //do something
         }
     });
     
     router.get('/user/:username', function(req, res, next) {
       res.render('index', { title: 'Express'+ " " + req.params.username });
     });
     ```

     当访问被匹配到的路径时，如 http://localhost:3000/user/nenhall，会发现终端中打印了 all methods captured，而且浏览器中显示了 user: nenall。说明请求先被第一条路由规则捕获，完成 console.log 使用 next() 转移控制权，又被第二条规则捕获，向浏览器返回了信息。

### 模板引擎

#### 简介

> 模板引擎（Template Engine）是一个从页面模板根据一定的规则生成 HTML 的工具

模板引擎以数据和页面模板为输入，生成 HTML 页面，然后返回给控制器， 由控制器交回客户端。下图是模板引擎在 MVC 架构中的示意图

![](https://blogimage-1257063273.cos.ap-guangzhou.myqcloud.com/20190222142954.png)   

#### 使用模板引擎

> 基于 JavaScript 的模板引擎有许多种实现，我们推荐使用 ejs （Embedded JavaScript）， 因为它十分简单，而且与 Express 集成良好。由于它是标准 JavaScript 实现的，因此它不仅 可以运行在服务器端，还可以运行在浏览器中
>
> ejs 的标签系统非常简单，它只有以下3种标签:
>
> * <% code %>：JavaScript 代码。
>
> * <%= code %>：显示替换过 HTML 特殊字符的内容。
>
> * <%- code %>：显示原始 HTML 内容。

* 我们在 app.js 中通过以下两个语句设置了模板引擎和页面模板的位置：

  ```javascript
  // app.js
  // view engine setup
  app.set('views', path.join(__dirname, 'views'));
  app.set('view engine', 'ejs');
  // 表明要使用的模板引擎是 ejs， 页面模板在 views 子目录下。 在 routes/index.js 的 exports.index 函数中通过如下语句调用模板引擎：
  ```

  ```javascript
  // router/index.js
  router.get('/', function(req, res, next) {
    res.render('index', { title: 'Express' });
  });
  // res.render 的功能是调用模板引擎，并将其产生的页面直接返回给客户端。它接受 两个参数，第一个是模板的名称，即 views 目录下的模板文件名，不包含文件的扩展名；第二个参数是传递给模板的数据，用于模板翻译
  // res.render('index', { title: 'Express' }); 这句代码的意思：引用了views目录下名为index.ejs文件，并把` { title: 'Express' }`这一个参数过去
  ```

#### 页面布局

> Express3+之后页面布局一起文件放在了/views/index.ejs文件中，在之前是有单独的layout.ejs文件

#### 片段视图

>  Express 的视图系统还支持片段视图 （partials），它就是一个页面的片段，通常是重复的 内容，用于迭代显示。通过它你可以将相对独立的页面块分割出去

```javascript
// 1. 在router/index.js 增加如下代码
router.get('/list', function(req, res) {
  res.render('list', {
  title: 'List',
  items: [1991, 'byvoid', 'express', 'Node.js']
  });
 });

// 2. 在 views 目录下新建 list.ejs，内容是：
<ul><%- partial('listitem', items) %></ul>
// 3. 同时新建 listitem.ejs，内容是：
<li><%= listitem %></li>
// 4.访问 http://localhost:3000/list，可以在源代码中看到刚才的内容：
```

partial 是一个可以在视图中使用函数，它接受两个参数，第一个是片段视图的名称， 第二个可以是一个对象或一个数组，如果是一个对象，那么片段视图中上下文变量引用的就 是这个对象；如果是一个数组，那么其中每个元素依次被迭代应用到片段视图。片段视图中 上下文变量名就是视图文件名，例如上面的'listitem'。

#### 视图助手

>  Express 提供了一种叫做视图助手的工具，它的功能是允许在视图中访问一个全局的函数 或对象，不用每次调用视图解析的时候单独传入。前面提到的 partial 就是一个视图助手。
>
> 视图助手有两类：静态视图助手和动态视图助手。
>
> * 静态视图助手：可以是任何类型的对象，包括接受任意参数的函数，但访问到的对象必须是与用户请求无关的，可以通过 app.helpers() 函数注册，它接受一个对象，对象的每个属性名称为视图助手的名称，属性值对应视图助手的值。
>
> * 动态视图助手：只能是一个函数，这个函数不能接受参数，但可以访问 req 和 res 对象；通过 app.dynamicHelpers() 注册，方法与静态视图助手相同，但每个属性的值必须为一个函数，该函数提供 req 和 res

   ### 建立微博网站

* 功能点分析：一个完整的微博系统应该支持信息的评论、转发、圈 点用户等功能，但出于演示目的，我们不能一一实现所有功能，只是实现一个微博社交网站 的雏形。

* 路由规划：整个网站的骨架部分，因为它处于整个架构的枢纽位置，相当于各个接口之间的粘合剂， 所以应该优先考虑。根据功能设计，我们把路由按照以下方案规划。

  * 首页

  * 用户的主页

  * 发表信息

  * 用户注册

  * 用户登录

  * 用户登出

  * 用户状态细分：
    * 发表信息以及用户登出页面必须是已登录用户才能操作的功能
    * 用户注册和用户登入所面向的对象必须是未登入的用户
    * 首页和用户主页则 针对已登入和未登入的用户显示不同的内容。

* 具体实现省略，主要是用到第三方库

* 关键知识点：app.post(‘/...', **function**(req, res) {})

  * req.body 就是 POST 请求信息解析过后的对象， 例如我们要访问用户传递的
     password 域的值，只需访问 req.body['password'] 即可。
  * req.flash 是 Express 提供的一个奇妙的工具，通过它保存的变量只会在用户当前
     和下一次的请求中被访问，之后会被清除，通过它我们可以很方便地实现页面的通知
     和错误信息显示功能。
  * res.redirect 是重定向功能，通过它会向用户返回一个 303 See Other 状态，通知
     浏览器转向相应页面。
  * crypto 是 Node.js 的一个核心模块，功能是加密并生成各种散列，使用它之前首先
     要声明 var crypto = require('crypto')。我们代码中使用它计算了密码的散
     列值。
  * 通过 req.session.user = newUser 向会话对象写入了当前用户的信息，在后面
     我们会通过它判断用户是否已经登录。

## 进阶话题

### 模块加载机制

> Node.js 的模块加载对用户来说十分简单，只需调用 require 即可，但其内部机制较为 复杂。我们通过这一节简要介绍一下 Node.js 模块加载的一些细节

#### 模块的类型

Node.js 的模块可以分为两大类：一类是核心模块，另一类是文件模块。

1. 核心模块就是 Node.js 标准 API 中提供的模块，如 fs、http、net、vm 等，这些都是由 Node.js 官方提供的模块， 编译成了二进制代码。 我们可以直接通过 require 获取核心模块， 例如 require('fs') 。 核心模块拥有最高的加载优先级， 换言之如果有模块与其命名冲突， Node.js 总是会加载核心模块。
2. 文件模块则是存储为单独的文件（或文件夹）的模块，可能是 JavaScript 代码、JSON 或 编译好的 C/C++ 代码。文件模块的加载方法相对复杂，但十分灵活，尤其是和 npm 结合使 用时。在不显式指定文件模块扩展名的时候，Node.js 会分别试图加上 .js、.json 和 .node扩展名。.js 是 JavaScript 代码，.json 是 JSON 格式的文本，.node 是编译好的 C/C++ 代码

#### 按路径加载模块

文件模块的加载有两种方式，一种是按路径加载，一种是查找 node_modules 文件夹。
如果 require 参数以“ / ”开头，那么就以绝对路径的方式查找模块名称，例如 require
('/home/byvoid/module') 将会按照优先级依次尝试加载 /home/byvoid/module.js、/home/byvoid/module.json 和 /home/byvoid/module.node。
 如果 require 参数以“ ./ ”或“ ../ ”开头，那么则以相对路径的方式来查找模块， 这种方式在应用中是最常见的。例如前面的例子中我们用了require('./hello')来加载 同一文件夹下的hello.js。

#### 通过查找 node_modules 目录加载模块

* 如果require参数不以“ / ”、“ ./ ”或“ ../ ”开头，而该模块又不是核心模块，那么就 要通过查找 node_modules 加载模块了。我们使用npm获取的包通常就是以这种方式加载的。在某个目录下执行命令npm install express，你会发现出现了一个叫做node_modules 的目录，里面的结构大概如下图所示。

* 在 node_modules 目录的外面一层，我们可以直接使用 require('express') 来代替 require('./node_modules/express')。这是Node.js模块加载的一个重要特性：通过查 找 node_modules 目录来加载模块。
* 当 require 遇到一个既不是核心模块，又不是以路径形式表示的模块名称时，会试图 在当前目录下的 node_modules 目录中来查找是不是有这样一个模块。如果没有找到，则会 在当前目录的上一层中的 node_modules 目录中继续查找，反复执行这一过程，直到遇到根 目录为止。

![](https://blogimage-1257063273.cos.ap-guangzhou.myqcloud.com/20190225094729.png)

举个例子，我们要在 /home/byvoid/develop/foo.js 中使用 require('bar.js') 命令，Node.js会依次查找：

* /home/byvoid/develop/node_modules/bar.js

* /home/byvoid/node_modules/bar.js

* /home/node_modules/bar.js

* /node_modules/bar.js
   为什么要这样做呢？因为通常一个工程内会有一些子目录，当子目录内的文件需要访问 到工程共同依赖的模块时，就需要向父目录上溯了。比如说工程的目录结构如下：

  我们不仅要在 project 目录下的 app.js 中使用 require('express')，而且可能要在 controllers 子目录下的 index_controller.js 中也使用 require('express')，这时就需要向 父目录上溯一层才能找到 node_modules 中的 express 了。

  ![](https://blogimage-1257063273.cos.ap-guangzhou.myqcloud.com/20190225095154.png)

#### 加载缓存

> 我们在前面提到过，Node.js 模块不会被重复加载，这是因为 Node.js 通过文件名缓存所 有加载过的文件模块，所以以后再访问到时就不会重新加载了。注意，Node.js 是根据实际文件名缓存 的 ， 而不是 require() 提供的参数缓存的 ， 也就是说即使你分别通过 require('express') 和 require('./node_modules/express') 加载两次，也不会重复加载，因为尽管两次参数不同，解析到的文件却是同一个

#### 加载顺序

> 下面总结一下使用 require(some_module) 时的加载顺序。
> (1) 如果some_module 是一个核心模块，直接加载，结束。
> (2) 如果some_module以“ / ”、“ ./ ”或“ ../ ”开头，按路径加载 some_module，结束。
> (3) 假设当前目录为 current_dir，按路径加载 current_dir/node_modules/some_module。
>  如果加载成功，结束。
> 如果加载失败，令current_dir为其父目录。
> 重复这一过程，直到遇到根目录，抛出异常，结束。

### 控制流

基于异步 I/O 的事件式编程容易将程序的逻辑拆得七零八落，给控制流的疏理制造障碍。

* 循环的陷阱

  ```javascript
  //forloop.js
  var fs = require('fs');
  var files = ['a.txt', 'b.txt', 'c.txt'];
  for (var i = 0; i < files.length; i++) {
      fs.readFile(files[i], 'utf-8', function (err, contents) {
          console.log(files[i] + ': ' + contents);
      });
  }
  ```

   这段代码的功能很直观，就是依次读取文件 a.txt、b.txt、c.txt，并输出文件名和内容。 假设这三个文件的内容分别是 AAA、BBB 和 CCC，那么我们期望的输出结果就是：

  ```
  a.txt: AAA 
  b.txt: BBB 
  c.txt: CCC
  ```

  可是我们运行这段代码的结果是怎样的呢？竟然是这样的结果：

  ```
  undefined: AAA 
  undefined: BBB 
  undefined: CCC
  ```

  这个结果说明文件内容正确输出了，而文件名却不对，也就意味着，contents 的结果 是正确的，但 files[i] 的值是 undefined。这怎么可能呢，文件名不正确却能读取文件 内容？既然难以直观地理解，我们就把 files[i] 分解并打印出来看看，在读取文件的回调 函数中分别输出 files、i 和 files[i]。

  修改后的代码：

  ```javascript
  //forloopi.js
  var fs = require('fs');
  var files = ['a.txt', 'b.txt', 'c.txt'];
  for (var i = 0; i < files.length; i++) {
      fs.readFile(files[i], 'utf-8', function (err, contents) {
          console.log(files);
          console.log(i);
          console.log(files[i]);
      });
  }
  // 运行修改后的代码，结果如下：
  [ 'a.txt', 'b.txt', 'c.txt' ] 3
  undefined
  [ 'a.txt', 'b.txt', 'c.txt' ] 3
  undefined
  [ 'a.txt', 'b.txt', 'c.txt' ] 3
  undefined
  ```

  看到这里是不是有点启发了呢？三次输出的 i 的值都是 3，超出了 files 数组的下标 范围，因此 files[i] 的值就是 undefined 了。这种情况通常会在 for 循环结束时发 生， 例如 for (var i = 0; i < files.length; i++) ， 退出循环时 i 的值就是 files.length 的值。既然 i 的值是 3，那么说明了事实上 fs.readFile 的回调函数中 访问到的 i 值都是循环退出以后的，因此不能分辨。而 files[i] 作为 fs.readFile 的 第一个参数在循环中就传递了，所以文件可以被定位到，而且可以显示出文件的内容。

  

  解决方案：利用JavaScript 函数式编程的特性，手动建立一个闭包：

  ```javascript
  //forloopclosure.js
  var fs = require('fs');
  var files = ['a.txt', 'b.txt', 'c.txt'];
  for (var i = 0; i < files.length; i++) {
      (function (i) {
          fs.readFile(files[i], 'utf-8', function (err, contents) {
              console.log(files[i] + ': ' + contents);
          });
      })(i);
  }
  //上面这个方法：事实上以上这种写法并不常见，因为它降低了程序的可读性，故不推荐使用，大多数情 况下我们可以用数组的 forEach 方法解决这个问题：
  //callbackforeach.js
  var fs = require('fs');
  var files = ['a.txt', 'b.txt', 'c.txt'];
  files.forEach(function (filename) {
      fs.readFile(filename, 'utf-8', function (err, contents) {
          console.log(filename + ': ' + contents);
      });
  });
  ```

### Node.js 应用部署

#### 日志功能

> 这个服务器运行时没有产生任何日志，包括访问日志和错误日志，所以有必要实现它的日志功能
>
> Express支持两种运行模式：开发模式和产品模式，前者的目的是利于调试，后者则是利于部署。使用产 品模式运行服务器的方式很简单，只需设置NODE_ENV 环境变量。通过NODE_ENV=production node app.js命令运行服务器可以看到：`Express server listening on port 3000 in production mode`

* 实现日志功能：

  Express 提供了一个访问日志中间件，只需指定stream 参数为一个输出流即可将访问日 志写入文件。打开app.js，在最上方加入以下代码：

  ```javascript
  var fs = require('fs');
  var accessLogfile = fs.createWriteStream('access.log', {
      flags: 'a'
  });
  var errorLogfile = fs.createWriteStream('error.log', {
      flags: 'a'
  });
  //然后在app.configure 函数第一行加入：
  app.use(express.logger({
      stream: accessLogfile
  }));
  5
  //至于错误日志， 需要单独实现错误响应， 修改如下：
  app.configure('production', function () {
      app.error(function (err, req, res, next) {
          var meta = '[' + new Date() + '] ' + req.url + '\n';
          6
          errorLogfile.write(meta + err.stack + '\n');
          next();
      });
  });
  7
  //这段代码的功能是通过app.error 注册错误响应函数， 在其中将错误写入错误日志流。
  //现在重新运行服务器， 在浏览器中访问,即可在app.js 同一目录下的 access.log 文件中查看
  ```

#### 使用**cluster**模块

> 从0.6 版本开始，Node.js 提供了一个核心模块：cluster。cluster的功能是生成与当 前进程相同的子进程，并且允许父进程和子进程之间共享端口。Node.js 的另一个核心模块 child_process 也提供了相似的进程生成功能，但最大的区别在于cluster 允许跨进程端 口复用，给我们的网络服务器开发带来了很大的方便。

* 为了在外部模块调用app.js，首先需要禁止服务器自动启动。修改app.js，在app.listen (3000); 前后加上判断语句：

  ```javascript
  if (!module.parent) {
      app.listen(3000);
      console.log("Express server listening on port %d in %s mode", app.address().port,
          app.settings.env);
  }
  //这个语句的功能是判断当前模块是不是由其他模块调用的，如果不是，说明它是直接启 动的，此时启动调试服务器；如果是，则不自动启动服务器
  ```

* 接下来就让我们通过cluster 调用app.js。创建cluster.js，内容如下所示：

  ```javascript
  // cluster.js
  var cluster = require('cluster');
  var os = require('os');
  // 获取CPU 的数量
  var numCPUs = os.cpus().length;
  var workers = {};
  if (cluster.isMaster) {
      // 主进程分支
      cluster.on('death', function (worker) {
          // 当一个工作进程结束时，重启工作进程 delete workers[worker.pid];
          worker = cluster.fork();
          workers[worker.pid] = worker;
      });
      // 初始开启与CPU 数量相同的工作进程 for (var i = 0; i < numCPUs; i++) { var worker = cluster.fork();
      workers[worker.pid] = worker;
  } else {
      // 工作进程分支，启动服务器
      var app = require('./app');
      app.listen(3000);
  }
  // 当主进程被终止时，关闭所有工作进程
  process.on('SIGTERM', function () {
      for (var pid in workers) {
          process.kill(pid);
      }
      process.exit(0);
  });
  ```

  cluster.js 的功能是创建与CPU 核心个数相同的服务器进程，以确保充分利用多核CPU 的 资源。主进程生成若干个工作进程，并监听工作进程结束事件，当工作进程结束时，重新启 动一个工作进程。分支进程产生时会自顶向下重新执行当前程序，并通过分支判断进入工作 进程分支，在其中读取模块并启动服务器。通过cluster启动的工作进程可以直接实现端口 复用，因此所有工作进程只需监听同一端口。当主进程终止时，还要主动关闭所有工作进程。

   在终端中执行node cluster.js 命令，可以看到进程列表中启动了多个 node 进程（8 核CPU）：

  ![](https://blogimage-1257063273.cos.ap-guangzhou.myqcloud.com/20190225164726.png)

  

  #### 启用脚本

  > 我们启动脚本来简化维护工作。如果你维护过Linux 服务器，会对 /etc/init.d/ 下面的脚本有印象。 例如使用 /etc/init.d/nginx start 和 /etc/init.d/ nginx stop 可以启动和关闭Nginx 服务器。我们通过bash 脚本也来实现一个类似的功能， 创建microblog 并使用chmod +x nodeblog 赋予其执行权限，脚本内容为：

  ```shell
  #! /bin/sh
  // 只支持 POSIX 操作系统，如 Linux、Mac OS 等，在 Windows不支持
  NODE_ENV = production
  DAEMON = "node cluster.js"
  NAME = Microblog
  DESC = Microblog
  PIDFILE = "microblog.pid"
  case "$1" in
  start)
  echo "Starting $DESC: "
  nohup $DAEMON > /dev/null &
      echo $! > $PIDFILE
  echo "$NAME.";;
  stop)
  echo "Stopping $DESC: "
  pid = 'cat $PIDFILE'
  kill $pid
  rm $PIDFILE
  echo "$NAME.";;
  esac exit 0
  
  //运行
  ./nodeblog start  //关闭 stop
  ```

  它的功能是通过nohup 启动服务器，使进程不会因为退出终端而关闭，同时将主进程 的pid 写入microblog.pid 文件。当调用结束命令时，从microblog.pid 读取pid 的值，终止主 进程以关闭服务器。

#### 共享 80 端口

> 到目前为止，网站都是运行在3000端口下的，也就是说用户必须在网址中加入:3000才 能访问网站。默认的HTTP 端口是80，因此必须监听80端口才能使网址更加简洁。如果整个 服务器只有一个网站，那么只需让app.js 监听80 端口即可。但很多时候一个服务器上运行 着不止一个网站，尤其是还有用其他语言（如PHP）写成的网站，这该怎么办呢？此时虚拟 主机可以粉墨登场了。
>
> 虚拟主机，就是让多个网站共享使用同一服务器同一IP地址，通过域名的不同来划分请 求。主流的HTTP服务器都提供了虚拟主机支持，如Nginx、Apache、IIS等。我们以Nginx为 例，介绍如何通过反向代理实现Node.js 虚拟主机。

​	在Nginx 中设置反向代理和虚拟主机非常简单，下面是配置文件的一个示例：

```
server {
    listen 80;
    server_name mysite.com;
    location / {
        proxy_pass http: //localhost:3000; }
    }
}
```

 	这个配置文件的功能是监听访问mysite.com 80 端口的请求， 并将所有的请求转发给 http://localhost:3000，即我们的Node.js 服务器。现在访问http://mysite.com/，就相当于服务器访问http://localhost:3000了。
 	在添加了虚拟主机以后，还可以在Nginx配置文件中添加访问静态文件的规则（具体请 参考Nginx文档），删去app.js 中的app.use(express.static(__dirname + '/public'));。 这样可以直接让Nginx 来处理静态文件，减少反向代理以及Node.js 的开销。

### Node.js 不适合做什么

1. 计算密集型的程序
2. 单用户多任务型应用
3. 逻辑十分复杂的事务
4.  Unicode 与国际化

## 附录

###作用域 

1. 作用域

   以下函数在c、c++、java中都是不合法的，在 JavaScript 中却完全合法，这是因为 JavaScript 的作用域完全是由函数来决定的，if、for 语句中的花括号不是独 立的作用域。

   ```javascript
   if (true) {
       var somevar = 'value';
   }
   console.log(somevar); // 输出 value
   ```

   

2. 不同于大多数类 C 的语言，由一对花括号封闭的代码块就是一个作用域，JavaScript 的作用域是通过函数来定义的，在一个函数中定义的变量只对这个函数内部可见，我们称为函 数作用域。在函数中引用一个变量时，JavaScript 会先搜索当前函数作用域，或者称为“局 部作用域”，如果没有找到则搜索其上层作用域， 一直到全局作用域。我们看一个简单的 例子：

   ```javascript
   var v1 = 'v1';
   var f1 = function () {
       console.log(v1); // 输出 v1 
   };
   f1();
   
   var f2 = function () {
       var v1 = 'local';
       console.log(v1); // 输出 local 
   };
   f2();
   //以上示例十分明了， JavaScript 的函数定义是可以嵌套的， 每一层是一个作用域， 变量 搜索顺序是从内到外。 下面这个例子可能就有些令人困惑：
   var scope = 'global';
   var f = function () {
       console.log(scope); // 输出 undefined
       var scope = 'f';
   }
   f();
   ```

   上面代码可能和你预想的不一样，没有输出 global，而是undefined，这是为什么呢？ 这是 JavaScript 的一个特性，按照作用域搜索顺序，在 console.log 函数访问 scope 变 量时，JavaScript 会先搜索函数 f 的作用域，恰巧在 f 作用域里面搜索到 scope 变量， 所以上层作用域中定义的 scope 就被屏蔽了，但执行到 console.log 语句时，scope 还 没被定义，或者说初始化，所以得到的就是 undefined 值了。

3. 函数作用域的嵌套

   ```javascript
   var f = function () {
       var scope = 'f0';
       (function () {
           var scope = 'f1';
           (function () {
               console.log(scope); // 输出 f1
           })();
       })();
   };
   f();
   //上面是一个函数作用域嵌套的例子，我们在最内层函数引用了 scope 变量，通过作用 域搜索，找到了其父作用域中定义的 scope 变量。
   ```

   有一点需要注意：函数作用域的嵌套关系是定义时决定的，而不是调用时决定的，也就 是说，JavaScript 的作用域是静态作用域，又叫词法作用域，这是因为作用域的嵌套关系可 以在语法分析时确定，而不必等到运行时确定。下面的例子说明了这一切：

   ```javascript
   var scope = 'top';
   var f1 = function () {
       console.log(scope);
   };
   f1(); // 输出 top
   var f2 = function () {
       var scope = 'f2';
       f1();
   };
   f2(); // 输出 top
   //这个例子中，通过 f2 调用的 f1 在查找 scope 定义时，找到的是父作用域中定义 的 scope 变量，
   //而不是 f2 中定义的 scope 变量。这说明了作用域的嵌套关系不是在调用 时确定的，而是在定义时确定的。
   ```

4. 全局作用域

   在 JavaScript 中有一种特殊的对象称为 全局对象。这个对象在Node.js 对应的是 global 对象，在浏览器中对应的是 window 对象。由于全局对象的所有属性在任何地方都是可见的， 所以这个对象又称为 全局作用域。全局作用域中的变量不论在什么函数中都可以被直接引 用，而不必通过全局对象。

    满足以下条件的变量属于全局作用域：

   * 在最外层定义的变量；
   * 全局对象的属性；
   * 任何地方隐式定义的变量（未定义直接赋值的变量）。
     * 需要格外注意的是第三点，在任何地方隐式定义的变量都会定义在全局作用域中，即不 通过 var 声明直接赋值的变量。这一点经常被人遗忘，而模块化编程的一个重要原则就是 避免使用全局变量，所以我们在任何地方都不应该隐式定义变量。

### 闭包

> 闭包（closure）是函数式编程中的概念，出现于 20 世纪 60 年代，最早实现闭包的语言 是 Scheme，它是 LISP 的一种方言。之后闭包特性被其他语言广泛吸纳。

```javascript
var generateClosure = function () {
    var count = 0;
    var get = function () {
        count++;
        return count;
    };
    return get;
};
var counter = generateClosure();
console.log(counter()); // 输出 1 
console.log(counter()); // 输出 2 
console.log(counter()); // 输出 3
```

* 这段代码中，generateClosure() 函数中有一个局部变量count，初值为 0。还有一 个叫做 get 的函数，get 将其父作用域，也就是 generateClosure() 函数中的 count 变 量增加 1，并返回 count 的值。generateClosure() 的返回值是 get 函数。在外部我们 通过 counter 变量调用了 generateClosure() 函数并获取了它的返回值，也就是 get 函 数，接下来反复调用几次 counter()，我们发现每次返回的值都递增了 1。
* 让我们看看上面的例子有什么特点， 按照通常命令式编程思维的理解， count 是 generateClosure 函数内部的变量，它的生命周期就是 generateClosure 被调用的时 期，当 generateClosure 从调用栈中返回时，count 变量申请的空间也就被释放。问题是，在 generateClosure() 调用结束后，counter() 却引用了“已经释放了的” count 变量，而且非但没有出错，反而每次调用 counter() 时还修改并返回了 count。这是怎 么回事呢？
* 这正是所谓闭包的特性。当一个函数返回它内部定义的一个函数时，就产生了一个闭包， 闭包不但包括被返回的函数， 还包括这个函数的定义环境。 上面例子中， 当函数 generateClosure() 的内部函数 get 被一个外部变量 counter 引用时，counter 和 generateClosure() 的局部变量就是一个闭包。如果还不够清晰，下面这个例子可以帮助 你理解：

#### 用途

**嵌套的回调函数**:闭包有两个主要用途，一是实现嵌套的回调函数，二是隐藏对象的细节

#### 实现私有成员

>  我们知道，JavaScript 的对象没有私有属性，也就是说对象的每一个属性都是曝露给外部 的。这样可能会有安全隐患，譬如对象的使用者直接修改了某个属性，导致对象内部数据的一 致性受到破坏等。JavaScript通过约定在所有私有属性前加上下划线（例如_myPrivateProp），表示这个属性是私有的，外部对象不应该直接读写它。但这只是个非正式的约定，假设对象 的使用者不这么做



本文结束，谢谢！

