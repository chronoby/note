# 数据链路层

## 数据链路层的设计问题

数据链路层使用物理层提供的服务在通信信道上发送和接受比特，主要任务是将一个原始的传输设施变成一条没有漏检传输错误的线路。功能包括：

- 向网络层提供一个定义良好的服务接口
- 处理传输错误
- 调节数据流，确保慢速的接收方不会被快速的发送方淹没

发送方将输入的数据拆分成数据帧(data frame)。如果服务时可靠的，接收方必须确认正确收到的每一帧，即给发送方发挥同一个确认帧(acknowledgement frame)
### 提供给网络层的服务

- 无确认的无连接服务
- 有确认的无连接服务
- 有确认的有连接服务

### 成帧

数据链路层需要将物理层的比特流拆分成多个离散的帧，为每个帧计算一个成为校验和的端灵牌，并将该校验和放在帧中一起传输

- 字节计数法

用头部的一个字段表示改帧中的字符数

- 字节填充的标志字节法

每个帧用一些特殊的字节作为开始和结束

- 比特填充的标志比特法

发送方在数据中遇到连续五个1，便自动在输出的比特流中填入一个比特0

- 物理层编码违禁法

### 差错控制

确保所有的帧最终都被传递给了目标机器的网络层，并保持正确的顺序

- 提供反馈
  - Positive acknowledgement
  - Negative acknowledgement
- 提供超时计时器
- 对帧进行计数

### 流量控制

放置接收方被大量数据淹没

- 基于反馈的流量控制
- 基于速率的流量控制


## 差错检测和纠正

两种基本策略

- 在每个被发送的数据块中包含足够多的冗余信息，以便接收方据此推断出被发送的数据是什么，使用纠错码(error- correcting code)，适合不可靠的信道
- 包含冗余信息，是的接收方推断出是否发生了错误，使用了检错码(error-detecting code)，适合可靠的信道

### 纠错码

- 海明码

校验位数：2^r >= r + m + 1

- 二进制卷积码
- 里的所罗门码
- 低密度奇偶校验码

### 检错码

- 奇偶: 补1或0使得1的数目为奇数或偶数
- 校验和
- 循环冗余校验(CRC)

## 基本数据链路层协议

## 滑动窗口协议