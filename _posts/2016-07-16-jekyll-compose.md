---
layout: post
title: Jekyll::Compose 简洁的 Jekyll 博客辅助脚本
date: 2016-07-16 21:05:00
categories: Programming
tags: 工具
excerpt: "Jekyll::Compose 是一个简洁的 Jekyll 博客辅助脚本, 可极大简化 Jekyll 博客创作和发布的过程, 减少我们的重复操作."
---

在使用 Jekyll 博客的过程中, 我们经常需要重复`建立新草稿/博客/页面-发布草稿`等操作. 其中按照 Jekyll 格式重命名文件, 迁移目录等步骤比较琐碎, 完全可以自动化. [jekyll-compose](https://github.com/jekyll/jekyll-compose) 就是这样一个插件. 它可极大简化 Jekyll 博客创作和发布的过程, 减少我们的重复操作. 这篇文章简单介绍其安装和使用.

## 安装 Jekyll::Compose 并激活

在 Jekyll 根目录的 `Gemfile` 加入:

    gem 'jekyll-compose', group: [:jekyll_plugins]

然后执行:

    $ bundle

## 使用 Jekyll::Compose 创作和发布博客

Jekyll::Compose 提供了简洁的几个命令: draft, post, publish, unpublish, page.

我们可以使用 `jekyll command` 或者 `bundle exec jekyll command`(官方更推荐这个方法) 调用这些命令.

使用 `$ jekyll help` 可以显示以下提示

```sh
  draft      # Creates a new draft post with the given NAME
  post       # Creates a new post with the given NAME
  publish    # Moves a draft into the _posts directory and sets the date
  unpublish  # Moves a post back into the _drafts directory
  page       # Creates a new page with the given NAME
```

实例:

新建页面:

    $ jekyll page "My New Page"
    $ bundle exec jekyll page "My New Page"

新建文章:

    $ jekyll post "My New Post"
    $ bundle exec jekyll post "My New Post"

新建草稿:

    $ jekyll draft "My new draft"
    $ bundle exec jekyll draft "My new draft"

发布草稿:

    $ jekyll publish _drafts/my-new-draft.md
    $ bundle exec jekyll publish _drafts/my-new-draft.md
    # 可在发布时使用 `--date` 指定文章日期
    $ jekyll publish _drafts/my-new-draft.md --date 2014-01-24
    $ bundle exec jekyll publish _drafts/my-new-draft.md --date 2014-01-24

取消发布(把发布的文章退回草稿区 `_draft/`):

    $ jekyll unpublish _posts/2014-01-24-my-new-draft.md
    $ bundle exec jekyll unpublish _posts/2014-01-24-my-new-draft.md

## 修改 Jekyll compose 增加页面模板

由于我的博客添加了 category 和 tag 等描述字段, 每篇文章都手动添加也很麻烦. 借鉴 [Allow additional front matter for Post by toshimaru jekyll/jekyll-compose](https://github.com/jekyll/jekyll-compose/pull/41/files), 我也对 Jekyll compose 的源码进行了[微小的修改](https://github.com/mofhu/jekyll-compose/commit/853caf1227f7c72ce33a833748ca49e5af29351a), 使这部分内容也可在 draft 和 publish 时自动填写.

只需修改 Jekyll compose 中的 post, draft 相关文件, 即可添加支持. 使用时在 Jekyll 目录下的 `_config.yml` 中添加需要填写的字段及其默认值即可.