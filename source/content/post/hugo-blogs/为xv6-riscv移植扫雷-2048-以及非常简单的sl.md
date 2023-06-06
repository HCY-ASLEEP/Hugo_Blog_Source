---
title: "为 xv6-iscv 移植扫雷(minesweeper) 2048 以及非常简单的 sl"
date: 2023-06-06T13:59:46+08:00
tags: 
    - Xv6
---

### 前言

我使用的 xv6-riscv 是 fork MIT 官方的库，在它的基础上面进行移植，这个教学系统原本是与 6.1810 一起配套使用的，下面这个是官方仓库地址：

👉 [https://github.com/mit-pdos/xv6-riscv](https://github.com/mit-pdos/xv6-riscv)

这是我所做过的移植交互程序及之前 SZU OS 课程在 xv6 上面做的一些实验：

👉 [我对 xv6-riscv 的一些改动](https://github.com/HCY-ASLEEP/szu-xv6-riscv-adapted/commits?author=HCY-ASLEEP)

### 2048 移植以及相关移植准备

终端交互移植最关键的就是要把缺失的库函数都补完，首先就得增加和改进 xv6-riscv 原有的终端交互相关的库

在 xv6-riscv 的顶层目录下新建一个 include 文件夹，里面存放改进的头文件

| Head | What it does  | 
| ------- | ------- |
| assert.h | 断言的相关宏定义和函数声明，用于断言检查 |
| ctype.h  | 字符处理的函数和宏定义，对字符进行分类和转换 |
| limits.h | 定义了各种数据类型的取值范围以及其他与整数类型相关的常量 |
| stdarg.h | 提供了支持可变参数函数的宏和类型定义 |
| stdbool.h| 定义了布尔类型和相关的常量，没错，xv6-riscv 原本甚至不支持 bool 型变量 |
| stddef.h | 提供了一些与指针、大小和偏移量相关的常量和类型定义 |
| stdio.h  | 用于处理标准输入输出 |
| stdlib.h | 用于内存管理、字符串转换、伪随机数生成、算术运算和程序终止等 || string.h | 这些函数和宏定义包括字符串复制、连接、比较、搜索、分割等操作|
| termios.h| 用于控制和配置终端的行为 |
| unistd.h | 定义了一些与系统调用、文件操作、进程控制、环境变量和程序终止相关的函数和常量|

上面这些改进的库函数里面最关键的是 termios.h 和 stdio.h ，没错，xv6-riscv 连标准输入输出都没有，刷新终端都做不到。。。即便如此 termios.h 移植之后也是极度残缺的，因为没有 xv6-riscv 压根就没有提供 ioctl 的系统调用！也正是因为这个原因，C语言里面的标准库 curses.h (可以控制光标位置、输出文本、设置颜色、处理用户输入等,使用这些函数和宏，可以在终端中实现基本的交互式图形界面) 压根移植不了，这进一步导致 vi 移植不了

**syscall ioctl  -->>  termios.h / curses.h  -->>  vi / nano  -->>  fuck?**

这些改经的库函数里面有一个库非常有趣，就是 stdarg.h ，有了它之后你就可以使用省略号作为函数的参数！

```c
#include <stdio.h>
#include <stdarg.h>

void printValues(int num, ...)
{
    va_list args;
    va_start(args, num); // 初始化va_list

    for (int i = 0; i < num; i++)
    {
        int value = va_arg(args, int); // 从va_list中获取下一个参数
        printf("%d ", value);
    }

    va_end(args); // 结束va_list的访问
    printf("\n");
}

int main()
{
    printValues(3, 10, 20, 30); // 调用打印函数并传递三个参数

    return 0;
}

// 输出结果为：10 20 30 
```

目前有一点不足的是这些移植的库函数所定义的宏或者函数会与 kernel 文件夹下面的冲突，也就是重定义错误，比如 stdio.h 里面的 printf 与 kernel 文件夹下的 printf 完全不一样

接下来在 xv6 的顶层目录新建 lib 目录，里面新建一个 libc.c 文件用于实现之前 include 文件夹下定义的改进函数

然后在 user 目录里面新建 2048.c ，里面存放 2048 这个小游戏的实现，整个游戏使用 wasd 进行方向控制，q 控制退出游戏，每次操作接收一个操作字母加回车

最后修改 Makefile ，将改进的库以及 2048.c 纳入编译的范围

注意的是，移植之后可能要细微修改 kernel 下面的 virtio.h 和 virtio_disk.c ，不然 xv6 可能无法正常启动，会抛出一个 panic 信息

具体的 2048 实现以及相关改动都在这一个 👉 [commit](https://github.com/HCY-ASLEEP/szu-xv6-riscv-adapted/commit/7384d0c6cbd7f5cb0c86b6b0c85b3fb5efff4a36#diff-a309633cef6e0f9097454736037a5fa2d769e4130f3f4618c5925e2c2e2723e7) 里面

### 移植扫雷(minesweeper)

因为有了 2048 移植之后的功能库，扫雷的移植就非常简单了，修改 Makefile 和添加 minesweeper 的实现就行，具体参考这个 👉 [commit](https://github.com/HCY-ASLEEP/szu-xv6-riscv-adapted/commit/83ef9c619f57e7651f9eecd77960b88a5cba6e10)

### "移植"小火车 sl
最后是一个非常简陋的小火车 sl ，这不能说是移植，应该说是我模仿 sl 👉 [官方仓库](https://github.com/mtoyoda/sl) 结合能够用的库自己实现的，没有 ioctl 系统调用是真的痛啊！本来想参考 👉 [nyuichi/xv6](https://github.com/nyuichi/xv6) 移植这个 ioctl 系统调用的，但是 xv6-riscv 的接口与 xv6-x86 的内核接口差别有点大，有点超出能力范围之内了。。。小火车具体实现参考这个 👉 [commit](https://github.com/nyuichi/xv6)

### End

代码已经全部上传到我的 👉 [github repo](https://github.com/HCY-ASLEEP/szu-xv6-riscv-adapted) 里面，欢迎 star
