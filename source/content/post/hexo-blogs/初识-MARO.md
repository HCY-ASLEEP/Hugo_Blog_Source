---
title: 初识 MARO
date: 2022-10-26 10:20:02
tags:
    - MARO
---
### [MARO ，“Multi-Agent-Resource Optimization” ](https://github.com/microsoft/maro)<!--more-->，中文是“多代理资源优化” ，使用强化学习来解决资源调度的一个平台
**可以应用 MARO 的实际例子：**

- CIM,"Container Inventory Management",中文是“集装箱库存管理”。全球贸易里面会有很多港口，但是每个港口需要的空集装箱都不一样，有的港口可能空集装箱是剩余的（比如进口向港口），有的港口可能空集装箱不够（比如出口向港口）
	- 而使用 MARO 可以解决这个问题，使得每个港口尽可能分配到恰当的空集装箱资源，不多不少。
	- 在这个场景里面，空集装箱是中心资源，导致资源数量改变的事件有两个：
		- 第一个是 Order ，即订单，订单会导致货物从 source port 运到 destination port ， 这个时候 source port 出货，empty container 会减少，destination port 进货，empty container 会增加
		- 第二个是 repositioning，使用 MARO 重定位空集装箱，平衡全球空集装箱分布。
######

![](https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/cim.container_flow.svg)

- 对上面这个流程图的解释
	- 托运人（shipper）生成订单（send order）后，相应源端口（source port）的空容器将被释放（release empty）给托运人（shipper）
	- 托运人（shipper）将用货物装满集装箱，将其变成满载货物，然后在将满载货物的集装箱运回到（return laden）源港口（source port）
	- 船（vessel）到港口（source port）之后被装载满载货物的集装箱（load laden）
	- 船航行到进口港（source port），卸货（discharge laden）
	- 满载货物将被释放（release laden）给收货人（consignee），收货人将取出其中的货物，集装箱再次变空返回港口（return empty）
######
- 这个时候我们发现，整个过程我们还有五个部分没有提到，分别是 agent ，operate empty ，load/discharge empty 
	- 为了为了重新平衡集装箱分布，每个港口（port）的代理商（agent）将决定每次船舶（vessel）到达港口时如何重新分配（repostioning）空集装箱
		- 船只（vessel）到达港口时，是往船上装载空集装箱（load empty），还是消费船上原有的空集装箱（discharge empty）
		- 分配 load/discharge empty 的数量
######
- MARO 就是帮助 agent 调整 load/discharge empty 的数量，决策目标不仅要考虑自身未来的供需情况，还要考虑上下游港口的需求和情况
	- 出口导向型港口（例如中国的港口）显示出明显的高需求特征，通常需要额外的空集装箱供应，这些港口将倾向于从船上卸载空集装箱
	- 虽然以进口为导向的港口具有显著的盈余特征，但通常从收货人那里收到许多空集装箱，因此，如果存在空闲容量，面向进口的港口将倾向于将多余的空集装箱装入船舶
######
- **简单拓扑结构:**
![](https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/cim.toys.svg)
- 这里要特别注意上面这个图里面，这里的 S 和 D 并不是 source 和 destination，而是 empty containers 的 supplier 和 demander，这里 order 也并不是货物订单，而是 empty containers 的订单
- 上图实线表示货物流向，虚线表示订单流向，S 与 D 由订单（Order）决定，订单发起方为 D，订单收到方为 S
	- **拓扑（1）** 有四个 port ，D1 和 D2 是简单的需求者（需要额外 empty container 的端口），而 S2 是简单的供应商（具有剩余空容器的端口），尽管 S1 是一个简单的目标端口，但它位于两个服务路由的交点，这使其成为此拓扑中的特殊端口，为了实现全局最优，S1 必须学会区分服务路由并执行特定于服务路由的重新定位操作
	- **拓扑（2）** 中有五个端口，根据订单，D1 和 D2 是简单的需求者，而 S1 和 S2 是简单的供应商，作为服务航线交汇处的港口，T1港口虽然可以达到自平衡状态，但仍对全局最优起着重要作用，T1 的最佳重新定位策略是将多余的空容器从左侧服务路由转移到右侧服务路由，此外，D1 和 D2 应该学会只卸载它们需要的 empty 数量，并将多余的 empty 留给其他端口
	- **拓扑（3）** 中有六个端口，简单的需求者 D1 和 D2 ，简单的供应商 S1 和 S2 ，以及自平衡端口 T1 和 T2 ，比拓扑（2）更困难的是，应该采取更多的转移来将多余的空集装箱从最左边的服务路线重新定位到最右边的航线，这意味着需要一个涉及更多港口的多步骤解决方案
######
![](https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/cim.global_trade.svg)
- 这是基于真实世界数据设计的拓扑,大多数港口不再具有简单的供需功能。港口之间的合作要复杂得多，很难手动找到有效的重新定位策略
######
- **入门:**
- 安装
```python
pip install pymaro
```
- MARO 算法有两个关键步骤
- 算法决策事件 DecisionEventDecisionEvent
	- tick (int)：相应的刻度
	- port_idx（int）：需要响应环境的端口/代理的 ID
	- vessel_idx（int）：港口/代理人的船舶/操作对象的 ID
	- action_scope（操作范围）：操作范围有两个属性，load表示可以从船舶港口装载的最大数量，discharge表示从船舶到港口可以卸货的最大数量
	- early_discharge（int）：当船上的可用容量不足以装载满载物时，船上的一些空容器将被提前卸货以释放空间，由于满载而提前卸货的空容器数量记录在该字段中
- 行动 Action
	- none，这意味着什么都不做
	- a valid instance，有效实例：
	- vessel_idx（int）：港口/代理人的船舶/操作对象的 ID
	- port_idx（int）：执行此操作的端口/代理的 ID
	- action_type（操作类型）：在此操作中是装载还是卸载空容器
	- 数量（int）：要装载/卸载的空容器的（非负）数量
######
![](https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/maro_overview.svg)
- **上图是 MARO 框架图**
	- Simulation toolkit：它提供了一些预定义的场景，以及用于构建新场景的可重用轮子
	- RL toolkit：它为 RL 提供了全栈抽象，例如代理管理器、代理、RL 算法、学习器、参与者和各种塑造者
	- Distributed toolkit：提供分布式通信组件、消息自动处理、集群配置、作业编排等用户定义功能的接口
    
<br/>

