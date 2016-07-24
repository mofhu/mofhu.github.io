---
layout: post
title: 在 AWS Elastic Beanstalk 部署 Django 应用
date: 2016-07-23 22:30:00
categories: Programming
tags: 从零到一 AWS Django
excerpt: "ConfusedChemDogs 是 Organization Lab 的第一个上线应用. 这篇文章简单记录了最初版本的部署过程."

---

最近和小伙伴一起设计了一个 Django 应用的雏形 [Confused ChemDogs](http://confusedchemdogs.club/). 我们先部署了一个相当粗糙的 MVP 版本(甚至没有任何功能). 这里记录过程中的一些折腾, 也作为一篇 wiki 备用吧:)

主要参考 AWS 官方教程 [^1].

## Django 应用

建立一个可用的 Django 应用.

使用 `$ python manage.py runserver` 测试可在本地正确访问即可.

## AWS EB CLI

按照官方教程:

1. 运行 `pip freeze` 得到 `requirements.txt`. 后续 Elastic Beanstalk 会使用这个文件建立合适的 EC2 实例
2. 建立 `.ebextensions` 目录, 并进行 `django.config` 的初始化设置.
3. 使用 EB CLI 部署站点
    1. 使用的命令可能包括 `eb init`, `eb create`, `eb status`, `eb deploy`等等.
    2. EB CLI 使用 git 管理代码.

## 连接 GoDaddy 域名

按照上面的流程, 此时我们已经可以访问部署在 AWS 上的网站了. 但目前的域名还是 AWS 提供的 `http://app_name.location.elasticbeanstalk.com/` 如果我们需要把自己购买的域名连接到站点, 那么可以配置一个 AWS Elastic IP, 再把 IP 与域名连接:

1. AWS Elastic IP 中建立一个新的 Elastic IP, 并与自己的 EC2 实例连接.
2. 配置 AWS Security Groups, 使站点可以被公网访问. (这里把我坑了很久, 开始还以为是)
3. 在 GoDaddy 域名 DNS 设置中添加一条 A 记录, 与自己的 Elastic IP 相同.

等待域名服务器刷新后, 就可以使用自己的域名访问网站了 :)

---

[^1]: [Deploying a Django Application to Elastic Beanstalk - AWS Elastic Beanstalk](http://docs.aws.amazon.com/elasticbeanstalk/latest/dg/create-deploy-python-django.html)
