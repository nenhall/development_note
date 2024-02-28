# fastlane 自动打包

## 安装前的准备工作:

* 安装[Homebrew](https://brew.sh)：(已安装的略过)
    ```
    /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
    ```
    
* 确认ruby，终端查看下ruby版本，如果没有安装需要先安装**ruby**
    ```
    ruby -v
    ```

* 确认是否安装了Xcode命令行工具
    > 如果已经安装，则会提示：
    ```
    xcode-select: error: command line tools are already installed, use " Software Upate"to install updates
    > 未安装执行如下命令安装：
    xcode-select --install
    > 更新(非必要执行的命令)：
    Software Upate
    ```

* 开启xcode：
* ```
    sudo xcode-select --switch <xcode_folder_path>
    > e.g. sudo xcode-select --switch /Applications/Xcode.app
    ```



## 安装 fastlane

* gem 安装
    ```
    sudo gem install fastlane
    ```



* brew 安装

    ```
    brew cask install fastlane
    ```



## 工程初始化fastlane

* 进入工程主目录： 

    ```
    cd /项目目录
    ```

    

* fastlane初始化，执行如下命令：

    ```
    fastlane init
    ```

    > 安装firim插件，如果需要

    ```
    fastlane add_plugin firim
    ```

	> 安装fir插件，如果需要
	```
	fastlane add_plugin pgyer
	```


* 到目前为止，fastlane的初始化工作已经完成。

  
## fastlane配置说明：
### Appfile文件配置：
### Fastfile文件配置：

* e.g.
    platform :ios do desc "push a new betaDebug build to fir" lane :betaDebug do
    \# add actions here: https://docs.fastlane.tools/actions
      build_app(project: "MontnetsLiveKing.xcodeproj", 
       scheme: "MontnetsLiveKing", 
       export_method: "development", 
       output_directory: "/fastlane/package", 
       configuration: "Release",
       output_name: "ipa name",
       clean: true)
       firim(firim_api_token: "9e2cf8f6c0955333b1f1f291bfa2586a")
         end
    end

    

* 常用参数说明，具体的请看官方文档，更详细[docs.fastlane](https://docs.fastlane.tools/)：
    ```
    workspace: "fastlaneDemo.workspace" //要编辑的工程空间名牌
    export_method： must be: ["app-store", "ad-hoc", "package", "enterprise", "development", "developer-id"]
    project: workspace \ project \ scheme (options,dg 三选一)
    ```

    其它相关关键字：

    ```
    clean, 
    output_directory, 
    output_name, 
    configuration, 
    silent, 
    codesigning_identity, 
    skip_package_ipa, 
    include_symbols, 
    include_bitcode, 
    export_method    export_options, 
    export_xcargs, 
    skip_build_archive, skip_archive, 
    build_path, archive_path, 
    derived_data_path, 
    result_bundle, 
    buildlog_path, 
    sdk, 
    toolchain, 
    destination, 
    export_team_id,
    xcargs, 
    xcconfig, 
    suppress_xcode_output, 
    disable_xcpretty, 
    xcpretty_test_format, 
    xcpretty_formatter, 
    xcpretty_report_junit, 
    xcpretty_report_html, 
    xcpretty_report_json, 
    analyze_build_time, 
    xcpretty_utf, 
    skip_profile_detection
    
    ```
