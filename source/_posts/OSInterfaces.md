---
title: 操作系统接口
date: 2020-11-27 19:44:30
tags: Operating System
categories: Operating System
index_img: /img/20201129OS.jpg
---

# Overview

如下图所示是一个计算机系统，最底层有有一些硬件资源，比如: CPU, 内存，磁盘，网卡。

在上层运行一些应用程序，比如一个文本编辑器（VI），一个C编译器(CC), 一个Shell。这些程序都运行在一个空间中：用户空间(user space)。

除了用户空间还存在一个内核空间(kernel space)。kernel对底层管理硬件资源，为上层程序提供了大量的服务。他对用户空间程序(进程)，对内存进行分配，进行访问控制......。kernel程序总是第一个被启动的，他也是独一无二的存在。



<img src="OS.jpg" alt="OS" style="zoom:80%;" />

用户空间又如何与内核空间进行交互呢？这是通过一系列的接口来实现的。通常这通过系统调用 (system call)来实现。

如下图所示，在用户空间中有一个shell程序在运行。如果这个时候得到一条读取文件的命令，shell invoke `read` 这个system call， 跳转到内核空间，执行该命令，然后将结果返回用户空间。 [这里是这么实现的呢]

<img src="image-20201127194539928.png" alt="image-20201127194539928" style="zoom:80%;" />



# 一些系统调用

## `fork`

| System Call  | Description                          |
| ------------ | ------------------------------------ |
| `int fork()` | Create a process, return child's PID |

`fork` 的作用是克隆进程，也就是将原先的一个进程再克隆出一个来，克隆出的这个进程就是原进程的子进程，这个子进程和其他的进程没有什么区别，同样拥有自己的独立的地址空间。不同的是子进程是在fork返回之后才开始执行的。

在XV6中，父子进程除了fork的返回值，其他都是一样的。除了内存是一样的以外，文件描述符的表单也从父进程拷贝到子进程。所以如果父进程打开了一个文件，子进程可以看到同一个文件描述符，尽管子进程看到的是一个文件描述符的表单的拷贝。

`fork`函数有三个返回值

* 该进程为父进程时，返回子进程的pid
*  该进程为子进程时，返回0
*  fork执行失败，返回-1

<img src="v2-c5c3ba7e5e1f3eb0127683faef9cce32_720w.png" alt="img" style="zoom:67%;" />

```cpp
#include<stdio.h>
#include<unistd.h> 	//for fork
#include<sys/wait.h>//for wait

int main() {
  int pid = fork();
  if(pid > 0) {
    printf("parent: child = %d\n", pid);
    pid = wait(0);
    printf("child %d is done\n", pid);
  } else if(pid == 0) {
    printf("child: exiting\n");
  } else {
    printf("fork error\n");
  }
}
```

输出

```shell
$./a.out
parent: child = 24482
child: exiting
child 24482 is done
```



## `exec`

| System call                          | Description                                                  |
| ------------------------------------ | ------------------------------------------------------------ |
| `int exec(char* file, char* argv[])` | Load a file and execute it with arguments; only returns if error |



把当前进程的内存替换为文件里保存的内存镜像并执行之。`exec`有两个参数，第一个是要执行的程序，第二个这个程序的参数(以字符串数组的形式出现)。

1. exec系统调用会保留当前的文件描述符表单。所以任何在exec系统调用之前的文件描述符，例如0，1，2等。它们在新的程序中表示相同的东西。
2. 通常来说exec系统调用不会返回，因为exec会完全替换当前进程的内存，相当于当前进程不复存在了，所以exec系统调用已经没有地方能返回了。

```cpp
#include<stdio.h>
#include <unistd.h>

int main() {
  char* argv[] = {"echo", "this", "is", "echo", 0};
  execv("/bin/echo", argv);

  printf("exec failed\n");
}
```

输出

```shell
$ ./a.out  
this is echo
```



## shell是怎么工作的

首先看 `main` 函数，main loop通过 `getcmd`读取一行输入

然后调用 `fork`函数，创建一个shell process的copy。子进程通过`runcmd`执行命令，父进程等待。

```cpp
int
main(void)
{
  	static char buf[100];
	..

      // Read and run input commands.
    while(getcmd(buf, sizeof(buf)) >= 0){
    	...
        if(fork1() == 0)
          runcmd(parsecmd(buf));
        wait(0);
    }
    exit(0);
}
```



比如用户在shell中输入了 `echo hello`, `getcmd`获取了这条命令，通过fork创建了子进程，子进程调用 `runcmd`函数，`runcmd`将`echo hello`作为其参数。在`runcmd`中，他调用 `exec`函数执行`echo`。在某个时刻, `echo`调用 `exit`函数，这使父进程return from `wait`

<img src="OS-2.png" alt="OS-2" style="zoom:80%;" />

# `fork()` & `exec()` OR `forkexec()`

{% note warning %}

如上面所说，`fork`了之后再需要调用`exec`将替换子进程的内存，为什么不直接将`fork`和`exec`合并成一个system call呢?

{%endnote%}

将`fork`与`exec`分成两个系统调用，方便了I/O redirection的实现

如下所示, 我们希望将echo hello的输出结果重定向到`newfile.txt`。

```shell
$ echo hello > newfile.txt
```

shell实现的方法是: 在子进程创建后，调用 `exec`之前，shell关闭标准输出，并且打开文件`newfile.txt`, 这样结果就不会被输出到屏幕上，而是被输出到`newfile.txt`。

当然这个效果也依赖于操作系统对文件描述符的管理方式。文件描述符由kernel管理的对象，用一个比较小的整数来表示，把文件描述符指向的对象称为file，通过文件描述符可以对文件进行读写操作。通常，0为standard input, 1为standard output, 2为standard error。在分配文件描述符时，UNIX系统会从0开始寻找第一个可以使用的文件描述符。shell关闭了标准输出后，`STDOUT_FILENO`就是第一个可以使用的文件描述符。调用`open`函数打开文件后，这个文件就会被分配到这个文件描述符。

考虑，如果将`fork`, `exec`合并为一个系统调用`forkexec`，那怎么才能实现重定向呢？有几下几种思考

* shell在调用`forkexec`之前修改他的I/O
* `forkexec`接受I/O重定向的参数
* 每个程序自己处理I/O重定向问题

显然，这些都比较复杂。还是将`fork`和`exec`作为两个函数比较方便。但是，这也有一个问题，fork需要拷贝内存，而exec需要替换内存，这样是比较浪费时间的，所以后面会采用copy-on-write的方式进行优化。