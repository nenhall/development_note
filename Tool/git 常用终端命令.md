相关链接：[SVN 终端命令](https://www.jianshu.com/p/541e090992ba)
<br>
####  配置语法：
> 以斜杠“/”开头表示目录；
> 以星号“*”通配多个字符；
> 以问号“?”通配单个字符
> 以方括号“[]”包含单个字符的匹配列表；
> 以叹号“!”表示不忽略(跟踪)匹配到的文件或目录；
> <br>
#### Git 忽略文件、文件夹
```
*.a       # 忽略所有 .a 结尾的文件
!lib.a    # 但 lib.a 除外
/TODO     # 仅仅忽略项目根目录下的 TODO 文件，不包括 subdir/TODO
build/    # 忽略 build/ 目录下的所有文件
doc/*.txt # 会忽略 doc/notes.txt 但不包括 doc/server/arch.txt
```
<br>
#### eg：
* 过滤文件夹：
  /mtk/       表示过滤这个文件夹

* 指定过滤某种类型的文件：
  ```
  \*.zip
  \*.rar
  \*.via
  \*.tmp
  \*.err
  ```
* 指定过滤某个文件：
  ```
  /mtk/do.c
  /mtk/if.h
  ```
* 保守模式负责设置哪些文件不被过滤，也就是哪些文件要被跟踪。
跟踪某个文件夹
   ```
  !/plutommi/mmi
   ```

#### 跟踪文件
* 跟踪某类文件
  ```
  \!\*.c
  \!*.h
  ```

* 跟踪某个指定文件
  `!/plutommi/mmi/mmi_features.h`
  
  >采用共享模式与保守模式结合配置的办法。
  >eg：一个文件夹下有很多文件夹和文件，而我只想跟踪其中的一个文件，这样设置就可以满足这种情况，先用共享模式把整个目录 都设置为不跟踪，然后再用保守模式把这个文件夹中想要跟踪的文件设置为被跟踪，配置很简单，就可以跟踪想要跟踪的文件。

<br>

#### .gitignore忽略文件规则
   > .gitignore只能忽略那些原来没有被track的文件，如果某些文件已经被纳入了版本管理中，则修改.gitignore是无效的。那么解决方法就是先把本地缓存删除（改变成未track状态），然后再提交:

   ```
   git rm --cached <FILENAME>: //后面放你要删除的缓存文件名
  或者
  git rm -r --cached . //删除所有文件缓存
  或者
  git rm -r *.a //删除所有.a文件缓存
  git add .
  git commit -m "update .gitignore"
   ```
<br>
#### Git 全局设置:
```
git config --global user.name "xxx"
git config --global user.email "xxx@126.com"
```
<br>
#### 创建 git 仓库:
```
mkdir chinafm //创建文件夹，此步可以跳过
cd chinafm    //进入到刚创建的文件夹，此步可以跳过
git init      //初始化一个仓库
touch README.md //创建一个README.md文件
git add README.md //把刚创建的文件添加到git(添加单个指定文件)
git add .                     //添加所有改变后的文件
git commit -m "first commit" //增加更新注释(说明)，双引号内的就是你要写的注释
//添加仓库关联 https://git.oschina.net/xxxx.git换成你的仓库地址
git remote add origin https://git.oschina.net/xxxx.git
git push -u origin master //推送代码到到git仓库
```
<br>
#### 删除相应文件，还在利用git命令从git中删除：
```
git filter-branch --force --index-filter "git rm --cached --ignore-unmatch Project1/Project1.1\ Sample\ Project/output.txt"  --prune-empty --tag-name-filter cat -- --all

//删除cache
git filter-branch --index-filter 'git rm -r --cached --ignore-unmatch static/a.bin' HEAD
git commit --amend -CHEAD
git push origin master
```
<br>
#### git回滚，执行完commit后，想撤回commit，怎么办？
```
git reset --soft HEAD^
```

### git撤销本地所有修改（新增、删除、修改）
```
git checkout . #本地所有修改的。没有的提交的，都返回到原来的状态
git stash #把所有没有提交的修改暂存到stash里面。可用git stash pop回复。
git reset --hard HASH #返回到某个节点，不保留修改。
git reset --soft HASH #返回到某个节点。保留修改

git clean -df #返回到某个节点
git clean 参数
    -n 显示 将要 删除的 文件 和  目录
    -f 删除 文件
    -df 删除 文件 和 目录
或者可以直接：
git checkout . && git clean -xdf
```

回滚可参考：https://blog.csdn.net/kongbaidepao/article/details/52253774
###### 注意要在.git文件夹目录下执行以上命令

<br>
##### 给某工程单独添加git的配置文件(非全局)：
1，创建 `.giignore`；
2，打开在里面按上面的语法添加你需要忽略的文件；


##### Please commit your changes or stash them before you merge.
```
git stash
git pull
git stash pop
```
> 1. git stash: 备份当前的工作区的内容，从最近的一次提交中读取相关内容，让工作区保证和上次提交的内容一致。同时，将当前的工作区内容保存到Git栈中。
> 2. git stash pop: 从Git栈中读取最近一次保存的内容，恢复工作区的相关内容。由于可能存在多个Stash的内容，所以用栈来管理，pop会从最近的一个stash中读取内容并恢复。
> 3. git stash list: 显示Git栈内的所有备份，可以利用这个列表来决定从那个地方恢复。
> 4. git stash clear: 清空Git栈。此时使用gitg等图形化工具会发现，原来stash的哪些节点都消失了。

##### error: Pulling is not possible because you have unmerged files.
1. git.pull会使用git merge导致冲突，需要将冲突的文件resolve掉 git add -u, git commit之后才能成功pull:
```
$ git -u add .
$ git commit -m "xxx"
$ git pull
$ git push //如果需要
```

2. 如果我们确定远程的分支正好是我们需要的，而本地的分支上的修改比较陈旧或者不正确，那么可以直接丢弃本地分支内容，运行如下命令(看需要决定是否需要运行git fetch取得远程分支)：

```
$:git reset --hard origin/master
或者
$:git reset --hard ORIG_HEAD
```
> 注意：git merge会形成MERGE-HEAD(FETCH-HEAD) 。git push会形成HEAD这样的引用。HEAD代表本地最近成功push后形成的引用。

3. 我们不能丢弃本地修改，因为其中的某些内容的确是我们需要的，此时需要对unmerged的文件进行手动修改，删掉其中冲突的部分，然后运行如下命令
```
$:git add filename
$:git commit -m "message"
```

git与远程仓库关联：
```
git remote add origin https://github.com/ruqibazao/AVFoundation_note.git
```

### git--删除.DS_Store
引用：https://www.cnblogs.com/zwhblog/p/7921600.html
一、未提交的项目执行下面的操作，提交文件时候则不提交.DS_Store文件 
1. 添加忽略 
cd到git目录下新建.gitignore文件并配置忽略文件 
1)、vim .gitignore 
2)、输入以下内容后保存 
.DS_Store 
*/.DS_Store 
2. 提交项目

二、如果包含.DS_Store的项目已经提交，那么需要先删除本地的.DS_Store文件 并 配置完忽略后，再次提交

删除当前目录下的所有的.DS_Store 
1.` find . -name '*.DS_Store' -type f -delete` 
  **将 .DS_Store 加入到 .gitignore:**`echo .DS_Store >> ~/.gitignore`
2. `git add –all` 
3. `git commit -m '.DS_Store banished!'` 
4. `git push origin dev`(推送到远端dev分支)

禁用或启用自动生成
禁止.DS_store生成：
`defaults write com.apple.desktopservices DSDontWriteNetworkStores -bool TRUE`
恢复.DS_store生成：恢复.DS_store生成：
`defaults delete com.apple.desktopservices DSDontWriteNetworkStores`

> DS_Store 是 Finder 用来存储这个文件夹的显示属性的：比如文件图标的摆放位置。删除以后的副作用就是这些信息的失去。（当然，这点副作用其实不是太大。和别人交换文件（或你做的网页需要上传的时候）应该把 .DS_Store [文件删除]比较妥当，因为里面包含了一些你不一定希望别人看见的信息（尤其是网站，通过 .DS_Store 可以知道这个目录里面所有文件的清单，很多时候这是一个不希望出现的问题）。



**2,查看到未传送到远程代码库的****提交描述/说明**



```html
git cherry -v
```

显示结果类似于这样：
\+ b6568326134dc7d55073b289b07c4b3d64eff2e7 add default charset for table items_has_images
\+ 4cba858e87752363bd1ee8309c0048beef076c60 move Savant3 class into www/includes/class/

**
**

**3,查看到未传送到远程代码库的****提交详情**



```html
git log master ^origin/master
```