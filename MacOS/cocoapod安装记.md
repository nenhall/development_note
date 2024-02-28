# cocoapod安装记

## gem ：

更新 gem

 `$ gem update --system # 这里请翻墙一下``$ gem -v``2.6.3`

更新gem sources

 `$ gem sources --add https://gems.ruby-china.org/ --remove https://rubygems.org/``$ gem sources -l``https://gems.ruby-china.org``# 确保只有 gems.ruby-china.org`



## Ruby 安装

 `RVM 安装``$ gpg --keyserver hkp://keys.gnupg.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3 ``$ \curl -sSL https://get.rvm.io | bash -s stable ``$ source ~/.bashrc $ source ~/.bash_profile`

修改 RVM 的 Ruby 安装源到 Ruby China 的 Ruby 镜像服务器，这样能提高安装速度

 `$ echo "ruby_url=https://cache.ruby-china.org/pub/ruby" > ~/.rvm/user/db`

Ruby 的安装与切换，列出已知的 Ruby 版本

 `rvm list known`

安装一个 Ruby 版本

 `rvm install 2.2.0 --disable-binary`

这里安装了最新的 2.2.0, rvm list known 列表里面的都可以拿来安装。

切换 Ruby 版本

 `rvm use 2.2.0`

如果想设置为默认版本，这样一来以后新打开的控制台默认的 Ruby 就是这个版本

 `rvm use 2.2.0 --default `

查询已经安装的ruby

 `rvm list`

[ruby-china链接](https://ruby-china.org/wiki/rvm-guide)

安装错误：

1. gpg: command not found 
    解决：`brew install gnupg gnupg2` 但执行这条命令前你需要安装`Homebrew`



## Homebrew安装

 `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"``brew install wget`



## 安装RVM baby 版本管理器：

 `$curl -L get.rvm.io | bash -s stable`

待安装完成后，再执行如下命令：

 `$source ~/.bashrc``$source ~/.bash_profile`



## cocoapods安装

 `$ sudo gem install cocoapods`