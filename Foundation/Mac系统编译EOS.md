# Mac系统编译EOS区块链时遇到的错误：



[中文链接](http://www.eosdata.io/)

执行`./eosio_build.sh`的时候报错：

`ERROR: Failed to find Gettext libintl (missing: Intl_INCLUDE_DIR)`

[参考链接](https://github.com/EOSIO/eos/issues/2028?ref=tokendaily)

链接上说执行：`brew unlink gettext && brew link –force gettext`可以解决

我执行后却报错：`No such keg: /usr/local/Cellar/–force`

**我最终解决的流程：**



1. 新开个终端窗口：

    `$ find /usr -name libintl* -print 2>/dev/null`

    执行这句代码的时候如果报这个错：`zsh: no matches found: libintl*`

    解决办法：

    > 在~/.zshrc中加入: `setopt no_nomatch`保存, 然后终端执行`source .zshrc`命令

    

        打印如下信息：
    
        /usr/local/Cellar/gettext/0.19.8.1/include/libintl.h
    
        /usr/local/Cellar/gettext/0.19.8.1/lib/libintl.8.dylib
    
        /usr/local/Cellar/gettext/0.19.8.1/lib/libintl.a
    
        /usr/local/Cellar/gettext/0.19.8.1/lib/libintl.dylib
    
        /usr/local/Cellar/gettext/0.19.8.1/share/gettext/intl/libintl.rc



2. 执行命令：



    ```
    
    brew list --versions gettext
    
    brew unlink gettext && brew link –force gettext
    
    ```

3. 重新执行：

    `$ find /usr -name libintl* -print 2>/dev/null`

    

        打印如下信息：
        
        /usr/local/Cellar/gettext/0.19.8.1/include/libintl.h
        
        /usr/local/Cellar/gettext/0.19.8.1/lib/libintl.8.dylib
        
        /usr/local/Cellar/gettext/0.19.8.1/lib/libintl.a
        
        /usr/local/Cellar/gettext/0.19.8.1/lib/libintl.dylib
        
        /usr/local/Cellar/gettext/0.19.8.1/share/gettext/intl/libintl.rc
        
        /usr/local/include/libintl.h
        
        /usr/local/lib/libintl.8.dylib
        
        /usr/local/lib/libintl.a
        
        /usr/local/lib/libintl.dylib


    ​    

4. 这次打印的信息是不是比上次多了，再执行：

    `./eosio_build.sh`

5. 终于成功编译：

     

    ```
    
    ...省略...
    
    [100%] Built target nodeos
    
    [100%] Linking CXX executable unit_test
    
    [100%] Built target unit_test
    
    [100%] Built target unit_test
    
     _______ _______ _______ _________ _______
    
     ( ____ \( ___ )( ____ \\__ __/( ___ )
    
     | ( \/| ( ) || ( \/ ) ( | ( ) |
    
     | (__ | | | || (_____ | | | | | |
    
     | __) | | | |(_____ ) | | | | | |
    
     | ( | | | | ) | | | | | | |
    
     | (____/\| (___) |/\____) |___) (___| (___) |
    
     (_______/(_______)\_______)\_______/(_______)
    ```



     EOSIO has been successfully built. 00:12:48



     To verify your installation run the following commands:



     /usr/local/bin/mongod -f /usr/local/etc/mongod.conf &
    
     cd /Users/nenhall/Downloads/SafariDown/eos/build; make test



     For more information:
    
     EOSIO website: https://eos.io
    
     EOSIO Telegram channel @ https://t.me/EOSProject
    
     EOSIO resources: https://eos.io/resources/
    
     EOSIO wiki: https://github.com/EOSIO/eos/wiki
    
     ```





