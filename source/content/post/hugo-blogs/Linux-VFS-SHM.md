---
title: "Linux 虚拟文件系统（虚拟内存文件系统）以及共享内存原理"
date: 2024-11-30T19:35:39Z
draft: false
---

最近看了[《Understanding the Linux® Virtual Memory Manager》](https://pdos.csail.mit.edu/~sbw/links/gorman_book.pdf)里面的第十二章 **SHARED MEMORY VIRTUAL FILESYSTEM** ，对文件系统以及内存文件管理有了更加深入的了解，下面是看了这一章节之后对其中一些概念的理解以及拓展，要是想了解这一章，建议读原文，配合这篇博客辅助理解

### Linux 哲学
- 在 Linux 里面，一切皆文件，所有的东西都可以看作一个文件，而凡是文件，都应该支持`POSIX`文件操作（比如 `read()` ，`write()` ，`open()`）
- 每一块内存对象，都可以被看作一个文件，一旦赋予这块内存对象相对应的文件描述，就可以使用像使用普通文件那样子操作内存对象
- 而这也正是 `VFS`（虚拟文件系统 `virtual file system` ，包括内存文件系统以及共享内存管理系统）的设计理念以及实现方向

### VMA（virtual memory area）
- Linux 内核用`vm_area_struct`结构体描述某一段连续的虚拟内存区域VMA（virtual memory area），每个虚拟内存区域 `VMA` 都有自己的`vm_area_struct` 结构体
- 内存描述符 `mm_struct` 指向进程的整个地址空间，`vm_area_struct` 只是指向了虚拟空间的一段
  ![](https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/v2-426557bb4eb1e7044bd649483942c2ad_r.jpg)
- `vm_area_struct` 是由双向链表链接起来的，它们是按照虚拟地址降序排序的，每个这样的结构都对应描述一个地址空间范围
- 为了快速根据地址找到对应的 `VMA`，内核对其建立了红黑树索引，红黑树的每个叶子结点就是一个VMA区域，引入红黑树的好处是可以提高查找VMA的效率（即便VMA的数量翻倍，VMA的查找次数也只增加一次）
  ![](https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/v2-82dec5bde95009f934d3978c15412626_1440w.jpg)
- 之所以这样分隔是因为每个虚拟区间可能来源不同，有的可能来自可执行映像，有的可能来自共享库，而有的可能是动态内存分配的内存区，所以对于每个由 `vm_area_struct` 结构所描述的区间的处理操作和它前后范围的处理操作不同，因此 linux 把虚拟内存分割管理，并利用了虚拟内存处理例程 `vm_ops` 来抽象对不同来源虚拟内存的处理方法
- 不同的虚拟区间其处理操作可能不同，`linux` 在这里利用了面向对象的思想，即把一个虚拟区间看成是一个对象，用 `vm_area_struct` 描述这个对象的属性，其中的 `vm_operation` 结构描述了在这个对象上的操作
  ![](https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/v2-ce0aa70282615def188d51d5ee745e14_1440w.jpg)
  

### 文件系统、文件类型、`page` 的划分
- 为了方便理解，在此将文件系统划分为内存文件系统（虚拟文件系统）与硬盘文件系统（物理文件系统），在书的这一章节里面，也将广义上的文件分为 `virtual file` 和 `physics file`
- 在书的这一章节里面，将内存页面 `page` 划分为 `anonymous pages` （没有物理文件支持的内存页面）与 `pages backed by a file`（由物理文件映射到内存的某些 `pages`）

### 硬盘文件系统
