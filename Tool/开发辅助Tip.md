# 开发辅助工具Tip

1. 安装新的shell后，默认的终端可能会出现目录权限问题：

   ```
   [oh-my-zsh] Insecure completion-dependent directories detected:
   drwxrwxrwx 7 hans admin 238 2 9 10:13 /usr/local/share/zsh
   drwxrwxrwx 6 hans admin 204 10 1 2017 /usr/local/share/zsh/site-functions
   
   [oh-my-zsh] For safety, we will not load completions from these directories until
   [oh-my-zsh] you fix their permissions and ownership and restart zsh.
   [oh-my-zsh] See the above list for directories with group or other writability.
   
   [oh-my-zsh] To fix your permissions you can do so by disabling
   [oh-my-zsh] the write permission of "group" and "others" and making sure that the
   [oh-my-zsh] owner of these directories is either root or your current user.
   [oh-my-zsh] The following command may help:
   [oh-my-zsh] compaudit | xargs chmod g-w,o-w
   
   [oh-my-zsh] If the above didn't help or you want to skip the verification of
   [oh-my-zsh] insecure directories you can set the variable ZSH_DISABLE_COMPFIX to
   [oh-my-zsh] "true" before oh-my-zsh is sourced in your zshrc file.
   ```

   解决方案：
   ```shell
   // 终端执行如下命令
   chmod 755 /usr/local/share/zsh
   chmod 755 /usr/local/share/zsh/site-functions
   // 原文类似问题：[https://github.com/robbyrussell/oh-my-zsh/issues/6835](https://github.com/robbyrussell/oh-my-zsh/issues/6835)
   ```



