# git cheatSheet
> 参考廖雪峰git教程(https://www.liaoxuefeng.com/wiki/896043488029600)


# 基础使用

## git config配置参数
> 这里演示配置user.name和user.email
```bash
// 配置user.name
git config --global user.name "mrx151"

// 配置user.email
git config --global user.email "ele7enx@vip.qq.com"
```
> 查询配置
```
git config --get "user.name"
git config --get "user.email"
```

## git init
> 在安装了git的前提下,将任意目录纳入git管理
```
git init
```
- 该命令会在给定目录生成一个名为.git的(隐藏)目录.
- git可以管理当前目录下文本文件的改动,精确到文本内的单词.
- 但对于二进制文件,如图片/word等,git只能跟踪变化,不能精确到单词的变化.
- 建议使用utf-8编码

## git status
> 该命令可查看本地仓库当前的状态

- 属于哪个branch(默认branch为master)
- 未纳入版本管理的文件(untracked files),显示为红色
- 纳入版本管理,已修改未add的的文件(not **staged** for commit),显示为红色
- 已add(staged)未commit,显示为绿色
- 如果上面的文件都没有,会显示`working tree clean`

## git diff {文件名}
> 该命令可查看(纳入版本管理的)文件发生的变化
```
git diff {文件名}
```
此外,还可以跟某个version比对
```
git diff {versionId} {fileName}
```
- 这里versionId也可以是HEAD

## git add {文件路径}
> add的本质是将文件提交到暂存区(stage)
```
git add {文件路径}
```
- 可以add单一的file,也可以提交directory(效果是递归地add下面所有的变更)

## git commit -m "{提交说明}"
> commit的本质是将暂存区(stage)的所有内容提交到当前分支(branch)
```
git commit -m "{提交说明}"
```
- 将所有add未commit的file进行commit,必须要有提交说明(message)

### 对commit的理解
> commit相当于是一个快照,你可以随时(把已纳入版本管理的files)回到某个commit的状态






## git checkout -- {文件名}
- 此命令用来放弃工作区中某个文件的修改,彻底还原到HEAD指向的版本.(使用reset只改变HEAD指向但不会改变内容)
- 两个横线是必填的
### 跟 git reset 的配合
- git reset 用于处理已经add到stage的情况下回滚,在没有添加`--hard`的情况下,只是改变HEAD指向而不会改变文件内容;反之,会强制将内容也回滚到所设置的版本
- git checkout -- 用于回滚内容到HEAD中保存的状态,可在reset后使用.

## git rm
> 将文件删除应用到stage,不能用git add,相应地可以使用git rm,效果跟add是一样的
- 当然,只要commit过,该文件总可以通过`reset --hard`或`reset`和`checkout --`恢复

# commit设置HEAD
> 每次commit都创建了一个快照,git内部有一个HEAD指向了当前所在的快照,可通过`git reset [version]`命令设置HEAD指向的快照.

## git log
> 显示当前项目详细的commit记录,包括提交人(Author),提交时间(Date),commit的id
```
git log
```
- 如果要显示精简的信息`git log --pretty=oneline`

## git reset
> 有两种方式,一种是回退几步,相当于相对reset;另一种是输入versionId,相当于绝对reset
- **注意**:执行reset后,未stage的数据会被冲掉
```
git reset --hard HEAD~1
```
- 这里1代表回退一步,如果是2就是回退两步(廖雪峰教程里提示用^,这个windows系统不支持)
```
git reset --hard {versionId}
```
- 这里的versionId对应着的是每个commit后自动生成的

## git reflog
> 另一种形式的commit log,不仅记录commit,还记录了全部的HEAD(因git reset)发生的变化

# branch分支
> 这里通过一般工作流程来说明究竟branch有什么用
- master是主分支,一般只有代码可以上新版本的时候才会使用它,其commit不多
- 日常开发使用dev分支,需要发版本的时候,才用master去吸收dev分支的内容
- 多人协作,每个人在得到需求时,都使用自己的分支(比如john-issue101),开发完毕后push到dev分支

## git branch
> 查看当前工作目录下的所有分支,当前分支(HEAD指向的分支)会被加上星号`*`凸显展示

## git branch {branchName}
> 用当前分支创建branch,其本质只是创建了一个新的branch指针

## git checkout {branchName}
> 切换到指定branch

## git checkout -b {branchName}
> 相当于`git branch {branchName}`和`git checkout {branchName}`两个命令的合并

## git merge {branchName}
> 将branchName合并到当前分支.
- 注意,合并前要切换到被合并的分支.
- 合并会将两边的修改整合到本branch
- 发生冲突的原则--只要两边在同一个地方(同一行,或者连接着的两行)发生了修改,就会有冲突
- 冲突后,会在文件中标记本branch的内容和合并分支中的内容


# stash配置
> 用于完整地保护当前环境(包括未add的内容)

## git stash
> 该命令可保存当前所有状态为一个stash

## git stash list
> 查看所有stash
stash的信息格式如下:
```
stash@{0}: WIP on dev: 65ff14c 添加gitignore
stash@{1}: WIP on master: 8d0322d master消除冲突
```
- 这里是一个列表,最新的索引为0,依次向下

## git stash apply {stash@{0}}
> 恢复到指定stash,注意写法

## git stash drop {stash@{0}}
> 删除指定stash

## git stash pop
> 简化版,相当于恢复最晚的stash并删除这个stash

## git cherry-pick {versionId}
> 顾名思义,恢复stash的时候,往往期间已经有commit了,这里使用这个命令,单独merge某个commit的提交
- 比如开发途中突然出现了一个bug,这个时候首先用stash保存当前开发状态
- 修复bug后,恢复到这个stash,bug修复的提交当然是没有的
- 这个时候就可以用cherry-pick,找到这个commit的versionId,然后提交即可