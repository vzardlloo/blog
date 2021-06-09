---
title: 同步Github上的fork
date: 2017-10-13 15:08:51
tags:  Github
categories: Github
---
我们有时候会在Github上fork一个我们感兴趣的项目到自己的仓库，等一段时间过后这个项目已经更新了，但是自己仓库里还停留在刚fork时的状态，那么我们如何把自己fork到仓库的项目同步到作者的进度呢？这篇博客主要就是讲一下这个问题。
这个问题我有两种解决的办法：
一种是在Github上操作，把自己仓库的项目设成`base`,把作者的的项目设成`head`,然后给自己提pr,最后在pull下来就行了。这种方法的缺点就是要开网页点来点去，如果你正在沉迷在命令行里无法自拔可能不太喜欢这种方法。
<!--more-->
第二种方法是在把作者的项目添加为自己的远程分支，然后需要同步的时候`fetch`一下这个远程分支，然后切换的本地分支（如果不在的话），然后把远程仓库合并到本地分支。具体方法如下：
+ 先查看一下自己的远程分支：
```
git remote -v
# origin  https://github.com/YOUR_USERNAME/YOUR_FORK.git (fetch)
# origin  https://github.com/YOUR_USERNAME/YOUR_FORK.git (push)
```
+ 如果作者的仓库不在远程分支中，则把他添加到远程分支：
```
git remote add upstream https://github.com/ORIGINAL_OWNER/ORIGINAL_REPOSITORY.git
```
+ 再次查看远程分支情况，确保已经加入：
```
git remote -v
# origin    https://github.com/YOUR_USERNAME/YOUR_FORK.git (fetch)
# origin    https://github.com/YOUR_USERNAME/YOUR_FORK.git (push)
# upstream  https://github.com/ORIGINAL_OWNER/ORIGINAL_REPOSITORY.git (fetch)
# upstream  https://github.com/ORIGINAL_OWNER/ORIGINAL_REPOSITORY.git (push)
```
+ 从上游仓库 fetch 分支和提交点，传送到本地，并会被存储在一个本地分支 upstream/master
```
git fetch upstream
# remote: Counting objects: 75, done.
# remote: Compressing objects: 100% (53/53), done.
# remote: Total 62 (delta 27), reused 44 (delta 9)
# Unpacking objects: 100% (62/62), done.
# From https://github.com/ORIGINAL_OWNER/ORIGINAL_REPOSITORY
#  * [new branch]      master     -> upstream/master
```
+ 切换到本地主分支(如果不在的话)
```
git checkout master
# Switched to branch 'master'
```
+ 把 upstream/master 分支合并到本地 master 上，这样就完成了同步，并且不会丢掉本地修改的内容。
```
git merge upstream/master
# Updating a422352..5fdff0f
# Fast-forward
#  README                    |    9 -------
#  README.md                 |    7 ++++++
#  2 files changed, 7 insertions(+), 9 deletions(-)
#  delete mode 100644 README
#  create mode 100644 README.md
```
+ 同步到Github上的自己的项目
```
git push origin master
```

OK,这个小问题都到此结束！














