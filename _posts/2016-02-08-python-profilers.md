---
layout: post
title: "Python 性能分析: cProfile"
date: 2016-02-08 11:00:00
categories: 性能分析 从零到一 Chaos-to-sth Python 编程 Programming
excerpt: Python 性能分析 cProfile pstats Chaos
---


这几天在写一个很小的俄罗斯方块 AI, 写出来的代码策略还不错, 就是运行速度不够快. 几次在策略端尝试优化遍历策略之后效果都很有限, 于是觉得这么盲目优化不是好办法, 应该先找到代码的短板, 再进一步优化对应部分, 如果 Python 搞不定, 就准备上 Cython 或者 C.

Google了一番, 发现 Python 3 的标准库里主要提供了两个模块 `[timeit](https://docs.python.org/3/library/timeit.html)` 和 `[cProfile/profile](https://docs.python.org/3/library/profile.html)`. 前者更适合测试代码片段, 后者则可以从一段代码中输出各个函数运行的次数, 时间等信息. 于是我简单使用了 `cProfile` 模块监控并优化我的 AI 代码性能. 下面就以这个实例介绍 cProfile 模块的基本使用方法:

## cProfile 模块上手

cProfile 模块的使用相当简单, 不加任何设置就可以很轻松的测试代码, 以测试 main 函数为例:

```python
import cProfile
cProfile.run('main()')
```

原始代码输出的结果如下(11 个新方块):

```python
BLANK_STATUS = [[0 for i in range(0, MAX_WIDTH)] for j in range(0, MAX_HEIGHT)]
from copy import deepcopy
under_block_matrix = deepcopy(BLANK_STATUS)
```

```
         5448067 function calls (4555747 primitive calls) in 26.357 seconds

   Ordered by: standard name

   ncalls  tottime  percall  cumtime  percall filename:lineno(function)
        1    0.000    0.000   26.356   26.356 <string>:1(<module>)
     4975    0.070    0.000    0.090    0.000 Nuwa.py:111(clear)
    19360    0.146    0.000    1.328    0.000 Nuwa.py:123(transpose)
     4964    0.477    0.000   24.074    0.005 Nuwa.py:127(score)
       11    0.085    0.008   26.341    2.395 Nuwa.py:15(ai_action)
    10178    0.591    0.000    0.591    0.000 Nuwa.py:225(height_status)
     5214    0.319    0.000    1.127    0.000 Nuwa.py:240(profile_status)
     2249    0.050    0.000    0.068    0.000 Nuwa.py:257(score_profile)
        1    0.001    0.001   26.356   26.356 Nuwa.py:341(main)
        1    0.000    0.000    0.000    0.000 Nuwa.py:347(<listcomp>)
        1    0.000    0.000    0.000    0.000 Nuwa.py:348(<listcomp>)
     5214    0.231    0.000    2.186    0.000 Nuwa.py:82(generate)
        1    0.000    0.000    0.000    0.000 _bootlocale.py:11(getpreferredencoding)
821678/3718    9.663    0.000   22.876    0.006 copy.py:137(deepcopy)
   743600    1.467    0.000    1.467    0.000 copy.py:192(_deepcopy_atomic)
78078/3718    4.224    0.000   22.757    0.006 copy.py:215(_deepcopy_list)
    78078    0.522    0.000    0.835    0.000 copy.py:256(_keep_alive)
       33    0.000    0.000    0.000    0.000 tetris.py:16(__init__)
     5214    0.038    0.000    0.038    0.000 tetris.py:192(clean_up)
        1    0.000    0.000    0.000    0.000 {built-in method _getdefaultlocale}
    19360    0.996    0.000    0.996    0.000 {built-in method array}
        1    0.000    0.000   26.357   26.357 {built-in method exec}
   981552    1.934    0.000    1.934    0.000 {built-in method id}
    58898    0.116    0.000    0.116    0.000 {built-in method len}
     3604    0.011    0.000    0.011    0.000 {built-in method max}
     2249    0.007    0.000    0.007    0.000 {built-in method min}
        1    0.000    0.000    0.000    0.000 {built-in method open}
       11    0.010    0.001    0.010    0.001 {built-in method print}
     5967    0.016    0.000    0.016    0.000 {built-in method sum}
   892320    1.817    0.000    1.817    0.000 {method 'append' of 'list' objects}
        1    0.000    0.000    0.000    0.000 {method 'disable' of '_lsprof.Profiler' objects}
  1643356    3.248    0.000    3.248    0.000 {method 'get' of 'dict' objects}
    42377    0.130    0.000    0.130    0.000 {method 'index' of 'list' objects}
      157    0.000    0.000    0.000    0.000 {method 'insert' of 'list' objects}
        1    0.000    0.000    0.000    0.000 {method 'read' of '_io.TextIOWrapper' objects}
    19360    0.186    0.000    0.186    0.000 {method 'tolist' of 'numpy.ndarray' objects}
```

代码运行的绝大部分时间都在 deepcopy 函数 (因此我前面的代码优化可以说一直没在点子上...). 在我的代码之中, deepcopy 用来初始化一个 10*20 的嵌套列表. (在实现这个功能的时候, 我还曾因为在开始用了浅拷贝(切片第一维), 由于第二维拷贝的是指向原始列表第二维的指针, 最终原始列表被反复修改, 导致代码出现了 bug. ) 由于每一次遍历新方块的位置都需要初始化一次列表, deepcopy 就导致了代码整体性能很低.

## 代码性能优化

下面就是尝试优化这两行代码了. 我想到的思路有两个: 
1. 改用一维列表初始化, 这样切片就可以拷贝, 避免使用 deepcopy; 再用 numpy 或者其它方法格式化一维列表, 希望可以比 deepcopy 快
2. 更进一步, 把初始化的常量对象改为 numpy.array, 然后每次初始化时候使用 `numpy.array.tolist()`, 自然就生成一个全新的嵌套列表.

```python
BLANK_STATUS_LIST = [0 for i in range(0, MAX_WIDTH * MAX_HEIGHT)]
under_block_matrix = (np.reshape(np.asarray(BLANK_STATUS_LIST[:]), (20,10))).tolist()
```

那么, 修改之后的效果如何呢?

```
         227995 function calls in 3.871 seconds

   Ordered by: standard name

   ncalls  tottime  percall  cumtime  percall filename:lineno(function)
        1    0.000    0.000    3.871    3.871 <string>:1(<module>)
     4975    0.074    0.000    0.094    0.000 Nuwa.py:111(clear)
    19360    0.148    0.000    1.360    0.000 Nuwa.py:123(transpose)
     4964    0.514    0.000    1.534    0.000 Nuwa.py:127(score)
       11    0.087    0.008    3.856    0.351 Nuwa.py:15(ai_action)
    10178    0.610    0.000    0.610    0.000 Nuwa.py:228(height_status)
     5214    0.328    0.000    1.153    0.000 Nuwa.py:243(profile_status)
     2249    0.051    0.000    0.069    0.000 Nuwa.py:260(score_profile)
        1    0.001    0.001    3.871    3.871 Nuwa.py:344(main)
        1    0.000    0.000    0.000    0.000 Nuwa.py:350(<listcomp>)
        1    0.000    0.000    0.000    0.000 Nuwa.py:351(<listcomp>)
     5214    0.238    0.000    2.239    0.000 Nuwa.py:82(generate)
        1    0.000    0.000    0.000    0.000 _bootlocale.py:11(getpreferredencoding)
     3718    0.021    0.000    0.039    0.000 fromnumeric.py:125(reshape)
     3718    0.021    0.000    0.197    0.000 numeric.py:394(asarray)
       33    0.000    0.000    0.000    0.000 tetris.py:17(__init__)
     5214    0.039    0.000    0.039    0.000 tetris.py:193(clean_up)
        1    0.000    0.000    0.000    0.000 {built-in method _getdefaultlocale}
    23078    1.197    0.000    1.197    0.000 {built-in method array}
        1    0.000    0.000    3.871    3.871 {built-in method exec}
    58898    0.117    0.000    0.117    0.000 {built-in method len}
     3604    0.011    0.000    0.011    0.000 {built-in method max}
     2249    0.007    0.000    0.007    0.000 {built-in method min}
        1    0.000    0.000    0.000    0.000 {built-in method open}
       11    0.009    0.001    0.009    0.001 {built-in method print}
     5967    0.016    0.000    0.016    0.000 {built-in method sum}
        1    0.000    0.000    0.000    0.000 {method 'disable' of '_lsprof.Profiler' objects}
    42377    0.130    0.000    0.130    0.000 {method 'index' of 'list' objects}
      157    0.000    0.000    0.000    0.000 {method 'insert' of 'list' objects}
        1    0.000    0.000    0.000    0.000 {method 'read' of '_io.TextIOWrapper' objects}
     3718    0.019    0.000    0.019    0.000 {method 'reshape' of 'numpy.ndarray' objects}
    23078    0.233    0.000    0.233    0.000 {method 'tolist' of 'numpy.ndarray' objects
```

代码速度一下变成了原来的 6.8 倍!


再看看第二个方法, 把切片的步骤也省掉会不会更快:

```python
BLANK_STATUS = [[0 for i in range(0, MAX_WIDTH)] for j in range(0, MAX_HEIGHT)]
under_block_matrix = np.asarray(BLANK_STATUS).tolist()

220559 function calls in 3.746 seconds
```

又有一些进步.

那么我们直接传递 numpy 对象, 只需要调用一次 tolist():

```python
BLANK_STATUS = [[0 for i in range(0, MAX_WIDTH)] for j in range(0, MAX_HEIGHT)]
BLANK_STATUS_ARRAY = np.asarray(BLANK_STATUS)
under_block_matrix = BLANK_STATUS_ARRAY.tolist()

213123 function calls in 3.507 seconds
```

这个速度对于目前的代码来说已经足够快了:)

## 使用 pstats 输出性能分析结果

前面 cProfile 输出的默认结果还有个小小的遗憾, 就是没有很好的格式, 如果能排序和筛选就更好了. [pstats]() 模块就可用多种方式排序并输出性能分析结果, 只需要添加几行代码: 

```python
    import cProfile
    cProfile.run('main()', 'restats')  # 把 cProfile 的结果输出

    import pstats
    p = pstats.Stats('restats')  # pstats 读取输出的结果
    p.sort_stats('cumulative').print_stats()  # 按照 cumtime 排序, print_stats(n) 则显示前 n 行
```

```
Mon Feb  8 12:09:45 2016    restats

         213123 function calls in 3.587 seconds

   Ordered by: cumulative time

   ncalls  tottime  percall  cumtime  percall filename:lineno(function)
        1    0.000    0.000    3.587    3.587 {built-in method exec}
        1    0.000    0.000    3.587    3.587 <string>:1(<module>)
        1    0.001    0.001    3.587    3.587 Nuwa.py:344(main)
       11    0.086    0.008    3.571    0.325 Nuwa.py:15(ai_action)
     5214    0.241    0.000    2.235    0.000 Nuwa.py:82(generate)
    19360    0.147    0.000    1.354    0.000 Nuwa.py:123(transpose)
     4964    0.475    0.000    1.256    0.000 Nuwa.py:127(score)
     5214    0.328    0.000    1.149    0.000 Nuwa.py:243(profile_status)
    19360    1.016    0.000    1.016    0.000 {built-in method array}
    10178    0.608    0.000    0.608    0.000 Nuwa.py:228(height_status)
    23078    0.232    0.000    0.232    0.000 {method 'tolist' of 'numpy.ndarray' objects}
    42377    0.130    0.000    0.130    0.000 {method 'index' of 'list' objects}
    58898    0.117    0.000    0.117    0.000 {built-in method len}
     4975    0.072    0.000    0.092    0.000 Nuwa.py:111(clear)
     2249    0.052    0.000    0.069    0.000 Nuwa.py:260(score_profile)
     5214    0.039    0.000    0.039    0.000 C:\developer\autoTetris\AI\Nuwa\tetris.py:193(clean_up)
     5967    0.016    0.000    0.016    0.000 {built-in method sum}
     3604    0.011    0.000    0.011    0.000 {built-in method max}
       11    0.009    0.001    0.009    0.001 {built-in method print}
     2249    0.007    0.000    0.007    0.000 {built-in method min}
      157    0.000    0.000    0.000    0.000 {method 'insert' of 'list' objects}
       33    0.000    0.000    0.000    0.000 C:\developer\autoTetris\AI\Nuwa\tetris.py:17(__init__)
        1    0.000    0.000    0.000    0.000 {built-in method open}
        1    0.000    0.000    0.000    0.000 {method 'read' of '_io.TextIOWrapper' objects}
        1    0.000    0.000    0.000    0.000 C:\Python34\lib\_bootlocale.py:11(getpreferredencoding)
        1    0.000    0.000    0.000    0.000 {built-in method _getdefaultlocale}
        1    0.000    0.000    0.000    0.000 Nuwa.py:351(<listcomp>)
        1    0.000    0.000    0.000    0.000 Nuwa.py:350(<listcomp>)
        1    0.000    0.000    0.000    0.000 {method 'disable' of '_lsprof.Profiler' objects
```

## 小结

基于Python 自带电池 ("batteries included") 的哲学, 在性能分析方面标准库中提供了方便易用的 timeit 和 cProfile/profile 模块, cProfile 模块可很方便地评价程序的性能, 并寻找最值得优化的短板.


## 参考资料

- [Python standard library: timeit](https://docs.python.org/3/library/timeit.html)
- [Python standard library: cProfile/profile](https://docs.python.org/3/library/profile.html)
  - 特别推荐 [27.4.2. Instant User’s Manual](https://docs.python.org/3/library/profile.html#instant-user-s-manual) 快速上手
- [Python Tutorial 第六堂（2）效能評測、PyCon Taiwan](http://www.codedata.com.tw/python/python-tutorial-the-6th-class-2-performance-pycon-taiwan/)
- [Python 做效能分析（Profiling）的兩個方式](http://littleq.logdown.com/posts/2014/03/20/python-do-efficiency-analysis-profiling-both-ways)