# VSCode配置 Python 环境

1. 下载 visual studio vode

2. 安装必要的插件：

   1. Anaconda Extension Pack：
             这个插件就推荐给用anaconda的同学了，大大增强了代码提示功能。原始的代码提示基本只包含了python标准库，有了这个插件之后各种第三方库基本都能实现代码提示了，并且还会额外显示每个方法的帮助。

   2. Path Autocomplete：
         有时候程序需要读取文件，自己手动去复制文件路径还是比较麻烦的，不过有了这个插件就方便多了，它能自动感知当前目录下所有的文件，只需要你自己选择就好了。

   3. 如何使vscode运行程序时在当前文件夹中打开终端？
      vscode运行task,调试或者直接在终端运行文件时都是默认在vscode打开的文件夹中打开终端运行或者调试程序的，这时候存在的问题是，如果运行一个在子文件夹中需要读取文件的程序，按照其默认设置只能把文件放在主文件夹中，虽然也能通过cd之类的操作解决，但本着能省事就省事的原则，可以通过修改launch.json文件实现调试程序时在当前文件夹中打开终端运行程序，我的launch.json的文件如下：

      ```json
      {
      "name": "Python",
      "type": "python",
      "request": "launch",
      "stopOnEntry": false,
      "pythonPath": "${config:python.pythonPath}",
      "program": "${file}",
      "cwd": "${fileDirname}",
      "env": {},
      "envFile": "${workspaceRoot}/.env",
      "debugOptions": ["WaitOnAbnormalExit",
      								"WaitOnNormalExit",
      								"RedirectOutput"]
      }
      ```

   4. 其它常用插件:

      <img src="https://blog-1257063273.cos.ap-chengdu.myqcloud.com/2020/20201008140329.png" alt="其它常用插件" style="zoom:50%;" />

