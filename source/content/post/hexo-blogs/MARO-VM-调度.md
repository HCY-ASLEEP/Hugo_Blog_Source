---
title: MARO VM 调度
date: 2022-10-27 11:01:57
tags:
    - MARO
---
### 背景<!--more-->
- 在云服务期间，用户请求具有一定数量资源的 **虚拟机（VM）（Virtual Mechine）**，例如中央处理器、内存等
- 假设一个特定的时间，VM 请求的数量和到达模式是固定的，给定一个物理资源有限的 **物理机（PM）（Physical Mechine）** 集群
- 不同的 VM 分配策略导致数据中心的成功完成量不同，运营成本也不同
- 对于云提供商，一个好的 VM 分配策略可以最大限度地提高资源利用率，从而可以通过向用户提供更多的 VM 来增加利润
- 对于云用户，良好的 VM 分配策略可以最大程度地缩短 VM 响应时间，并提供更好的使用体验

###### 

### 资源供需
<img src="https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/2022.10.27.20.00.43.png"/>

- 每个 **PM** 中的物理资源是中心资源，包括 **物理内核** 和 **内存**
	
	- VM 请求需要一定数量的 **物理资源** ，资源要求因不同的 VM 请求而异 
	- 只要指定的 PM 的剩余资源足够，**模拟器** 就会将 VM 分配到指定 PM ，VM 会在指定 PM 中创建 
	- VM 的资源利用率动态变化，PM 的实时能耗将在 **Runtime-Simulation（模拟器）** 中被模拟出来
	- VM 执行一段时间后完成其任务，**模拟器** 将释放分配给此 VM 的资源，并从 PM 中解除分配此 VM ，物理资源被释放，可以处理下一个 VM 请求 

<img src="https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/2022.10.28.15.14.52.png"/>


###### 

### VM Request

- MARO 和机器学习算法原理类似，需要 **样本数据** 训练出 **模型（找出当前场景的规律）**，再通过模型去 **预测** 怎样的行为更加正确符合实际
- VM scheduling 场景里面，**样本数据** 是 VM Request**s** ，样本数据从实际工作负荷中统一采样
- 只要原始数据集足够大，采样率不太小，采样的 VM Requests**（复数名词）** 就可以被认为遵循与原始请求类似的分布
- 一个 VM Request 包含 **VM 信息**（如 订阅 ID、部署 ID 和 VM 类别）、**VM 的所需资源**（包括所需的 CPU 核心数和内存）以及 **剩余缓冲时间（remaining buffer time）**

###### 

### VM 类型
- 交互式

	- 交互式 VM 通常需要较低的响应时间，因此设置此类 VM 只能分配给不可超额订阅的 PM 服务器  
###### 
- 延迟不敏感

	- 不区分延迟的 VM 通常用于批处理任务或开发工作负荷，可以将此类 VM 分配给可过度订阅的 PM 服务器

###### 

### VM 分配

- 根据 **有效的 PM 列表** ，**模拟器记录的历史信息** 以及 **VM 的详细所需资源** ，**VM 调度器（决策代理）** 将根据其分配策略做出决策
- 两种有意义的操作

	- 将 **有效的 PM ID** 传送到模拟器
	- 推迟如果 **剩余缓冲区时间** 足够，则可以稍后将处理的 VM Request
	
###### 

### Oversubscription 超额订阅

- 考虑到各种服务级别，将物理机分为可超额订阅和非超额订阅的
- 所谓超额，就比如 10 个 VM 实际上只使用 7 个 PM （就是厂商为了省钱）
- 对于超额订阅，可以在 **config.yml** 中设置参数
- 在此场景，有两个资源可能被超额订阅，CPU 和 内存，因可以设置这两个的最大超额订阅率

	- **MAX_CPU_OVERSUBSCRIPTION_RATE** ，CPU 的超额订阅率，默认设置为 1.15 ，意味着每个 PM 最多可以分配其资源容量的 1.15 倍 
	
	- **MAX_MEM_OVERSUBSCRIPTION_RATE** ，内存的超额订阅率，与 CPU 的类似

- 为了保护 PM 免受过载的影响，需要考虑 CPU 利用率 ，MAX_UTILIZATION_RATE 被用作安全机制

	- **MAX_UTILIZATION_RATE** ，默认设置为 1，这意味着在筛选有效 PM 时，允许的最大物理 CPU 使用率为 100% 

###### 

### Runtime Simulation

- #### 动态利用率

	- 为了使模拟环境最接近真实情况，MARO 模拟每个 VM 的资源利用率（当前仅为 CPU 利用率）
	- **模拟的** VM CPU 利用率根据**实际的** VM 工作负载读数而变化
	- MARO 还将根据每个 PM 中的**实时** VM 定期更新**实时**资源利用率
	
- #### 实时能耗

	- 不同的 VM 分配会导致 PM 集群的能耗不同，MARO 还根据 CPU 利用率模拟（计算）能耗
		
		- 能耗曲线
			
			- 这个非线性曲线反映了 CPU 利用率 与 能耗 的关系，用于模拟（计算）能耗
	<img src="https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/vm.energy_curve.svg"/>

###### 

### Overload

- 由于 VM 的 CPU 使用率随时间而变化，因此在启用超额订阅时，VM 的 CPU 使用率之和可能会超过物理资源的容量，这种情况称为过载
	
	- 目前对于过载的情况，MARO 只支持**静默（杀死）所有虚拟机** 或 **仅记录过载时间**，在 config.yml 里面设置
	
		- **KILL_ALL_VMS_IF_OVERLOAD** 

			- 如果启用此操作，则一旦发生重载，将解除分配位于重载 PM 的**所有** VM
			- 考虑到过载的影响，MARO 仍然会计算高利用率的能耗，静默行动对 PM 利用率的影响将反映在下一次 tick 中 

###### 
- 无论是否启用终止所有 VM，过载 PM 的数量和过载 VM 的数量都会被计算
- 这两个指标是累积值，将被记录为环境指标

###### 

### VM 解除分配

- MARO 模拟器会定期检查每次 tick 中完成任务的虚拟机
- 完成的 VM 意味着它经历了一个完整的生命周期，已准备好终止，它所占用的资源最终将再次可用
- 然后，模拟器将释放已完成的 VM 的资源，并最终从 PM 中删除 VM

###### 

### Quick Start

- 准备两个 csv 文件 vm_table 和 cpu_readings_file
	
	- vm_table
		
		- **vm_id**:       int, 每个 vm 的id
		- **sub_id**:      int, subscription id（每个 vm 的订阅 id）
		- **deploy_id**:   int, 每个 vm 的部署 id
		- **timestamp**:   int, 每个 vm 的创建时间
		- **vm_deleted**:  int. 每个 vm 的删除时间
		- **vm_lifetime**: int, 每个 vm 的生存时间，Lifetime = deletion time - creation time (timestamp) + 1
		- **vm_category**: int, 目前有三种类型
			
			- Delay-Insensitive
			
				- 可能延迟的 VM 工作负荷，例如批处理任务或测试工作负荷
				- 可以将此类 VM 分配给可过度订阅的 PM 

			- Interactive

				- 交互式 VM 工作负荷，需要用户及时响应
				- 此类 VM 只能分配给不可超额订阅的 PM 

			- Unknown
				
				- 未知类型
				- 为避免过载，此类 VM 被视为交互式 VM，只能分配给不可超额订阅的 PM
	
	- cpu_readings_file 
		
		- **timestamp**:   int, 与 vm_table 中的 timestamp 匹配
		- **vm_id**: int, 与 vm_table 中的 vm_id 匹配
		- **cpu_utilization**: float, VM CPU 的利用率，以百分比单位 （%）存储


###### 

### 构建命令

- 将 CSV 数据集构建为 MARO 模拟器可以使用的二进制文件

	```
	# maro data build --meta $PATH_TO_META_FILE --file $PATH_TO_CSV_FILE  --output $PATH_TO_OUTPUT_FILE
	maro data build --meta ~/.maro/data/vm_scheduling/meta/vmtable.yml  --file ~/.maro/data/vm_scheduling/.build/azure.2019.10k/vmtable.bin --output $PWD/vmtable.bin
	```
	- --meta：必需，用于指定 meta file 的路径。默认情况下，meta file 位于
	
		```
		~/.maro/data/vm_scheduling/meta/
		```
	- --file：必需，用于指定源 CSV 数据文件的路径，如果需要多个源 CSV 数据文件，则可以在特定文件中列出源文件的所有完整路径，并使用 @ 符号指定这个特定文件
	- --output：必需，用于指定目标二进制文件的路径 

- 生成二进制文件之后，在 topologies 目录下的 config.yml 中指定 VM_TABLE 和 CPU_READINGS 的直接路径

###### 

### Environment Interface

- #### DecisionPayload
	
	- 一旦环境需要代理的响应来促进模拟，它就会抛出一个带有 DecisionPayload 的 PendingDecision 事件
	- DecisionPayload 包含以下信息
		
		- **valid_pms (List[int])** ：被视为有效的 PM ID 列表（其 CPU 和内存资源足以满足传入的 VM 请求） 
		- **vm_id (int)** ：传入的 VM Request（正在等待分配的 VM Request）的 vm_id ，
		- **vm_cpu_cores_requirement (int)** ：传入的 VM Request 的 CPU 内核数量
		- **vm_memory_requirement (int)** ：传入的 VM Request 请求的内存资源大小
		- **remaining_buffer_time（int）** ：当使用 remaining_buffer_time 时，VM Request 将被视为失败，可以在 config.yml 里面设置

- #### Action
	
	- 从环境中获取 PendingDecisionAction 事件后，代理应使用 Action 进行响应，以下是有效的 Action
		
		- **None**：除了忽略此 VM Request 之外什么都不执行
		- **AllocateAction**：VM 的创建时间将固定在它收到这个 Request 的 tick 处，模拟器将更新目标 PM 的工作负载（CPU 核心数量，内存和能耗），这个 Action 包括:
			
			- **vm_id(int)**：等待分配资源的 VM 的 ID
			- **pm_id(int)**：计划将 VM 分配到的 PM 的 ID
		
		- **PostponeAction**：计算 remaining buffer time，这个 Action 包括：
			
			- **vm_id (int)** ：等待分配的 VM 的 ID
			- **postpone_step（int）**：分配要推迟的次数，单位是 DELAY_DURATION ，1 表示延迟 1 DELAY_DURATION ，可以在 config.yml 中设置
			- 如果时间仍然足够，模拟器将重新生成一个新的请求事件，新需求事件的 仅在剩余缓冲时间上与旧事件不同
			- 如果时间用完，模拟器会将其记录为失败的分配
			 
###### 

### Example

```python
import random

from maro.simulator import Env
from maro.simulator.scenarios.vm_scheduling import AllocateAction, DecisionPayload, PostponeAction

# Initialize an Env for vm_scheduling scenario
# 初始化环境
env = Env(
  scenario="vm_scheduling",     
  topology="azure.2019.10k",    
  start_tick=0,                
  durations=8638,              
  snapshot_resolution=1
)

# 初始化变量，声明类型，":"用于声明类型
metrics: object = None
decision_event: DecisionPayload = None
is_done: bool = False
action: AllocateAction = None

# Start the env with a None Action
# 开始模拟
metrics, decision_event, is_done = env.step(None)

while not is_done:
    valid_pm_num: int = len(decision_event.valid_pms)
	# 作出决策
    if valid_pm_num <= 0:
        # No valid PM now, postpone.
		# 没有可用的 PM ，推迟分配
        action: PostponeAction = PostponeAction(
            vm_id=decision_event.vm_id,
            postpone_step=1
        )
    else:
        # Randomly choose an available PM.
		# 有可用的 PM ，随机选一个 PM 与 VM 绑定
        random_idx = random.randint(0, valid_pm_num - 1)
        pm_id = decision_event.valid_pms[random_idx]
        action: AllocateAction = AllocateAction(
            vm_id=decision_event.vm_id,
            pm_id=pm_id
        )
	
	# 采取行动
    metrics, decision_event, is_done = env.step(action)

print(f"[Random] Topology: azure.2019.10k. Total ticks: 8638. Start tick: 0")
print(metrics)
```




<br/>

