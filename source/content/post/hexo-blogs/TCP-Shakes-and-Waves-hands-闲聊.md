---
title: TCP Shakes/Waves Hands 闲谈
date: 2022-11-07 13:58:17
tags:
    - 网络
    - TCP
    - 对话
---

<div align="right"><div style="width: 60%; border-style: solid; border-width: 1px; border-radius: 16px; position: relative; padding:30px; text-align:center">
面试官你好，请问面试可以开始了吗
</div></div><br/>

<!--more-->
 

<div align="left"><div style="width: 60%; border-style: solid; border-width: 1px; border-radius: 16px; position: relative; padding:30px; text-align:center">
嗯，开始吧
</div></div><br/>

 

<div align="left"><div style="width: 60%; border-style: solid; border-width: 1px; border-radius: 16px; position: relative; padding:30px; text-align:center">
今天来聊聊TCP吧，TCP的各个状态还有印象吗？
</div></div><br/>

 

<div align="right"><div style="width: 60%; border-style: solid; border-width: 1px; border-radius: 16px; position: relative; padding:30px; text-align:center">
还有些许印象的，要不我就来简单说下TCP的三次握手和四次挥手的流程吧
</div></div><br/>

 

<div align="right"><div style="width: 60%; border-style: solid; border-width: 1px; border-radius: 16px; position: relative; padding:30px; text-align:center">
说完这两个流程，就能把TCP的状态给涵盖上了
</div></div><br/>

 

<div align="left"><div style="width: 60%; border-style: solid; border-width: 1px; border-radius: 16px; position: relative; padding:30px; text-align:center">
可以
</div></div><br/>

 

<div align="right"><div style="width: 60%; border-style: solid; border-width: 1px; border-radius: 16px; position: relative; padding:30px; text-align:center">
在说TCP的三次握手和四次挥手之前，我先给你画下TCP的头部格式呗（：
</div></div><br/>

 

<div align="right"><div style="width: 80%; border-style: solid; border-width: 1px; border-radius: 16px; position: relative; padding:30px; text-align:center">
<img src="https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/008i3skNgy1gvbm49hyr9j60u00vvn2o02.jpg"/>
</div></div><br/>

 

<div align="right"><div style="width: 60%; border-style: solid; border-width: 1px; border-radius: 16px; position: relative; padding:30px; text-align:center">
对于TCP三次握手和四次挥手，我们最主要的就是关注TCP头部的序列号、确认号以及几个标记位（SYN/FIN/ACK/RST）
</div></div><br/>

 

<div align="right"><div style="width: 60%; border-style: solid; border-width: 1px; border-radius: 16px; position: relative; padding:30px; text-align:center">
序列号：在初次建立连接的时候，客户端和服务端都会为「本次的连接」随机初始化一个序列号。（纵观整个TCP流程中，序列号可以用来解决网络包乱序的问题）
</div></div><br/>

 

<div align="right"><div style="width: 60%; border-style: solid; border-width: 1px; border-radius: 16px; position: relative; padding:30px; text-align:center">
确认号：该字段表示「接收端」告诉「发送端」对上一个数据包已经成功接收（确认号可以⽤来解决网络包丢失的问题）
</div></div><br/>

 

<div align="right"><div style="width: 60%; border-style: solid; border-width: 1px; border-radius: 16px; position: relative; padding:30px; text-align:center">
而标记位就很好理解啦。SYN为1时，表示希望创建连接。ACK为1时，确认号字段有效。FIN为1时，表示希望断开连接。RST为1时，表示TCP连接出现异常，需要断开
</div></div><br/>

 

<div align="right"><div style="width: 80%; border-style: solid; border-width: 1px; border-radius: 16px; position: relative; padding:30px; text-align:center">
<img src="https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/008i3skNgy1gvbm4ptqt5j60v00h0q6x02.jpg"/>
</div></div><br/>

 

<div align="right"><div style="width: 60%; border-style: solid; border-width: 1px; border-radius: 16px; position: relative; padding:30px; text-align:center">
下面就先从三次握手开始吧，期间我也会在三次握手中涉及到的TCP状态也说下的
</div></div><br/>

 

<div align="right"><div style="width: 60%; border-style: solid; border-width: 1px; border-radius: 16px; position: relative; padding:30px; text-align:center">
TCP三次握手的过程其实就是在：确认通信双方（客户端和服务端）的序列号
</div></div><br/>

 

<div align="right"><div style="width: 80%; border-style: solid; border-width: 1px; border-radius: 16px; position: relative; padding:30px; text-align:center">
<img src="https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/008i3skNgy1gvbpkkvctrj614e0diq4702.jpg"/>
</div></div><br/>

 

<div align="right"><div style="width: 60%; border-style: solid; border-width: 1px; border-radius: 16px; position: relative; padding:30px; text-align:center">
它的过程是这样的
</div></div><br/>

 

<div align="right"><div style="width: 60%; border-style: solid; border-width: 1px; border-radius: 16px; position: relative; padding:30px; text-align:center">
在最开始的时候，客户端和服务端都处于 CLOSE 状态
</div></div><br/>

 

<div align="right"><div style="width: 60%; border-style: solid; border-width: 1px; border-radius: 16px; position: relative; padding:30px; text-align:center">
服务器主动监听某个端口，处于 LISTEN 状态
</div></div><br/>

 

<div align="right"><div style="width: 60%; border-style: solid; border-width: 1px; border-radius: 16px; position: relative; padding:30px; text-align:center">
客户端会随机生成出序列号（这里的序列号一般叫做client_isn），并且把标志位设置为SYN（意味着要连接），然后把该报文发送给服务端
</div></div><br/>

 

<div align="right"><div style="width: 60%; border-style: solid; border-width: 1px; border-radius: 16px; position: relative; padding:30px; text-align:center">
客户端发送完SYN报文以后，自己便进入了 SYN_SEND 状态
</div></div><br/>

 

<div align="right"><div style="width: 80%; border-style: solid; border-width: 1px; border-radius: 16px; position: relative; padding:30px; text-align:center">
<img src="https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/008i3skNgy1gvbpqrtuz5j61ai0ggjsz02.jpg"/>
</div></div><br/>

 

<div align="right"><div style="width: 60%; border-style: solid; border-width: 1px; border-radius: 16px; position: relative; padding:30px; text-align:center">
服务端接收到了客户端的请求之后，自己也初始化对应的序列号（这里的序列号一般叫做 server_isn）
</div></div><br/>

 

<div align="right"><div style="width: 60%; border-style: solid; border-width: 1px; border-radius: 16px; position: relative; padding:30px; text-align:center">
在「确认号」字段里填上client_isn + 1（相当于告诉客户端，已经收到了发送过来的序列号了） ，并且把 SYN 和 ACK 标记位都点亮(置为1)
</div></div><br/>

 

<div align="right"><div style="width: 60%; border-style: solid; border-width: 1px; border-radius: 16px; position: relative; padding:30px; text-align:center">
把该报文发送客户端，服务端的状态变成 SYN-REVD 状态
</div></div><br/>

 

<div align="right"><div style="width: 80%; border-style: solid; border-width: 1px; border-radius: 16px; position: relative; padding:30px; text-align:center">
<img src="https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/008i3skNgy1gvcuaebe9oj619g0u0gox02.jpg"/>
</div></div><br/>

 

<div align="right"><div style="width: 60%; border-style: solid; border-width: 1px; border-radius: 16px; position: relative; padding:30px; text-align:center">
客户端收到服务端发送的报文后，就知道服务端已经接收到了自己的序列号（通过确认号就可以知道），并且接收到了服务端的序列号(server_isn)
</div></div><br/>

 

<div align="right"><div style="width: 60%; border-style: solid; border-width: 1px; border-radius: 16px; position: relative; padding:30px; text-align:center">
此时，客户端需要告诉服务端自己已经接收到了他发送过来的序列号，所以在「确认号」字段上填上server_isn+1，，并且标记位 ACK 为1
</div></div><br/>

 

<div align="right"><div style="width: 80%; border-style: solid; border-width: 1px; border-radius: 16px; position: relative; padding:30px; text-align:center">
<img src="https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/008i3skNgy1gvcueanzntj61380u0adr02.jpg"/>
</div></div><br/>

 

<div align="right"><div style="width: 60%; border-style: solid; border-width: 1px; border-radius: 16px; position: relative; padding:30px; text-align:center">
客户端在发送报文之后，进入 ESTABLISHED 状态，而服务端接收到客户端的报文之后，也进入 ESTABLISHED 状态
</div></div><br/>

 

<div align="right"><div style="width: 60%; border-style: solid; border-width: 1px; border-radius: 16px; position: relative; padding:30px; text-align:center">
这就是三次握手的过程以及涉及到的TCP状态
</div></div><br/>

 

<div align="right"><div style="width: 60%; border-style: solid; border-width: 1px; border-radius: 16px; position: relative; padding:30px; text-align:center">
总结下来，就是双方都把自身的序列号发给对方，看对方能不能接收到。如果「确认可以」，那就可以正常通信。（三次握手这个过程就可以看到双方都有接收和发送的能力）
</div></div><br/>

 

<div align="left"><div style="width: 60%; border-style: solid; border-width: 1px; border-radius: 16px; position: relative; padding:30px; text-align:center">
那两次握手行吗？
</div></div><br/>

 

<div align="right"><div style="width: 60%; border-style: solid; border-width: 1px; border-radius: 16px; position: relative; padding:30px; text-align:center">
两次握手只能保证客户端的序列号成功被服务端接收，而服务端是无法确认自己的序列号是否被客户端成功接收。所以是不行的（：
</div></div><br/>

 

<div align="left"><div style="width: 60%; border-style: solid; border-width: 1px; border-radius: 16px; position: relative; padding:30px; text-align:center">
了解了，那我想问问序列号为什么是随机的？以及序列号是怎么生成的？
</div></div><br/>

 

<div align="right"><div style="width: 60%; border-style: solid; border-width: 1px; border-radius: 16px; position: relative; padding:30px; text-align:center">
一方面为了安全性（随机ISN能避免非同一网络的攻击），另一方面可以让通信双方能够根据序号将「不属于」本连接的报文段丢弃
</div></div><br/>

 

<div align="right"><div style="width: 60%; border-style: solid; border-width: 1px; border-radius: 16px; position: relative; padding:30px; text-align:center">
序列号怎么生成的？这…随便猜下就应该跟「时钟」和TCP头部的某些属性做运算生成的吧，类似于雪花算法（：具体我忘了
</div></div><br/>

 

<div align="left"><div style="width: 60%; border-style: solid; border-width: 1px; border-radius: 16px; position: relative; padding:30px; text-align:center">
既然网络是不可靠的，那建立连接不是会经过三次握手吗？那要是在中途丢了，怎么办？
</div></div><br/>

 

<div align="right"><div style="width: 60%; border-style: solid; border-width: 1px; border-radius: 16px; position: relative; padding:30px; text-align:center">
假设第一个包丢了，客户端发送给服务端的 SYN 包丢了（简而要之就是服务端没接收到客户端的SYN包）
</div></div><br/>

 

<div align="right"><div style="width: 60%; border-style: solid; border-width: 1px; border-radius: 16px; position: relative; padding:30px; text-align:center">
客户端迟迟收不到服务端的ACK包，那会周期性超时重传，直到收到服务端的ACK
</div></div><br/>

 

<div align="right"><div style="width: 60%; border-style: solid; border-width: 1px; border-radius: 16px; position: relative; padding:30px; text-align:center">
假设第二个包丢了，服务端发送的SYN+ACK包丢了（简而要之就是客户端没接收到服务端的SYN+ACK包）
</div></div><br/>

 

<div align="right"><div style="width: 60%; border-style: solid; border-width: 1px; border-radius: 16px; position: relative; padding:30px; text-align:center">
服务端迟迟收不到客户端的ACK包，那会周期性超时重传，直到收到客户端的ACK
</div></div><br/>

 

<div align="right"><div style="width: 60%; border-style: solid; border-width: 1px; border-radius: 16px; position: relative; padding:30px; text-align:center">
假设第三个包丢了（ACK包），客户端发送完第三个包后单方面进入了 ESTABLISHED 状态，而服务端也认为此时连接是正常的，但第三个包没到达服务端
</div></div><br/>

 

<div align="right"><div style="width: 60%; border-style: solid; border-width: 1px; border-radius: 16px; position: relative; padding:30px; text-align:center">
一、如果此时客户端与服务端都还没数据发送，那服务端会认为自己发送的SYN+ACK的包没发送至客户端，所以会超时重传自己的SYN+ACK包
</div></div><br/>

 

<div align="right"><div style="width: 60%; border-style: solid; border-width: 1px; border-radius: 16px; position: relative; padding:30px; text-align:center">
二、如果这时候客户端已经要发送数据了，服务端接收到了ACK + Data数据包，那自然就切换到 ESTABLISHED 状态下，并且接收客户端的Data数据包
</div></div><br/>

 

<div align="right"><div style="width: 60%; border-style: solid; border-width: 1px; border-radius: 16px; position: relative; padding:30px; text-align:center">
三、如果此时服务端要发送数据了，但发送不了，会一直周期性超时重传SYN + ACK，直到接收到客户端的ACK包
</div></div><br/>

 

<div align="right"><div style="width: 80%; border-style: solid; border-width: 1px; border-radius: 16px; position: relative; padding:30px; text-align:center">
<img src="https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/008i3skNgy1gvdzs5pbp3j60so048dgb02.jpg"/>
</div></div><br/>

 

<div align="left"><div style="width: 60%; border-style: solid; border-width: 1px; border-radius: 16px; position: relative; padding:30px; text-align:center">
嗯，是不是要讲下四次挥手了？
</div></div><br/>

 

<div align="right"><div style="width: 60%; border-style: solid; border-width: 1px; border-radius: 16px; position: relative; padding:30px; text-align:center">
嗯，在建立完连接之后，客户端和服务端双方都处于 ESTABLISHED 状态
</div></div><br/>

 

<div align="right"><div style="width: 60%; border-style: solid; border-width: 1px; border-radius: 16px; position: relative; padding:30px; text-align:center">
断开连接双方都有权利的，下面我还是以客户端主动断开为例好啦
</div></div><br/>

 

<div align="right"><div style="width: 60%; border-style: solid; border-width: 1px; border-radius: 16px; position: relative; padding:30px; text-align:center">
客户端打算关闭连接，会发 FIN 报文给服务端（其实就是把标志位 FIN 点亮），客户端发送完之后，就进入FIN_WAIT_1状态
</div></div><br/>

 

<div align="right"><div style="width: 60%; border-style: solid; border-width: 1px; border-radius: 16px; position: relative; padding:30px; text-align:center">
服务端收到 FIN 报文之后，回复 ACK 报文给客户端（表示已经收到了），服务端发送完之后，就进入 CLOSE_WAIT 状态
</div></div><br/>

 

<div align="right"><div style="width: 60%; border-style: solid; border-width: 1px; border-radius: 16px; position: relative; padding:30px; text-align:center">
客户端接收到服务端的 ACK 报文，就进入了 FIN_WAIT_2 状态
</div></div><br/>

 

<div align="right"><div style="width: 80%; border-style: solid; border-width: 1px; border-radius: 16px; position: relative; padding:30px; text-align:center">
<img src="https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/008i3skNgy1gvdztfq4shj61as0jw0uz02.jpg"/>
</div></div><br/>

 

<div align="right"><div style="width: 60%; border-style: solid; border-width: 1px; border-radius: 16px; position: relative; padding:30px; text-align:center">
这时候，服务器可能还有数据要发送给客户端，等服务端确认自己已经没有数据返回给客户端之后，就发送FIN报文给客户端了，自己进入 LAST_ACK 状态
</div></div><br/>

 

<div align="right"><div style="width: 60%; border-style: solid; border-width: 1px; border-radius: 16px; position: relative; padding:30px; text-align:center">
客户端收到服务端的FIN报文之后，回应ACK报文，自己进入 TIME_WAIT 状态
</div></div><br/>

 

<div align="right"><div style="width: 60%; border-style: solid; border-width: 1px; border-radius: 16px; position: relative; padding:30px; text-align:center">
服务端收到客户端的ACK报文之后，服务端就进入 CLOSE 状态
</div></div><br/>

 

<div align="right"><div style="width: 60%; border-style: solid; border-width: 1px; border-radius: 16px; position: relative; padding:30px; text-align:center">
客户端在TIME_WAIT等到2MSL，也进入了 CLOSE 状态
</div></div><br/>

 

<div align="right"><div style="width: 80%; border-style: solid; border-width: 1px; border-radius: 16px; position: relative; padding:30px; text-align:center">
<img src="https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/008i3skNgy1gvdzz74bokj614q0u0gp002.jpg"/>
</div></div><br/>

 

<div align="right"><div style="width: 60%; border-style: solid; border-width: 1px; border-radius: 16px; position: relative; padding:30px; text-align:center">
四次挥手的流程到这里就结束了，结合三次握手，TCP的各个状态也已经说完了
</div></div><br/>

 

<div align="left"><div style="width: 60%; border-style: solid; border-width: 1px; border-radius: 16px; position: relative; padding:30px; text-align:center">
嗯嗯，刚聊完四次挥手嘛，那你觉得为什么是四次呢？
</div></div><br/>

 

<div align="right"><div style="width: 60%; border-style: solid; border-width: 1px; border-radius: 16px; position: relative; padding:30px; text-align:center">
其实很好理解，当客户端第一次发送 FIN 报文之后，只是代表着客户端不再发送数据给服务端，但此时客户端还是有接收数据的能力的。而服务端收到FIN报文的时候，可能还有数据要传输给客户端，所以只能先回复 ACK给客户端
</div></div><br/>

 

<div align="right"><div style="width: 60%; border-style: solid; border-width: 1px; border-radius: 16px; position: relative; padding:30px; text-align:center">
等到服务端不再有数据发送给客户端时，才发送 FIN 报文给客户端，表示可以关闭了
</div></div><br/>

 

<div align="right"><div style="width: 60%; border-style: solid; border-width: 1px; border-radius: 16px; position: relative; padding:30px; text-align:center">
所以，一来一回就四次了
</div></div><br/>

 

<div align="right"><div style="width: 80%; border-style: solid; border-width: 1px; border-radius: 16px; position: relative; padding:30px; text-align:center">
<img src="https://raw.githubusercontent.com/HCY-ASLEEP/picture-bed/main/picture-bed/008i3skNgy1gve15uf3m1j60ze04gwf802.jpg"/>
</div></div><br/>

 

<div align="left"><div style="width: 60%; border-style: solid; border-width: 1px; border-radius: 16px; position: relative; padding:30px; text-align:center">
从四次挥手的流程上来看，有个 TIME_WAIT 状态，你知道这个状态干什么用的吗？（等待 2MSL）
</div></div><br/>

 

<div align="right"><div style="width: 60%; border-style: solid; border-width: 1px; border-radius: 16px; position: relative; padding:30px; text-align:center">
主要有两个原因吧。1.保证最后的 ACK 报文 「接收方」一定能收到（如果收不到，对方会 重发 FIN 报文）2. 确保在创建新连接时，先前网络中残余的数据都丢失了
</div></div><br/>

 

<div align="right"><div style="width: 60%; border-style: solid; border-width: 1px; border-radius: 16px; position: relative; padding:30px; text-align:center">
其实也比较好理解的。就正如我们重启服务器一样，会先优雅关闭各种资源，再留有一段时间，希望在这段时间内，资源是正常关闭的，这样重启服务器（或者发布）就基本认为不会影响到线上运行了
</div></div><br/>

 

<div align="left"><div style="width: 60%; border-style: solid; border-width: 1px; border-radius: 16px; position: relative; padding:30px; text-align:center">
假设 TIME_WAIT 状态多过会有什么危害？怎么解决呢？
</div></div><br/>

 

<div align="right"><div style="width: 60%; border-style: solid; border-width: 1px; border-radius: 16px; position: relative; padding:30px; text-align:center">
从流程上看， TIME_WAIT 状态 只会出现在 主动发起 关闭连接的一方。危害就是会占用内存资源和端口呗（毕竟在等待嘛），解决的话，有Linux参数可以设置，具体忘了额
</div></div><br/>

 

<div align="left"><div style="width: 60%; border-style: solid; border-width: 1px; border-radius: 16px; position: relative; padding:30px; text-align:center">
今天最后再问个问题吧，我们常说TCP连接，那这个连接到底是什么？你是怎么理解的？
</div></div><br/>

 

<div align="right"><div style="width: 60%; border-style: solid; border-width: 1px; border-radius: 16px; position: relative; padding:30px; text-align:center">
其实从三次握手可以发现的是，TCP建立连接无非就是交换了双方的状态（比如序列号）。然后就没有然后了…连接本质上「只是互相维持一个状态，有连接特性」
</div></div><br/>

 

<div align="left"><div style="width: 60%; border-style: solid; border-width: 1px; border-radius: 16px; position: relative; padding:30px; text-align:center">
好吧
</div></div><br/>


