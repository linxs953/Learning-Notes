## 传输层



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
  - 首要目标：同步序列号
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
    - server收到client的报文，状态变为CLOSE_WAIT
    - server发送ack=seq+1，调用close函数，发送FIN以及seq=client.seq，进入LAST_ACK
    - client收到server的ack，进入FIN_WAIT2，收到server的FIN以及seq，进入TIME_WAIT
    - client发送ack=server.seq+1，client会等待两分钟然后进入CLOSED
    - server收到client发送的ack，进入CLOSED状态




