---
layout: post
title: "Git 入门笔记"
date: 2016-02-01 22:20:00
categories: Programming
tags: 笔记 从零到一 Git 工具
excerpt: "Git 入门哪家强? 我还是兹磁 Pro Git."
---

* content
{:toc}

**注意**: 如果你想学习 Git 的基本用法, 请点击 [**Code School - Try Git**](https://try.github.io/) 
自己打一打命令试一试. 入门要练! 不要看! 

## 本地的版本管理

1. 初始化
  - 安装 Git 后, `git init` 即可建立仓库
  (不小心在错误的地方初始化仓库的话, 删除 `.git` 文件夹就可以不再用 Git 管理.)

2. 添加文件 `git add <file>`
    - 添加到(stage) 文件. 注意, 只要 stage 之后, 文件就有了一份备份. 
    而且也可以通过 `git reset` 随时再从 staged 状态取消.
    - `.gitignore` file 可用来标记出不想被 git 记录的文件, 如 .pyc 文件
        + GitHub 提供了很多语言的 [.gitignore 的标准模板](https://github.com/github/gitignore), 
        在线建立仓库时可以选择, 也根据个人需要随时再添加相关内容.
        + 注意如果先加上了文件, 再设置的 .gitignore, 那么已经加入的文件还会被 Git 记录, 
         可通过 `git rm` 删除

3. `git status`
    - 最常用的命令之一, 用来看看仓库发生了哪些变化.
    - 如果一切满意就可以进行 提交, 分支等操作
    - 觉得长可以试试 `-s`

4. `git diff`
    - 和 `git status` 是对好基友.
    - 还常用 `git diff <commit>` / `git diff --staged`

5. 提交 `git commit`
    - 提交是 Git 追溯版本的最小单位, 因此提交非常非常重要
    - `git commit -m "the description of changes"`
    - 有很多所谓的 "Best practices" 都与提交有关: 
        - 多次小的提交
        - 一次提交围绕着一个功能
        - 提交的代码应该经过测试
        - 提交时应写详细的注释: 说明提交的目的, 与前一个版本的区别等等
        - 多人协作时应更严谨一些
    - `git commit --amend` 可以修改前一次提交 (注意: 不要修改已经 push 的提交!!)

6. `git log` 显示提交日志, commit hash 从这里找

7. 删除
    - `git rm` 删文件之后还要用这个命令或者 `git add .` Git 才知道.
    - 移动文件可用 `git mv`

8. 回退
    - `git reset commit_id`
    - `--hard` 要小心使用. `git reset` 是安全的
    - 常用的简写
        - `HEAD`: 最近的提交; `HEAD^` 前一个提交; `HEAD^^`, `HEAD~100`
        - commit id: commit 31dc03905a9866775fe45ac8ae22382c685ce66e
    - `git reflog` 记录了所有 HEAD 的修改

9. 分支
    - Git 的主要功能之一
    - `git branch <branch name>`: list (without argument) and add new branch
    - `git checkout` 切换分支
        - **切换分支前, 一定要把当前工作区清理干净**: 否则修改也会被带到分支中去
        - 如果当前分支比较乱, 可用 
        [`git stash`](https://git-scm.com/book/zh/v2/
        Git-%E5%B7%A5%E5%85%B7-%E5%82%A8%E8%97%8F%E4%B8%8E%E6%B8%85%E7%90%86) 储藏起来; 
        以后再用 `git stash apply` 恢复
    - `git merge <name>`: merge a branch to branch in use.
        - 遇到不能直接 merge 的情况不要慌! 不要慌! 通常仔细看看改改就好了.
    - Best practices: 
        - master / dev / bugs / features...
        - master 始终保持干净
        - 考虑适当使用 tags
    - 更详细和权威的介绍: [分支 - Pro Git](https://git-scm.com/book/zh/v2/Git-%E5%88%86%E6%94%AF-%E5%88%86%E6%94%AF%E7%AE%80%E4%BB%8B); [常用的分支工作流(workflow)](https://git-scm.com/book/zh/v2/Git-%E5%88%86%E6%94%AF-%E5%88%86%E6%94%AF%E5%BC%80%E5%8F%91%E5%B7%A5%E4%BD%9C%E6%B5%81)

## Remote, pull and push

1. 远程仓库
    - 本地 Git 可以很好地管理代码; 远程仓库则让我们可以进行多人协作, 同时, Git 分布式的设计让代码更加安全.
    - 著名的项目托管网站有 [GitHub](https://github.com/), [Bitbucket](https://bitbucket.org/) 等, 国内也有 [GitCafe](https://gitcafe.com/) 等.
    - From local to remote: `git remote add origin <link>`
    - from remote to local: `git clone <link>`
    - 我们经常会遇到有多个 remote 仓库的情况, 如你是项目管理员, 有几个人分别更新他们各自的代码, 
    但他们都在自己的仓库工作. 此时你可以方便地从多个仓库选择代码更新到自己的本地.
    详见 [Pro Git](https://git-scm.com/book/zh/v1/
    Git-%E5%9F%BA%E7%A1%80-%E8%BF%9C%E7%A8%8B%E4%BB%93%E5%BA%93%E7%9A%84%E4%BD%BF%E7%94%A8)
    - `git add remote [shortname] [url]`
    - `git remote` 显示所有的远程仓库, `-v` 显示对应的克隆地址

2. Pull 拉取远程仓库
    - `git pull <remote> <branch>`
    - pull == fetch + merge

3. Push
    - `git push <remote> <branch>`

## 项目托管网站

### GitHub

- Fork + Pull request 是 [GitHub](https://github.com/)
工作流的重点. 一个常见的工作流:
    - 管理员管理主仓库, 其它人在自己的 Fork 中工作; 如果是 organization/repo 的项目,
    一般大家都会在自己的 Fork 下工作, 主仓库只用来管理.
    - 新的版本在自己的分支开发后, 与最新的远程 master merge 好, 推送自己的 master, 
    再 PR 主仓库(这样主仓库的 master 在 PR 时就可以直接 merge 了) 
    - PR 后管理员通过 PR 页面讨论代码
    - 更详细的介绍: [GitHub - Pro Git](https://git-scm.com/book/zh/v2/GitHub-%E8%B4%A6%E6%88%B7%E7%9A%84%E5%88%9B%E5%BB%BA%E5%92%8C%E9%85%8D%E7%BD%AE)

- 主要的协作功能还有 Issue 和 Wiki

- [GitHub Pages](https://pages.github.com/) 可通过 Jekyll 托管静态站点. 
除了介绍项目外, 也可以作为托管主机用来写博客.

- [Gist](https://gist.github.com/) 可以用来存放小代码片段, 也可以用来写便签 :)

- GitHub 的开源项目免费, 私有项目按**仓库数量**收费 (目前最小的个人 plan 是 5 个私有仓库, $7/month.) 
不限制合作者的数量.

### Bitbucket

与 GitHub 的收费方式不同, [Bitbucket](https://bitbucket.org/)
的私有仓库按照**合作者数量**收费, 每个用户的私有仓库数量无限制. 
免费私有仓库的合作者数量是 5 人. 最小的 plan 是 10 人, $10/month. 

对于不想花钱的用户来说, Bitbucket 更适合个人或小团队存放一些私人项目. 
更具体的讨论可以看[这里](http://www.oschina.net/translate/bitbucket-vs-github-its-more-than-just-features?print), 
不过这篇文章的年头稍长(2014 年 5 月), 可能与现在的情况稍有差别.

## 附注: 炫酷的 Git Terminal 配置

Git 为 bash, zsh 等 shell 都提供了补全库和自定义提示符. 
如果你使用了一些强大的 zsh 框架, 如[oh-my-zsh](http://ohmyz.sh/), 
那么你的 Terminal 可以变得相当炫酷:

![oh-my-zsh Powerline](http://7xqt2z.com1.z0.glb.clouddn.com/blog%2Ffigures%2F2016-02-01-git-in-zsh.gif)

上图中配置的主题是 [Powerlevel9k](https://github.com/bhilburn/powerlevel9k).


---

References and Resources

- [Code School - Try Git](https://try.github.io/) 入门要练! 不要看!
- 专业的看这本 - **Pro Git**: [English](https://git-scm.com/book/en/v2), 
[中文](https://git-scm.com/book/zh/v2)
- [Git cheet sheet](http://7xqt2z.com1.z0.glb.clouddn.com/
blog%2Ffigures%2F2016-02-01-git-cheat-sheet.png) 
打印出来放在手边即可. [源地址](https://www.git-tower.com/blog/git-cheat-sheet/) 也有中文的哦
- [廖雪峰的 Git 教程](http://www.liaoxuefeng.com/wiki/
0013739516305929606dd18361248578c67b8067c8c017b000) 入门也可以看这个教程

