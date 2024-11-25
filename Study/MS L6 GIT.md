2024-11-25 15:27

Tags:[[Git]] [[Missing semeter]]

# git的数据模型以及内部实现细节
## 版本控制系统概述
使用快照储存每次的修改记录和修改信息,极其有助于并行处理项目以及调试错误
git就是一个行业标杆的分布式版本控制系统.github是其online 托管平台之一
## 底层设计
### 内部数据模型
- git称direction为tree,file为blob
- tree是一个递归的数据结构
- 快照:将整个树拍下来,可以看作带有时间戳的目录副本
- git使用有向无环图记录快照序列
>[!info] 伪代码表示git底层逻辑
>	type blob=array\<byte>//文本文件就是byte组成的数组
>	type tree=map\<name,subtree|file>//树是从名字到子树或文件的映射
>	type commit=struct{
>	parent: array\<commit>//可能有多个父节点
>	author&message: string
>	snapshot: tree}

>[!info] 以上是git内部的数据结构.下面是git对disk中实际数据存储和寻址的实现:
>	type object=blob|tree|commit
>	object_in_disk=map\<harshid,object>

- harshid是使用harsh函数生成的id,即哈希值.
- harsh函数根据数据内容生成一个短字符串,可作为对象(object)的id
- 磁盘中存储id,通过哈希值寻址到对应的object,即从id到object的映射 

>[!info] 问题来了:所有映射通通储存在git仓库里面,一定会造成巨大空间占用!

- 实际上,git只需要储存高效压缩格式的快照和author&message信息,以及对象对应的id
- git内置一个对象表,这个表将所有id与对象内容建立双射.只有object表里存储实际数据.
- 由于双射,git除了涉及内容本身的操作时,调用的都是哈希值,commit中父节点数组存储的也是哈希值,这样就大大减少了运行时开销
### 缓冲区
- git没有删除commit的指令,一旦commit,就无法删除单个commit,除非删库.That's why there's a buffer between repository and local directory.
## Git进阶操作
有关git基础命令见[[Git]].
### 查看commit和回档
1. git cat-file -p(print) hashid 打印log中某次提交的内容的id以及提交信息.
	- 进一步对tree使用上面命令,打印tree中blob的哈希值.
	- 对blob使用上述,打印blob的实际内容
	- 与show的不同:show直接打印commit修改的内容
2. git log的更多有用后缀名
	1. --oneline只打印commit message的title
	2. --graph制图,让log更美观,配合--decorate
	3. --reverse翻转历史记录,从头到尾顺序下来(注意不能与graph混用)
	4. --all log默认只打印当前分支,而--all可打印所有分支的提交记录
3. git节点间移动
	- 实际上,移动的只是HEAD指针.
	- 使用git checkout移动HEAD指针到任意branch/节点上
	- 此时你电脑上就是HEAD指针所指的内容
4. difftool的应用
	 - git difftool 后面可以加两个参数, 表示从前者到后者的变化,如git difftool HEAD^ HEAD.只有一个参数时默认与HEAD对比
5. git blame +文件名 具体查看文件每行的提交信息
### 分支相关
1. git branch
	1. 不加参数时显示目前有的分支.
	2. 加参数\<name\>创建目标分支.
	3. git checkout -b \<name\> 创建分支并切换
2. git merge/rebase 将目标分支合并到当前分支.一个是非线性一个是线性.
	-  可能出现合并冲突:使用editor手动合并后,add再merge/rebase --continue
## 远程仓库 
- 使用ls .git查看当前目录的git repository.它包含所有objects和refs(引用)等信息,以及配置
- 远程仓库就是当前仓库在其他用户或主机上的副本.
- git remote查看当前库的所有远程仓库.
	- git init --bare 设置一个空仓库,适合用于做远程仓库
	- git remote add \<name\> \<URL\>
	- 实际上,以上两步加在一起就是 git clone \<URL\> \<目录名\>
- git push \<remote name\> \<local branch\>:\<remote branch\>
- git clone --shallow可只复制最后一次commit
有关分支操作和远程操作,有个很棒的可视化教程:
[git分支教程](https://learngitbranching.js.org/?demo=&locale=zh_CN)