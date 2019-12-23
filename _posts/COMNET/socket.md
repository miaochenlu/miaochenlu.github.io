# 网络编程

# 1. client-server编程模型

一个应用是由一个服务器进程和一个或者多个客户端进程组成。

服务器管理某种资源，并且通过操作者众资源来为他的客户端提供某种服务。

客户端-服务器模型中的基本操作是***事务***。一个客户端-服务器事务由以下四步组成

* 一个客户端需要服务时，向服务器发送一个请求，发起一个事务
* 服务器收到请求后，解释它，并以适当的方式操作它的资源
* 服务器给客户端发送一个相应，并等待下一个请求
* 客户端收到响应并且处理它

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20191220190636513.png" alt="image-20191220190636513" style="zoom:50%;" />

# 2. socket

## 2.1 IP地址

一个IP地址是一个32位无符号整数，网络程序将IP地址存放在in_addr中

```cpp
//IP address structure
struct in_addr {
  uint32_t s_addr; //Address in network byte order(big endian)
}
```

{:.warning}

要注意的是,TCP/IP为任意整数数据项定义了统一的网络字节顺序\[network byte order][大端]。IP地址结构中存放的地址就是大端顺序。

一下是unix提供的在网络和主机字节顺序间转换的函数

```cpp
#include<arpa/inet.h>
//host to network long 将32位整数有主机字节顺序转换为网络字节顺序
uint32_t htonl(uint32_t hostlong);
//host to network short 将16位整数有主机字节顺序转换为网络字节顺序
uint16_t htons(uint16_t, hostshort);

//network to host long
uint32_t ntohl(uint32_t netlong);
//network to host short
uint16_t ntohs(uint16_t netshort);
```



IP地址通常用点分十进制表示法来表示。如128.2.194.242表示地址0x0882c2f2

unix提供的函数

```cpp
#include<arpa/inet.h>
//[p]presentation to network 将点分十进制串转换为一个二进制的网络字节顺序的IP地址
int inet_pton(AF_INET, const char* src, void* dst);
//将二进制的网络字节顺序的IP地址转换为点分十进制
const char* inet_ntop(AF_INET, const void* src, char* dst, socklen_t size);
```



## 2.2 因特网连接

一个套接字是连接的一个端点，每个套接字是有一个IP地址和一个16位的整数端口组成的，用"地址:端口"来表示。

当客户端发起一个连接请求时，客户端的套接字地址中的端口是由***内核自动分配***的，称为临时端口。

服务器套接字端口通常是某个知名端口。

一个连接由它两端的套接字地址唯一确定。由以下元组来表示

`{cliaddr:cliport, servaddr:servport}`

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20191220192649561.png" alt="image-20191220192649561" style="zoom:50%;" />



## 2.3 套接字接口

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20191220210026202.png" alt="image-20191220210026202" style="zoom:50%;" />

### A. 套接字地址结构

```cpp
//IP socket address structure
struct sockaddr_in {
  uint16_t 				sin_family;			//protocol family(always AF_INET)
  uint16_t				sin_port;				//port number in network byte order
  struct in_addr	sin_addr;				//IP address in network byte order
  unsigned char		sin_zero[8];		//pad to sizeof(struct sockaddr)
}
```

connect, bing, accept函数需要一个指向与协议相关的套接字地址结构sockaddr的指针。然后要求应用程序将与协议特定的结构的指针强制转换成这个通用结构

```cpp
struct sockaddr {
  uint16_t sa_family;
  char sa_data[14];
}
typedef struct sockaddr SA;
```

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20191220205654511.png" alt="image-20191220205654511" style="zoom:50%;" />

## B. socket()

客户端和服务器使用`socket`函数来创建一个socket descriptor

```cpp
#include<sys/types.h>
#include<sys/socket.h>

int socket(int domain, int type, int protocol);
```

要想使套接字称为连接的一个端点，就用如下硬编码的参数来调用socket函数

```cpp
clientfd = socket(AF_INET, SOCK_STREAM, 0);
```

* AF_INET表示我们正在使用32位IP地址

* SOCK_STREAM表示这个套接字是连接的一个端点



{:.warning}

socket返回的clientfd描述符是部分打开的，不能用于读写



## C. Connect()

client通过调用connect来建立和服务器的连接

```cpp
#include<sys/socket.h>
int connect(int clientfd, const struct sockaddr* addr, socketlen_t addrlen);
```

`connect`函数试图与套接字地址为addr的服务器建立一个因特网连接

* addrlen是`sizeof(sockaddr_in)`

connect函数会阻塞，一直到连接成功建立或是发生错误

如果连接成功，则clientfd描述符已经准备好读写了，并且得到的连接是由套接字对`(x:y,addr.sin_addr:addr:sin_port)`刻画的，其中x表示客户端的IP地址，y是临时端口。



剩下的bind,listen, accept函数，服务器用他们来和客户端建立连接

## D. bind()

```cpp
#include<sys/socket.h>
int bind(int sockfd, const struct sockaddr* addr, socketlen_t addrlen);
```

bind告诉内核将addr中的服务器套接字地址和套接字描述符sockfd联系起来



## E. listen()

```cpp
#include<sys/socket.h>
int listen(int sockfd, int backlog);
```

listen函数将sockfd从一个主动套接字转化为一个监听套接字[listening socket]

该套接字可以接受来自客户端的连接请求。

backlog参数暗示了内核在开始拒绝连接请求之前，队列中要排队的未完成的连接请求的数量，通常会设为较大的值，比如1024



## F. accept()

```cpp
#include<sys/socket.h>
int accept(int listenfd, struct sockaddr* addr, int* addrlen);
```

accept函数等待来自客户端的连接请求到达侦听描述符listenfd

然后在addr中填写客户端的套接字地址，并且返回一个已连接描述符[connected descriptor]。这个描述符可被用来利用Unix I/O函数与客户端通信。



<img src="/Users/jones/Library/Application Support/typora-user-images/image-20191220201248379.png" alt="image-20191220201248379" style="zoom:50%;" />









## 2.4 主机和服务的转换

linux提供了一些强大的函数`getaddrinfo`和`getnameinfo`实现二进制套接字地址结构的主机名、主机地址、服务名、端口号的字符串表示之间的相互转化。

### A. getaddrinfo()

```cpp
#include<sys/types.h>
#include<sys/socket.h>
#include<netdb.h>

int getaddrinfo(const char* host, const char* service,
                const struct addrinfo* hints,
                struct addrinfo** result);
void freeaddrinfo(struct addrinfo* result);
const char* gai_strerror(int errcode);
```

给定host和service[套接字地址的两个组成部分], getaddrinfo返回result

* host可以是域名，也可以是数字地址[如点分十进制]

* service可以是服务名，[如http], 也可以是十进制端口号

  * 如果不想把主机名转换成地址，可以把host设置为NULL, service也是一样，但是必须至少指定两者中的一个

* 可选的参数hints是一饿addrinfo结构，它提供对getaddrinfo返回的套接字地址列表的更好的控制。

  * 如果要传递hints参数，只能设置下列字段：
    * `ai_family`
    * `ai_socktype`
    * `ai_protocol`
    * `ai_flags`
    * 其他字段必须设置为0或NULL

  * 我们通常用memset将整个结构清零，然后有选择地设置一些字段

result是一个指向addrinfo结构的链表，其中每个结构指向一个对应于host和service的套接字地址结构

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20191220201923422.png" alt="image-20191220201923422" style="zoom:50%;" />

在客户端调用了`getaddrinfo`函数之后，可以遍历这个列表，依次尝试每个套接字地址，直到调用socket和connect成功，建立起连接。

服务器也要遍历列表中的每个套接字地址，直到调用socket和bind成功，描述符会被绑定到一个合法的套接字地址。

为了避免内存泄漏，应用程序必须在最后调用`freeaddrinfo`，释放这个链表。

如果`getaddrinfo`返回***非零的错误代码***，应用程序可以调用`gai_strerror`将该代码转换成消息字符串。

* getaddrinfo默认可以返回IPv4和IPv6套接字地址。`ai_family`设置为`AF_INET`会将列表限制为IPv4地址；设置为`AF_INET6`则限制为IPv6地址
* 对于host关联的每个地址，`getaddrinfo`默认最多返回三个`addrinfo`结构,每个`ai_socktype`字段不同；一个是连接，一个是数据报，一个是原始套接字。`ai_socktype`设置为`SOCK_STREAM`将列表限制为每个地址最多一个`addrinfo`结构，该结构的套接字地址可以作为连接的一个端点
* `ai_flags`是一个位掩码
  * `AI_ADDECONFIG`。如果在使用连接，就推荐使用这个标志
  * `AI_CANONNAME`。默认为null, 如果设置了这个标志，就是告诉getaddrinfo将列表中第一个addrinfo结构的ai_canonname字段指向host的权威名字
  * `AI_NUMERICSERV`。参数service默认可以是服务名或者端口号。这个标志强制参数service为端口号
  * `AI_PASSIVE`。



```cpp
struct addrinfo {
  int 						ai_flags;
  int 						ai_family;
  int 						ai_socktype;
  int 						ai_protocol;
  char						*ai_canonname;
  size_t					ai_addrlen;
  struct sockaddr	*ai_addr;
  struct addrinfo	*ai_next;
}
```



### B. getnameinfo

getnameinfo将一个套接字地址转换成相应的主机和服务名字符串。

```cpp
int getnameinfo(const struct sockaddr* sa, socklen_t salen,
                char* host, size_t hostlen,
                char* service, size_t servlen, int flags);
```



```cpp
#include "csapp.h"

int main(int argc, char** argv) {
    struct addrinfo *p, *listp, hints;
    char buf[MAXLINE];
    int rc, flags;


    //get a list of addrinfo records
    memset(&hints, 0, sizeof(struct addrinfo));
    hints.ai_family = AF_INET;				//IPv4 only
    hints.ai_socktype = SOCK_STREAM;	//Connections only
    if((rc = getaddrinfo("www.baidu.com", NULL, &hints, &listp)) != 0) {
        fprintf(stderr, "getaddrinfo error: %s\n", gai_strerror(rc));
        exit(1);
    }

    //walk the list and display each IP address
    flags = NI_NUMERICHOST;
    for(p = listp; p; p = p->ai_next) {
        getnameinfo(p->ai_addr, p->ai_addrlen, buf, MAXLINE, NULL, 0, flags);
        printf("%s\n", buf);
    }
    //clean up
    freeaddrinfo(listp);
    exit(0);
}
```

首先初始化hints结构，使getaddrinfo返回我们想要的地址。在这里，我们想要查找32位IP地址`AF_INET`，用作连接的端点`SOCK_STREAM`。

因为只想`getaddrinfo`转换域名，所以用`service`参数为NULL来调用它

调用`getaddrinfo`之后，会遍历`addrinfo`结构，用`getnameinfo`将每个套接字地址转换成点分十进制字符串

遍历完列表之后，调用`freeaddrinfo`释放列表



## 2.5 套接字接口的辅助函数

### A. open_clientfd

```cpp
#include "csapp.h"
int open_clientfd(char* hostname, char* port);//成功则为描述符，出错为-1
```



### B. open_listenfd

```cpp
#include "csapp.h"
int open_listenfd(char* port);
```



`client.c`

```cpp
#include "csapp.h"

int main(int argc, char** argv) {
  int clientfd;
  char* host, *port, buf[MAXLINE];
  rio_t rio;//也是一个descriptor
  if(argc != 3) {
    fprintf(stderr, "usage: %s <host> <port>\n", argv[0]);
    exit(0);
  }
  host = argv[1];
  port = argv[2];
  client = open_clientfd(host, port);
  Rio_readinitb(&rio, clientfd);
  while(fgets(buf, MAXLINE, stdin) != NULL) {
    Rio_writen(clientfd, buf, strlen(buf));	//发送给server
    Rio_readlineb(&rio, buf, MAXLINE);			//读取server
  
    fputs(buf, stdout);
  }
  close(clientfd);
  exit(0);
}
```

`server.c`

```cpp
#include "csapp.h"
void echo(int connfd) {
  int listenfd, connfd;
  socklen_t clientlen;
  
  struct sockaddr_storage clientaddr;	//enough space for any address
  char client_hostname[MAXLINE], client_port[MAXLINE];
  if(argc != 2) {
    fprintf(stderr, "usage: %s <port>\n", argv[0]);
    exit(0);
  }
  listenfd = open_listenfd(argv[1]);
  while(1) {
    clientlen = sizeof(struct sockaddr_storage);
    connfd = Accept(listenfd, (SA*)&clientaddr, &clientlen);
    getnameinfo((SA*)&clientaddr, clientlen, client_hostname, MAXLINE, client_port, MAXLINE, 0);
    printf("Connected to (%s, %s)\n", client_hostname, client_port);
    echo(connfd);
    close(connfd);
  }
  exit(0);
}

void echo(int connfd) {
  size_t n;
  char buf[MAXLINE];
  rio_t rio;
  Rio_readinitb(&rio, connfd);
  while((n = Rio_read_readlineb(&reo, buf, MAXLINE)) != 0) {
    printf("server received %d bytes\n", (int)n);
    Rio_writen(connfd, buf, n);
  }
}
```



# 5. Web server

## 5.2 Web内容

对于Web客户端和服务器而言，内容是与一个MIME[Multipurpose Internet Mail Extensions]类型相关的字节序列

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20191222044007513.png" alt="image-20191222044007513" style="zoom:50%;" />

Web服务器以两种不同的方式向客户端提供内容

* serving static content

> 取一个磁盘文件，并将它的内容返回给客户端。磁盘文件称为静态内容

* serving dynamic content

> 运行一个可执行文件，并将它的输出返回给客户端。运行时可执行文件产生的输出称为动态内核

每条Web服务器返回的内容都是和它管理的某个文件相关联的。这些文件中的每一个都有一个唯一的名字，叫做***<u>URL</u>[universal resource locator]***,例如URL

`http://www.google.com:80/index.html`

表示因特网主机www.google.com上一个称为/index.html的html文件



可执行文件的URL可以在文件名后面包括程序参数。?字符分割文件名和参数，而且每个参数都用&字符分隔开，如

`http://bluefish.ics.cs.cmu.edu:8000/cgi-bin/adder?15000&213`

标识了一个叫做`/cgi-bin/adder`的可执行文件，会带两个参数字符串15000和213来调用它。



在事务过程中，client和server使用的是URL的不同部分。例如：

* 客户端使用前缀`http://www.google.com:80`来决定与哪类服务器联系，服务器在哪里，它监听的port是多少。
* 服务器使用后缀`/index.html`来发现他文件系统中的文件，并确定请求的是静态内容还是动态内容

{:.warning}

最小的URL后缀是"/"字符，所有服务器将其扩展为某个默认的主页，例如/index.html。这也解释了后面的GET / HTTP/1.1

## 5.3 HTTP事务

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20191222045146848.png" alt="image-20191222045146848" style="zoom:50%;" />

* 第一行，从linux shell运行TELNET, 要求打开一个到AOL Web服务器的连接
* TELNET打印三行输出，打开连接，等待我们输入文本
* 每次输入一个文本行，并回车，TELNET会读取该行，在后面加上回车和换行符号"\r\n", 并且将这一行发送到服务器



### A. HTTP请求

一个HTTP请求的组成

* 一个请求行request line
* 后面跟随0个或者多个请求报头request header
* 再跟随一个空的文本行来终止报头列表



***I. request line形式***

`method URI version`

`method`: GET POST OPTIONS HEAD PUT DELETE TRACE

GET方法指导服务器生成和返回URI[uniform resource identifier统一资源标识符]标识的内容。***URI是相应的URL后缀***，包括文件名和可选的参数

`version`字段表明了该请求遵循的HTTP版本



***II. 请求报头***

为服务器提供了额外的信息

`header-name: header-data`



### B. HTTP响应

一个HTTP响应的组成

* 一个响应行
* 后面跟随着0个或者更多的响应报头
* 再跟随一个终止报头的空行
* 再跟随一个响应主题

response header

`version status-code status-message`



<img src="/Users/jones/Library/Application Support/typora-user-images/image-20191222050547989.png" alt="image-20191222050547989" style="zoom:50%;" />