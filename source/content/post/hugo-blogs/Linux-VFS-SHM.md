---
title: "Linux 虚拟文件系统（虚拟内存文件系统）以及共享内存原理"
date: 2024-11-30T19:35:39Z
draft: false
---

最近看了[《Understanding the Linux® Virtual Memory Manager》](https://pdos.csail.mit.edu/~sbw/links/gorman_book.pdf)里面的第十二章 **SHARED MEMORY VIRTUAL FILESYSTEM** ，对文件系统以及内存文件管理有了更加深入的了解，下面是看了这一章节之后对其中一些概念的理解以及拓展，要是想了解这一章，建议读原文，配合这篇博客辅助理解

## Linux 哲学
- 在 Linux 里面，一切皆文件，所有的东西都可以看作一个文件，而凡是文件，都应该支持`POSIX`文件操作（比如 `read()` ，`write()` ，`open()`）
- 每一块内存对象，都可以被看作一个文件，一旦赋予这块内存对象相对应的文件描述，就可以使用像使用普通文件那样子操作内存对象
- 而这也正是 `VFS`（虚拟文件系统 `virtual file system` ，包括内存文件系统以及共享内存管理系统）的设计理念以及实现方向

## VMA（virtual memory area）
- Linux 内核用`vm_area_struct`结构体描述某一段连续的虚拟内存区域VMA（virtual memory area），每个虚拟内存区域 `VMA` 都有自己的`vm_area_struct` 结构体
- 内存描述符 `mm_struct` 指向进程的整个地址空间，`vm_area_struct` 只是指向了虚拟空间的一段，这块虚拟内存区域VMA的地址范围为 `[vm_start, vm_end)` ，左开右闭
  <img src="https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/v2-426557bb4eb1e7044bd649483942c2ad_r.jpg" width=60% align="middle">
- `vm_area_struct` 是由双向链表链接起来的，它们是按照虚拟地址降序排序的，每个这样的结构都对应描述一个地址空间范围
- 为了快速根据地址找到对应的 `VMA`，内核对其建立了红黑树索引，红黑树的每个叶子结点就是一个VMA区域，引入红黑树的好处是可以提高查找VMA的效率（即便VMA的数量翻倍，VMA的查找次数也只增加一次）
  ![](https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/v2-82dec5bde95009f934d3978c15412626_1440w.jpg)
- 之所以这样分隔是因为每个虚拟区间可能来源不同，有的可能来自可执行映像，有的可能来自共享库，而有的可能是动态内存分配的内存区，所以对于每个由 `vm_area_struct` 结构所描述的区间的处理操作和它前后范围的处理操作不同，因此 linux 把虚拟内存分割管理，并利用了虚拟内存处理例程 `vm_ops` 来抽象对不同来源虚拟内存的处理方法
- 不同的虚拟区间其处理操作可能不同，`linux` 在这里利用了面向对象的思想，即把一个虚拟区间看成是一个对象，用 `vm_area_struct` 描述这个对象的属性，其中的 `vm_operation` 结构描述了在这个对象上的操作
  ![](https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/v2-ce0aa70282615def188d51d5ee745e14_1440w.jpg)
- 虚拟内存空间管理概括图
  ![](https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/v2-410e447557fe925077f0580463875666_r.jpg)

  ```c++
  # https://elixir.bootlin.com/linux/v2.6.0/source/include/linux/mm.h#L51
  struct vm_area_struct {
  	struct mm_struct * vm_mm;	/* The address space we belong to. */
  	unsigned long vm_start;		/* Our start address within vm_mm. */
  	unsigned long vm_end;		/* The first byte after our end address
  					   within vm_mm. */
  
  	/* linked list of VM areas per task, sorted by address */
  	struct vm_area_struct *vm_next;
  
  	pgprot_t vm_page_prot;		/* Access permissions of this VMA. */
  	unsigned long vm_flags;		/* Flags, listed below. */
  
  	struct rb_node vm_rb;
  
  	/*
  	 * For areas with an address space and backing store,
  	 * one of the address_space->i_mmap{,shared} lists,
  	 * for shm areas, the list of attaches, otherwise unused.
  	 */
  	struct list_head shared;
  
  	/* Function pointers to deal with this struct. */
  	struct vm_operations_struct * vm_ops;
  
  	/* Information about our backing store: */
  	unsigned long vm_pgoff;		/* Offset (within vm_file) in PAGE_SIZE
  					   units, *not* PAGE_CACHE_SIZE */
  	struct file * vm_file;		/* File we map to (can be NULL). */
  	void * vm_private_data;		/* was vm_pte (shared mem) */
  };
  ```

## `struct address_space`
- 看 `linux` 内核很容易被 `struct address_space` 这个结构迷惑，它是代表某个地址空间吗？实际上不是的，它是用于管理文件 `struct inode` 映射到内存的页面 `struct page` 的，其实就是每个 `file` 都有这么一个结构，将文件系统中这个 `file` 对应的数据与这个 `file` 对应的内存绑定到一起
- 与之对应，`address_space_operations` 就是用来操作该文件映射到内存的页面，比如把内存中的修改写回文件、从文件中读入数据到页面缓冲等
- 一个具体的文件在打开后，内核会在内存中为之建立一个 `struct inode` 结构（该 `inode` 结构也会在对应的 `file` 结构体中引用），其中的 `i_mapping` 域指向一个 `address_space` 结构
- 一个文件就对应一个 `address_space` 结构，一个 `address_space` 与一个偏移量能够确定一个 `page cache` 或 `swap cache` 中的一个页面，当要寻址某个数据时，很容易根据给定的文件及数据在文件内的偏移量而找到相应的页面
- `struct file` 和 `struct inode` 结构体中都有一个 `struct address_space` 指针，实际上，`file -> f_mapping` 是从对应 `inode -> i_mapping` 而来, `inode -> i_mapping -> a_ops` 是由对应的文件系统类型在生成这个 `inode` 时赋予的
  ```c++
  # https://elixir.bootlin.com/linux/v2.6.0/source/include/linux/fs.h#L319
  struct address_space {
  	struct inode		*host;		/* owner: inode, block_device */
  	struct radix_tree_root	page_tree;	/* radix tree of all pages */
  	spinlock_t		page_lock;	/* and spinlock protecting it */
  	struct list_head	clean_pages;	/* list of clean pages */
  	struct list_head	dirty_pages;	/* list of dirty pages */
  	struct list_head	locked_pages;	/* list of locked pages */
  	struct list_head	io_pages;	/* being prepared for I/O */
  	unsigned long		nrpages;	/* number of total pages */
  	struct address_space_operations *a_ops;	/* methods */
  	struct list_head	i_mmap;		/* list of private mappings */
  	struct list_head	i_mmap_shared;	/* list of shared mappings */
  	struct semaphore	i_shared_sem;	/* protect both above lists */
  	atomic_t		truncate_count;	/* Cover race condition with truncate */
  	unsigned long		dirtied_when;	/* jiffies of first page dirtying */
  	unsigned long		flags;		/* error bits/gfp mask */
  	struct backing_dev_info *backing_dev_info; /* device readahead, etc */
  	spinlock_t		private_lock;	/* for use by the address_space */
  	struct list_head	private_list;	/* ditto */
  	struct address_space	*assoc_mapping;	/* ditto */
  };
  ```

## 文件系统、文件类型、`page` 的划分
- 为了方便理解，在此将文件系统划分为内存文件系统（虚拟文件系统）与硬盘文件系统（物理文件系统），在书的这一章节里面，也将广义上的文件分为 `virtual file` 和 `physics file`
- 在书的这一章节里面，将内存页面 `page` 划分为 `anonymous pages` （没有物理文件支持的内存页面）与 `pages backed by a file`（由物理文件映射到内存的某些 `pages`）

## `page cache` 与 `swap cache`
- `page cache` 是与文件映射对应的，而 `swap cache` 是与匿名页对应的
- 如果一个内存页面不是文件映射，则在换入换出的时候加入到 `swap cache` ，如果是文件映射，则不需要交换缓冲
- 这两个的相同点就是它们都是 `address_space` ，都有相对应的文件操作：一个被访问的文件的物理页面都驻留在 `page cache` 或 `swap cache` 中，一个页面的所有信息由 `struct page` 来描述
- 一般情况下用户进程调用 `mmap()` 时，只是在进程空间内新增了一块相应大小的缓冲区，并设置了相应的访问标识，但并没有建立进程空间到物理页面的映射
- 因此，第一次访问该空间时，会引发一个缺页异常。对于共享内存映射情况，缺页异常处理程序首先在 `swap cache` 中寻找目标页（符合 `address_space` 以及偏移量的物理页），如果找到，则直接返回地址；如果没有找到，则判断该页是否在交换区 (`swap area`)，如果在，则执行一个换入操作；如果上述两种情况都不满足，处理程序将分配新的物理页面，并把它插入到 `page cache` 中，程最终将更新进程页表
- 对于映射普通文件情况（非共享映射），缺页异常处理程序首先会在 `page cache` 中根据 `address_space` 以及数据偏移量寻找相应的页面，如果没有找到，则说明文件数据还没有读入内存，处理程序会从磁盘读入相应的页面，并返回相应地址，同时，进程页表也会更新

## 硬盘文件系统

## `tmpfs` 和 `shm` 共性与区别
`tmpfs` 和 `shm` 都是基于内存的文件系统，它们将文件存储在内存中，而不是磁盘上，尽管它们有一些相似之处，但它们的用途、配置方法以及操作上也有一些关键的区别
- **`tmpfs`** 用于存储临时文件，通常挂载到 `/tmp` 等目录，并提供标准的文件操作接口
- **`shm`** 主要用于进程间通信，提供共享内存的功能，进程通过 `shmget()` 和 `mmap()` 创建和访问共享内存区域

### 共同点
1. **基于内存**：`tmpfs` 和 `shm` 都是内存文件系统，文件的数据存储在内存中，而不是磁盘上，因此读写速度非常快
   
2. **内存管理**：它们都使用了 Linux 内存管理机制，文件存储在虚拟内存中，可以像普通文件一样进行操作，同时支持内存映射（`mmap()`）等内存操作

3. **支持匿名共享内存**：`tmpfs` 和 `shm` 都支持共享内存，多个进程可以将文件映射到自己的虚拟内存空间，实现进程间的内存共享

4. **文件系统类型**：它们都是 Linux 内核中的文件系统类型，可以通过 `mount` 命令挂载到指定路径上
   
6. **核心实现**： `tmpfs` 和 `shm` `share core functionality` ，比如说对虚拟文件 `inode` 的描述都是使用 `shmem_inode_info` （ `shmem_inode_info` 可以看作 `inode` 的继承）
   ```c++
   struct shmem_inode_info {
   	spinlock_t		lock;
   	unsigned long		next_index;
   	swp_entry_t		i_direct[SHMEM_NR_DIRECT]; /* for the first blocks */
   	struct page	       *i_indirect; /* indirect blocks */
   	unsigned long		alloced;    /* data pages allocated to file */
   	unsigned long		swapped;    /* subtotal assigned to swap */
   	unsigned long		flags;
   	struct list_head	list;
   	struct inode		vfs_inode;
   };
   ```

### 区别

| 特性              | **tmpfs**                                   | **shm**                                    |
|------------------|--------------------------------------------|--------------------------------------------|
| **用途**          | 临时文件存储系统，用于存储不需要持久化的临时文件。 | 主要用于进程间通信（IPC），存储共享内存区域。 |
| **挂载点**        | 通常挂载在 `/tmp`，用于存储临时文件。          | 通常挂载在 `/dev/shm`，用于共享内存。        |
| **配置方式**      | 需要显式挂载（`mount -t tmpfs`）。              | 使用 `shmget()` 和 `mmap()` 创建共享内存区域。 |
| **文件系统类型**  | `tmpfs` 文件系统类型。                        | `shm` 是 `tmpfs` 的一个变体，专门用于共享内存。 |
| **文件命名**      | 文件有具体的路径名，文件名可以由用户定义。        | 文件名由内核管理，通常是匿名的或者以 `SYSVNN` 命名。 |
| **清除方式**      | 文件在系统关闭或卸载时丢失，类似于 RAM 磁盘。   | 共享内存区域在使用 `shmctl()` 删除时丢失。    |
| **支持进程间通信**| 不直接支持进程间通信，只是一个临时文件系统。     | 直接支持进程间的内存共享和通信。               |

### 具体区别的解释：

1. **用途**
   - `tmpfs` 主要用于存储临时文件，类似于传统的磁盘文件系统，但是文件内容完全存储在内存中，因此非常快速。例如，系统的 `/tmp` 目录通常会使用 `tmpfs` 来存储临时数据
   - `shm` 主要用于进程间通信（IPC）。它提供了一种机制，让不同进程可以通过共享一块内存区域来交换数据

2. **挂载点和配置**
   - `tmpfs` 通常在系统启动时挂载到 `/tmp` 等路径下，使用 `mount -t tmpfs` 来挂载
   - `shm` 则通过内核实现，通常会挂载到 `/dev/shm` 上。用户和应用程序通过 `shmget()` 或 `mmap()` 来创建和访问共享内存区域

3. **文件系统类型和文件名**
   - `tmpfs` 是标准的内存文件系统，可以存储具有具体文件路径和名称的文件。用户可以像操作普通文件一样创建、删除文件
   - `shm` 是内核为支持共享内存而创建的文件系统。它的文件名通常是匿名的，或者由 `shmget()` 动态创建，文件名通常不会直接暴露给用户

4. **文件清除**
   - `tmpfs` 中的文件会在系统重启时消失，或者在文件被删除时清除
   - `shm` 中的共享内存区域会在进程结束后自动清除，或者由系统调用（如 `shmctl()`）显式删除

### 使用方法上的区别

#### 1. **tmpfs** 使用方法：
- 挂载 `tmpfs` 文件系统：
  ```bash
  mount -t tmpfs tmpfs /mnt/tmp
  ```
  这会把 `tmpfs` 文件系统挂载到 `/mnt/tmp`，接下来你可以在这个目录下存储文件：
  ```bash
  echo "Hello, tmpfs!" > /mnt/tmp/hello.txt
  ```

- `tmpfs` 是一个传统的文件系统，文件通过标准的文件操作（如 `open()`、`read()`、`write()` 等）进行访问。

#### 2. **shm** 使用方法：
- 创建共享内存：
  `shmget()` 用于创建一个新的共享内存段：
  ```c
  int shmid = shmget(IPC_PRIVATE, 1024, IPC_CREAT | 0666);
  ```
  上面创建了一个大小为 1024 字节的共享内存段。

- 映射共享内存：
  `shmat()` 将共享内存映射到当前进程的地址空间：
  ```c
  char *shm_ptr = (char *)shmat(shmid, NULL, 0);
  ```

- 进程间通信：通过将共享内存映射到不同进程的虚拟内存空间，多个进程可以读写共享内存进行通信

- 删除共享内存：
  ```c
  shmctl(shmid, IPC_RMID, NULL);
  ```

### 举例说明

#### 使用 `tmpfs` 的例子：
假设你有一个需要临时存储的文件，并希望它在系统重启时被清除。你可以使用 `tmpfs` 来挂载一个内存文件系统，并在其中创建文件
```bash
# 挂载 tmpfs 到 /mnt/tmp
mount -t tmpfs tmpfs /mnt/tmp

# 创建一个临时文件
echo "Temporary file content" > /mnt/tmp/tempfile.txt

# 查看文件
cat /mnt/tmp/tempfile.txt
```
这里，文件会存储在内存中，重启后文件会消失

#### 使用 `shm` 的例子：
假设你需要在两个进程之间共享数据，可以使用 `shm` 来创建共享内存段
```c
// 创建共享内存
int shmid = shmget(IPC_PRIVATE, 1024, IPC_CREAT | 0666);

// 将共享内存附加到进程地址空间
char *shm_ptr = (char *)shmat(shmid, NULL, 0);

// 向共享内存写入数据
strcpy(shm_ptr, "Shared memory data");

// 在另一个进程中读取共享内存
printf("Shared memory content: %s\n", shm_ptr);
```
这里，通过共享内存，两个进程可以共享数据


