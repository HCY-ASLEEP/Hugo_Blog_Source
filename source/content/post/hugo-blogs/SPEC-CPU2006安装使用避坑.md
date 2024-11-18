---
title: "SPEC CPU2006安装使用避坑"
date: 2024-11-17T21:34:26Z
draft: false
---

## 安装
- 网上找到的版本应该是这个`speccpu2006-v1.0.1-newest.tar`，在想要安装的目录下执行`tar -xvf speccpu2006-v1.0.1-newest.tar`就可以解压安装
- 进入到目录里面`cd speccpu2006-v1.0.1`
- 安装 spec ，`./install.sh`，如果安装出现错误，极有可能是有一些文件没有可执行文件，递归赋予可执行文件就行`chmod -R a+x .`，不会有问题的
- 安装完成之后执行`./shrc`激活spec环境（以后每次重新打开一个 shell 会话的时候都需要激活一下）

## SPEC 基本概念

### 构成
- SPEC有两个部分组成，一个是数据，一个是代码（代码以项目的方式组织），运行需要指定数据以及项目
- 数据有三个规格
  1. `test`（小型）
  2. `train`（中型）
  3. `ref`（大型）
- 代码有以下选项
  1. `int`（整型运算性能）
  2. `float`，也叫 fp（浮点运算性能）
  3. `all_c`（全部 c 项目）
  4. `all_cpp`（全部 cpp 项目）
  5. `all`（全部项目）
  6. 特定项目，比如 `453.povray`

### 编译标准
- C 语言要使用 `-std=gnu89`
- CPP 要使用 `-std=c++98`
- 如果要启用 address sanitizer，要注意防止 sanitizer 检测到错误的时候 abort 程序而导致 benchmark 提前终止，需要使用以下编译选项
    ```bash
    -fsanitize=address -fsanitize-recover=address
    ```
  并且要注入环境变量`ASAN_OPTIONS=halt_on_error=0:detect_leaks=0`

### 运行模式
spec 有两种运行模式，一个是 base ，基本模式，一般使用这个，一个是 peak ，性能模式，测试峰值性能极限

### Fortran 错误
- 由于某些项目是有部分使用 fortran 来实现的，所以如果没有安装 gfortran 的时候可能会导致其编译不了，解决方法有二
    1. 安装 gfortran
    2. 让 `FC=echo` ，相当于直接告诉它不要编译这部分了，浪费时间的

### 日志位置
- 运行完成之后在安装底层目录的 result 文件夹下可以看到运行日志以及结果
    1. 看结尾
       - 以 log 结尾的文件是一个完整运行日志，包括编译输出，运行输出，以及结果归纳
       - 以其他文件类型后缀结尾的都是测试结果归纳，只不过文件格式不一样，内容一样的，一般看txt就行
    2. 看开头
       - `CINT` 表示使用 C/C++ 编写的 `int benchmark`
       - `CFP` 表示使用 C/C++ 编写的 `float benchmark`
    3. 看中间
       - 中间是一个编号，遵循以时间为递增的规律
- 另外在每个项目的文件下也会有单独的编译日志以及运行日志，例如
  - `ls ./benchspec/CPU2006/400.perlbench/run/build_base_1.0000/`这个目录下可以看到编译日志
    - 如果这个项目编译出错了，可以在这个目录下找到`make.err`日志
  - `ls ./benchspec/CPU2006/400.perlbench/run/run_base_test_1.0000/`这个目录下可以看到运行日志

### 看日志
- 有时候 spec 自带的结果汇总会不完整，因为有些程序有可能被 asan 终止之后在拉起就不被计分了，所有有时候得去日志里面翻找成功运行项目案例的运行时间，在上面提到的那个完整日志里面全局搜索关键字 `success` 就可以了（忽略大小写）
  - log2excel 脚本，这里是一个简单的将这些 success 项目信息转为 excel 表格的脚本，以及应该喂给这个脚本的 success 数据格式：
    ```
    CC               = /root/asanopt/llvm-4.0.0-project/build/bin/clang    -std=gnu89 -fsanitize=address -fsanitize-recover=address -m64 -g
    CXX              = /root/asanopt/llvm-4.0.0-project/build/bin/clang++  -std=c++98 -fsanitize=address -fsanitize-recover=address -m64 -g
    
    400.perlbench base ref ratio=12.85, runtime=760.415379
    400.perlbench base ref ratio=14.33, runtime=681.948309
    400.perlbench base ref ratio=14.44, runtime=676.611816
    401.bzip2 base ref ratio=18.30, runtime=527.208650
    401.bzip2 base ref ratio=18.55, runtime=520.089746
    401.bzip2 base ref ratio=18.78, runtime=513.887221
    403.gcc base ref ratio=18.22, runtime=441.892276
    403.gcc base ref ratio=19.51, runtime=412.592274
    403.gcc base ref ratio=19.76, runtime=407.424780
    429.mcf base ref ratio=29.58, runtime=308.278307
    429.mcf base ref ratio=36.16, runtime=252.184882
    429.mcf base ref ratio=38.58, runtime=236.364282
    433.milc base ref ratio=20.98, runtime=437.482181
    433.milc base ref ratio=21.23, runtime=432.368999
    433.milc base ref ratio=21.41, runtime=428.706485
    444.namd base ref ratio=19.20, runtime=417.752721
    444.namd base ref ratio=19.35, runtime=414.419211
    444.namd base ref ratio=20.47, runtime=391.783225
    445.gobmk base ref ratio=21.42, runtime=489.668323
    445.gobmk base ref ratio=22.60, runtime=464.136214
    445.gobmk base ref ratio=22.85, runtime=458.984958
    447.dealII base ref ratio=27.67, runtime=413.422023
    447.dealII base ref ratio=28.80, runtime=397.228149
    447.dealII base ref ratio=30.58, runtime=374.084230
    450.soplex base ref ratio=28.75, runtime=290.083530
    450.soplex base ref ratio=31.28, runtime=266.656672
    450.soplex base ref ratio=32.73, runtime=254.838331
    453.povray base ref ratio=20.95, runtime=253.951463
    453.povray base ref ratio=21.30, runtime=249.714080
    453.povray base ref ratio=22.47, runtime=236.739422
    456.hmmer base ref ratio=14.50, runtime=643.463325
    456.hmmer base ref ratio=16.22, runtime=575.344854
    456.hmmer base ref ratio=16.35, runtime=570.789854
    458.sjeng base ref ratio=21.67, runtime=558.318427
    458.sjeng base ref ratio=24.55, runtime=492.913527
    458.sjeng base ref ratio=25.18, runtime=480.538434
    462.libquantum base ref ratio=62.08, runtime=333.748264
    462.libquantum base ref ratio=68.76, runtime=301.351093
    462.libquantum base ref ratio=73.88, runtime=280.453628
    464.h264ref base ref ratio=31.03, runtime=713.099006
    464.h264ref base ref ratio=33.94, runtime=652.062848
    464.h264ref base ref ratio=35.00, runtime=632.321436
    470.lbm base ref ratio=58.00, runtime=236.895693
    470.lbm base ref ratio=63.69, runtime=215.746827
    470.lbm base ref ratio=64.17, runtime=214.102158
    473.astar base ref ratio=17.80, runtime=394.369525
    473.astar base ref ratio=18.05, runtime=388.900111
    473.astar base ref ratio=18.77, runtime=374.065830
    482.sphinx3 base ref ratio=30.24, runtime=644.427922
    482.sphinx3 base ref ratio=30.28, runtime=643.674597
    482.sphinx3 base ref ratio=32.57, runtime=598.409849
    483.xalancbmk base ref ratio=20.32, runtime=339.625837
    483.xalancbmk base ref ratio=20.72, runtime=332.940290
    483.xalancbmk base ref ratio=21.84, runtime=315.878660
    998.specrand base ref ratio=119.83, runtime=0.083455
    998.specrand base ref ratio=122.88, runtime=0.081378
    998.specrand base ref ratio=123.67, runtime=0.080860
    999.specrand base ref ratio=122.42, runtime=0.081687
    999.specrand base ref ratio=123.98, runtime=0.080658
    999.specrand base ref ratio=124.79, runtime=0.080134
    ```

    ```python
    import re
    import sys
    from statistics import median
    from openpyxl import Workbook
    
    # 检查是否提供了文件名参数
    if len(sys.argv) != 2:
        print("用法: python process_data_to_excel.py <文件名>")
        sys.exit(1)
    
    # 获取用户输入的文件名
    filename = sys.argv[1]
    
    # 读取文件
    try:
        with open(filename, 'r') as file:
            data = file.readlines()
    except FileNotFoundError:
        print(f"文件 {filename} 未找到。")
        exit()
    
    # 跳过前三行
    data = data[3:]
    
    # 解析数据
    results = {}
    for line in data:
        line = line.strip()
        match = re.match(r'(\d+\.\w+)\s+base\s+ref\s+ratio=(\d+\.\d+),\s+runtime=(\d+\.\d+)', line)
        if match:
            name, ratio, runtime = match.groups()
            if name not in results:
                results[name] = [[], []]
            results[name][0].append(float(ratio))
            results[name][1].append(float(runtime))
    
    # 创建 Excel 文件
    wb = Workbook()
    ws = wb.active
    ws.title = "Results"
    
    # 写入标题行
    ws.append(["Name", "Median Ratio", "Median Runtime"])
    
    # 写入数据（保留完整精度）
    for name, content in results.items():
        median_ratio = median(content[0])
        median_runtime = median(content[1])
        ws.append([name, median_ratio, median_runtime])
    
    # 保存 Excel 文件
    output_file = filename+".xlsx"
    wb.save(output_file)
    print(f"结果已保存到 {output_file}")
    ```
    
- 要找出那些编译不成功的项目，只需要在完整日志里面全局搜索关键字 `error building`（忽略大小写）
- 补充说明：
  - `999.specrand` `999.specrand` 这两个项目的数据不重要，因为这两个项目只是验证 spec 环境的完整性的，比如说能不能编译

### 看结果报告

- 总报告例子：
  ```yaml
  Producing Reports
  mach: default
    ext: 1
      size: test
        set: int
          format: raw -> /root/spec2006/result/CINT2006.001.test.rsf
          format: ASCII -> /root/spec2006/result/CINT2006.001.test.txt
          format: Screen ->
  
                                    Estimated                       Estimated
                  Base     Base       Base        Peak     Peak       Peak
  Benchmarks      Ref.   Run Time     Ratio       Ref.   Run Time     Ratio
  -------------- ------  ---------  ---------    ------  ---------  ---------
  400.perlbench      --     6.96           -- S
  401.bzip2          --     6.18           -- S
  403.gcc            --     1.33           -- S
  429.mcf            --     2.05           -- S
  445.gobmk          --    19.0            -- S
  456.hmmer          --     3.27           -- S
  458.sjeng          --     4.78           -- S
  462.libquantum     --     0.0351         -- S
  464.h264ref        --    14.0            -- S
  471.omnetpp        --   393              -- S
  473.astar          --     8.30           -- S
  483.xalancbmk      --     0.120          -- S
   Est. SPECint_base2006                   --
   Est. SPECint2006                                                   Not Run
  
        set: fp
          format: raw -> /root/spec2006/result/CFP2006.001.test.rsf
          format: ASCII -> /root/spec2006/result/CFP2006.001.test.txt
          format: Screen ->
  
                                    Estimated                       Estimated
                  Base     Base       Base        Peak     Peak       Peak
  Benchmarks      Ref.   Run Time     Ratio       Ref.   Run Time     Ratio
  -------------- ------  ---------  ---------    ------  ---------  ---------
  410.bwaves                                  NR
  416.gamess                                  NR
  433.milc           --      6.52          -- S
  434.zeusmp                                  NR
  435.gromacs                                 NR
  436.cactusADM                               NR
  437.leslie3d                                NR
  444.namd           --      9.79          -- S
  447.dealII                                  NR
  450.soplex         --      0.024         -- S
  453.povray         --      0.875         -- S
  454.calculix                                NR
  459.GemsFDTD                                NR
  465.tonto                                   NR
  470.lbm            --      1.54          -- S
  481.wrf                                     NR
  482.sphinx3        --      1.22          -- S
   Est. SPECfp_base2006                    --
   Est. SPECfp2006                                                    Not Run
  ```
  
  - `INT` 部分：
  ```yaml
        set: int
          format: raw -> /root/spec2006/result/CINT2006.001.test.rsf
          format: ASCII -> /root/spec2006/result/CINT2006.001.test.txt
          format: Screen ->
  
                                    Estimated                       Estimated
                  Base     Base       Base        Peak     Peak       Peak
  Benchmarks      Ref.   Run Time     Ratio       Ref.   Run Time     Ratio
  -------------- ------  ---------  ---------    ------  ---------  ---------
  400.perlbench      --     6.96           -- S
  401.bzip2          --     6.18           -- S
  403.gcc            --     1.33           -- S
  429.mcf            --     2.05           -- S
  445.gobmk          --    19.0            -- S
  456.hmmer          --     3.27           -- S
  458.sjeng          --     4.78           -- S
  462.libquantum     --     0.0351         -- S
  464.h264ref        --    14.0            -- S
  471.omnetpp        --   393              -- S
  473.astar          --     8.30           -- S
  483.xalancbmk      --     0.120          -- S
   Est. SPECint_base2006                   --
   Est. SPECint2006                                                   Not Run
  ```
  
- `FP` 部分：
  ```yaml
        set: fp
          format: raw -> /root/spec2006/result/CFP2006.001.test.rsf
          format: ASCII -> /root/spec2006/result/CFP2006.001.test.txt
          format: Screen ->
  
                                    Estimated                       Estimated
                  Base     Base       Base        Peak     Peak       Peak
  Benchmarks      Ref.   Run Time     Ratio       Ref.   Run Time     Ratio
  -------------- ------  ---------  ---------    ------  ---------  ---------
  410.bwaves                                  NR
  416.gamess                                  NR
  433.milc           --      6.52          -- S
  434.zeusmp                                  NR
  435.gromacs                                 NR
  436.cactusADM                               NR
  437.leslie3d                                NR
  444.namd           --      9.79          -- S
  447.dealII                                  NR
  450.soplex         --      0.024         -- S
  453.povray         --      0.875         -- S
  454.calculix                                NR
  459.GemsFDTD                                NR
  465.tonto                                   NR
  470.lbm            --      1.54          -- S
  481.wrf                                     NR
  482.sphinx3        --      1.22          -- S
   Est. SPECfp_base2006                    --
   Est. SPECfp2006                                                    Not Run
  ```
  
- **Base** 和 **Peak** 的区别在于编译器优化选项的使用：
  - **Base**：
    - 使用一组严格限制的编译器优化选项。
    - 所有基准测试必须使用相同的优化设置。
    - 更强调结果的可重复性和一致性。
  - **Peak**：
    - 可以为每个基准测试单独选择优化选项。
    - 更倾向于追求单个基准的最佳性能。
    - 优化灵活性更高，但可能降低可重复性。
  
- **完整解释表格**
  
  | **字段**          | **解释**                                                                                     |
  |--------------------|---------------------------------------------------------------------------------------------|
  | **Base Ref.**      | 官方标准硬件在 Base 配置下的参考运行时间。                                                    |
  | **Base Run Time**  | 你的系统在 Base 配置下实际运行该基准所需的时间。                                              |
  | **Base Ratio**     | 性能比值，`Base Ref. / Base Run Time`，数值越大表示性能越好。                                  |
  | **Peak Ref.**      | 官方标准硬件在 Peak 配置下的参考运行时间（一般等于 Base Ref.）。                               |
  | **Peak Run Time**  | 你的系统在 Peak 配置下实际运行该基准所需的时间。                                              |
  | **Peak Ratio**     | 性能比值，`Peak Ref. / Peak Run Time`，数值越大表示性能越好。                                  |
 
