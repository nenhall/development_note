## 开源项目到CocoPods

> 文中主要是记录当时操作的一些记录，初步的具体操作建议看文章底部的链接，更详细。

### podspec 创建到发布流程简介

1. 首先电脑上得先安装 cocoapods

2. 查看自己是否注册：`pod trunk me`

3. 注册：(加上--verbose可以输出详细错误信息，便于出错时查看)

   `pod trunk register example@example.com 'you name'  --verbose`

4. 创建 .podspec 文件：`pod spec create NHExtension`

5. 编辑 .podspec 文件，把关键的信息填上，不需要的删除了

   ```ruby
   s.name：名称，pod search搜索的关键词,注意这里一定要和.podspec的名称一样
   s.version：版本号，to_s：返回一个字符串
   s.author:作者
   s.homepage:项目主页地址
   s.summary: 项目简介
   s.source_files: 源代码文件目录，别人pod下来的就指定的文件
   s.source:项目源码所在地址
   // eg.
   s.source = { :git => "https://github.com/nenhall/NHAVEditor.git", :tag => "#{spec.version}" }
   s.license:许可证
   s.platform:项目支持平台
   s.requires_arc: 是否支持ARC
   s.source_files:需要包含的源文件
   s.public_header_files:需要包含的头文件
   s.ios.deployment_target:支持的pod最低版本
   // 其他一些非必要字段
   s.social_media_url:社交网址
   s.resources:资源文件，如图片
   s.frameworks: 依赖系统库 ["AVFoundation", "UIKit"]
   s.dependency: 依赖他人的开源库，不能依赖未发布的库
   // 还有更多字段请自己查看官方文档
   https://guides.cocoapods.org/syntax/podspec.html
   ```

   * source_files写法及含义

     ```
     s.source_files  = "NHAVEditor/**/*.{h,m}", "NHAVEditor/Unit/*.{h,m}”
     *表示匹配所有文件
     *.{h,m}表示匹配所有以.h和.m结尾的文件
     **表示匹配所有子目录
     ```

6. 验证 .podspec

   * `cd 到 .podspec 所在的目录下`
   * `pod spec lint NHAVEditor.podspec --verbose` 加verbose是为了方便看错误

7. 发布 podspec

   `pod trunk push NHExtension.podspec` NHExtension对应你自己的 pod 库名称



### 发布/更新中的一些操作细节

> 不管你的spec版本是否已经第一次发布还是更新，你都必须在更改过代码的同时更新 `podspec文件中的s.version 与 git 的 tag 值对应`，然后 git 提交代码，对外发布版本时还要创建同名branch，再单独打个git tag 推送上去，别人安装你的库的时候，就会找到你写的tag对应的版本下载

```
// 推送 tag 的操作
1、进入到工程目录 
2、`$ git tag 0.0.1` 添加一个本地tag 
3、`$ git push --tags` 把当前所有 tag 推送到 git服务器
4、`$ git branch 0.0.1` 创建一个对应的分支
5､ `$ git push origin 0.0.1 ` 把分支推送到 git 服务器

// 相关命令：
$ git tag //查看所有的tag 
$ git tag -d 1.1.3 //删除一个本地tag
$ git branch -d 0.0.1 // 删除本地分支
$ git push origin :refs/tags/1.2  // 删除远程库tag
$ git push origin --delete 0.0.1  // 删除远程分支(当tag和分支同名时，需要使用下面一条命令进行删除)
//删除远程分支，其实这相当于推送一个空的本地分支上去
$ git push origin :refs/heads/0.0.1 
$ git push origin refs/heads/0.0.1:refs/heads/0.0.1 //将本地的分支0.0.1  推送到远程origin下
这两条命令是为pod添加版本号并打上tag:
$ set the new version to 2.0.0
$ set the new tag to 2.0.0
`pod cache clean --all` //清除缓存
`pod lint` 命令添加 `--skip-import-validation` 参数，lint 将跳过验证 pod 是否可以导入。
`pod repo push` 命令添加 `--skip-import-validation` 参数，push 将跳过验证 pod 是否可以导入
```
[pod lint命令详情](https://guides.cocoapods.org/terminal/commands.html#pod_lib_lint)
 [pod repo push 命令详情](https://guides.cocoapods.org/terminal/commands.html#pod_repo_push)

Tag 打成功后你就能在 GitHub 主页看到对应的 tag ，如下图 ;

![image.png](https://upload-images.jianshu.io/upload_images/2443108-2ac61ac9be5e9e00.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


删除一个已发布的 podspec 版本
```
// 删除一个POD的特定版本来纠正意外推送。
$ pod trunk delete PODNAME VERSION
// 你也可以放弃整个POD和所有版本。
$ pod trunk deprecate PODNAME
确认时，请回复一个"y"(小写字母 y)
```

### 操作中遇到的一些错误及解决方案

1. 搜索不到自己的库
   ```
   // 如果搜索不到自己的库，可以尝试使用如下命令，新测有效：
   // 方案一：
   rm ~/Library/Caches/CocoaPods/search_index.json
   pod search NHHUDExtend
   
   // 方案二：
   `pod setup` 然后再`pod search NHHUDExtend`,
   // 不行就往下执行
   ` pod repo update`  然后再`pod search NHHUDExtend`
   // 如果还不行请使用：
   `pod search NHHUDExtend --simple`这个估计跟cocoapod的搜索算法有关。
   ```

2. 报错: ··· error: include of non-modular header inside framework module ··· [-Werror,-Wnon-modular-include-in-framework-module]

   ```
   解决办法：在pod lib lint 或者 pod spec lint 以及 pod repo push ....时候加上   --use-libraries
   pod lib lint --use-libraries
   #或者
   pod spec lint --use-libraries
   #当然，在提交的时候也要加上
   pod repo push <repoName> <podspec> --use-libraries
   如果有警告，可以在最后加：`--allow-warnings` 就表示可以忽略所有的warnings
   ```

3. 报错：Could not find a `ios` simulator (valid values: ). Ensure that Xcode -> Window -> Devices has at least one `ios` simulator listed or otherwise add one.

   ```
   这个问题是pod依赖的组件fourflusher与xcode版本不匹配造成的，可以使用如下命令更新
   1.sudo gem uninstall fourflusher
   2.sudo gem install fourflusher
   必要的话还需要更新pod
   
   sudo gem update cocoapods
   源自：http://www.ppkanshu.com/index.php/post/2451.html
   ```

4. pod trunk push NHAVEditor.podspec 报错：

   ```
   - ERROR | [iOS] unknown: Encountered an unknown error ([!] /usr/bin/git clone https://github.com/nenhall/NHAVEditor.git /var/folders/0m/qqqqnntd2x375659j8q_28080000gn/T/d20190816-3615-1gglqv9 --template= --single-branch --depth 1 --branch 0.0.1
   
   Cloning into '/var/folders/0m/qqqqnntd2x375659j8q_28080000gn/T/d20190816-3615-1gglqv9'...
   error: RPC failed; curl 56 LibreSSL SSL_read: SSL_ERROR_SYSCALL, errno 54
   fatal: the remote end hung up unexpectedly
   fatal: early EOF
   fatal: index-pack failed
   ```

   解决方案：

   ```
   $ sudo xcode-select -switch /Applications/Xcode.app/Contents/Developer
   // 如果你有多个xcode ，请自行更改成对应目录
   ```

   



相关参考链接:
[手把手教你发布自己的cocoapods开源库](http://www.jianshu.com/p/3a365f273439)

[手把手教你发布代码到CocoaPods(Trunk方式)](https://www.cnblogs.com/wengzilin/p/4742530.html)

[Publish Your Pods on CocoaPods with Trunk](http://yulingtianxia.com/blog/2014/05/26/publish-your-pods-on-cocoapods-with-trunk)

下面这一篇文章中说的方法是老版的方法，不过核心流程还是可以参考的
[CocoaPods详解之----制作篇](http://blog.csdn.net/wzzvictory/article/details/20067595)

pod 相关错误解决指南参考链接：
[COCOAPODS创建私有PODS](https://www.cnblogs.com/tufeibo/p/5654268.html)
[Cocoapods详解之---进阶篇2](https://blog.csdn.net/yuanmengong886/article/details/57085348)