> 现如今的开发流程中，Git已经成为我们无所不在的好朋友，大量的代码托管在第三方代码管理平台，所以熟练地使用Git也成为程序员的基本功，列出Git命令前先描述一下Git的一些基本概念

## 阅读本文您将收获
* Git 的基本语法
* 合并代码提交的操作

## 基本概念
* 四个区
	* 工作区(Working Area) 
	* 暂存区(Stage) 
	* 本地仓库(Local Repository) 
	* 远程仓库(Remote Repository)
* 五种状态
	* 未修改(Origin) 
	* 已修改(Modified)
	* 已暂存(Staged)
	* 已提交(Committed) 
	* 已推送(Pushed)

## 新建代码库
#### 在当前目录新建一个Git代码库
*  `git init`

#### 新建一个目录，将其初始化为Git代码库
*  `git init [project-name]`

#### 下载一个项目和它的整个代码历史
*  `git clone [url]`

## 配置
#### 显示当前的git位置
*  `git config --list`

#### 编辑git配置文件
*  `git config -e [--global]`

#### 设置提交代码时的用户信息
*  `git config [--global] user.name "[name]"`
*  `git config [--global] user.email "[email address]"`

## 增加或删除文件

#### 添加指定文件到暂存区(文件中间使用空格区分)
*  `git add [file1] [file2] ...`

#### 添加指定目录到暂存区，包括子目录
*  `git add [dir]`

#### 添加当前目录的所有文件到暂存区
* `git add`

#### 删除工作区文件，并且将这次删除放入暂存区
*  `git rm [file1] [file2] ...`

#### 停止追踪指定文件，但该文件会保留在工作区
*  `git rm --cached [file]`

#### 改名文件，并且将这个改名放入暂存区
*  `git mv [file-original] [file-renamed]`

## 代码提交
#### 提交暂存区到仓库区
*  `git commit -m [message]`

#### 提交暂存区的指定文件到仓库区
*  `git commit [file1] [file2] ... -m [message]`

#### 提交工作区自上次commit之后的变化,直接到仓库区
*  `git commit -a`

#### 提交时显示所有diff信息
*  `git commit -v`

#### 使用第一次新的ommmit,替代上一次提交如果代码没有任何新变化，则用来改写上一次commit的提交信息
*  `git commit --amend -m [messsage]`

#### 重新上一次commit,并包括指定文件的新变化
*  `git commit --amend [file1] [file2] ...`

#### 跟踪所有改动过的文件
*  `git add .`

#### 跟踪指定文件
*  `git add [file]`

#### 提交修改
*  `git push [branch]`

## 分支
#### 列出本地所有分支 
*  `git branch`

#### 列出所有远程分支 
*  `git branch -r`

#### 列出所有本地分支和远程分支 
*  `git branch -a`

#### 新建一个分支，但依然停留在当前分支 
*  `git branch [branch-name]`

#### 新建一个分支，并切换到该分支 
*  `git checkout -b [branch]`

#### 快速切换到上一个分支
*  `git checkout -`

#### 新建一个分支，指向指定commit 
*  `git branch [branch] [commit]`

#### 新建一个分支，与指定的远程分支建立追踪关系 
*  `git branch --track [branch] [remote-branch]`

#### 切换到指定分支，并更新工作区 
*  `git checkout [branch-name]`

#### 建立追踪关系，在现有分支与指定的远程分支之间 
*  `git branch --set -upstream [branch] [remote-branch]`

#### 合并指定分支到当前分支 
*  `git merge [branch]`

#### 快速合并刚才使用过的分支
*  `git merge -`

#### 选择一个commit,合并进当前分支　　　　 
*  `git cherry-pick [commit]`

#### 删除分支 
*  `git branch -d [branch-name]`

#### 删除远程分支 
*  `git push origin --delete [branch-name] `
*  `git branch -dr [remote/branch]`

## 撤销更改
#### 仅在本地更改代码，未add或commit代码
* `git reset --hard` 
或者
* `git checkout .`

#### 已暂存，未提交，即add代码但是还未commit代码
* `git reset` 然后执行 `git checkout .`
或者
* `git reset --hard`

#### 已提交，未推送，即还未push
* `git reset --hard origin/master`

### 已推送
* `git reset --hard HEAD^` 然后执行 `git push -f`

## 将多次commit合并为一个并merge到master
* 举个栗子，想把 `feature-6793` 分支的所有提交在上线前合并成一个提交：
* 基于`master`新建`branch``feature-6793-push`
* `git checkout -b feature-6793-push`切换分支* `git merge --squash origin/feature-6793`合并为一个分支* `git commit -m '这次上线的需求描述'` commit请求
* `git push origin master` 推到远程分支master

## 一张随时可看的指令图片

![](../images/git-order.jpg)