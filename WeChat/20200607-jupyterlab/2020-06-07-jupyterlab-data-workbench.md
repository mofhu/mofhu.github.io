---
layout: post
title: "JupyterLab: my new workbench for data processing of high-throughput proteomics"
date: 2020-06-07 08:00:00
categories: Programming
tags: JupyterLab interactive-programming Python
excerpt: 基于 JupyterLab 构建高通量蛋白质组学数据处理及可视化平台
---
* content
{:toc}


## a brief introduction of JupyterLab and interactive programming interface

In a word, JupyterLab is a next-generation web-based user interface for Project Jupyter. [^1] [简介视频(Youtube)](https://youtu.be/A5YyoCKxEOU)

也就是说, 如果你正在使用基于 Python 的科学数据分析与可视化生态系统 (numpy, scipy, pandas, sci-kit learn, matplotlib, seaborn, bokeh), 或者喜欢基于 Python 的交互式编程环境(IPython, Jupyter notebook). 那么你都可以考虑迁移到 JupyterLab.

目前我接触过的相关环境中, 最接近 JupyterLab 的项目应该是 Rstudio.

相对于 Jupyter notebook, JupyterLab 对于项目全局的支持更好, 很适合需要同时查看数据结构和编写开发代码的工作需求. 因此, 如果你正在大量使用 Jupyter notebook, 那就立刻迁移到 JupyterLab 吧!


## my current set up of python-based data pipeline and visualization

在我目前的工作中, 大量使用 Python 作为高通量数据的分析和可视化工具. 和几年前基于 Excel 的工作流相比, code-based 工作流的优势主要有以下几方面:

- 对大量同类型数据的处理方便 (batch-process), **很适合方法优化/方法比较的应用场合**; Excel 需要进行大量重复性的鼠标操作, 麻烦不说, 还极易出现错漏且很难发现.
- 代码片段便于复用和分享. Excel 需要变成宏才有更好的复用可能性, 但花费的时间也随之显著增加; 另外相当于另学一门脚本语言, 没有必要 (有这时间为啥不直接学 SQL...)
- 数据结构的变化, 迫使使用者认真思考自己需要做的事情. 往往能通过数据结构倒逼架构优化, 提高数据分析质量[^2]
- 以键盘操作为主, 避免了盯鼠标光标的劳累和错误(也可能是我 Excel 不够精到. 不过 Excel 调图似乎确实没办法靠键盘)

当然, Excel 胜在人人可用 (架 Python 相关环境, 特别是在 m$ 下面安装预编译的各种科学计算包有时还是很麻烦的, 不过有 conda 等之后, 也变得可以接受了.)

目前我采用的数据分析架构主要是基于 pandas + matplotlib(seaborn), interactive programming interface 刚刚迁移到 JupyterLab (配合 jupyterlab-vim 插件[^3]).

需要考虑的几方面:

- data i/o: `pandas` is good enough for database search result (csv at PSM level)
- data processing: `pandas` is good enough for filter, pivot, merge. for advanced data process (e.g. machine learning based method), consider `sci-kit learn`
- visualization: `matplotlib (color by seaborn)` is good for static plot. 很多场合(如方法对比和优化) 希望能有动态的图片(interactive plot), 可以通过 `bokeh` 实现.


## MassSpecDashboard as a high-throughput QC pipeline

我目前的工作中, 很日常但又需要花费不少时间的工作就是仪器的状态监控. 因此我也初步搭建了仪器质控的 data pipeline, 来比较迅速地提取关键的质控指标并绘图[^4]. 这部分工作目前以 [MassSpecDashboard](https://github.com/mofhu/MassSpecDashboard) 开源在 GitHub. 欢迎大家使用并提建议.

这个项目的愿景是: 通过稳定的工作流 (well-defined workflow), 将相对复杂的用于蛋白质鉴定的高分辨质谱仪的质控流程简化为每个用户都能清晰且准确理解的一张图片. 从而:

- 使不熟悉液质联用仪复杂结构的用户也能快速实现对系统状态的准确判断, 提高质谱仪的可用机时和高质量数据的产出比例
- 减少用于分析仪器质控数据的时间, 减少仪器操作人员的工作量
- 方便地进行仪器方法优化时的结果对比


---

[^1]: https://jupyterlab.readthedocs.io/en/stable/getting_started/overview.html
[^2]: 关于数据结构和架构的讨论, 可参考旧文[人人都是架构师](http://frank-the-obscure.me/2019/05/18/everyone-is-an-architect/)
[^3]: 目前(20200607), JupyterLab 2.0 和 jupyterlab-vim 有兼容性问题, 可暂时使用 https://github.com/axelfahy/jupyterlab-vim
[^4]: 由于人类视觉比数字敏感得多, 因此我个人强烈建议大家通过画图 (而非表格) 展示关键结果
