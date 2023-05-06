---
title: MARO Distibuted Toolkit
date: 2022-11-04 18:15:29
tags:
    - MARO
---
### MARO 三层框架<!--more-->

![](https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/maro_overview.svg)

</br> 

**接下来展示的是第三层 Distibuted toolkit**

![](https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/overview.svg)

- MARO Distibuted Toolkit 遵循 message-passing 模式，即不同组件之间的协作基于消息**发送**和**接收**
- 典型的 **master/worker** 分布式程序通常包含以下步骤

	- master 会将任务（w/ or w/o data）发送到 worker
	- worker 将在其本地计算环境或本地设备中完成任务
	- worker 将计算结果返回到 master

    ![](https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/v2-b5f0db269480aceb6590007f8ad9dfe8_r.jpg)

	
- 根据实际需要，主控组件和工作组件之间的通信方式可以是同步的，也可以是异步的

</br> 

**关键部件**

![](https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/key_components.svg) 

- **Comunication**

	- 大致功能预览

		- 提供通用的消息传递接口
			
			- send, receive
			- broadcast
			- scatter 
			
		</br> 
        
		- 通信组件使用 **可替换** 的通信协议驱动程序来适应不同的通信协议栈
			
            - TCP/IP
            - InfiniBand
		
		</br> 
        
		- Peer Discovering
		
		- 部分故障恢复
		
		- 条件事件自动调度

	</br> 

	- **Proxy** 
		
		![](https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/proxy.svg)

		</br> 
		
		- Proxy 提供通信原语的实现，是通信操作接口，是通信组件的主要实体
		- Proxy 默认使用 ZeroMQ 框架
		- Proxy 为基于 Redis 的 peer discovering 提供支持
		- 分布式通信原语常见操作如下
		
			</br> 
			
			- **Broadcast**

				![](https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/v2-c9aa7762a6ec00d370c58de183441362_r.jpg)

                </br>

				![](https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/v2-1ff295f93679ebe9a03ad510259ead8b_r.jpg)

			</br>
			</br> 
			</br> 
			
			- **Scatter**

				![](https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/v2-be03c436a4f699aa001deb4490f33813_r.jpg)

                </br>
                
				![](https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/v2-f17bd118677f919e255d5b1689fc66dc_r.jpg)
                

			</br> 
			</br> 
			</br> 

			- **Reduce (强调聚合之后处理)**

                </br>

				![](https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/v2-466054a11a994842eb1b062b13b9bde3_r.png)

                </br>
				
				![](https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/v2-c7bdad601780f9798a62c2dfb1bbef4d_r.jpg)

                </br>

			- **Gather (单纯聚合没有额外处理)**

                </br>
                
				![](https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/v2-3b2ec50810fc8d92971a4b7c0b800b1b_r.jpg)

                </br>
                
				![](https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/v2-dc3fcf248c39b4a76947bcea140840d1_720w.webp)

                </br>

			- **All Reduce**
			
                </br>
                
				![](https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/v2-0e90f4c9b66d42dfa41145d3b6a52361_r.jpg)
			
			
                </br>
                
				![](https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/v2-80b1bd60a2fdefb19f792fdf193c6d76_r.jpg)

			</br> 

			- **All Gather**

                </br>
                
				![](https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/v2-ce5261aec55090a1f9e9dd5233b22af9_r.jpg)

				
                </br>
                
				![](https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/v2-831e0b04646c78f9e74bf4f29c35b8af_720w.webp)

			</br> 

			- **Reduce Scatter**

				![](https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/v2-14cdd631faae00452885a116dd36737c_720w.webp)

			</br>

			- **All to All**
			
				![](https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/v2-945ffd7612632fa88ed2bc68ec832071_r.jpg)

	</br> 

	- **Message**
	
		- 用于打包组件之间的通信内容，消息实例的主要属性包括
		
			- tag：自定义属性，可用于通过 **conditional event register table** 实现自动调度逻辑
			- source：message 发送者的别名
			- destination：message 接收者的别名
			- payload：用于远程函数调用的 Python 对象
			- session_id（自动生成）：特定会话的 UUID ，一个会话可能包含多条消息
			- message_id（自动生成）：特定消息的 UUID
			
		</br> 
	
		- Example
			
			```python
			from maro.communication import Message

			message = Message(tag="check_in",
			                  source="worker_001",
			                  destination="master",
			                  body="")
			```
	
	</br> 
    
	- **Session Message**

		- MARO 为常见的分布式场景提供了两种预定义的会话类型
		
			- **Task Session**
			
				- 存在 master 和 worker 关系
				- 用于描述从 master 发送到 worker 的 computing task
					
					- master 将 task 发送给 worker
					- 一旦 worker 收到 task ，worker 就开始执行 task
					- worker 将 computing result 返回给 master
			
			</br> 

			- **Notification Session**
			
				- sender 和 receiver 关系
				- 用于信息同步
					
					- sender 发送 notification message
					- receiver 接收 notification message
		
		</br> 

		- session 的每个阶段由 proxy 在内部维护
		- Example
        
		    ```python
		    from maro.communication import SessionMessage, SessionType
		    
		    task_message = SessionMessage(tag="sum",
		                                  source="master",
		                                  destination="worker_001",
		                                  body=[0, 1, 2, ...],
		                                  session_type=SessionType.TASK)
		    
		    notification_message = SessionMessage(tag="check_out",
		                                          source="worker_001",
		                                          destination="master",
		                                          body="",
		                                          session_type=SessionType.NOTIFICATION)
		    ``` 

	</br> 

	- **MARO 通信原语实际接口**

			
		- receive：用于持续接收消息
		receive_by_id：仅接收具有给定 session ID 的消息
		- send：单播，这是一种阻塞、一对一的发送模式，监视并收集来自远程对等方的回复消息   
		- isend：非阻塞版的 send ，将立即返回 message session ID，该 ID 可由  receive_by_id 使用
		- scatter：send 的高级版本，用于向 peer 发送消息，并监视和收集来自 peer 的回复消息，不是真正的多播，每条消息都会经过完整的 TCP/IP 堆栈（ZeroMQ driver），如果要发送的消息完全相同，并且想要更好的性能，请改用 broadcast 接口
		- iscatter：非阻塞版本的 scatter ，message session ID 将立即返回，可由 receive_by_id 使用
		- broadcast：阻塞，用于向所有订阅者广播消息，将监视并收集所有订阅者的回复消息
		- ibroadcast：非阻塞版本的 broadcast ，相关 message session ID 将立即返回，可供 receive_by_id 使用

	</br> 
	
	- **Conditional Event Register Table**
	
		- 提供消息自动发送机制
		- 通过将 conditional event 和相关的 handler function 注册到注册表中，当 conditional event 满足时，handler function 将与接收到的消息一起自动执行

		</br> 
		![](https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/register_table.register.svg)
		
		</br> 

		- conditional event 用于声明自动触发相关 handler function 所需的消息组
		- unit event 是条件事件中的最小组件，声明格式分三段
			
			- source：用于声明所需的消息源
			- tag：消息实例的属性
			- amount：所需的消息实例量

			```python
			unit_event_abs = "worker:update:10"

			unit_event_rel = "worker:update:60%"
			```

		- AND OR 操作支持更复杂的业务逻辑
			
			```python
			combined_event_and = ("worker_01:update:2",
                      "worker_02:update:3",
                      "AND")

			combined_event_or = ("worker_03:update:1",
			                      "worker_04:update:5",
			                      "OR")
			
			combined_event_mix = (("worker_01:update:2", "worker_02:update:3", "AND"),
			                      "worker_03:update:1",
			                      "OR")
			```

		- Handler function 是绑定到特定 conditional event 的用户定义的回调函数，当满足事件的条件时，相关消息将被发送到处理程序函数执行

			![](https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/register_table.trigger.svg)

			```python
			# A common handler function signature
			def handler(that, proxy, messages):
			    """
			        Conditional event handler function.
			
			        Args:
			            that: local instance reference, which needs to be operated.
			            proxy: the proxy reference for remote communication.
			            messages: received messages.
			    """
			    pass
			```

	</br> 

	- **Distributed Decorator**

		- 从本地函数类生成分布式 worker 类的帮助程序
			
			```python
			from maro.communication import dist, Proxy

			# Initialize proxy instance for remote communication.
			proxy = Proxy(group_name="master-worker",
			              component_type="worker",
			              expected_peers=[("master", 1)])
			
			# Declare the trigger condition of rollout event.
			rollout_event = "master:rollout:1"
			
			# Implement rollout event handler logic.
			def on_rollout(that, proxy, messages):
			    pass
			
			# Assemble event-handler directory.
			handler_dict = {rollout_event: on_rollout}
			
			# Convert a local functional class to a distributed one.
			@dist(proxy, handler_dict)
			class Worker:
			    def __init__(self):
			        pass
			```

	


