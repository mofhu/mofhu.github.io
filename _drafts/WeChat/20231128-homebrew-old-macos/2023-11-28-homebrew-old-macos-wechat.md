---
layout: post
title: "一台 MacBook Pro 的七年之痒"
date: 2023-11-28 6:00:00
categories: Programming
tags: Developer
excerpt: "当硬件和软件被社区放弃, 你就只能自救. 升级还是不升级, 这是一个问题"
---

* content
{:toc}


(配 MBP keyboard fig - wechat only)


最近因为想要做一点 macOS 开发的尝试, 我试着在自己已使用 7 年有余的 MBP 15 (MacBook Pro Retina, 13-inch, Early 2015, aka model A1502) 上部署比较新的开发工具, 这篇短文简单记录踩到的坑和部分处理方式, 供有需要的朋友参考:)

以下内容适合 macOS Catalina 10.15.7, 2.7 GHz Dual-Core Intel Core i5, 16GB memory.

## xcode

xcode 安装唯一的坑是需要极多的硬盘空间(50+GB) 对于只有 256GB SSD 的我来说也是一个挑战.

经过一顿倒腾, 还是实现了 10.15.7 下 xcode last stable version (12.4, 12D4e) 的安装.

使用 app store 安装最后支持版本的老办法: 

1. 先更新新版; 
2. app store 提示不支持该版本OS但可安装最后支持的版本; 
3. 下载更新.

## homebrew

这个坑就大了... homebrew 已经放弃支持 10.15.7. 因此一切都要靠自己.

由于 blog 部署在 github pages (jekyll) 上, github 自动更新相关依赖(至少包括强制安全更新), 但我本地 jekyll/ruby 环境已经很久没动过. ruby 就是目前的第一个大坑.

由于 brew 自动安装一般是直接下载源码 build, 在目前网络环境下有两个主要问题:

1. 下载源码的网站经常无法连接
2. build 时候出现各种大问题, 如缺乏依赖(但 makefile 配置有误, 未发现问题) [^1]

更糟糕的是, 这两个问题可能会`互相耦合`(比如 make 到一半的时候突然又开始下载包, 这是坑爹啊...)

几个可以尝试的策略:

1. 改用手机热点访问互联网. 个人和一位朋友感觉这个方案有时候效果不错, 至少中国移动手机热点常有效果.
2. 魔改 homebrew, 包括而不限于以下:

回滚仓库(`git reset --hard old_commit`) commit 在 homebrew repo 自行寻找

```
export HOMEBREW_NO_INSTALL_FROM_API=1
export HOMEBREW_NO_INSTALL_CLEANUP=TRUE
export HOMEBREW_NO_INSTALLED_DEPENDENTS_CHECK=TRUE
```

3. 直接绕过 homebrew 安装 (e.g. 对于 python, build 时间长且易出错, 基本款 python 完全可以直接官网下载 dmg...)

另外我还需要吐槽 homebrew 很多莫名其妙的依赖, 如 ruby 2.7 为什么会依赖 python 3.12? 这个先后顺序应该是前者先出来好几年吧?!

最终解决方案(jekyll): 放弃 homebrew ruby; 改为 homebrew 升级了 `rbenv`, 一步搞定. 至少又可以本地部署 jekyll 测试了:)

also resolved pyenv updating. 开发环境也是时候从 3.8 迁移到 3.11-3.12 了...

## 最后的思考

所以, 我最后又来到了一个关键问题, 现在是否是升级 OS 的时间点呢? 如果升级了 OS, homebrew should be able to handle all this for me...

看到一个 [Youtube 视频](https://www.youtube.com/watch?v=-ZlZNI8n1AU&t) 似乎升级 12.7 (last compatible OS of my MBP...) 也没什么问题...

那么试着列举一下优点和缺点(of 12.7):

+ 开发环境更现代 (esp. xcode)
+ OS 和 iOS 更统一
+ 软件还在支持 (e.g. homebrew)
- 经典的 macOS `逐代卡`: 新版本 OS 在老机型(特别是在内存等硬件指标不充足时)上实际不太可用 [^2].
- OS 总会有一些细节微调不满意(如右上角的小图标栏空间变得更舒展, 导致可用图标数显著下降)
- 开发环境需要大量微调才能使用, 相当耗时, 且遇到问题难以解决
    - py/pyenv/jupyter 环境往往有特殊包配置和 framework 重写. 当然, 说不定迁移到 conda 等一步到位的方案更合适


所以, 这个 `收益-损失` 方程更有意思的一点是: `如果我们把收益-损失作为关于时间的函数画出来, 随着时间的推移, 二者的关系也会发生变化`:

- 初期, 毫无升级必要
- 中期, 升级需求逐渐增加
- 后期, 维持版本收益下降(因为需要维护更多不支持的包); 升级收益增加, 但也可能受到新系统速度的拖累
- 大后期, 收益和损失同时下降. 更需要考虑更换电脑/OS(上述 youtube 视频中, 有网友甚至提到了 win11 会快如闪电...)

最终的希望是, 委托同款同配置 MBP 的朋友帮忙升过去测试一下. 如果(至少)系统不卡, 则考虑找一个相对有空闲的日子, 焚香沐浴, 进行升级:) [^3]


## 吐槽苹果时间

因此, 近几年 mac 的起售价格非常具有欺骗性.
哪怕只是浏览网页(除非你只用命令行 SSH 开发-那完全可以用个老款 mac 或者用廉价台式机), 8G 内存对于现在的用户来说几乎没有任何可行性(不信你随便打开一个 4k youtube/bilibili 视频试试).

个人拟考虑的配置(考虑到希望使用 5-10 年)是内存尽量拉满[^4], CPU 则可根据实际需求 (e.g. 是否经常做计算密集型任务) 选择. 当然, 这样的 mac 售价就很惊人了...

但是, `多花钱买一台能用的机器`,远比`少花钱买一台不能用的机器`要值, 如果它是你的生产工具(请留意! 思考工具也是生产工具, 思考是最重要的生产活动之一), 那么绝对属于`该花花-该省省`之中, 最偏向于`该花花`一端的产品.

可能唯二限制我更换的, 一是口袋中的余钱; 二是 A1502 手感出众的薄膜键盘.

---

footnotes:

[^1]: 如果叠加上你的电脑性能不佳, build 一次十多分钟, 更新软件就变得更加闹心.
[^2]: 因此, 近几年 mac 的起售价格非常具有欺骗性, 详见下文.
[^3]: 当然, 一定不要忘了做好 timemachine backup. 最近家里配了一台 QNAP NAS, 实现了本地冷热双备, 这方面甚是满意.
[^4]: 对于 macbook air 和 macbook pro都是! 因为如果你买 pro, 一定是有更多使用需求的, 32G 恐怕真的不够. 拉满不失为一个不错的选择, 另一个选项是规划好大概的更换周期(对于生产工具更换周期和折旧当然非常重要), (如果合适)到期换新.
