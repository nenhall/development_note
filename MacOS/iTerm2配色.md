[solarized](http://ethanschoonover.com/solarized)

[solarized](http://ethanschoonover.com/solarized)配色，效果还不错。官网下载，解压，然后打开 iTerm2 下的偏好设置 preference ，点开profiles下的colors 选项，点击右下角的Color Presets选项，选择import ，导入解压到的 `solarized` 文件下的 `iterm2-colors-solarized` 文件夹下的 `Solarized Dark.itermcolors`。

## 安装oh-my-zsh

github连接：<https://github.com/robbyrussell/oh-my-zsh> 
 使用 crul 安装：

 `sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"`

或使用wget：

 `sh -c "$(wget https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh -O -)"`

#### 更改主题

[oh-my-zsh-Themes](https://github.com/robbyrussell/oh-my-zsh/wiki/Themes) 
 安装成功后，用vim打开隐藏文件 .zshrc ，修改主题为 agnoster： 
 `ZSH_THEME="agnoster"` 
 应用这个主题需要特殊的字体支持，否则会出现乱码情况，这时我们来配置字体：

1. [Meslo](https://github.com/powerline/fonts/blob/master/Meslo%20Slashed/Meslo%20LG%20M%20Regular%20for%20Powerline.ttf) 字体下载。

   - 手动下载： [Meslo字体](https://github.com/powerline/fonts/blob/master/Meslo%20Slashed/Meslo%20LG%20M%20Regular%20for%20Powerline.ttf) ，点开连接点击 view raw 下载字体。

   - 终端命令行下载、安装：

      `# clone````$ git clone https://github.com/powerline/fonts.git --depth=1````# install````$ cd fonts``$  ./install.sh````# clean-up a bit````$  cd ..``$ rm -rf fonts`

2. 安装字体到系统字体册。

3. 应用字体到iTerm2下，我自己喜欢将字号设置为14px，看着舒服（iTerm -> Preferences -> Profiles -> Text -> Change Font）。

4. 重新打开iTerm2窗口，这时便可以看到效果了。

5. 如果你感觉变化不大，你可以改变你的字体颜色，把其改亮一些： 
    移动到 ~/.oh-my-zsh/custom/plugins/zsh-autosuggestions 路径下:`cd .oh-my-zsh/custom//plugins/zsh-autosuggestions` 
    用 vim 打开 zsh-autosuggestions.zsh 文件，修改:`ZSH_AUTOSUGGEST_HIGHLIGHT_STYLE='fg=10'`

#### 安装其它插件

**命令自动补全插件：**当我们输入命令时，终端会自动提示你接下来可能要输入的命令，这时按 → 键便可输出这些命令，非常方便。

设置如下:

1. 克隆仓库到本地 ~/.oh-my-zsh/custom/plugins 路径下: 
    `git clone git://github.com/zsh-users/zsh-autosuggestions $ZSH_CUSTOM/plugins/zsh-autosuggestions`
2. 用 vim 打开 .zshrc 文件，找到 plugins=，在括号里面原有的基础上添加`zsh-autosuggestions`

**安装autojump插件：**自动跳转到你进入过的目录：

1. 使用vim .zshrc打开.zshrc
2. 找到 plugins=，在括号里面原有的基础上添加autojump
3. 新开一行，添加：`[[ -s \$(brew --prefix)/etc/profile.d/autojump.sh ]] && . $(brew --prefix)/etc/profile.d/autojump.sh`
4. :wq保存退出，重启终端。 
    添加后大概长这样：

 `    plugins=(``    zsh-autosuggestions git autojump``    )`

**隐藏用户名信息** 
 一般终端每一行前都会有xxx@xxxdeMacbook-Pro:我们可以将其隐藏掉。  
 进入`.oh-my-zsh` > `themes` > `agnoster(找到你对应的主题文件)`，编辑agnoster.zsh-theme文件： 

> 在主题文件中找到`# Context: user@hostname (who am I and where am I)`，大概在80行，然后再往下找到`prompt_segment black default "%(!.%{%F{yellow}%}.)$USER@%m"`，把@%m删除，改完后如下：`prompt_segment black default "%(!.%{%F{yellow}%}.)$USER`，保存关闭，重启终端。

**语法高亮插件**

1. 使用homebrew安装 zsh-syntax-highlighting 插件：  
    brew install zsh-syntax-highlighting 
2. 配置.zshrc文件: 
    vim ~/.zshrc 
3. 在最后插入下面这一行代码:  
    source /usr/local/share/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh 
4. 输入命令重新加载配置文件：  
    source ~/.zshrc