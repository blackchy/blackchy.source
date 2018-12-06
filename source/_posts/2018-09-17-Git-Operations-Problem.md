---
title: Git:操作和问题的收集
date: 2018-09-17 00:00:01
categories: git
tags: git
---

Git是一个很好的工具，偶尔想用某个命令的时候却突然忘记了:(，网上再去查找又有点浪费时间，所以收集了一些常用的命令，顺带一些其他相关的东西。
<!-- more -->

### Git安装

Git安装、配置等教程省略，网上搜索。

### GitHub

Github上创建空项目会提示如何初步使用

![img][1]

### Git操作收集

1. git变基

	在需要变基的分支上：

	`#git rebase <other branch>`

	如果有冲突，解决冲突后

	`#git rebase --continue`

2. git合并commit并强推

	查看有几笔提交	

	`#git log` 

	编辑commit（n为commit数量）

	`#git rebase -i HEAD~n`  

	强推到远程

	`#git push -f origin chy/add-market`

	终止rebase

	`#git rebase --abort` 

3. git合并分支到new主分支

	`#git rebase -i new-master`

4. git合并其他分支到当前分支

	`#git merge <other branch>`

	如果没有冲突，直接就完成了

	如果有冲突，则解决冲突，然后执行：

	`#git add .`

	`#git commit`

5. git回到之前的节点

	(首先`#git log`查看节点)
	
	回到节点06b3680，并保留更改

	`#git reset --soft 06b3680`

	回到节点06b3680，并丢弃修改

	`#git reset --hard 06b3680`

6.  git获取远程分支

	`#git fetch`

	`#git checkout -b local-branchname origin/remote_branchname`

7. git强制覆盖本地文件

	`#git fetch --all`

	`#git reset --hard origin/master`

	`#git pull`

8. git强制跳转到某个版本

	查看所有的commit

	`git log -g`

	强制跳转到某个commit版本

	`git reset --hard d87da1f81de631fbe68c60c1e192aebce5a3cc72`

9. git提交、拉取、推送

	提交全部

	`#git add -A`

	`#git commit -m "Your mark!"`

	拉取同步远程仓库master分支

	`#git pull origin master`

	推送到远程仓库master分支

	`#git push origin master`

10. git修改远程仓库地址

	`#git remote set-url origin http://xxxxx.git`

11. git暂存文件

	将文件放入暂存区

	`#git stash `

	弹出暂存区的文件

	`#git stash pop `

	丢弃暂存区的文件

	`#git stash drop `


### Git问题收集

1. `error: RPC failed; result=22, HTTP code = 411`

	`fatal: The remote end hung up unexpectedly`

	`fatal: The remote end hung up unexpectedly`
	
	这个是因为http buffer不够造成的，可以简单配置git来解决：

	git config http.postBuffer 67108864 2.想要把已经push的文件（夹）加入到.ignore中 <br> 直接添加到.ignore中是不能成功的，因为已经push到服务器了，需要先删除本地缓存的文件：

	git rm -r --cached .idea 然后再将其添加到.ignore中，最后git push就ok了

	有些文件无法删除，比如sh.exe.stackdump，亦可用此法。
	
	修正：

	处理sh.exe.stackdump文件方法：
	
	1. 关闭SourceTree
	
	2. AndroidStudio中删除sh.exe.stackdump文件
	
	3. 再进行git相关操作，不会因为sh.exe.stackdump文件而出现问题

2. `fatal: Unable to create '/.git/index.lock': File exists.`

	解决办法：

	`$rm -f ./.git/index.lock`

	`In your repository directory.`

### Pro Git(中文版) 链接

[Pro Git(中文版)][2]

[1]:/uploads/git-operation-probem/img_git_new_repository.jpg
[2]:https://gitee.com/progit/index.html