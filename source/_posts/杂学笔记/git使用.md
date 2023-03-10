---
title: git使用
categories: 笔记
tags: 
- git
cover: https://s1.ax1x.com/2023/03/10/ppnbMQ0.jpg
---
## 项目分支情况
* 远程分支：目前只需要一个远程master分支就好，大家直接在上面开发提交。
* 本地分支，建议也是一个就够用了，绑定远程的master分支。如果想随便写东西实验，新建其他分支就好。
## 安装项目到本地
1. 在新建文件夹路径下创建git仓库：
```BASH
git init
```
2. 添加远程仓库：
```BASH
git remote add origin https://gitee.com/lei-xilin/ScheduleManager.git
```
3. 下拉远程分支：
```BASH
git pull origin master
```
4. 本地master分支绑定远程master分支：
```BASH
git branch --set-upstream-to=origin/master master
```
5. 测试：
```BASH
git pull
```
    若出现“Already up to date.”说明成功。

## 工作流程
### 打开电脑，首先先拉取最新的代码：
```BASH
git pull
```
#### 若出现冲突，则进行解决,解决完冲突之后，先提交，然后再次拉取代码，最后将解决完冲突的合并代码上传。
```BASH
git commit -a -m 解决冲突
git pull
git push
```
#### 若无冲突，正常开始工作。
### 工作完成，进行代码提交
若有新建文件需要上传，先添加该文件：
```BASH
git add <新加入的文件>
```
之后进行本地提交：
```BASH
git commit -a -m 某工作完成
```
最后提交到远程分支：
```BASH
git push
```

若出现冲突，则先pull，手动修改冲突，本地提交之后重新提交到远程分支。

## 常用git命令
追踪所有文件：
```BASH
git add -A .
```
合并分支：
```BASH
git merge <branch name>
```
强制覆盖本地分支：
```BASH
git pull --allow-unrelated-histories
```
抛弃所有未保存的修改，回到最近一次commit的状态：
```BASH
git checkout .
```
本地回滚到某个commit，xxxxxx为commit编号：
```BASH
git reset --hard xxxxxx
```
查看当前文件状态，文件状态常见的有untracked(未跟踪)，modified(已修改)，deleted(已删除)等：
```BASH
git status
```
查看所有分支名称:
```BASH
git branch -a
```
查看所有分支的绑定情况：
```BASH
git branch -vv
```
新建本地分支：
```BASH
git checkout -b <name>
```
删除本地分支：
```BASH
git branch -d <name>
```
查看本地提交记录：
```BASH
git reflog
```