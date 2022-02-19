---
title: git使用
author: helaine
date: 2022-02-17 16:40:33
tags: ['git']
categories: git的使用
---

# git的使用

{% note info, 这一part主要关于git的使用和基本步骤。 %}

## git不同情况的使用

{% title h3 , 企业项目 %}
**这个时候一般都是有代码和分支**
* 1.拉取代码到本地 git clone
* 2.在本地新建个人分支并切换：
`git checkout -b dev //dev是分支名字`
* 3.关联本地分支和远程分支 
`git branch --set-upstream-to=origin/本地分支对应的远程分支  你当前的本地分支`
* 4.git pull 拉取最新代码
* 5.在该分支上进行代码的add和commit
* 6.git push origin 分支名字

{% title h3 , 个人项目 %}
#### 通过github新建项目
1.在github上新建仓库 
![新建仓库](1.png)
2.在本地 vscode 下载
`git clone 仓库地址`
3.编写代码
4.暂存
`git add .`
5.提交
`git commit -m '提交信息'`
6.关联本地分支和远程分支 
`git branch --set-upstream-to=origin/本地分支对应的远程分支  你当前的本地分支`
7.推送代码
`git push origin 分支`
如果没有在github远程仓库上新建分支，默认分支名字是HEAD:main
`git push origin HEAD:main`
也可以默认 {% pbg yellow , git push origin HEAD %}，这个时候会自动创建跟本地分支一样的分支并推送。

#### 通过本地新建项目
需要自己初始化一个项目，多出.git目录，下面保存的是版本信息。
`git init`
后面的步骤如图上,但是要关联本地仓库和远程仓库
`git remote add origin 仓库地址 //关联远程库`

#### 把已有项目和github上关联
**这个时候一般都是有代码和分支**
1.需要自己选择创建新分支还是到master分支上
* 直接git push会报错,需要将本地分支关联到远程分支
	**报错：更新被拒绝，因为远程仓库包含您本地尚不存在的提交。这通常是因为另外一个仓库已向该引用进行了推送。再次推送前，您可能需要先整合远程变更**
2.提示远程仓库没有本地的代码，所以拉取代码
* git pull 拉取最新代码
	**提示信息,当前分支没有跟踪信息,请指定您要合并哪一个分支**
因为在执行git pull, git push操作时不知道指定对应的哪个远程分支，你只要没有显示指定，git pull的时候，就会提示你。
解决方法:
`git branch --set-upstream-to=origin/本地分支对应的远程分支  你当前的本地分支	`	


## 常见的git命令使用
`git config  —list //显示git配置`
`git remote -v  //查看远程仓库详细信息`
`git remote remove origin //解除远程仓库关联`
`git status //查看仓库状况`
`git branch -r //列出所有远程分支`
`git branch -a //列出所有本地分支和远程分支`
`git reset 版本号 //回退版本`
`git checkout -b dev //创建并切换到dev分支`
`git merge dev //把dev分支合并到当前分支上`
* git pull和git fetch的区别
	* `git fetch  //是将远程主机的最新内容拉到本地，用户在检查了以后决定是否合并到工作本机分支中。`
	* `git pull  //则是将远程主机的最新内容拉下来后直接合并，即：git pull = git fetch + git merge，这样可能会产生冲突，需要手动解决。`

`git branch --unset-upstream //撤销本地分支与远程分支的映射关系`
`git branch -vv //查看本地分支和远程分支的映射关系`
`git log —oneline —graph //查看git 分支 tree`
`git checkout . //恢复暂存区的所有文件到工作区`

`git rebase //分支劈叉` [解释1](https://www.jianshu.com/p/f7ed3dd0d2d8)  [解释2](https://www.bilibili.com/video/av51229455?zw)
{% title h3 , commit格式%}
![commit](feat.png)





