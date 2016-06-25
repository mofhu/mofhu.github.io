---
layout: post
title: "GitBook auto summary"
date: 2015-07-20 23:21:54
categories: Programming
tags: GitBook Python
excerpt: "GitBook auto summary 是我编写的一个小脚本, 可以根据现有的目录自动生成 GitBook 的 Summary.md 文件."
---

* content
{:toc}

[**GitBook auto summary**](http://frank-the-obscure.me/GitBook-auto-summary/)

Automatically update SUMMARY.md of a [GitBook](https://www.gitbook.com/) repository.

自动输出 [GitBook](https://www.gitbook.com/) 仓库的 SUMMARY.md 文件.

## Usage

1. `$ python gitbook-auto-summary.py`
    - use argument `-o` to overwrite SUMMARY.md without checking.
2. input directory (it should be the *root* directory of a GitBook repo)
3. the auto summary file `SUMMARY.md` will be created in the same directory.

Tested with Python 3.4.3 in Windows 7 and OS 10.10

## Examples


folder tree:

    .
    ├── README.md  
    ├── SUMMARY.md  
    ├── md  
    │   └── SUMMARY.md  
    ├── nomd  
    └── os-and-os-path.md  


output SUMMARY.md:


    # Summary
    - [os-and-os-path](./os-and-os-path.md)
    - [README](./README.md)
    - md
      - [SUMMARY](md/SUMMARY.md)


