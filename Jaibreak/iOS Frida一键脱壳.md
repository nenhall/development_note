#### 前沿

> iOS砸壳方式很多：dumpdecrypted，Clutch，Frida，本篇介绍Frida。

#### 1 准备工作

###### 1.1 iOS端

> 安装ssh
>  安装Frida源
>  添加源：[https://build.frida.re](https://links.jianshu.com/go?to=https%3A%2F%2Fbuild.frida.re)
>  下载对应Frida 32或者64位

###### 1.2 Mac 端

> 1.2.1 安装homebrew

```bash
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

> 1.2.2 安装python

```undefined
brew install python
```

mac里会共存2.7版本（系统默认）和你新安装的3.6.X版本

###### 1.2.3 安装wget

```undefined
 brew install wget
```

###### 1.2.4 安装pip

```csharp
wget https://bootstrap.pypa.io/get-pip.py

sudo python get-pip.py
```

###### 1.2.5 安装usbmuxd

```csharp
brew install usbmuxd

rm ~/get-pip.py
```

###### 1.2.6 安装frida

```undefined
sudo pip install frida
```

###### 1.2.7 配置Frida-iOS-dump环境

```swift
sudo mkdir /opt/dump && cd /opt/dump && sudo git clone https://github.com/AloneMonkey/frida-ios-dump
```

###### 1.2.8 安装依赖

```swift
sudo pip install -r /opt/dump/frida-ios-dump/requirements.txt --upgrade
```

###### 1.2.9 设置别名dump.py

```bash
vim ~/.bash_profile   

alias dump.py="/opt/dump/frida-ios-dump/dump.py"

source ~/.bash_profile
```

##### 2 查看效果

###### 2.1 一个终端输入

```undefined
 iproxy 2222 22
```

###### 2.2 链接手机，另一个终端

```css
ssh -p 2222 root@127.0.0.1
```

###### 2.3 查看当前安装应用

```swift
cd /opt/dump/frida-ios-dump
dump.py -l
```

###### 2.4 一键脱壳

```css
dump.py  bundle ID
```

##### 3 验证是否脱壳

> 利用MachOView进行分析 (cryptid 0 为脱壳, 1为没有脱壳)
>  通过otool命令行也可以：otool -l 可执行文件路径 | grep crypt
>  cryptid 0 为脱壳, 1为没有脱壳