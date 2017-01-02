---
layout: post
title: opencv-python3-pyenv
date: 2017-01-02 22:00:54
categories: Programming
tags: 从零到一 opencv python
excerpt: "OpenCV 是一个常用的开源图形库, 这篇文章记录在 OS X 10.12 的 pyenv python 3 下安装 OpenCV 的方法."
---

* content
{:toc}

[OpenCV](http://opencv.org/) 是一个常用的开源图形库. 在我的一些小项目中希望通过识图读取屏幕中的信息(如游戏中的牌号图片).
而初期使用的基于 Python 的 [PyAutoGUI](https://pyautogui.readthedocs.io/en/latest/) 速度较慢: 截图用时在 1s 量级, 图片的比较也较慢, 达不到快速响应的需要.
因此希望通过基于 C++ 的 OpenCV 解决识图的问题.

用 OpenCV + Python3 + pyenv 进行了多次查询, 最后主要参考一篇日文博客的代码完成了安装[^1].

## Homebrew 安装 OpenCV3

Homebrew 提供了 OpenCV3 和 python3 的绑定安装脚本. 直接执行即可安装全部依赖, 并安装 OpenCV3.

`$ brew install homebrew/science/opencv3 --with-python3`

## 在 pyenv 的 python3 中调用 OpenCV

目前 pyenv 的 python3 中还不能使用 OpenCV. 这是由于 Homebrew 安装的 OpenCV 链接到的 python3 不是 pyenv 的版本.

下面我们把 OpenCV 生成的 .so 库 link 或 copy 到 pyenv 的 python3 目录下.

`/usr/local/Cellar/opencv3/3.2.0/lib/python3.5/site-packages/cv2.cpython-35m-darwin.so`

`~/.pyenv/versions/3.5.1/Python.framework/Versions/3.5/lib/python3.5/site-packages/cv2.cpython-35m-darwin.so`

## 测试 OpenCV + python3

```
$python
Python 3.5.1 (default, Apr  8 2016, 16:58:45)
[GCC 4.2.1 Compatible Apple LLVM 7.3.0 (clang-703.0.29)] on darwin
Type "help", "copyright", "credits" or "license" for more information.
>>> import cv2
>>> cv2.__version__
'3.2.0'
```

Done.

## 其它的安装方式: 源码安装 OpenCV3

StackOverflow 上也有源码安装 OpenCV3 的教程[^2].

大致思路是先下载源码, 再用 cmake 设置相关编译参数, 编译带 python3 支持的 OpenCV.

实践的时候编译 OpenCV3 不成功, 以后如果需要编译带 opencv-contrib 的版本的时候再研究吧.

---

References:

[^1]: [MacOS Sierra(10.12) に Pyenv で Python3 + openCV3 をインストールする - Qiita](http://qiita.com/masaori/items/0c78fcd58a6c6bf4f655)
[^2]: [opencv3.0 - Compiling OpenCV3 with pyenv using python 3.5.0 on OSX - Stack Overflow](http://stackoverflow.com/questions/33250375/compiling-opencv3-with-pyenv-using-python-3-5-0-on-osx)
