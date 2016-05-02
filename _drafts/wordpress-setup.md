---
layout: post
title: "OS X 下本地安装调试 WordPress"
categories: WordPress 编程 笔记 Programming
---

## OS X 下本地安装调试 WordPress

- 安装 MAMP
- 设置 MAMP
- 安装 WordPress
- 本地调试 WordPress
- 打包上传到远程服务器

前一阵莫名躺枪帮忙做网站, 考虑到维护成本, 必须想办法做一个带所见即所得编辑器后台的网站(否则就都得自己维护了), 于是想到了著名的 WordPress. 然而不可能在服务器上大规模修改和调试网站, 于是就研究了一下如何在本地安装调试 WordPress, 简记如下.

### 安装 MAMP

MAMP stands for Macintosh, Apache, MySQL, and PHP.

这里需要注意的是只要使用 MAMP 即可(不需要 MAMP PRO)

### 设置 MAMP

WordPress 需要设置 MySQL 和 Apache 才能安装.

因此我们需要在 MAMP 界面和管理网站中进行设置. 按照官方教程设置即可.

### 安装 WordPress

设置好 MAMP 后, 我们就在本地构建了 wp 的安装环境. 下面按照经典的 5 分钟安装即可.

### 本地调试 WordPress

访问本机地址即可看到 WordPress 前台和后台.

### 打包上传到远程服务器



---

- [WordPress 官方教程(英文)](https://codex.wordpress.org/Installing_WordPress_Locally_on_Your_Mac_With_MAMP)
- [本地安装WordPress调试以及上传到远程服务器](http://sparkandshine.net/local-install-wordpress-debugging-and-upload-to-remote-server/)
