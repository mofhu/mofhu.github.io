---
layout: post
title: "Git 入门笔记"
date: 2016-02-01 22:20:00
categories: 编程 Programming 笔记 从零到一 Chaos-to-sth Git
excerpt: Git 笔记
---


## Git basics

1. `git init`

2. `git add <file>`
  - Use "\*", etc.
  - eg. `git add "\*.md"`
  - `git add .` to add all to commit
  - `.gitignore` file 可用来标记出不想被 git 记录的文件, 如 .pyc 文件
  - GitHub 建立仓库时提供了很多 .gitignore 的标准模板, 可根据个人需要再添加相关内容

3. `git commit`

	`git commit -m "the description of changes, will be shown in logs, so it's important"`
	`git commit -am (or -a -m) "description"`
	commit all changes ONLY in files git has already managed. (i.e. not applied to files not added. so to apply all changes, using git add before git commit.)
	git add and git commit
	[details](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/0013745374151782eb658c5a5ca454eaa451661275886c6000)

4. `git status`
5. `git diff`
6. `git log`
7. On deleting 
	`git rm` deleting file do not deleting it in git.
	`git checkout` can be used for replacing files in working folder.

8. On resetting

	`git reset --hard commit_id`
	HEAD: this version
	HEAD^, HEAD^^, HEAD~100
	>normal commit id: commit 31dc03905a9866775fe45ac8ae22382c685ce66e
	`git log`
	to get commit id.
	`git reflog`
	to get to a later version.

 9. Branching
	`git branch <branch name>`: list (without argument) and add new branch
	`git checkout`
	short command for branch and checkout: `git checkout -b <branch name>`
	`git merge <name>`: merge a branch to branch in use.
	`git branch -d <name>`: deleting a branch
	branching with conflicts: need to add later
	general branching strategy: master-dev-bugs & features

10. Tagging
	`git tag <tag-name>`

## Remote, push and pull

1. From local to remote: `git remote add origin <link>`; 

from remote to local: `git clone <link>`; 

details `git remote -v`

2. Push 

	first push: `git push -u origin master`
	for later pushes: `git push origin master`

3. Add remote:
我们经常会遇到有多个 remote 仓库的情况, 如你是项目管理员, 
有几个人分别更新他们各自的代码, 但他们都在自己的仓库工作.
此时你可以方便地从多个仓库选择代码更新到自己的本地.

`git add remote [shortname] [url]`

`git remote` 显示所有的远程仓库, `-v` 显示对应的克隆地址

此部分详见 [Pro Git相关章节](https://git-scm.com/book/zh/v1/Git-%E5%9F%BA%E7%A1%80-%E8%BF%9C%E7%A8%8B%E4%BB%93%E5%BA%93%E7%9A%84%E4%BD%BF%E7%94%A8)


## GitHub

- Fork

使用刚刚提到的多个 remote 可以方便地从主仓库 pull 到最新的更新合并到自己的 fork 之中.

- Pull request

## References and Resources
- [Git cheet sheet](http://www.git-tower.com/blog/assets/2013-05-22-git-cheat-sheet/cheat-sheet-large01.png) 打印出来放在手边即可
- [廖雪峰的 Git 教程](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000)
- Pro Git: [English](https://git-scm.com/book/en/v2), [中文](https://git-scm.com/book/zh/v2)
