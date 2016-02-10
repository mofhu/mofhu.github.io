---
layout: post
title: "Vim 入门小记"
date: 2015-12-24 22:39:54
categories: Vim 从零到一 Chaos-to-sth 编程 Programming
excerpt: 爱折腾的我玩起了 Vim.
---

* content
{:toc}


## Why vim?

其实, 最简单的原因, 是没钱买 Sublime Text, 看交钱提醒看烦了...

追溯到大一时候的计概课, 曾玩过 Emacs (那是遥远的2007年了), 不过 vi/vim 一直没试过. 所以, 奇怪的下了决定开始折腾一番:)

## 入门小记

- 三种模式: 与 Emacs 的各种风骚快捷键不同, vi 通过不同的模式进行操作.
  - normal mode: 操作 各种模式下 esc 均可进入
  - insert mode: 输入, normal mode 下 i R a A
  - command mode: 复杂命令, 如保存, 退出等.
- 光标移动: normal mode 下
  - `hjkl` 分别是四个基本光标移动操作
  - `c-f`, `c-b` 上下翻页
  - `0`, `$` 分别是移动到本行开头和最后
  - `G`, `gg`, #line g
- 打开, 保存与退出
  - 在终端中, `vi [filename]`
  - 在vi 中, `:e`
  - 保存: `:w`
  - 退出不保存: `:q`
  - 退出并保存: `:x` 或 `:wq` (二者的区别是:x 只在文件变化时更新修改时间, :wq 则总会更新修改时间)
  - 退出并放弃修改: `:q!`

## 下一步

总算可以开始用了(终于不像以前误入 vi 都不会退出了), 下面, 正如 vimtutor 所言, 只有在用中学才是科学的, 那么以后 Markdown (比如这篇)也要试着用 vi 来写喽 XD

另外, 命令不是用来背的, 在 Git cheat sheet 背面手写了 Vim cheat sheet 作为备忘录 :)

---

References

- vim tutor: `vimtutor` in terminal
- [鸟哥的 vim 介绍](http://linux.vbird.org/linux_basic/0310vi.php): cheat sheet 部分主要记录了鸟哥图中的命令
- [spf13-vim @ GitHub](https://github.com/spf13/spf13-vim)
  - 这里是一个 vim 的配置版本, 暂时先用着, 毕竟自己从零开始配置 .vimrc 还是有些太折腾了(重点是其实不会折腾)
