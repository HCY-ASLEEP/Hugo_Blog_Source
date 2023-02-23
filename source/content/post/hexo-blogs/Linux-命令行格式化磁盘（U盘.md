---
title: Linux 命令行格式化磁盘（U盘)
date: 2022-11-17 16:27:52
tags:
    - Linux
---

#### 注意：以下操作都属于高危行为，请谨慎使用！<!--more-->

- 在插入 U盘 之前，先查看有哪些磁盘

    ![](https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/2022.11.17.16.36.19.png)

    - 可以看到加粗的有两行，第一行开头是 "Disk" ，第二行开头是 "Device"
    - 整个输出只有一行加粗的 "Disk" ，表示目前只有一个硬盘
    - 每一个加粗的 "Device" 都对应上一行的 "Disk"
    - "Device" 里面的内容表示 "Disk" 里面的分区


- 在插入 U盘 之后，再查看有哪些磁盘

    ![](https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/2022.11.17.16.39.41.png)
    
    - 发现多了一行加粗的 "Disk"
    - 这个正是我们插入的 U盘
    
    
- 卸载 U盘
    
    ```bash
    umount /dev/sda
    ```
    
    - #### /dev/后面的设备要根据你的实际情况而定，否则后面格式化，丢失数据！！

    
- 格式化 U盘 ，并且建立 vfat 文件系统
    
    ```bash
    mkfs.vfat -I /dev/sda
    ```
    
    - ##### /dev/后面的设备要根据你的实际情况而定，否则后面格式化，丢失数据！！

###### 

- 最后再 mount 上 U盘 ，或者把 U盘 拨了再插上，系统可能会自动 mount 上, 就可以使用 U盘 了

- 异常处理

    - 假设 U盘 信息如下
        
        ```bash
        Disk /dev/sdb：7.5 GiB，8004304896 bytes，15633408 sectors
        Units：sectors of / 1 * 512 = 512 bytes
        Sector size(logical/physical)：512 bytes / 512 bytes
        I/O size(mininum/optimal)：512 bytes / 512 bytes
        Disklabel type：dos
        Disk identifier：0x663eb4c4
        
        Device    boot      Start     End Sectors  Size Id Type
        /dev/sdb1    *          0 3815135 3815136  1.8G  0 Empty
        /dev/sdb2         3737268 3741939    4672  2.3M ef EFI (FAT-12/16/32)
        ```
       
        - 如果 mkfs.vfat /dev/sdb 出现如下错误
        
            ```bash
            mkfs.vfat 3.0.10 (12 Sep 2010)
            mkfs.vfat: unable to open /dev/sdb
            ```
        
        - 则需要先格式化 /dev/sdb1 ，即使用 mkfs.vfat /dev/sdb1 命令，将 /dev/sdb1 先格式化掉，然后再格式化 /dev/sdb
        
        - 如果出现如下错误
        
            ```bash
            mkfs.vfat 3.0.10 (12 Sep 2010)
            mkfs.vfat: Device partition expected, not making filesystem on entire device '/dev/sdb' (use -I to override)
            ```
            
        - 系统提示需要使用 -I 参数来完成格式化：mkfs.vfat -I /dev/sdb, 这样就可以完全格式化的U盘
        
        
        

