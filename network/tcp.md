## 传输层


## mtu 和 mss的区别

- mtu: 最大报文单位，局域网中的交换机定义的，和设备相关
- mss：tcp连接中的一个选项，在建立三次握手时，确定通信双方传输的最大报文长度



### tcp

- 发展历史
  - 1973，v1，包括ip功能的tcp协议，rfc675，没有分层
  - 1977，tcpv2
  - 1978，tcpv3，提出分层tcp和ip
  - 1980 tcpv4+ipv4


- 目标
  - 容错：丢失报文恢复
  - 支持多设备，数据链路很多不同设备接入
  - 传输效率，头部字节小


- 特点
  - 两方
  - 保证可靠性
  - 基于字节流的协议，tcp分包


- 流类型
  - 二进制流：以位作基本单位
  - 字节流：以一个字节作基本单位
  
- 解决问题
  - 每个报文需要有序号syn
  - 流量缓冲


- 建立连接
  - 首要目标：同步序列号
  - 确定窗口大小
  - 三次握手
    - client调用connect函数，内核发送SYN，sequence，此时client的状态变为SYNC_SENT
    - server收到client发送过来的报文，调用listen函数监听端口，server的状态变为SYNC_RECV，发送自己的SYN以及ack=client.seq+1
    - client收到server的ack之后，状态变为established，发送ack=server.seq+1给server
    - server收到之后变为established
  - 数据传输   
    - client调用write，发送报文，seq和ack
    - server调用read，获取报文，发送seq=ack+1表示收到
  - 四次挥手
    - client调用close函数，发送FIN以及seq，状态变为FIN_WAIT1
    - server收到client的报文，状态变为CLOSE_WAIT
    - server发送ack=seq+1，调用close函数，发送FIN以及seq=client.seq，进入LAST_ACK
    - client收到server的ack，进入FIN_WAIT2，收到server的FIN以及seq，进入TIME_WAIT
    - client发送ack=server.seq+1，client会等待两分钟然后进入CLOSED
    - server收到client发送的ack，进入CLOSED状态

- tcp调整读写缓冲区
  - 调整linux缓冲区
    - 自动调整开关
      - net.ipv4.tcp_moderate_rcvbuf=1
      - net.ipv4.tcp_mem = a b c(数值为int)
    - 自动调整读写缓冲区
      - net.ipv4.tcp_wmem = a b c(数值为int)
        - 默认数值是b，当调整开关为1时，表示开启，有多个tcp连接时，调整到c；如果并发多个连接，内存占用大，大于`net.ipv4.tcp_mem`的默认值(默认为b)时缓冲区调整为a，小于`net.ipv4.tcp_mem`时调整为c
      - net.ipv4.tcp_wmem = a b c(数值为int)
- 为什么需要拥塞控制算法
  - 滑动窗口仅仅只是用来防止把客户端和服务器打爆，所以需要提前确定通信双方的接收能力
  - 仅仅使用滑动窗口无法避免丢包
    - 双方通信的链路中有很多交换机和路由器设备，他们之间的带宽是不一样的
    - 如果没有拥塞机制控制发送的速度，那么拥塞就无法避免


- 慢启动与拥塞控制
  - 慢启动
    - RTO超时(tcp超时重传)
      - 发送一个包，超过一定时间没有返回ack，就会触发RTO，重新进入慢启动
    - 初始拥塞窗口
      - 查看当前窗口大小 `ss -nil | grep cwnd`
      - 修改当前窗口 `ip route  | while read r; do ip route change $r initcwnd 10;done`
    - 快速重传
      - 当接受方收到一个out of order的seq时，发送一个ack告诉发送方丢失了某个报文
      - 发送方连续收到三次这个ack时，重发这个丢失的报文，不用等到RTO超时
    - RTO和RTT计算方式
      - todo
  - 拥塞避免
    - 记忆拥塞避免阈值
      - 当出现rto超时时，会记录导致拥塞的当前值，然后进入慢启动，当到达这个阈值时，会一点一点往上加
  - 快速重传与快速恢复
    - 重传策略
      - SACK：选择性确认
        - net.ipv4.tcp_sack=1
        - 通过多个left_edge-right_edge，比如服务端发了这些已收到当序号[a,b],[c,d],[e,f]，那么在这三个数组之间缺失的序号需要重发

- 测量驱动的拥塞控制算法
  - 计算出带宽和每次发包的时延（只要不丢包，带宽都是不变的）
  - 当发现带宽不变，时延持续上升的时候，说明中间链路出现积压
    - 此时将当前发送的包个数设置为拥塞避免值


// todo
- tcp字符流对消息边界的影响
  - 传输多个有边界的消息时，本身的分包机制会影响上层消息的读写性能
