## arp

- 工作在L2层之上，通过ip地址广播获取mac地址的协议
- 采用request-reply形式
  - request：who is 10.0.0.2? tell 10.0.0.1
  - reply: 10.0.0.2 is at [mac_address]



## rarp

- 工作在L2层之上，通过mac地址广播获取ip地址的协议，场景： 局域网内加入一台新设备，比如打印机，有mac地址，需要路由器给它分配一个ip地址
- 采用request-reply形式
  - request：who is mac_address_2, tell mac_address_1
  - reply: mac_address_2 is at 10.0.1.10




