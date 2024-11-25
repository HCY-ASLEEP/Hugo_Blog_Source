  ---
title: "Linux磁盘占用分析"
date: 2024-11-25T19:27:02Z
draft: false
---
前几天服务器又宕机了，仔细排查原来是磁盘的占用满了，在这里记录一下使用到的命令以及相关小知识
在 linux shell 里面查看磁盘占用信息主要有两个命令，一个是 `du` ，另外一个是 `df`
### `du` 和 `df` 的区别

- **du : disk usage**
  - 主要用于递归计算指定文件夹的大小
  - 它通过搜索文件来计算每个文件的大小然后累加，只能看到当前存在的，没有被删除的文件
  - 使用 `du - sh` 可以显示当前目录的总磁盘使用量，并且以易读的格式（如 K、M、G）显示
    - `-s` 或 `--summarize`：只显示总计的磁盘使用量，而不是列出每个子目录的详细使用量，也就是说，它会显示当前目录（或指定目录）的总大小，而不是列出每个子目录的大小
    - `-h` 或 `--human-readable`：以更易读的格式显示磁盘使用量，例如以 K（千字节）、M（兆字节）、G（吉字节）等单位显示，而不是以字节为单位
      ```bash
      
      ```
  - 而 `du -h` 可以查看当前目录下所有文件和子目录的大小,这个会递归列出所有子目录占用信息，内容会非常多
  - 有些情况下为了可以自顶向下逐层文件夹地分析哪些目录是导致磁盘臃肿的原因，可以使用 `--max-depth` 参数，下面是只递归展示一层（第一层）的占用情况（ --max-depth 1 ）
    ```bash
    root@xtz:~/build# du -h --max-depth 1 
    2.7M	./unittests
    244K	./cmake
    32K	./docs
    124K	./share
    13G	./bin
    32K	./libexec
    11M	./test
    3.6G	./tools
    4.1M	./include
    1.5M	./examples
    32K	./runtimes
    13G	./lib
    99M	./utils
    150M	./projects
    8.9M	./CMakeFiles
    30G	.
    ```
  - 但是使用 `du -h --max-depth 1` 的结果并不是按照磁盘占用情况从小到大的顺序排序的，还是不够直观地分析占用情况，于是通过使用 `sort -rh` 进行进一步排序
    ```bash
    root@xtz:~/build# du -h --max-depth 1 | sort -rh
    30G	.
    13G	./lib
    13G	./bin
    3.6G	./tools
    150M	./projects
    99M	./utils
    11M	./test
    8.9M	./CMakeFiles
    4.1M	./include
    2.7M	./unittests
    1.5M	./examples
    244K	./cmake
    124K	./share
    32K	./runtimes
    32K	./libexec
    32K	./docs
    ```
  - 其中 `sort` 目录的选项 `-r` 表示逆序，不加 `-r` 就是顺序；由于前面的 `du` 使用了 `-h` 选项，所以 `sort` 也要启用 `-h` 选项才可以和 `du` 结果匹配起来进行排序
   
- **df : disk free**
  - `df` 命令则是通过文件系统来快速获取空间大小的信息（如硬盘、分区、挂载点等），用于检查整个文件系统或分区的大小，而不可以做到像 `du` 那样子针对某一个目录的大小进行分析
  - 当删除一个文件时，该文件并不会立即在文件系统中消失，而是暂时消失，直到所有程序都不再使用它时，才会根据操作系统的规则释放掉
  - `df` 命令记录的是通过文件系统获取到的文件大小，包括已经删除但尚未释放的文件，因此在某些情况下比du命令显示的空间要大
  - 当文件系统也确定删除了该文件后，`du` 和 `df` 显示的空间大小会一致。使用 `df -h` 可以以易读的格式显示磁盘空间和使用情况，无论在什么目录下使用 `df` 得到的结果都是一样的
    ```bash
    root@xtz:~/asanopt/llvm-4.0.0-project/ASanBuild# df -h
    Filesystem      Size  Used Avail Use% Mounted on
    overlay         439G  208G  210G  50% /
    tmpfs            64M     0   64M   0% /dev
    shm              64M     0   64M   0% /dev/shm
    /dev/sda1       439G  208G  210G  50% /etc/hosts
    tmpfs            32G     0   32G   0% /proc/acpi
    tmpfs            32G     0   32G   0% /sys/firmware
    ```

