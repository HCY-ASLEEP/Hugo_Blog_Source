---
title: "Linux 虚拟文件系统（虚拟内存文件系统）以及共享内存原理"
date: 2024-11-30T19:35:39Z
draft: false
---

最近看了[《Understanding the Linux® Virtual Memory Manager》](https://pdos.csail.mit.edu/~sbw/links/gorman_book.pdf)里面的第十二章 **SHARED MEMORY VIRTUAL FILESYSTEM** ，对文件系统以及内存文件管理有了更加深入的了解，下面是看了这一章节之后对其中一些概念的理解以及拓展，要是想了解这一章，[建议读原文](https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/gordan-book-linux-vmm12-chap.pdf)，配合这篇博客辅助理解

## Linux 哲学
- 在 Linux 里面，一切皆文件，所有的东西都可以看作一个文件，而凡是文件，都应该支持`POSIX`文件操作（比如 `read()` ，`write()` ，`open()`）
- 每一块内存对象，都可以被看作一个文件，一旦赋予这块内存对象相对应的文件描述，就可以使用像使用普通文件那样子操作内存对象
- 而这也正是 `VFS`（虚拟文件系统 `virtual file system` ，包括内存文件系统以及共享内存管理系统）的设计理念以及实现方向

## VMA（virtual memory area）
- Linux 内核用`vm_area_struct`结构体描述某一段连续的虚拟内存区域VMA（virtual memory area），每个虚拟内存区域 `VMA` 都有自己的`vm_area_struct` 结构体
- 内存描述符 `mm_struct` 指向进程的整个地址空间，`vm_area_struct` 只是指向了虚拟空间的一段，这块虚拟内存区域VMA的地址范围为 `[vm_start, vm_end)` ，左开右闭
  <img src="https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/v2-426557bb4eb1e7044bd649483942c2ad_r.jpg" width=55%>
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
- `struct file` 和 `struct inode` 结构体中都有一个 `struct address_space` 指针
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
- `page cache` 作用
  - 当文件被读取时，操作系统会把文件内容加载到内存的 `page cache` 中
  - 如果同一个文件被再次访问，操作系统会直接从 `page cache` 中读取，而不是再次从磁盘读取，从而减少磁盘访问次数，提高性能
- `swap cache` 作用
  - `swap cache` 缓存的是已经交换到磁盘的数据，它是为了提高交换操作的效率，优化虚拟内存的交换操作
  - 如果内存中再次需要使用这些页面，操作系统会从 `swap cache` 里面寻找，然后再从 `swap` 里面查找
  - 同时对于那些刚刚从物理内存里面换出来的 `page` 以及从 `swap` 空间读取的 `page` 也会放在 `swap cache` 里面
- 一般情况下用户进程调用 `mmap()` 时，只是在进程空间内新增了一块相应大小的缓冲区，并设置了相应的访问标识，但并没有建立进程空间到物理页面的映射
- 因此，第一次访问该空间时，会引发一个缺页异常
- 对于共享内存映射情况
  - 缺页异常处理程序首先在 `swap cache` 中寻找目标页（符合 `address_space` 以及偏移量的物理页）
  - 如果找到，则直接返回地址
  - 如果没有找到，则判断该页是否在交换区 (`swap area`)，如果在，则执行一个换入操作
  - 如果上述两种情况都不满足，处理程序将分配新的物理页面，并把它插入到 `page cache` 中，最终将更新进程页表
- 对于映射普通文件情况（非共享映射）
  - 缺页异常处理程序首先会在 `page cache` 中根据 `address_space` 以及数据偏移量寻找相应的页面
  - 如果没有找到，则说明文件数据还没有读入内存，处理程序会从磁盘读入相应的页面，并返回相应地址，同时进程页表也会更新

## 硬盘（物理）文件系统
在 Linux 文件系统中，`dentry`（目录项）和 `inode`（索引节点）是两个核心概念，它们是文件系统内部用于表示和管理文件、目录及其属性的数据结构
### `dentry`（目录项）
- `dentry` 是一个 `目录项` 的数据结构，它表示文件`路径名`与文件在文件系统中的`位置`之间的映射关系
- 每个 `dentry` 关联了一个路径组件（如文件夹或文件名），并指向文件系统中的 `inode`，内核通过逐级解析路径来查找文件
  ```c
  struct dentry {
		......
  	struct inode  * d_inode;	/* Where the name belongs to - NULL is negative */
		......
  	struct list_head d_child;	/* child of parent list */
  	struct list_head d_subdirs;	/* our children */
		......
  	struct qstr d_name; /* file name */
		......
  };

  struct qstr {
	  const unsigned char * name;
	  unsigned int len;
	  unsigned int hash;
	  char name_str[0];
  };
  ```

### `inode`（索引节点）
- `inode` 是文件系统中用于描述文件或目录的元数据的数据结构，是文件的唯一标识符，除了文件名之外，它包含了文件的所有信息，例如：
  - 文件类型（普通文件、目录、符号链接等）
  - 文件权限（读、写、执行权限）
  - 文件所有者和用户组
  - 文件大小
  - 文件的时间戳（创建时间、修改时间、访问时间）
  - 文件数据块的位置（指向数据块的指针）
  - ...
`inode` 指向文件在磁盘上的物理位置，帮助操作系统定位文件数据块，从而实现文件的读取和写入


### `dentry` 和 `inode` 的关系
- 关联性
  - 每个 `dentry` 对应一个路径组件（例如某个文件或目录的名称），并且每个 `dentry` 都指向一个 `inode`
  - 通过 `inode`，操作系统可以找到文件的实际数据，而 `dentry` 则是通过文件名来指向 `inode`
  - 换句话说，`dentry` 提供了文件路径到文件数据位置（即 `inode`）的映射关系
- 路径解析
  - 当一个路径被访问时，内核会逐步解析路径中的每个目录项（`dentry`），通过目录项找到对应的 `inode`
  - 每个目录项（`dentry`）都存储了一个指向对应 `inode` 的指针
  - 当最终解析到文件名时，内核通过该 `inode` 获取文件的元数据并定位文件数据块
- 缓存机制
  - 为了提高性能，内核会缓存路径名和 `inode` 之间的映射关系，这样在访问同一个文件或目录时，可以避免重复解析路径

### 用 `dentry` 构建“多叉树”文件系统
- 在 Linux 内核中只需要用到 `struct list_head d_child` 和 `struct list_head d_subdirs` 这两个两个关键双向链表就可以实现目录树结构
  ```c
  struct dentry {
  	......
  	struct list_head d_child;	/* child of parent list */
  	struct list_head d_subdirs;	/* our children */
  	......
  };

  struct list_head {
    struct list_head *next, *prev;
  };
  ```
- `d_child`
  - 表示当前目录项在父目录的子项链表中的位置
  - 它连接到父目录的 `d_subdirs` 链表，用于形成目录树中的父子关系
  - `d_child -> prev` 为父目录或者兄弟
  - `d_child -> next` 为兄弟或者 `NULL`

- `d_subdirs`
  - 表示当前目录的子目录和文件列表
  - 用于遍历当前目录下的所有子项
  - 当前目录下的每个子项（文件或子目录）的 `d_child` 都会被挂接到 `d_subdirs` 链表中
  - `d_subdirs -> prev` 为 `NULL`
  - `d_subdirs -> next` 为当前目录下第一个孩子或者 `NULL`
  - 在当前目录新建文件时，创建的文件会以头插法插到 `d_subdirs -> next`

- `d_subdirs` 与 `d_child` 配合
  - `d_subdirs` 维护子项列表，`d_child` 链接到父目录的子项链表，形成一个双向链表结构的目录树

- 假设文件系统目录结构如下：
  ```yaml
  /
  ├── home/
  │   ├── user/
  │   │   ├── file1
  │   │   └── documents/
  └── etc/
  ```
- 那么它们之间的链表关系图应该是这样子的：
  ```yaml
  (root /) d_child
           d_subdirs
                +
                |
                +--> (home) d_child
                |           d_subdirs
                |                +
                |                |
                |                +--> (user) d_child
                |                            d_subdirs
                |                                 +
                |                                 |
                |                                 +--> (file1) d_child
                |                                 |
                |                                 +--> (documents) d_child
                |
                +--> (etc) d_child
  ```

- 现在还剩下最后一个问题，`d_subdirs` 是 `list_head` 类型的数据结构，它本身只包含两个指针：`next` 和 `prev`，应该如何通过 `list_head` 找到包含它的 `struct dentry` 呢？
- 在内核里面，实现这个目标需要依赖嵌套结构和偏移量计算
- 而这一步的关键函数是 `contianer_of`
  ```c
  #define container_of(ptr, type, member) \
    ((type *)((char *)(ptr) - offsetof(type, member)))
  ```
- 在 `dentry` 这个场景下，`container_of` 里面的各个参数可以这样子理解
	- `ptr` ：`list_head` 指针，例如 `&dir->d_subdirs`
	- `type`：包含 `list_head` 的结构体类型（这里是 `struct dentry`）
	- `member`：`list_head` 字段在结构体中的名字（这里是 `d_subdirs`）
  - `offsetof(type, member)`：获取 `member` 在 `type` 中的偏移量，通过 `ptr` 减去 `member` 的偏移量，计算出结构体的起始地址

## `tmpfs` 与 `shm` 联系

- `tmpfs` 是面向用户的、通用的基于内存的文件系统，使用 RAM 作为存储媒介，用于提供临时存储和共享内存功能，能够通过挂载点提供更多功能
- `shm` 是 Linux 内核内部实现匿名内存共享的机制，主要为内核服务，不对用户直接可见
- `shm` 是 `tmpfs` 的一种特殊用途变体，它对 `tmpfs` 的核心功能进行再次针对性封装和改进，为匿名页面提供统一的文件支持接口，使内核可以用文件操作函数（如 `readpage` 或 `writepage`）管理这些页面，实现匿名共享内存（如通过 `mmap` 创建的 `MAP_ANONYMOUS | MAP_SHARED` 区域）和 System V 共享内存（`shmget`）
- `shm` 对虚拟文件的描述都是使用 `shmem_inode_info` （ `shmem_inode_info` 可以看作 `inode` 的继承，在内存文件系统里面如果要创建一个文件，要先向系统申请一个 `inode` ，然后才是将这个 `inode` 传给 `shmem_inode_info` ，有点类似 C++ 里面的当一个子类要实例化的时候需要先实例化父类）
   ```c++
   // https://elixir.bootlin.com/linux/v2.6.0/source/include/linux/shmem_fs.h#L10
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
   
  | 特性                        | `shm`                                      | `tmpfs`                                   |
  |-----------------------------|--------------------------------------------|------------------------------------------|
  | **主要用途**                | 为匿名内存页面或共享内存区域提供支持       | 用户挂载点，用于存储临时文件或共享内存  |
  | **访问方式**                | 通过 `mmap` 或 `shmget/shmat` 接口访问     | 挂载为文件系统后，通过文件操作访问      |
  | **是否对用户可见**          | 不对用户直接可见（由内核管理）             | 可由用户挂载到目录，如 `/tmp` 或 `/dev/shm` |
  | **挂载方式**                | 通过 `kern_mount()` 自动挂载（内核专用）   | 需要显式挂载，系统管理员决定挂载点      |
  | **创建对象**                | 由内核为匿名页面或 `shmget` 区域创建       | 用户可以手动创建文件或目录              |
  | **主要接口**                | 内核内部的 `shmem` 操作                    | 文件系统接口（如 `open`、`write`）      |

### **`shm` 使用**
- System V 共享内存
  ```c
  // 使用 `shmget` 和 `shmat` 创建的共享内存段，通过 `shm` 提供底层支持
  int shmid = shmget(IPC_PRIVATE, 1024, IPC_CREAT | 0666);
  char *data = shmat(shmid, NULL, 0);
  strcpy(data, "Hello, shm!");
  printf("Data in shared memory: %s\n", data);
  ```

- 匿名共享内存
  ```c
  // 当进程调用 `mmap` 并指定 `MAP_ANONYMOUS | MAP_SHARED` 时，`shm` 会为这些匿名页面创建支持
  size_t size = 4096;  // 分配一个4KB的内存区域
  void *addr = mmap(NULL, size, PROT_READ | PROT_WRITE, MAP_SHARED | MAP_ANONYMOUS, -1, 0);
  if (addr == MAP_FAILED) {
      perror("mmap failed");
      return 1;
  }

  // 通过指针访问并修改匿名共享内存
  int *data = (int *)addr;
  ```

- 使用 `POSIX` 文件接口与 `shm` 结合
  ```c
  const char *shm_name = "/my_shm";  // 共享内存对象的名称
  size_t size = 4096;                // 共享内存的大小

  // 创建或打开共享内存对象
  // 使用 shm_open() 创建或打开共享内存对象后，返回的文件描述符可以通过 open() 进行访问
  int shm_fd = shm_open(shm_name, O_CREAT | O_RDWR, S_IRUSR | S_IWUSR);
  if (shm_fd == -1) {
      perror("shm_open");
      return 1;
  }

  // 设置共享内存的大小
  if (ftruncate(shm_fd, size) == -1) {
      perror("ftruncate");
      return 1;
  }

  // 将共享内存映射到进程的地址空间
  void *shm_ptr = mmap(NULL, size, PROT_READ | PROT_WRITE, MAP_SHARED, shm_fd, 0);
  if (shm_ptr == MAP_FAILED) {
      perror("mmap");
      return 1;
  }

  // 使用共享内存
  // 写入数据到共享内存
  snprintf((char *)shm_ptr, size, "Hello from shared memory!");

  // 解除映射
  if (munmap(shm_ptr, size) == -1) {
      perror("munmap");
      return 1;
  }

  // 关闭共享内存对象
  close(shm_fd);
  ```


### **`tmpfs` 使用**
- `tmpfs` 默认挂载到 `/tmp`，为用户提供快速的、基于 RAM 的临时文件存储空间，读写速度快，它也可以手动挂载到其他地方
  ```bash
  # 在 /mnt/tmpfs 挂载一个 tmpfs 文件系统
  sudo mount -t tmpfs -o size=128M tmpfs /mnt/tmpfs
  
  # 在 tmpfs 上创建文件
  echo "Hello, tmpfs!" > /mnt/tmpfs/testfile
  cat /mnt/tmpfs/testfile
  
  # 卸载 tmpfs
  sudo umount /mnt/tmpfs
  ```
- `tmpfs` 也可以使用多进程 `open` 或者多进程 `mmap` 到一个共同的 `tmpfs` 文件达到进程间通信的效果
  ```c
  int fd = open("example.txt", O_RDONLY);
  char *mapped = mmap(NULL, 4096, PROT_READ, MAP_PRIVATE, fd, 0);
  close(fd);
  ```


## 全局共享的零页（global zero page）

- Linux 内核中有一个全局共享的零页，所有字节都被初始化为零，这块内存页被全局所有进程共享，通常是只读的，多进程可以同时访问
- 当一个进程需要访问大量的全零数据（如未初始化的内存、扩展文件的空白部分），可以直接映射到全局零页，而无需实际分配和初始化物理内存，避免为每个进程单独分配一块全零的内存页
- 例如当文件通过 `truncate()` 扩展时，新增加的部分需要初始化为零。如果直接写零到磁盘或内存，会消耗资源
- 通过全局零页，文件系统可以将新增加的部分映射到这块零页，而不需要实际分配和初始化
- 又例如进程分配内存时，未使用的部分（例如通过 `mmap` 映射的匿名内存）通常被映射为零页，直到实际写入数据为止
- 由于零页是只读的，如果进程试图写入零页，会触发页错误（page fault），内核会复制一块新的物理页供进程使用，这叫做“写时复制” （Copy-on-Write, COW）
