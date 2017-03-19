---
layout: post
title: chaos to MoinMoin
date: 2017-03-20 06:42
categories: Programming
tags: wiki python MoinMoin
excerpt: "为了给个人数据库和文档集加上搜索功能和修改记录系统, 最近计划迁移文件夹为主的管理模式到 Wiki 管理模式. 最终选择了基于 Python 的 MoinMoin 作为 Wiki 服务器. 启动服务的过程相当简单. Let's write freely with MoinMoin."
---

* content
{:toc}

为了给个人数据库和文档集加上搜索功能和修改记录系统 (很多 raw file 等不便于直接 Git). 我决定迁移文件夹为主的管理模式到 Wiki 管理模式.


优点:

- 搜索功能: 最大的优势
- 所有修改的历史记录
- 整合 Git 之后的版本管理和容灾
- 浏览器 based, 轻量, 便于分享
- 促使自己形成文本为主的管理方式, 比 raw file (PPT/Word) based 好很多

缺点:

- 需要一定的折腾
- 初期可能不适应

[MoinMoin](https://moinmo.in/) 是一个基于 Python 的 Wiki 系统. 在众多 Wiki 中我决定支持 Python :)

MoinMoin 1.9 依赖于 Python 2.7, 在 pyenv 中配置文件夹的 local 版本即可.

以下是一些 [MoinMoin 教程](https://moinmo.in/WikiCourse)的摘录:

## MoinMoin editing

It is important to frequently use the **preview** function while editing a page, since using it will tell MoinMoin to create a backup of the editor's current content.

In a word, just uses MoinMoin's buttons: preview, edit, cancel, etc.

## wiki syntax - very similar to MarkDown

```text
- headlines: = first level =, 增加 = 的数量是更低级的标题
- text style:
    - ''italics''
    - '''bold'''
- links:
    - WikiNames
    - [[free link]]
    - [[page link|show name]]
- external links:
    - link
    - [[link|show name]]
    - {\{embed fig link}} (这里的 \ 因为 liquid 语法冲突加入.)
- attachments:
    - [[attachment:letter.txt]]
    - {\{attachment:foto.jpg}}

```

## Creating more pages

The easiest and best way to create more pages is to:

* think of a good page name. 
* edit an appropriate page, add a link to the new page, and save it.
* click on the link to the new page
* prepare to edit using:
    * an empty page.
    * a page template (a list of templates is displayed on the left).
    *  the list of similar page names shown to ensure that there isn't any slightly different named page on the same topic. 
* start editing, fill the page with content, and save it.

 This method makes sure that new pages are always accessible from links on already existing pages.


## Structure in the wiki

### Hierarchy levels

Many conventional websites use very branched structures with many hierarchy levels.
In wikis linking is heavily used. A simpler structure is often used to simplify matters:

* all important topics are located in main pages.
* only if a topic has many subtopics (which aren't main topics), subpages are used.
*  Sub pages are named as MainPage/SubPage

### Conventions

A wiki gives much freedom to the users; there aren't input masks like in database applications, but you can, in principle, do whatever you want and do it however you want.

Of course, you should avoid that to prevent complete chaos.

That's why it's usual for new wikis to think about structures and conventions, such as the:

* creation of a basic structure of pages.
* definitions of name conventions for page names.
* decision to use CamelCase or free links?.
* definitions of important categories.

It's usual for older wikis to simply carry on using existing structures.
