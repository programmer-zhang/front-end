# MAC 安装 Homebrew 及错误处理

> 文章建议阅读时间：5分钟

## 阅读此文您将收获到
* Homebrew 是用来做什么的
* 如何在 MAC 平台安装及卸载 Homebrew
* Homebrew如何使用

## 简介
* `Homebrew` 简称 `brew`
* `Homebrew` 是一款 `Mac OS` 平台下的软件包管理工具，很方便帮助我们实现安装、卸载、更新、查看、搜索等很多实用的功能。
* 简单的一条指令，就可以实现包管理

## 安装
### 安装  `Xcode Command Line Tools` 
* 在安装 `Homebrew` 之前，需要将 `Xcode Command Line Tools` 安装完成，这样你就可以使用基于 `Xcode Command Line Tools` 编译的 `Homebrew`

### 复制脚本命令
* 打开浏览器查看 [homebrew官方网站](https://brew.sh/) 最新下载脚本安装命令,并复制
	
```
// 打不开的可以复制以下命令，安装不上的请查看文章后面错误处理
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```
### 执行脚本命令
* 打开电脑终端，并在终端中输入刚才复制的脚本命令
* 安装过程中提示输入各种指令或密码

### 验证
* 安装成功后终端进行验证，输入`brew -v`
* 如果能正常出现 `brew` 的版本号，证明安装成功
* 如果不能出现，则安装失败，请查看文章最后错误处理

## 卸载
* 打开电脑终端，将安装命令最后的 `install` 替换成 `uninstall` 就可以

```
/usr/bin/ruby -e "$(curl -fsSL [https://raw.githubusercontent.com/Homebrew/](https://raw.githubusercontent.com/Homebrew/)install/master/uninstall)"
```
* 安装过程中提示输入各种指令或密码
* 最后提示卸载成功

## 相关指令
* `brew -v` 查询Homebrew版本
* `brew -h`	brew帮助
* `brew update`	更新Homebrew
* `brew install <pkg_name>`	安装任意软件
* `brew uninstall <pkg_name>`	卸载任意软件
* `brew search <pkg_name>`	查询任意包
* `brew list`	列出安装列表
* `brew info <pkg_name>` 查看任意包内容信息
* `brew upgrade <pkg_name>` 更新任意包
* `brew cleanup <pkg_name>` 删除具体旧软件
* `brew cleanup` 删除所有旧软件
* `brew outdated` 已安装的包是否需要更新
* `brew deps`	已安装的包是否需要更新
* 更多使用命令[请点击]([https://docs.brew.sh/](https://docs.brew.sh/))

## 安装时可能出现的问题
### 权限不足
* 安装时使用 `root` 权限执行脚本

### 链接被拒绝或无法连接
* 检查本机网络是否有代理，若有代理，将代理取消后重新操作
* 若无网络代理，尝试手动打开[Homebrew官方脚本](https://raw.githubusercontent.com/Homebrew/install/master/install)进行安装
* 若不能正常打开，则是网络问题，请尝试检查网络连接
* 若能正常打开，将网站中的脚本代码下载到本地，保存为 `brew_install.rb` 文件，文件位置随意，只要你能找到
* 终端执行 `curl` 命令，若出现 `curl: try 'curl --help' or 'curl --manual' for more information` 则正常执行下一步，若不出现此信息，则是其他问题，请先解决
* 在脚本文件所在文件夹使用终端执行 `ruby brew_install.rb`
* 开始正常下载安装

### 错误信息：curl 18 transfer closed with outstanding read data remaining(tag资源太大)
* 终端输入指令进行  进行调整 `curl` 的`postBuffer` 的默认值

```
git config --global http.postBuffer 524288000
```
* 然后正常执行安装过程

### 错误信息：curl 56 LibreSSL SSL_read: SSL_ERROR_SYSCALL, errno 54(镜像源访问失败)
* 将brew的install文件下载到本地

```
curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install >> brew_install
```
* 编辑 `brew_install` 文件,替换 `BREW_REPO` 和 `CORE_TAP_REPO` 两行为如下命令：(存在则替换，不存在就添加) 

```
BREW_REPO = "git://mirrors.ustc.edu.cn/brew.git".freeze
CORE_TAP_REPO = "git://mirrors.ustc.edu.cn/homebrew-core.git".freeze
```

* 执行命令开始安装过程 `/usr/bin/ruby ~/brew_install`

### 错误信息: in initialize: Version value must be a string; got a NilClass () (TypeError)
* 输入 `brew --version` 或直接执行 `brew` 操作报上述错误
* 原因是升级 `MAC` 系统后与 `Homebrew` 不兼容造成的
* 解决方式1: 固定版本
	* 终端输入 `vim /usr/local/Homebrew/Library/Homebrew/version.rb`
	* 注释掉文件的第 369 行和 372 行，将 372 行版本号固定为 `@version = '11.1'`(不同版本行数有出入，内容相对应就对)
	
	![](../images/homebrew-error.png)

* 解决方式2: 重新下载安装Homebrew
	* `brew update-reset`
	
* 验证方式: `brew --version` 出现版本信息即可正常使用了。
