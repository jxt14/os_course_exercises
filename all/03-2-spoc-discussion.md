# lec6 SPOC思考题


NOTICE
- 有"w3l2"标记的题是助教要提交到学堂在线上的。
- 有"w3l2"和"spoc"标记的题是要求拿清华学分的同学要在实体课上完成，并按时提交到学生对应的git repo上。
- 有"hard"标记的题有一定难度，鼓励实现。
- 有"easy"标记的题很容易实现，鼓励实现。
- 有"midd"标记的题是一般水平，鼓励实现。

## 与视频相关思考题

### 6.1	非连续内存分配的需求背景
 1.为什么要设计非连续内存分配机制？

- 连续分配时分配的内存必须连续，会产生碎片，且无法处理动态修改，使得内在利用率较低。

 2.非连续内存分配中内存分块大小有哪些可能的选择？大小与大小是否可变?

- 段式/页式

 3.为什么在大块时要设计大小可变，而在小块时要设计成固定大小？小块时的固定大小可以提供多种选择吗？

### 6.2	段式存储管理
 1.什么是段、段基址和段内偏移？

- 段：仿问方式和存储数据等属性大致相同的一段连续地址空间。
- 基址/偏移：用于对段中任意处物理地址的定位（段起始物理地址/相对起始地址的偏移）

 2.段式存储管理机制的地址转换流程是什么？为什么在段式存储管理中，各段的存储位置可以不连续？这种做法有什么好处和麻烦？

- 逻辑空间划分为段号和段内偏移，用段号在操作系统的段表中查询相应的段描述符，其中存放有段基址和段长度；再用段内偏移和段长度比较看是否越界，不越界则用段基址加上段内偏移完成转换。
- 不连续：因为程序逻辑中并不会用一个段的基址去访问另一个段。
- 好处：实现了一种非连续内存分配，提高了内存利用率，方便管理。
- 麻烦：需要进行地址转换，且多了一个地址空间。


### 6.3	页式存储管理
 1.什么是页（page）、帧（frame）、页表（page table）、存储管理单元（MMU）、快表（TLB, Translation Lookaside Buffer）和高速缓存（cache）？
 
 2.页式存储管理机制的地址转换流程是什么？为什么在页式存储管理中，各页的存储位置可以不连续？这种做法有什么好处和麻烦？

- 逻辑地址分为页号和页内偏移，用页号在页表中查询其对应的物理帧号，然后乘上帧大小加上偏移即可。
- 不连续：各页的页号不相同，亦有不同的页表项，程序仅通过页号转换寻址，因此不会用一个页的页号寻另一个页的址。
- 麻烦：页表可能很大；取址需要访问两次内存。

### 6.4	页表概述
 1.每个页表项有些什么内容？有哪些标志位？它们起什么作用？

- 页表项有：帧号和几个标志位
- 标志位分别是：存在位，修改位，引用位。用于在后续操作对页面的处理中提供相关信息。

 2.页表大小受哪些因素影响？

 - 逻辑地址的位数以及页面的大小。


### 6.5	快表和多级页表
 1.快表（TLB）与高速缓存（cache）有什么不同？

 2.为什么快表中查找物理地址的速度非常快？它是如何实现的？为什么它的的容量很小？
 
- 因为快表在CPU中，不需访问内存，所以速度快。将最近的访存页表项存入CPU中形成快表，快表采用关联存储实现。容量小也是因为在CPU中。

 3.什么是多级页表？多级页表中的地址转换流程是什么？多级页表有什么好处和麻烦？

- 将页号再分为多级，对应的页表形成树结构，前一级页表项中存储后一级页表项的起始地址。
- 好处：大大减少了页表的大小。
- 坏表：访存次数增加，在所有地址都需要被访问时（这种情况出现的机率很小），所占空间反而更大。

### 6.6	反置页表
 1.页寄存器机制的地址转换流程是什么？

- 对逻辑地址进行哈希映射，若有冲突则遍历链表，找到该逻辑地址其对应的页寄存器，得到物理地址。

 2.反置页表机制的地址转换流程是什么？

- 与页寄存器大致相同，仅仅是进程号也作为哈希的一部分。

 3.反置页表项有些什么内容？

- 进程号，逻辑面号以及指向链表下一项的指针

### 6.7	段页式存储管理
 1.段页式存储管理机制的地址转换流程是什么？这种做法有什么好处和麻烦？

- 先根据段号查找段表，段表指向页表，再根据页号查找页表，得到物理地址，最后加上偏移量得最终地址。

 2 如何实现基于段式存储管理的内存共享？
 
- 将不同段映射到同一个物理空间

 3.如何实现基于页式存储管理的内存共享？

- 多个页表项映射到同一个物理空间。


## 个人思考题
（1） (w3l2) 请简要分析64bit CPU体系结构下的分页机制是如何实现的



## 小组思考题
（1）(spoc) 某系统使用请求分页存储管理，若页在内存中，满足一个内存请求需要150ns (10^-9s)。若缺页率是10%，为使有效访问时间达到0.5us(10^-6s),求不在内存的页面的平均访问时间。请给出计算步骤。



（2）(spoc) 有一台假想的计算机，页大小（page size）为32 Bytes，支持32KB的虚拟地址空间（virtual address space）,有4KB的物理内存空间（physical memory），采用二级页表，一个页目录项（page directory entry ，PDE）大小为1 Byte,一个页表项（page-table entries
PTEs）大小为1 Byte，1个页目录表大小为32 Bytes，1个页表大小为32 Bytes。页目录基址寄存器（page directory base register，PDBR）保存了页目录表的物理地址（按页对齐）。

PTE格式（8 bit） :
```
  VALID | PFN6 ... PFN0
```
PDE格式（8 bit） :
```
  VALID | PT6 ... PT0
```
其
```
VALID==1表示，表示映射存在；VALID==0表示，表示映射不存在。
PFN6..0:页帧号
PT6..0:页表的物理基址>>5
```
在[物理内存模拟数据文件](./03-2-spoc-testdata.md)中，给出了4KB物理内存空间的值，请回答下列虚地址是否有合法对应的物理内存，请给出对应的pde index, pde contents, pte index, pte contents。
```
1) Virtual Address 6c74
   Virtual Address 6b22
2) Virtual Address 03df
   Virtual Address 69dc
3) Virtual Address 317a
   Virtual Address 4546
4) Virtual Address 2c03
   Virtual Address 7fd7
5) Virtual Address 390e
   Virtual Address 748b
```

比如答案可以如下表示： (注意：下面的结果是错的，你需要关注的是如何表示)
```
Virtual Address 7570:
  --> pde index:0x1d  pde contents:(valid 1, pfn 0x33)
    --> pte index:0xb  pte contents:(valid 0, pfn 0x7f)
      --> Fault (page table entry not valid)

Virtual Address 21e1:
  --> pde index:0x8  pde contents:(valid 0, pfn 0x7f)
      --> Fault (page directory entry not valid)

Virtual Address 7268:
  --> pde index:0x1c  pde contents:(valid 1, pfn 0x5e)
    --> pte index:0x13  pte contents:(valid 1, pfn 0x65)
      --> Translates to Physical Address 0xca8 --> Value: 16
```

[链接](https://piazza.com/class/i5j09fnsl7k5x0?cid=664)有上面链接的参考答案。请比较你的结果与参考答案是否一致。如果不一致，请说明原因。

（3）请基于你对原理课二级页表的理解，并参考Lab2建页表的过程，设计一个应用程序（可基于python、ruby、C、C++、LISP、JavaScript等）可模拟实现(2)题中描述的抽象OS，可正确完成二级页表转换。

[链接](https://piazza.com/class/i5j09fnsl7k5x0?cid=664)有上面链接的参考答案。请比较你的结果与参考答案是否一致。如果不一致，提交你的实现，并说明区别。

（4）假设你有一台支持[反置页表](http://en.wikipedia.org/wiki/Page_table#Inverted_page_table)的机器，请问你如何设计操作系统支持这种类型计算机？请给出设计方案。

 (5)[X86的页面结构](http://os.cs.tsinghua.edu.cn/oscourse/OS2019spring/lecture06)
---

## 扩展思考题

阅读64bit IBM Powerpc CPU架构是如何实现[反置页表](http://en.wikipedia.org/wiki/Page_table#Inverted_page_table)，给出分析报告。


## interactive　understand VM

[Virtual Memory with 256 Bytes of RAM](http://blog.robertelder.org/virtual-memory-with-256-bytes-of-ram/)：这是一个只有256字节内存的一个极小计算机系统。按作者的[特征描述](https://github.com/RobertElderSoftware/recc#what-can-this-project-do)，它具备如下的功能。
 - CPU的实现代码不多于500行；
 - 支持14条指令、进程切换、虚拟存储和中断；
 - 用C实现了一个小的操作系统微内核可以在这个CPU上正常运行；
 - 实现了一个ANSI C89编译器，可生成在该CPU上运行代码；
 - 该编译器支持链接功能；
 - 用C89, Python, Java, Javascript这4种语言实现了该CPU的模拟器；
 - 支持交叉编译；
 - 所有这些只依赖标准C库。
 
针对op-cpu的特征描述，请同学们通过代码阅读和执行对自己有兴趣的部分进行分析，给出你的分析结果和评价。
