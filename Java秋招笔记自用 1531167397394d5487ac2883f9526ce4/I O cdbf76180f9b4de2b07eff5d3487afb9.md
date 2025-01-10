# I/O

# I/O模型

1. 从计算机体系结构来说，I/O描述了计算机系统与外部设备之间的通信过程。
2. 从操作系统的角度来说，由程序对操作系统内核空间发起IO调用，具体的IO执行由操作系统来完成。
- **同步阻塞 IO 模型**中，应用程序发起 read 调用后，会一直阻塞，直到内核把数据拷贝到用户空间。当面对十万甚至百万级连接的时候，传统的 BIO 模型是无能为力的。因此，我们需要一种更高效的 I/O 处理模型来应对更高的并发量。
- **同步非阻塞 IO 模型**中，应用程序会一直发起 read 调用，等待数据从内核空间拷贝到用户空间的这段时间里，线程依然是阻塞的，直到在内核把数据拷贝到用户空间。应用程序不断进行 I/O 系统调用轮询数据是否已经准备好的过程是十分消耗 CPU 资源的。
- **IO 多路复用模型**中，线程首先发起 select 调用，询问内核数据是否准备就绪，等内核把数据准备好了，用户线程再发起 read 调用。read 调用的过程（数据从内核空间 -> 用户空间）还是阻塞的。IO 多路复用模型，通过减少无效的系统调用，减少了对 CPU 资源的消耗。
- **异步 IO 模型**是基于事件和回调机制实现的，也就是应用操作之后会直接返回，不会堵塞在那里，当后台处理完成，操作系统会通知相应的线程进行后续的操作。

BIO（Blocking I/O）、NIO（Non-blocking/New I/O）和AIO（Asynchronous I/O）？

- BIO是一种同步阻塞 IO 模型，客户端发起请求时会一直阻塞到处理完成；
- NIO是一种多路复用IO模型，只需要一个线程通过selector选择器管理多个客户端连接，客户端数据到了之后会为其服务；
- AIO是一种异步调用IO模型，客户端发起请求会直接返回，后台处理完成后，操作系统会通知进行后续操作。

# NIO

## 基础

- 支持面向缓冲区的、基于通道的IO操作，可以理解为非阻塞式IO；
- NIO相关的类放在java.nio包及其子包下，对原java.io包中很多类进行改写；
- 有三大核心部分：Channel通道、Buffer缓冲区、Selector选择器；
- 可以做到用一个线程处理多个操作。

### NIO和BIO的区别？

## 缓冲区Buffer

### 基本概念

Buffer类似于数组，可以保存多个相同数据类型的数据。

```java
Static xxxBuffer allocate(int capacity); // 创建一个容量为capacity的xxxBuffer对象
```

- capacity：Buffer的固定大小；
- limit：缓冲区中可以操作的数据的大小，写入时，限制等于buffer的容量；读时，等于写入的数据量。
- position：下一个读取或写入数据的索引；
- mark与reset：用mark()记录一个特定的position，可以通过reset()恢复到这个position。

### 常见方法

![Untitled](I%20O%20cdbf76180f9b4de2b07eff5d3487afb9/Untitled.png)

![Untitled](I%20O%20cdbf76180f9b4de2b07eff5d3487afb9/Untitled%201.png)

- 写入数据到Buffer；
- 调用flip()方法转换为读取模式；
- 从Buffer中读取数据；
- 调用buffer.clear()或者buffer.compact()清除缓冲区。

### 直接与非直接缓冲区

```java
// 申请直接内存的缓冲区，申请时比堆内存消耗更多的性能，但数据转移时很快
// 如果数据量很大，且生命周期又很长，或者频繁的IO操作，就比较适合使用直接内存
ByteBuffer buffer = ByteBuffer.allocateDirect(1024);
buffer.isDirect(); // true
```

## 通道Channel

- 通道表示IO源与目标打开的连接，本身不能访问数据，只能与Buffer交互；
- 类似于流，但可以同时进行读写，是双向的；
- Channel是个接口，主要实现包括：
    - FileChannel：读写文件；
    - DatagramChannel：通过UDP读写网络中的数据；
    - SocketChannel：通过TCP读写网络中的数据；
    - ServerSocketChannel：监听新进来的TCP连接，对于每一个新进来的连接都会创建一个SocketChannel。

### FileChannel

![Untitled](I%20O%20cdbf76180f9b4de2b07eff5d3487afb9/Untitled%202.png)

## 选择器Selector

- 选择器是SelectableChannel对象的多路复用器，选择器可以同时监控多个SelectableChannel的IO状况，即可使一个单独线程管理多个Channel。
- 选择器可以实现一个IO线程并发处理N个客户端的连接和读写操作，从根本上解决了传统同步阻塞IO一线程一连接的模型。

### 服务端

![Untitled](I%20O%20cdbf76180f9b4de2b07eff5d3487afb9/Untitled%203.png)

![Untitled](I%20O%20cdbf76180f9b4de2b07eff5d3487afb9/Untitled%204.png)

### 客户端

![Untitled](I%20O%20cdbf76180f9b4de2b07eff5d3487afb9/Untitled%205.png)