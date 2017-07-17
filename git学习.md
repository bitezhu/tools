---
title: git学习
tags: 学习笔记
grammar_cjkRuby: true
---


### 1. git安装
```markdown
安装完成后机器需要自报家门：名字和Email地址。
$ git config --global user.name "bitezhu"
$ git config --global user.email "zhush1992@163.com"
---global 这个参数会应用到所有仓库，当然也可以对单个仓库进行配置。
```
### 2.创建仓库
```markdown
$ mkdir zshlib
$ cd zshlib
$ git init
$ git add readme.txt (把文件添加到仓库,可反复多次使用，添加多个文件)
$ git commit -m "wrote a readme file" (把文件提交到仓库,会返回一个commit ID)
$ git status (随时查看仓库状态)
```

### 3.版本回退
如果对仓库下的文件进行多次修改，需要回复到之前的某个版本，可以用git log
```markdown
$ git log --pretty=oneline
$ git reset --hard HEAD^
```
* HEAD指向的版本就是当前版本，HEAD^表示上一个版本。Git允许我们在版本的历史之间穿梭，使用命令git reset --hard commit_id。
* 穿梭前，用git log可以查看提交历史，以便确定要回退到哪个版本。

* 要重返未来，用git reflog查看命令历史，以便确定要回到未来的哪个版本。

### 4.工作区和暂存区
工作区有一个隐藏目录.git,这个是git的版本库。版本库中有很多东西，其中最重要的就是成为stage（or index）的暂存区，还有git为我们自动创建的一个分支master，以及指向master的一个指针HEAD。
所以每次我们add，是将文件暂时添加到暂存区，每次修改，如果不add到暂存区，是不会加入到commit中的；commit则是把暂存区的所有内容提交到当前branch。

### 5.撤销修改
```markdown
$ git checkout -- filename
```
* git checkout -- filename意思就是，把readme.txt文件在工作区的修改全部撤销.
* git checkout -- file命令中的--很重要，没有--，就变成了“切换到另一个分支”的命令

### 6.删除文件
```markdown
$ git rm xxxfile
$ git commit -m 'rmfile'
$ git remote rm <repository-name>. （远程删除仓库）
```

### 7..远程推送
```markdown
$ git push origin master
```