# Transport layer

传输层把数据传递服务从两台计算机之间扩展到了两个进程之间

## 传输服务

- 最终目标：向它的用户提供高效的、可靠的和成本有效的数据传输服务
- 用户：应用层的进程
- 完成这项工作的硬件或软件称为传输实体

传输层存在的作用：

- 用户对网络层的路由器没有控制权
- 传输层的存在使得传输服务有可能比网络服务更加可靠

### 传输服务原语

原语是传输层为应用提供的操作，即传输服务接口，包括

listen, connect, send, receive, disconnect

### Berkeley socket

Berkeley socket 是 TCP 所用的套接字原语，得到了广泛使用

socket, bind, listen, accept, connect, send, receive, close

## 传输协议的要素

- 传输协议需要处理错误控制、顺序性和流量控制等问题
- 与数据链路层协议不同，网络层
  - 必须显式地指定接收方的地址
  - 初始连接的建立过程非常复杂
  - 网络层存在潜在的存储容量
  - 传输层上存在大量并且数量可变的连接，且连接间的相互竞争造成连接可用宽带的波动

### 寻址

指定要连接到哪个应用进程上

- 端口(port)
- 传输服务访问点(TSAP, transport service access point)
- 网络服务访问点(NSAP, network service access point)

### 建立连接

- 传输实体发送一个 connection request，等待 connect accepted
- 处理网络可能出现的丢失、延迟、损坏和数据包重复
- 可能的解决方法：
   - 使用一次性传输地址
   - 连接发起方为每一个连接分配一个唯一标识符
   - 上述两种方法要求每个传输实体无限期地维护一定数量的历史信息。如果机器崩溃将失效
- 限制数据包的生存周期

广泛使用的方法：

- 核心：源端用序号作为段的标签，使得该段在T秒内不被重用
- 这样在任何给定的时间内只能出现一个给定序号的数据包，重复的数据包必须被接收方丢弃
- 具有相同序号的老数据包不会击败新数据包被接收方接受

#### 三次握手

要解决的问题：无法判断 CONNECTION REQUEST 段中包含的那个初始序号是否与当前连接的序号重复

- 主机1选择一个序号x，并且发送一个包含x的 CONNECTION REQUEST 段给主机2
- 主机2回应一个 ACK 段作为对x的确认，并宣告他自己的初始序号y
- 主机1在它发送的第一个数据段中，对主机2选择的初始序号进行确认

### 连接释放

- 两军对垒问题：不存在解决的协议

- 将双方协商释放连接的需求推给传输层的用户

### 差错控制和流量控制

## 拥塞控制

要解决的问题：如果许多机器上的传输实体以太快的速度发送太多的数据包，就会使网络不堪重负变得拥塞，继而数据包被延迟和丢失，导致网络性能下降
