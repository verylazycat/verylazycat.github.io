---
title: BIO&NIO&AIO
tags: JAVA
---

Java 中的 BIO、NIO和 AIO 理解为是 Java 语言对操作系统的各种 IO 模型的封装。程序员在使用这些 API 的时候，不需要关心操作系统层面的知识，也不需要根据不同操作系统编写不同的代码。只需要使用Java的API就可以了

- #### **同步与异步**

1. **同步：** 同步就是发起一个调用后，被调用者未处理完请求之前，调用不返回。
2. **异步：** 异步就是发起一个调用后，立刻得到被调用者的回应表示已接收到请求，但是被调用者并没有返回结果，此时我们可以处理其他的请求，被调用者通常依靠事件，回调等机制来通知调用者其返回结果

同步和异步的区别最大在于异步的调用者不需要等待处理结果，被调用者会通过回调等机制来通知调用者其返回结果

- #### **阻塞和非阻塞**

1. **阻塞：** 阻塞就是发起一个请求，调用者一直等待请求结果返回，也就是当前线程会被挂起，无法从事其他任务，只有当条件就绪才能继续。
2. **非阻塞：** 非阻塞就是发起一个请求，调用者不用一直等着结果返回，可以先去干其他事情

- ## BIO (Blocking I/O)

同步阻塞I/O模式，数据的读取写入必须阻塞在一个线程内等待其完成

#### 传统 BIO

![BIO](/img/BIO.png)

采用 **BIO 通信模型** 的服务端，通常由一个独立的 Acceptor 线程负责监听客户端的连接。我们一般通过在 `while(true)` 循环中服务端会调用 `accept()` 方法等待接收客户端的连接的方式监听请求，请求一旦接收到一个连接请求，就可以建立通信套接字在这个通信套接字上进行读写操作，此时不能再接收其他客户端连接请求，只能等待同当前连接的客户端的操作执行完成， 不过可以通过多线程来支持多个客户端的连接，如上图所示。

如果要让 **BIO 通信模型** 能够同时处理多个客户端请求，就必须使用多线程（主要原因是 `socket.accept()`、 `socket.read()`、 `socket.write()` 涉及的三个主要函数都是同步阻塞的），也就是说它在接收到客户端连接请求之后为每个客户端创建一个新的线程进行链路处理，处理完成之后，通过输出流返回应答给客户端，线程销毁。这就是典型的 **一请求一应答通信模型** 。我们可以设想一下如果这个连接不做任何事情的话就会造成不必要的线程开销，不过可以通过 **线程池机制** 改善，线程池还可以让线程的创建和回收成本相对较低。使用`FixedThreadPool` 可以有效的控制了线程的最大数量，保证了系统有限的资源的控制，实现了N(客户端请求数量):M(处理客户端请求的线程数量)的伪异步I/O模型（N 可以远远大于 M），下面一节"伪异步 BIO"中会详细介绍到。

#### **我们再设想一下当客户端并发访问量增加后这种模型会出现什么问题？**

在 Java 虚拟机中，线程是宝贵的资源，线程的创建和销毁成本很高，除此之外，线程的切换成本也是很高的。尤其在 Linux 这样的操作系统中，线程本质上就是一个进程，创建和销毁线程都是重量级的系统函数。如果并发访问量增加会导致线程数急剧膨胀可能会导致线程堆栈溢出、创建新线程失败等问题，最终导致进程宕机或者僵死，不能对外提供服务。

- ## 伪异步 IO

为了解决同步阻塞I/O面临的一个链路需要一个线程处理的问题，后来有人对它的线程模型进行了优化一一一后端通过一个线程池来处理多个客户端的请求接入，形成客户端个数M：线程池最大线程数N的比例关系，其中M可以远远大于N.通过线程池可以灵活地调配线程资源，设置线程的最大值，防止由于海量并发接入导致线程耗尽

伪异步IO模型图

![伪异步io](/img/伪异步io.png)

采用线程池和任务队列可以实现一种叫做伪异步的 I/O 通信框架，它的模型图如上图所示。当有新的客户端接入时，将客户端的 Socket 封装成一个Task（该任务实现java.lang.Runnable接口）投递到后端的线程池中进行处理，JDK 的线程池维护一个消息队列和 N 个活跃线程，对消息队列中的任务进行处理。由于线程池可以设置消息队列的大小和最大线程数，因此，它的资源占用是可控的，无论多少个客户端并发访问，都不会导致资源的耗尽和宕机。

伪异步I/O通信框架采用了线程池实现，因此避免了为每个请求都创建一个独立线程造成的线程资源耗尽问题。不过因为它的底层任然是同步阻塞的BIO模型，因此无法从根本上解决问题。

在活动连接数不是特别高（小于单机1000）的情况下，这种模型是比较不错的，可以让每一个连接专注于自己的 I/O 并且编程模型简单，也不用过多考虑系统的过载、限流等问题。线程池本身就是一个天然的漏斗，可以缓冲一些系统处理不了的连接或请求。但是，当面对十万甚至百万级连接的时候，传统的 BIO 模型是无能为力的。因此，我们需要一种更高效的 I/O 处理模型来应对更高的并发量。

- ## NIO (New I/O)

NIO是一种同步非阻塞的I/O模型，在Java 1.4 中引入了NIO框架，对应 java.nio 包，提供了 Channel , Selector，Buffer等抽象。

NIO中的N可以理解为Non-blocking，不单纯是New。它支持面向缓冲的，基于通道的I/O操作方法。 NIO提供了与传统BIO模型中的 `Socket` 和 `ServerSocket` 相对应的 `SocketChannel` 和 `ServerSocketChannel` 两种不同的套接字通道实现,两种通道都支持阻塞和非阻塞两种模式。阻塞模式使用就像传统中的支持一样，比较简单，但是性能和可靠性都不好；非阻塞模式正好与之相反。对于低负载、低并发的应用程序，可以使用同步阻塞I/O来提升开发速率和更好的维护性；对于高负载、高并发的（网络）应用，应使用 NIO 的非阻塞模式来开发

-  NIO的特性/NIO与IO区别

#### Non-blocking IO（非阻塞IO）

**IO流是阻塞的，NIO流是不阻塞的。**

Java NIO使我们可以进行非阻塞IO操作。比如说，单线程中从通道读取数据到buffer，同时可以继续做别的事情，当数据读取到buffer中后，线程再继续处理数据。写数据也是一样的。另外，非阻塞写也是如此。一个线程请求写入一些数据到某通道，但不需要等待它完全写入，这个线程同时可以去做别的事情。

Java IO的各种流是阻塞的。这意味着，当一个线程调用 `read()` 或 `write()` 时，该线程被阻塞，直到有一些数据被读取，或数据完全写入。该线程在此期间不能再干任何事情了

#### Buffer(缓冲区)

**IO 面向流(Stream oriented)，而 NIO 面向缓冲区(Buffer oriented)。**

Buffer是一个对象，它包含一些要写入或者要读出的数据。在NIO类库中加入Buffer对象，体现了新库与原I/O的一个重要区别。在面向流的I/O中·可以将数据直接写入或者将数据直接读到 Stream 对象中。虽然 Stream 中也有 Buffer 开头的扩展类，但只是流的包装类，还是从流读到缓冲区，而 NIO 却是直接读到 Buffer 中进行操作。

在NIO厍中，所有数据都是用缓冲区处理的。在读取数据时，它是直接读到缓冲区中的; 在写入数据时，写入到缓冲区中。任何时候访问NIO中的数据，都是通过缓冲区进行操作。

最常用的缓冲区是 ByteBuffer,一个 ByteBuffer 提供了一组功能用于操作 byte 数组。除了ByteBuffer,还有其他的一些缓冲区，事实上，每一种Java基本类型（除了Boolean类型）都对应有一种缓冲区。

#### Channel (通道)

NIO 通过Channel（通道） 进行读写。

通道是双向的，可读也可写，而流的读写是单向的。无论读写，通道只能和Buffer交互。因为 Buffer，通道可以异步地读写。

#### Selectors(选择器)

NIO有选择器，而IO没有。

选择器用于使用单个线程处理多个通道。因此，它需要较少的线程来处理这些通道。线程之间的切换对于操作系统来说是昂贵的。 因此，为了提高系统效率选择器是有用的。

- #### NIO 读数据和写数据方式

通常来说NIO中的所有IO都是从 Channel（通道） 开始的。

- 从通道进行数据读取 ：创建一个缓冲区，然后请求通道读取数据。
- 从通道进行数据写入 ：创建一个缓冲区，填充数据，并要求通道写入数据。

数据读取和写入操作图示：

`channel` --->>>>`buffer`

`channel` <<<<----`buffer`

- ### NIO核心组件简单介绍

NIO 包含下面几个核心的组件：

- Channel(通道)
- Buffer(缓冲区)
- Selector(选择器)

整个NIO体系包含的类远远不止这三个，只能说这三个是NIO体系的“核心API”

## AIO (Asynchronous I/O)

AIO 也就是 NIO 2。在 Java 7 中引入了 NIO 的改进版 NIO 2,它是异步非阻塞的IO模型。异步 IO 是基于事件和回调机制实现的，也就是应用操作之后会直接返回，不会堵塞在那里，当后台处理完成，操作系统会通知相应的线程进行后续的操作。

AIO 是异步IO的缩写，虽然 NIO 在网络操作中，提供了非阻塞的方法，但是 NIO 的 IO 行为还是同步的。对于 NIO 来说，我们的业务线程是在 IO 操作准备好时，得到通知，接着就由这个线程自行进行 IO 操作，IO操作本身是同步的。（除了 AIO 其他的 IO 类型都是同步的，这一点可以从底层IO线程模型解释。

---------------------

#### 从操作系统上理解

考虑下面两种情况：

- 用系统调用`read`从socket里读取一段数据
- 用系统调用`read`从一个磁盘文件读取一段数据到内存

对于第一种情况，算作block，因为Linux无法知道网络上对方是否会发数据。如果没数据发过来，对于调用`read`的程序来说，就只能“等”。

对于第二种情况，**不算做block**

对于磁盘文件IO，Linux总是不视作Block

一个解释是，所谓“Block”是指操作系统可以预见这个Block会发生才会主动Block。例如当读取TCP连接的数据时，如果发现Socket buffer里没有数据就可以确定定对方还没有发过来，于是Block；而对于普通磁盘文件的读写，也许磁盘运作期间会抖动，会短暂暂停，但是操作系统无法预见这种情况，只能视作不会Block，照样执行

基于这个基本的设定，在讨论IO时，一定要严格区分网络IO和磁盘文件IO。NIO和后文讲到的IO多路复用只对网络IO有意义。

# BIO

有了Block的定义，就可以讨论BIO和NIO了。BIO是Blocking IO的意思。在类似于网络中进行`read`, `write`, `connect`一类的系统调用时会被卡住。

举个例子，当用`read`去读取网络的数据时，是无法预知对方是否已经发送数据的。因此在收到数据之前，能做的只有等待，直到对方把数据发过来，或者等到网络超时。

对于单线程的网络服务，这样做就会有卡死的问题。因为当等待时，整个线程会被挂起，无法执行，也无法做其他的工作。

这种Block是不会影响同时运行的其他程序（进程）的，因为现代操作系统都是多任务的，任务之间的切换是抢占式的。这里Block只是指Block当前的进程。

网络服务为了同时响应多个并发的网络请求，必须实现为多线程的。每个线程处理一个网络请求。线程数随着并发连接数线性增长。这的确能奏效。实际上2000年之前很多网络服务器就是这么实现的。但这带来两个问题：

- 线程越多，Context Switch就越多，而Context Switch是一个比较重的操作，会无谓浪费大量的CPU。

- 每个线程会占用一定的内存作为线程的栈。比如有1000个线程同时运行，每个占用1MB内存，就占用了1个G的内存。

问题的关键在于，当调用`read`接受网络请求时，有数据到了就用，没数据到时，实际上是可以干别的。使用大量线程，仅仅是因为Block发生，没有其他办法。

当然你可能会说，是不是可以弄个线程池呢？这样既能并发的处理请求，又不会产生大量线程。但这样会限制最大并发的连接数。比如你弄4个线程，那么最大4个线程都Block了就没法响应更多请求了。

要是操作IO接口时，操作系统能够总是直接告诉有没有数据，而不是Block去等就好了。于是，NIO登场

# NIO

NIO是指将IO模式设为“Non-Blocking”模式。在Linux下，一般是这样：

```c
void setnonblocking(int fd) {
    int flags = fcntl(fd, F_GETFL, 0);
    fcntl(fd, F_SETFL, flags | O_NONBLOCK);
}
```

在BIO模式下，调用read，如果发现没数据已经到达，就会Block住。

在NIO模式下，调用read，如果发现没数据已经到达，就会立刻返回-1, 并且errno被设为`EAGAIN`

于是，一段NIO的代码，大概就可以写成这个样子

```c
struct timespec sleep_interval{.tv_sec = 0, .tv_nsec = 1000};
ssize_t nbytes;
while (1) {
    /* 尝试读取 */
    if ((nbytes = read(fd, buf, sizeof(buf))) < 0) {
        if (errno == EAGAIN) { // 没数据到
            perror("nothing can be read");
        } else {
            perror("fatal error");
            exit(EXIT_FAILURE);
        }
    } else { // 有数据
        process_data(buf, nbytes);
    }
    // 处理其他事情，做完了就等一会，再尝试
    nanosleep(sleep_interval, NULL);
}
```

```c

```

这段代码很容易理解，就是轮询，不断的尝试有没有数据到达，有了就处理，没有(得到`EWOULDBLOCK`或者`EAGAIN`)就等一小会再试。这比之前BIO好多了，起码程序不会被卡死了

但这样会带来两个新问题：

- 如果有大量文件描述符都要等，那么就得一个一个的read。这会带来大量的Context Switch（`read`是系统调用，每调用一次就得在用户态和核心态切换一次）
- 休息一会的时间不好把握。这里是要猜多久之后数据才能到。等待时间设的太长，程序响应延迟就过大；设的太短，就会造成过于频繁的重试，干耗CPU而已。

要是操作系统能一口气告诉程序，哪些数据到了就好了。

于是IO多路复用被搞出来解决这个问题

# IO多路复用

IO多路复用（IO Multiplexing) 是这么一种机制：程序注册一组socket文件描述符给操作系统，表示“我要监视这些fd是否有IO事件发生，有了就告诉程序处理”。

IO多路复用是要和NIO一起使用的。尽管在操作系统级别，NIO和IO多路复用是两个相对独立的事情。NIO仅仅是指IO API总是能立刻返回，不会被Blocking；而IO多路复用仅仅是操作系统提供的一种便利的通知机制。操作系统并不会强制这俩必须得一起用——你可以用NIO，但不用IO多路复用，就像上一节中的代码；也可以只用IO多路复用 + BIO，这时效果还是当前线程被卡住。但是，**IO多路复用和NIO是要配合一起使用才有实际意义**。因此，在使用IO多路复用之前，请总是先把fd设为`O_NONBLOCK`。

对IO多路复用，还存在一些常见的误解，比如：

- **❌IO多路复用是指多个数据流共享同一个Socket**。其实IO多路复用说的是多个Socket，只不过操作系统是一起监听他们的事件而已。

  > 多个数据流共享同一个TCP连接的场景的确是有，比如Http2 Multiplexing就是指Http2通讯中中多个逻辑的数据流共享同一个TCP连接。但这与IO多路复用是完全不同的问题。

- **❌IO多路复用是NIO，所以总是不Block的**。其实IO多路复用的关键API调用(`select`，`poll`，`epoll_wait`）总是Block的，正如下文的例子所讲。

- ❌**IO多路复用和NIO一起减少了IO**。实际上，IO本身（网络数据的收发）无论用不用IO多路复用和NIO，都没有变化。请求的数据该是多少还是多少；网络上该传输多少数据还是多少数据。IO多路复用和NIO一起仅仅是解决了调度的问题，避免CPU在这个过程中的浪费，使系统的瓶颈更容易触达到网络带宽，而非CPU或者内存。要提高IO吞吐，还是提高硬件的容量（例如，用支持更大带宽的网线、网卡和交换机）和依靠并发传输（例如HDFS的数据多副本并发传输

操作系统级别提供了一些接口来支持IO多路复用，最老掉牙的是`select`和`poll`。

## select

`select`长这样：

```c
int select(int nfds, fd_set *readfds, fd_set *writefds, fd_set *exceptfds, struct timeval *timeout);
```

它接受3个文件描述符的数组，分别监听读取(`readfds`)，写入(`writefds`)和异常(`expectfds`)事件。那么一个 IO多路复用的代码大概是这样

```c
struct timeval tv = {.tv_sec = 1, .tv_usec = 0};

ssize_t nbytes;
while(1) {
    FD_ZERO(&read_fds);
    setnonblocking(fd1);
    setnonblocking(fd2);
    FD_SET(fd1, &read_fds);
    FD_SET(fd2, &read_fds);
    // 把要监听的fd拼到一个数组里，而且每次循环都得重来一次...
    if (select(FD_SETSIZE, &read_fds, NULL, NULL, &tv) < 0) { // block住，直到有事件到达
        perror("select出错了");
        exit(EXIT_FAILURE);
    }
    for (int i = 0; i < FD_SETSIZE; i++) {
        if (FD_ISSET(i, &read_fds)) {
            /* 检测到第[i]个读取fd已经收到了，这里假设buf总是大于到达的数据，所以可以一次read完 */
            if ((nbytes = read(i, buf, sizeof(buf))) >= 0) {
                process_data(nbytes, buf);
            } else {
                perror("读取出错了");
                exit(EXIT_FAILURE);
            }
        }
    }
}
```

首先，为了`select`需要构造一个fd数组（这里为了简化，没有构造要监听写入和异常事件的fd数组）。之后，用`select`监听了`read_fds`中的多个socket的读取时间。调用`select`后，程序会Block住，直到一个事件发生了，或者等到最大1秒钟(`tv`定义了这个时间长度）就返回。之后，需要遍历所有注册的fd，挨个检查哪个fd有事件到达(`FD_ISSET`返回true)。如果是，就说明数据已经到达了，可以读取fd了。读取后就可以进行数据的处理。

`select`有一些发指的缺点：

- `select`能够支持的最大的fd数组的长度是1024。这对要处理高并发的web服务器是不可接受的。
- fd数组按照监听的事件分为了3个数组，为了这3个数组要分配3段内存去构造，而且每次调用`select`前都要重设它们（因为`select`会改这3个数组)；调用`select`后，这3数组要从用户态复制一份到内核态；事件到达后，要遍历这3数组。很不爽。
- `select`返回后要挨个遍历fd，找到被“SET”的那些进行处理。这样比较低效。
- `select`是无状态的，即每次调用`select`，内核都要重新检查所有被注册的fd的状态。`select`返回后，这些状态就被返回了，内核不会记住它们；到了下一次调用，内核依然要重新检查一遍。于是查询的效率很低。

## poll

`poll`与`select`类似于。它大概长这样

```CQL
int poll(struct pollfd *fds, nfds_t nfds, int timeout);
```

`poll`的代码例子和`select`差不多，因此也就不赘述了。有意思的是`poll`这个单词的意思是“轮询”，所以很多中文资料都会提到对IO进行“轮询”。

`poll`优化了`select`的一些问题。比如不再有3个数组，而是1个`polldfd`结构的数组了，并且也不需要每次重设了。数组的个数也没有了1024的限制。但其他的问题依旧：

- 依然是无状态的，性能的问题与`select`差不多一样；
- 应用程序仍然无法很方便的拿到那些“有事件发生的fd“，还是需要遍历所有注册的fd。

目前来看，高性能的web服务器都不会使用`select`和`poll`。他们俩存在的意义仅仅是“兼容性”，因为很多操作系统都实现了这两个系统调用。



如果是追求性能的话，在BSD/macOS上提供了kqueue api；在Salorias中提供了/dev/poll（可惜该操作系统已经凉凉)；而在Linux上提供了epoll api。它们的出现彻底解决了`select`和`poll`的问题。Java NIO，nginx等在对应的平台的上都是使用这些api实现。

因为大部分情况下我会用Linux做服务器，所以下文以Linux epoll为例子来解释多路复用是怎么工作的。

# 用epoll实现的IO多路复用

epoll是Linux下的IO多路复用的实现。这里单开一章是因为它非常有代表性，并且Linux也是目前最广泛被作为服务器的操作系统。细致的了解epoll对整个IO多路复用的工作原理非常有帮助。

与`select`和`poll`不同，要使用epoll是需要先创建一下的。

```c
int epfd = epoll_create(10);
```

`epoll_create`在内核层创建了一个数据表，接口会返回一个“epoll的文件描述符”指向这个表。注意，接口参数是一个表达要监听事件列表的长度的数值。但不用太在意，因为epoll内部随后会根据事件注册和事件注销动态调整epoll中表格的大小。

![epoll](/img/epoll.png)

为什么epoll要创建一个用文件描述符来指向的表呢？这里有两个好处：

- epoll是有状态的，不像`select`和`poll`那样每次都要重新传入所有要监听的fd，这避免了很多无谓的数据复制。epoll的数据是用接口`epoll_ctl`来管理的（增、删、改）。
- epoll文件描述符在进程被fork时，子进程是可以继承的。这可以给对多进程共享一份epoll数据，实现并行监听网络请求带来便利。但这超过了本文的讨论范围，就此打住。

epoll创建后，第二步是使用`epoll_ctl`接口来注册要监听的事件

```c
int epoll_ctl(int epfd, int op, int fd, struct epoll_event *event);
```

其中第一个参数就是上面创建的`epfd`。第二个参数`op`表示如何对文件名进行操作，共有3种。

- `EPOLL_CTL_ADD` - 注册一个事件
- `EPOLL_CTL_DEL` - 取消一个事件的注册
- `EPOLL_CTL_MOD` - 修改一个事件的注册

第三个参数是要操作的fd，这里必须是支持NIO的fd（比如socket）。第四个参数是一个`epoll_event`的类型的数据，表达了注册的事件的具体信息。

```c
typedef union epoll_data {
    void    *ptr;
    int      fd;
    uint32_t u32;
    uint64_t u64;
} epoll_data_t;

struct epoll_event {
    uint32_t     events;    /* Epoll events */
    epoll_data_t data;      /* User data variable */
};
```

比方说，想关注一个fd1的读取事件事件，并采用边缘触发(下文会解释什么是边缘触发），大概要这么写：

```c
struct epoll_data ev;
ev.events = EPOLLIN | EPOLLET; // EPOLLIN表示读事件；EPOLLET表示边缘触发
ev.data.fd = fd1;
```

通过`epoll_ctl`就可以灵活的注册/取消注册/修改注册某个fd的某些事件

![epoll_ctl](/img/epoll_ctl.png)

第三步，使用`epoll_wait`来等待事件的发生

```CQL
int epoll_wait(int epfd, struct epoll_event *evlist, int maxevents, int timeout);
```

特别留意，这一步是"block"的。只有当注册的事件至少有一个发生，或者`timeout`达到时，该调用才会返回。这与`select`和`poll`几乎一致。但不一样的地方是`evlist`，它是`epoll_wait`的返回数组，里面**只包含那些被触发的事件对应的fd**，而不是像`select`和`poll`那样返回所有注册的fd。

![epoll_wait](/img/epoll_wait.png)

综合起来，一段比较完整的epoll代码大概是这样的

```c
#define MAX_EVENTS 10
struct epoll_event ev, events[MAX_EVENTS];
int nfds, epfd, fd1, fd2;

// 假设这里有两个socket，fd1和fd2，被初始化好。
// 设置为non blocking
setnonblocking(fd1);
setnonblocking(fd2);

// 创建epoll
epfd = epoll_create(MAX_EVENTS);
if (epollfd == -1) {
    perror("epoll_create1");
    exit(EXIT_FAILURE);
}

//注册事件
ev.events = EPOLLIN | EPOLLET;
ev.data.fd = fd1;
if (epoll_ctl(epollfd, EPOLL_CTL_ADD, fd1, &ev) == -1) {
    perror("epoll_ctl: error register fd1");
    exit(EXIT_FAILURE);
}
if (epoll_ctl(epollfd, EPOLL_CTL_ADD, fd2, &ev) == -1) {
    perror("epoll_ctl: error register fd2");
    exit(EXIT_FAILURE);
}

// 监听事件
for (;;) {
    nfds = epoll_wait(epdf, events, MAX_EVENTS, -1);
    if (nfds == -1) {
        perror("epoll_wait");
        exit(EXIT_FAILURE);
    }

    for (n = 0; n < nfds; ++n) { // 处理所有发生IO事件的fd
        process_event(events[n].data.fd);
        // 如果有必要，可以利用epoll_ctl继续对本fd注册下一次监听，然后重新epoll_wait
    }
}
```

所有的基于IO多路复用的代码都会遵循这样的写法：注册——监听事件——处理——再注册，无限循环下去。