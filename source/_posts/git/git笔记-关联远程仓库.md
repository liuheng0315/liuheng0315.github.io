---
title: git笔记-关联远程仓库
date: 2021-04-26 09:44:21
tags:
	- git
categories: 
	- git
cover: /images/git.jpeg
typora-root-url: ../..
---

git关联远程项目步骤：

git init

git remote add origin https://gitee.com/ZhongHuaShiShan/eshop.git

git fetch origin

git branch --set-upstream-to=origin/master master

git pull origin master --allow-unrelated-histories

git push -u origin master



git暂存：

git stash --暂存代码

git stash list  --查看暂存

git stash apply --恢复暂存