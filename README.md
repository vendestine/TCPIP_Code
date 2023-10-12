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


## Chapter2：
