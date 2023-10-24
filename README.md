## Chapter1：理解网络编程和套接字

### 网络编程是什么，socket是什么
网络编程就是编写程序 使两台连网的计算机相互交换数据
所以需要物理连接 和 编写数据传输软件 
对于编写数据传输软件，操作系统会提供名为socket的部件，所以socket就是网络数据传输的软件设备


### TCP socket
> tcp socket可以类比成telephone 

#### 服务端socket
function: 处理连接请求
也可以称呼为 接电话socket，listening socket，主要步骤就是模拟接电话的行为

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
`int socket(int domain, int type, int protocol);`

1. domain: socket中使用的协议族信息
2. type：socket数据传输类型
3. protocol: 协议

### 协议族 protocol family
socket中的协议有一些大的分类（包括很多子协议），这些大的分类成为 协议族

### socket类型 type
socket类型指的是 socket的数据传输方式
> 为什么已经确定协议族了，还需要确定数据传输方式
> 因为一个协议族中可能存在多种数据传输方式

#### 面向连接的socket （SOCK_STREAM）
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

### 地址信息

#### sockaddr 结构体 和 sockaddr_in 结构体

这两个结构体的作用都是**保存地址信息**
```
// 所有协议 地址信息
struct sockaddr {
    sa_family_t sin_family;   //地址族
    char sa_data[14];         //地址 ip + port
}

// 针对 IPv4协议 地址信息
struct sockaddr_in {
    sa_family_t     sin_family;   //地址族
    unit16_t        sin_port;     //16位TCP/UDP 端口号
    struct in_addr  sin_addr;     //32位IP地址
    char            sin_zero[8];  //对齐sockaddr
}

struct in_addr {
    In_addr_t s_addr;             //32位ipv4地址
}
```

#### bind函数
`int bind(int sockfd, struct sockaddr *myaddr, socklen_t addrlen);`

第二个参数需要sockaddr结构体的地址，里面包含了地址族，地址，端口号信息

Question 1：为什么不直接在sockaddr填充信息，而是在sockaddr_in填充信息然后传给sockaddr
ans: 因为sa_data没有填充的位需要置0，操作比较麻烦

Question 2: sockaddr_in保存的是ipv4协议地址信息，为什么还需要有成员变量sin_family
ans: sockaddr_in需要直接转化位sockaddr，内存需要对齐，所以需要有sin_family信息

### 网络字节序

CPU向内存存储数据的方式有两种
大端序（big endian）：高位字节存放到低位地址（从内存左边开始放，起始端是最大）
小端序（little endian）：高位字节存放到高位地址（从内存右边开始放，起始端是最小）

网络字节序是大端序
intel和amd cpu都采用小端序

Q: 如何判断该主机是小端序还是大端序
A: htons,htonl函数，转化字节序为网络字节序（大端序）,如果转化前后相等，说明是大端序，否则是小端序. 

Q:为什么转化为网络字节序后，小端序cpu访问数据不同？
A: 核心点：存储数据（写），访问数据（读）；
大端序 写从内存左边开始，读同样也是从左边开始；
小端序 写从内存右边开始，读同样也是从右边开始；

所以如果没有转化为网络字节序，写的时候原端序，读的时候原端序，写和读时数据一致；转化为网络字节序后，存储（写）的时候变成大端序
对于大端序来说，写读仍然都是大端序，写和读时数据一致
对于小端序来说，写是大端序，读是小端序，写和读时数据不一致

### 字符串转化为网络字节序的整数型
```
// 将字符串形式的ipv4地址 转化为 32位整数型数据
in_addr_t inet_addr(const char *string);

// 转化为32位整数型后，然后存入in_addr里
int inet_aton(const char *string, struct in_addr *addr);

// 32位整数型 转化为 ipv4地址
char *inet_ntoa(struct in_addr adr);  
//多次调用会覆盖之前的，所以调用后立马strcpy到其他变量
```

### ip地址初始化

标准写法
```
struct sockaddr_in addr;
char *serv_ip = "211.217.168.13";           //声明IP地址字符串
char *serv_port = "9190";                   //声明端口号字符串
memset(&addr, 0, sizeof(addr));             //结构体变量 addr 的所有成员初始化为0
addr.sin_family = AF_INET;                  //指定地址族
addr.sin_addr.s_addr = inet_addr(serv_ip);  //基于字符串的IP地址初始化
addr.sin_port = htons(atoi(serv_port));    //基于字符串的IP地址端口号初始化
```

INADDR_ANY
可以避免初始化ip地址，直接得到ip地址，但是注意一个主机可以有多个ip地址，取决于网卡数


