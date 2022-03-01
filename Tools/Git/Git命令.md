# Git2.x版本常用命令
## Git基本概念
### 工作区(Workspace)
磁盘目录

### 暂存区(Index / Stage)
一般存放在 .git 目录下的 index 文件（.git/index）中，所以暂存区也叫作索引（index）

### 本地仓库/版本库(Repository)
工作区的隐藏目录.git，是Git 的版本库（暂存区+分支）  

### 远程仓库(Remote)
远程服务器上的git仓库

### HEAD
HEAD通常指向目前所在的分支
> cat .git/HEAD

</br>  

## 新建代码库
// 在当前目录新建一个Git代码库  
git init  
// 新建一个目录，将其初始化为Git代码库  
git init [project-name]  
// 下载一个项目和它的整个代码历史  
git clone [url]

## 配置
```shell
# 显示当前的Git配置
$ git config --list
# 编辑Git配置文件
$ git config -e [--global]
# 设置提交代码时的用户信息
$ git config [--global] user.name "[name]"
$ git config [--global] user.email "[email address]"
# 颜色设置
git config --global color.ui true                         # git status等命令自动着色
git config --global color.status auto
git config --global color.diff auto
git config --global color.branch auto
git config --global color.interactive auto
git config --global --unset http.proxy                    # remove  proxy configuration on git
```
## 文件操作
### add
#### add -v 
 显示详细信息
#### add .
添加当前目录和子目录下相应（新增、更新、删除）文件。并且会根据.gitignore做过滤
#### add -u
添加当前目录和子目录下相应（更新、删除）文件,不包括新添加文件
#### add -A
等于add .
#### add --ignore-removal
添加当前目录和子目录下相应（更新、添加）文件,忽略删除的文件
#### add -f
允许添加忽略的文件
#### add -p
交互式挑选数据块,对于同一个文件的多处变化，可以实现分次提交

### rm
#### rm [file1] [file2]
删除工作区文件，并且将这次删除放入暂存区
#### rm --cached [file]
只从索引区删除,即停止追踪指定文件，但该文件会保留在工作区
#### rm -r 
递归删除工作区文件夹

### mv
#### mv [file-original] [file-renamed]
改名文件，并且将这个改名放入暂存区


## 撤销
### reset
执行 git reset HEAD 以取消之前 git add 添加，但不希望包含在下一提交快照中的缓存
#### --mixed
重置 HEAD 和索引。--mixed 为默认，可以不用带该参数，用于重置暂存区的文件与上一次的提交(commit)保持一致，工作区文件内容保持不变。
```
$ git reset HEAD^            # 回退所有内容到上一个版本  
$ git reset HEAD^ hello.php  # 回退 hello.php 文件的版本到上一个版本  
$ git reset 052e             # 回退到指定版本
```
#### --soft
只重置 HEAD
#### --hard
重置HEAD、暂存区与工作区，撤销工作区中所有未提交的修改内容，将暂存区与工作区都回到上一次版本，并删除之前的所有信息提交.  
注意：谨慎使用 –hard 参数，它会删除回退点之前的所有信息。
```
$ git reset –-hard HEAD~3  # 回退上上上一个版本  
$ git reset –-hard bae128  # 回退到某个版本回退点之前的所有信息。 
$ git reset --hard origin/master    # 将本地的状态回退到和远程的一样 
```
#### --keep
重置当前HEAD为指定commit，但保持暂存区和工作区不变


### revert
git revert HEAD~3：丢弃最近的三个commit，把状态恢复到最近的第四个commit，并且提交一个新的commit来记录这次改变  

git revert是用一次新的commit来回滚之前的commit，git reset是直接删除指定的commit

### checkout
checkout命令用于切换分支或恢复工作树文件  

checkout [file]: 恢复暂存区的指定文件到工作区  
checkout [commit] [file]: 恢复某个commit的指定文件到暂存区和工作区
checkout .: 恢复暂存区的所有文件到工作区
git checkout <分支>: 检出分支

#### checkout -f <分支>
如果本地有修改没有提交，切分支会报错，-f可以强制切分支，放弃本地修改
#### checkout -b <分支>
创建并检出一个新的分支

#### 使用场景
- 本地修改后未存储在暂存区时（git add）,使用 git checkout -- file撤销某文件的修改，撤销所有修改可以使用git checkout .
- 本地修改后已存储在暂存区时，使用git reset HEAD <file>,将暂存区的修改撤销掉，重新放回工作区，撤销工作区修改可以使用git checkout -- file
- 本地修改后提交到版本库（git commit）,可以使用git reset --hard HEAD^，回退到上一个版本

## 代码提交
### commit
#### commit -v
提交时显示所有diff信息
#### git commit -m [message]
提交说明
#### commit -a
提交工作区自上次commit之后所有改动的文件，直接到仓库区
#### commit -am
将add和commit合为一步
#### commit --amend -m [message]
使用一次新的commit，替代上一次提交，如果代码没有任何新变化，则用来改写上一次commit的提交信息
#### commit --amend [file1] [file2] ...
重做上一次commit，并包括指定文件的新变化


## 分支
### branch
#### branch
列出所有本地分支
#### branch -r
列出所有远程分支
#### branch -a
列出所有本地分支和远程分支
#### branch [branch-name]
新建一个分支，但依然停留在当前分支
#### branch [branch] [commit]
新建一个分支，指向指定commit
#### branch -t/--track [branch] [remote-branch]
新建一个分支，与指定的远程分支建立追踪关系。跟踪模式（参见 git-pull(1)）
#### branch --set-upstream [branch] [remote-branch]
建立追踪关系，在现有分支与指定的远程分支之间
#### branch -d [branch-name]
删除完全合并的分支
#### #### branch -D [branch-name]
删除分支（即使没有合并）
#### #### branch -m [branch-name]
移动/重命名一个分支，以及它的引用日志
#### #### branch -M [branch-name]
删除分支（即使没有合并）
#### #### branch -r/--remotes
作用于远程分支，比如
> git branch -dr [remote/branch]    // 删除远程分支

### merge
#### merge [branch]
合并指定分支到当前分支
#### merge -m
合并的提交说明（针对非快进式合并）

### cherry-pick
#### cherry-pick [commit]
选择一个commit，合并进当前分支

### checkout


## 标签
### tag
#### tag
列出所有tag
#### tag [tag]
新建一个tag在当前commit
#### tag [tag] [commit]
新建一个tag在指定commit
#### tag -d [tag]
删除本地tag
#### push [remote] [tag]
提交指定tag
#### push [remote] --tags
提交所有tag
#### push origin :refs/tags/[tagName]
删除远程tag
#### show [tag]
查看tag信息


## 查看信息
### status
显示有变更的文件

### log
显示当前分支的版本历史
#### log --stat
显示commit历史，以及每次commit发生变更的文件
#### log -S [keyword]
根据关键词搜索提交历史
#### log --follow [file]
显示某个文件的版本历史，包括文件改名
#### git log -5 --pretty --oneline
显示过去5次提交


### diff
显示暂存区和工作区的差异
#### diff --cached [file]
显示暂存区和上一个commit的差异
#### diff HEAD
显示工作区与当前分支最新commit之间的差异

### show
#### show [commit]
显示某次提交的元数据和内容变化
#### show --name-only [commit]
显示某次提交发生变化的文件
#### show [commit]:[filename]
显示某次提交时，某个文件的内容

### reflog
显示当前分支的最近几次提交

### stat
查看当前版本状态（是否修改）us


## 远程同步
### fetch
#### fetch [remote]
下载远程仓库的所有变动


### remote
#### remote -v
显示所有远程仓库
#### remote show [remote]
显示某个远程仓库的信息
#### remote add [shortname] [url]
增加一个新的远程仓库，并命名

### pull
取回远程仓库的变化，并与本地分支合并

### push
上传本地当前分支到远程仓库

