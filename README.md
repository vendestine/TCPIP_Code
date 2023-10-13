## Chapter1：理解网络编程和套接字

### 网络编程是什么，socket是什么
网络编程就是编写程序 使两台连网的计算机相互交换数据
所以需要物理连接 和 编写数据传输软件 
对于编写数据传输软件，操作系统会提供名为socket的部件，所以socket就是网络数据传输的软件设备


### TCP socket
> tcp socket可以类比成telephone 

#### 服务端socket
function: 处理连接请求
也可以称呼为 接电话socket，lisenting socket，主要步骤就是模拟接电话的行为

1. 调用socket函数创建socket (安装telephone)
2. 调用bind函数分配id地址和端口号（分配电话号码）
3. 调用listen函数将socket转化为可接受 连接请求 状态 （连接电话线）
4. 调用accept函数处理连接请求（接听电话）


#### 客户端socket
function：请求连接
也可以称呼为 打电话socket

1. 调用socket函数创建socket （安装电话）
2. 调用connect函数向服务端发送连接请求（拨打电话）

### Linux文件操作

#### 文件描述符
文件一般经过创建过程才会被分配文件描述符
0，1，2文件描述符默认分为输入输出对象stdin，stdout，stderr

为什么要用文件描述符？
首先文件信息非常多，为了方便称呼（标识）文件，操作系统会分配一个整数作为文件描述符

#### 文件操作
1. 打开文件
2. 关闭文件
3. 将数据写入文件
4. 读取文件中的数据


## Chapter2：套接字类型与协议设置

### 协议是什么
协议是对话中使用的通信规则，在计算机领域里，就是 计算机间对话的通信规则

### socket函数的参数
`int socekt(int domain, int type, int protocol);`

1. domain: socket中使用的协议族信息
2. type：socket数据传输类型
3. protocol: 协议

### 协议族 protocol family
socket中的协议有一些大的分类（包括很多子协议），这些大的分类成为 协议族

### socket类型 type
socket类型指的是 sokcet的数据传输方式
> 为什么已经确定协议族了，还需要确定数据传输方式
> 因为一个协议族中可能存在多种数据传输方式

#### 面向连接的socket （SOCK_STREM）
类似于传送带
1. 传输过程中数据不会丢失
2. 按序传输数据
3. 传输的数据不存在数据边界（boundary）


#### 面向消息的socket（SOCK_DGRAM）
类似于摩托车快递
1. 强调快速传输，不保证传输顺序
2. 传输的数据可能丢失也可能损坏
3. 传输的数据具有数据边界
4. 每次传输的数据大小有限制

### 协议参数
> 一般情况下，确定协议族和数据传输方式 即可确定协议，不需要第三个参数的话可以直接传0
> 有的时候仍然无法确定协议，所以需要第三个参数，协议protocol

IPv4协议族，SOCK_STREAM, 满足这两个条件的协议只有IPPROTO_TCP
`int tcp_socket - socket(PF_INET, SOCK_STREAM, IPPROTO_TCP)`

IPv4协议族，SOCK_DGRAM, 满足这两个条件的协议只有IPPROTO_UDP  
`int udp_socket - socket(PF_INET, SOCK_STREAM, IPPROTO_UDP)`


## Chapter3：地址族与数据序列

### IP地址
> 什么是ip，ip是internet protocol（网络协议）的简称

为使计算机（主机）连接到网络并收发数据，需要分配ip地址，ip地址分为两类IPv4和IPv6
IPv4：internet protocol version 4 |   4字节地址族
IPv6：internet protocol version 6 |   16字节地址族

详细探讨IPv4
ipv4有两部分，网络地址和主机地址，分为A,B,C,D,E等类型
A: 网络id 1字节 | 主机id 3字节
B：网络id 2字节 | 主机id 2字节
C: 网络id 3字节 | 主机id 1字节
D：多播ip地址

### 网络地址与主机地址

网络地址（网络id）区分网络，主机地址（主机id）区分内网里的主机
数据传输的时候，首先传到网络，网络（构成网络的路由器router或者交换机switch）接收到数据后，浏览主机地址后将数据传给目标主机、

> 构建网络需要一种物理设备 完成 外网与内网主机之间的数据交换，这种设备就是路由器或交换机
> 它们也是计算机，日常使用的计算机安装适当的软件，也可以用作交换机，交换机比路由器要简单一些

### 端口号
ip用于区分网络里的主机，但是主机可能接收多种数据，需要多个socket，用端口号区分
通过NIC（network interface card 网卡）接收的数据含有端口号，操作系统根据端口号，把数据传给相应端口的socket
数据传输接收地址，需要包含ip地址和端口号，这样数据才会被传输到对应的应用程序

