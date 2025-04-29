---
title: "LLVM 开发记录"
date: 2025-04-23T13:37:39Z
draft: false
---

## 使用自编译的`C++`标准库

不想使用系统自带的 C++ 库，需要自己编译一套 llvm 的 `libc++.so` 以及 `STL` 的时候，可以参考如下命令：
```bash
cmake -G Ninja\
       -DLLVM_ENABLE_PROJECTS="clang;clang-tools-extra;lldb;lld"\
       -DLLVM_ENABLE_RUNTIMES=all\
       -DLIBCXX_ENABLE_SHARED=ON\
       -DLIBCXXABI_ENABLE_SHARED=ON\
       -DLLVM_USE_LINKER=lld\
       -DCMAKE_BUILD_TYPE=Release\
       -DLLVM_BUILD_LLVM_DYLIB=ON\
       -DLLVM_LINK_LLVM_DYLIB=ON\
       -DCMAKE_INSTALL_PREFIX=/home/debian/tools/clang-llvm-21\
        ../llvm-project/llvm
```

编译完库之后，使用显式参数告诉`clang++`使用自编译库：
```bash
clang++ -stdlib=libc++ test.cpp -o test
```

使用`ripgrep`查找`libc++.so`所在目录：
```bash
debian@debian:~/tools/clang-llvm-21$ rg --files | rg so | rg "libc++"
share/libc++/v1/std/memory_resource.inc
share/libc++/v1/std/source_location.inc
lib/libclang.so.21.0.0git
lib/aarch64-unknown-linux-gnu/libc++abi.so.1.0
lib/aarch64-unknown-linux-gnu/libc++.so
lib/aarch64-unknown-linux-gnu/libc++.modules.json
lib/aarch64-unknown-linux-gnu/libc++.so.1.0
lib/libclang-cpp.so.21.0git
```

使用`ldd`或者运行可执行文件`test`之前，需要先注入环境变量：
```bash
# 仅供参考，这个目录应该是 libc++.so 所在的目录
export LD_LIBRARY_PATH=$HOME/tools/clang-llvm-21/lib/aarch64-unknown-linux-gnu/
```

## Floatzone 使用的 SPEC CPU 2017 配置

```cfg
# runcpu --config=asan_template --nobuild 500.perlbench
#--------- Global Settings -----------
label                 = infra-asan_O2
makeflags             = -j16
reportable            = no
strict_rundir_verify  = no
teeout                = yes
tune                  = base

#--------- How Many CPUs? ------------
intrate,fprate:
   copies            = 16
intspeed,fpspeed:
   threads           = 16

#--------- Compilers -----------------
default:
    CC                 = /root/floatzone/llvm-default/bin/clang -O2 -Wno-int-conversion -fsanitize=address -fno-sanitize-address-use-after-scope -fsanitize-address-use-after-return=never -DSPEC_OPENMP -fopenmp -Wno-deprecated-non-prototype
    CXX                = /root/floatzone/llvm-default/bin/clang++ -O2 -Wno-int-conversion -fsanitize=address -fno-sanitize-address-use-after-scope -fsanitize-address-use-after-return=never
    FC                 = /usr/bin/false
    CLD                = /root/floatzone/llvm-default/bin/clang -fsanitize=address -fno-sanitize-address-use-after-scope -fsanitize-address-use-after-return=never -fopenmp -L/root/floatzone/build/packages/rusage-counters/install/lib -Wl,--whole-archive -l:librusagecounters.a -Wl,--no-whole-archive
    CXXLD              = /root/floatzone/llvm-default/bin/clang++ -fsanitize=address -fno-sanitize-address-use-after-scope -fsanitize-address-use-after-return=never -fopenmp -L/root/floatzone/build/packages/rusage-counters/install/lib -Wl,--whole-archive -l:librusagecounters.a -Wl,--no-whole-archive
    COPTIMIZE          = -std=c99
    CXXOPTIMIZE        = -std=c++03
    CC_VERSION_OPTION  = --version
    CXX_VERSION_OPTION = --version
    FC_VERSION_OPTION  = --version

#--------- Portability -----------------
default:
     EXTRA_PORTABILITY = -DSPEC_LP64

500.perlbench_r,600.perlbench_s:
PORTABILITY   = -DSPEC_LINUX_X64

523.xalancbmk_r,623.xalancbmk_s:
PORTABILITY   = -DSPEC_LINUX

502.gcc_r,602.gcc_s=peak:
LDOPTIMIZE   = -z muldefs

intrate,intspeed:
LDCFLAGS   = -z muldefs
```
