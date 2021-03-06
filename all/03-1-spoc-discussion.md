# lec5: SPOC思考题

##**提前准备**
（请在上课前完成）

- 完成lec５的视频学习和提交对应的在线练习
- git pull ucore_os_lab, v9_cpu, os_course_spoc_exercises in github repos。这样可以在本机上完成课堂练习。
- 理解连续内存动态分配算法的实现（主要自学和网上查找）

NOTICE
- 有"hard"标记的题有一定难度，鼓励实现。
- 有"easy"标记的题很容易实现，鼓励实现。
- 有"midd"标记的题是一般水平，鼓励实现。


## 思考题
---

## “连续内存分配”与视频相关的课堂练习

### 5.1 计算机体系结构和内存层次

1.操作系统中存储管理的目标是什么？

- 在写程序时，每个程序有一个相对独立的逻辑地址空间，存储管理负责把逻辑地址空间转变为逻辑地址空间。需要满足，每个进程使用的物理空间相对独立，起到保护的作用，但又有可以共享的数据，即共享和保护的统一。同时需要一个虚拟地址空间，虚拟空间可访问的地址范围很大。

### 5.2 地址空间和地址生成
1.描述编译、汇编、链接和加载的过程是什么？

- qi：得到汇编语言的代码，但不会有具体地址，对以jmp地址，以符号代替具体地址
- 汇编：替换符号为模块内的具体地址，但没有考虑模块间调用关系，因此此处地址仅是模块内地址
- 连接：考虑模块间的互相调用关系，将地址转换为程序整体内的地址
- 加载：再根据程序和系统内部地址分配，对所有程序内地址进行重定位，得到最终逻辑地址

2.动态链接如何使用？尝试在Linux平台上使用LD_DEBUG查看一个C语言Hello world的启动流程。  (optional)



### 5.3 连续内存分配
1.什么是内碎片、外碎片？

- 内碎片是分配单元内部的空闲空间，即被分配但没用被用完的那部分空间
- 外碎片是分配单元之间的空闲空间

2.最先匹配会越用越慢吗？请说明理由（可来源于猜想或具体的实验）？

- 针对于某种范围的数据会越用越慢，例如，持续地要求分配一个固定大小的块，这个块的大小会占用一个空闲块，但不会用尽，且剩余空间不足够再分配一个块，如此分配，空闲块数量不会变，但小块越来越多，使得后面需要搜更多才能搜到可用块，使得效率下降。

3.最差匹配的外碎片会比最优适配算法少吗？请说明理由（可来源于猜想或具体的实验）？
4.理解0:最优匹配，1:最差匹配，2:最先匹配，3:buddy systemm算法中分区释放后的合并处理过程？ (optional)


### 5.4 碎片整理
1.对换和紧凑都是碎片整理技术，它们的主要区别是什么？为什么在早期的操作系统中采用对换技术？  

- 紧凑只是整理和合并内存中的碎片，并没有改变内存中的碎片总量；对换则是把等待中的进程移到外存中，增加了内存中的剩余空间。
- 早期操作系统的内存空间很小，要实现多进程交替运行，只能通过对换算法，不断将处于等待状态的进程换到内存中，使有紧凑是无法空出足够的内存空间的。且对换处理更为简单。

2.一个处于等待状态的进程被对换到外存（对换等待状态）后，等待事件出现了。操作系统需要如何响应？

- 将最新处于等待状态，在内存中的进程与对换区中的进程进行对换。

### 5.5 伙伴系统
1.伙伴系统的空闲块如何组织？

- 按大小和起始地址组成二维数组

2.伙伴系统的内存分配流程？伙伴系统的内存回收流程？

- 分配：设分配大小为k，寻找最小的可用空闲块，若空闲块大小>=2k，则将空闲块一分为二直分到上式不成立为止
- 回收：先回收，再检验是否能与相邻的块合并，要求大小相同为2^i，且低地址空闲块的起始地址为2^(i+1)的整数倍

## 课堂实践

观察最先匹配、最佳匹配和最差匹配这几种动态分区分配算法的工作过程，并选择一个例子进行分析分析整个工作过程中的分配和释放操作对维护数据结构的影响和原因。

  * [算法演示脚本的使用说明](https://github.com/chyyuu/os_tutorial_lab/blob/master/ostep/ostep3-malloc.md)
  * [算法演示脚本](https://github.com/chyyuu/os_tutorial_lab/blob/master/ostep/ostep3-malloc.py)

例如：
```
python ./ostep3-malloc.py -S 100 -b 1000 -H 4 -a 4 -l ADDRSORT -p BEST -n 5 -c
python ./ostep3-malloc.py -S 100 -b 1000 -H 4 -a 4 -l ADDRSORT -p FIRST -n 5 -c
python ./ostep3-malloc.py -S 100 -b 1000 -H 4 -a 4 -l ADDRSORT -p WORST -n 5 -c
```

### 扩展思考题 (optional)

1. 请参考xv6（umalloc.c），ucore lab2代码，选择四种（0:最优匹配，1:最差匹配，2:最先匹配，3:buddy systemm）分配算法中的一种或多种，在Linux应用程序/库层面，用C、C++或python来实现malloc/free，给出你的设计思路，并给出可以在Linux上运行的malloc/free实现和测试用例。


2. 阅读[slab分配算法](http://en.wikipedia.org/wiki/Slab_allocation)，尝试在应用程序中实现slab分配算法，给出设计方案和测试用例。
