---
title: System calls [os lab2]
date: 2020-12-16 20:21:28
index_img: /img/xv6lab.jpg
tags: [Operating System, xv6]
categories: Operating System
---



system call的相关代码
user-space code: `user/user.h` , `user/user.pl`
kernel-space code: `kernel/syscall.h` , `kernal/syscall.c`
process-related code: `kernel/proc.h` , `kernel/proc.c` 

先switch git branch到sycall

```bash
git fetch
git checkout syscall
make clean
```

# 1. System call tracing

{%note primary%}

In this assignment you will add a system call tracing feature that may help you when debugging later labs. You'll create a new `trace` system call that will control tracing. It should take one argument, an integer "mask", whose bits specify which system calls to trace. For example, to trace the fork system call, a program calls `trace(1 << SYS_fork)`, where `SYS_fork` is a syscall number from `kernel/syscall.h`. You have to modify the xv6 kernel to print out a line when each system call is about to return, if the system call's number is set in the mask. The line should contain the process id, the name of the system call and the return value; you don't need to print the system call arguments. The `trace` system call should enable tracing for the process that calls it and any children that it subsequently forks, but should not affect other processes.

{% endnote %}

这个任务会增加一个system call tracing的feature。我们需要创建一个新的system call `trace` 来控制tracing。他需要接收一个参数，一个整数"mask"，代表了需要trace的system calls。

Example: 

```bash
$ trace 32 grep hello README
3: syscall read -> 1023
3: syscall read -> 966
3: syscall read -> 70
3: syscall read -> 0
$
$ trace 2147483647 grep hello README
4: syscall trace -> 0
4: syscall exec -> 3
4: syscall open -> 3
4: syscall read -> 1023
4: syscall read -> 966
4: syscall read -> 70
4: syscall read -> 0
4: syscall close -> 0
$
$ grep hello README
$
$ trace 2 usertests forkforkfork
usertests starting
test forkforkfork: 407: syscall fork -> 408
408: syscall fork -> 409
409: syscall fork -> 410
410: syscall fork -> 411
409: syscall fork -> 412
410: syscall fork -> 413
409: syscall fork -> 414
411: syscall fork -> 415
...
$ 
```

In the first example above, trace invokes grep tracing just the read system call. The 32 is `1<<SYS_read` 

In the second example, trace runs grep while tracing all system calls; the 2147583647 has all 31 low bits set. 

In the third example, the program isn't traced, so no trace output is printed. In the fourth example, the fork system calls of all the descendants of the `forkforkfork` test in `usertests` are being traced. 

Your solution is correct if your program behaves as shown above (though the process IDs may be different).



## Some hints

{% note success%}

* Add `$U/_trace` to UPROGS in Makefile

{% endnote %}

<img src="Untitled.png" alt="Lab2%20System%20calls%203e297d6823c44cf89443d35be3f382cf/Untitled.png" style="zoom:50%;" />

{% note success%}

- Run **`make qemu`** and you will see that the compiler cannot compile `user/trace.c`

{% endnote %}

<img src="Untitled%201.png" alt="Lab2%20System%20calls%203e297d6823c44cf89443d35be3f382cf/Untitled%201.png" style="zoom:50%;" />

This is because the user-space stubs for the system call don't exist yet: 

{% note success%}

* add a prototype for the system call to `user/user.h` , a stub to `user/usys.pl`,  a syscall number to `kernel/syscall.h`

{% endnote %}

{% gi 3 3%}
  ![pic1](Untitled%202.png)
  ![pic1](Untitled%203.png)

![pic1](Untitled%204.png)

{% endgi %}



The Makefile invokes the perl script `user/usys.pl`, which produces `user/usys.S`, the actual system call stubs, which use the RISC-V ecall instruction to transition to the kernel.

将`SYS_trace`这个syscall number放入寄存器`a7`, 然后`ecall`进入kernel mode

```assembly
.global trace
trace:
 li a7, SYS_trace
 ecall
 ret
```

 Once you fix the compilation issues, run **trace 32 grep hello README**; it will fail because you haven't implemented the system call in the kernel yet.

<img src="Untitled%205.png" alt="Lab2%20System%20calls%203e297d6823c44cf89443d35be3f382cf/Untitled%205.png" style="zoom: 67%;" />



{% note success%}

- Add a `sys_trace()` function in `kernel/sysproc.c` that implements the new system call by remembering its argument in a new variable in the proc structure (see `kernel/proc.h`).

{% endnote %}

在 `kernel/proc.h` 中的proc结构体中新增一个mask成员变量。

<img src="Untitled%206.png" alt="Lab2%20System%20calls%203e297d6823c44cf89443d35be3f382cf/Untitled%206.png" style="zoom:50%;" />

在 `kernle/sysproc.c` 中增加 `sys_trace` 函数。The functions to retrieve system call arguments from user space are in `kernel/syscall.c`, and you can see examples of their use in `kernel/sysproc.c`.

在 `syscall.c` 中声明 `sys_trace` 函数，然后在 `syscalls` 这个函数指针数组中增加 `sys_trace` 。

{% gi 2 2%}
  ![pic1](Untitled%207.png)
  ![pic1](Untitled%209.png)

{% endgi %}

实现`sys_trace`函数，首先通过`argint`获取mask参数，然后将mask参数赋给proc

```c
uint
sys_trace(void) {
  int trace_mask;
  if(argint(0, &trace_mask) < 0) {
    return -1;
  }
  struct proc* p = myproc();
  p->mask = trace_mask;
  return 0;
}
```



{% note success%}

- Modify `fork()` (see `kernel/proc.c`) to copy the trace mask from the parent to the child process.

{% endnote %}



在`fork`函数中增加一行copy mask的代码

```c
np->mask = p->mask;//这行是新增的，copy mask
```



{% note success%}

- Modify the `syscall()` function in `kernel/syscall.c` to print the trace output. You will need to add an array of syscall names to index into.

{% endnote %}

在`kernel/syscall.c`中增加一个 `syscallnames` 这个数组，方便打印系统调用的名字

<img src="image-20201216215059739.png" alt="image-20201216215059739" style="zoom:67%;" />

`syscall`的代码修改如下: 

如果 `(1 << num) & p->mask != 0`  说明num属于mask trace的系统调用，按照格式打印系统调用内容: pid, syscall name, return value。注意return value存储在`a0`寄存器中

```c
void
syscall(void)
{
  int num;
  struct proc *p = myproc();

  num = p->trapframe->a7;
  if(num > 0 && num < NELEM(syscalls) && syscalls[num]) {
    p->trapframe->a0 = syscalls[num]();
    if((1 << num) & p->mask) {
      printf("%d: syscall %s -> %d\n", p->pid, syscall_names[num], p->trapframe->a0);
    }
  } else {
    printf("%d %s: unknown sys call %d\n",
            p->pid, p->name, num);
    p->trapframe->a0 = -1;
  }
}
```



# Sysinfo

{% note primary %}

In this assignment you will add a system call, `sysinfo`, that collects information about the running system. The system call takes one argument: a pointer to a struct sysinfo (see `kernel/sysinfo.h`). The kernel should fill out the fields of this struct: the freemem field should be set to the number of bytes of free memory, and the nproc field should be set to the number of processes whose state is not UNUSED. We provide a test program `sysinfotest`; you pass this assignment if it prints "sysinfotest: OK".

{% endnote %}

## Some hints

{% note success%}

- Add `$U/_sysinfotest` to `UPROGS` in Makefile

{% endnote %}

<img src="Untitled%2010.png" alt="Lab2%20System%20calls%203e297d6823c44cf89443d35be3f382cf/Untitled%2010.png" style="zoom:67%;" />

{% note success%}

- Run **make qemu**; `user/sysinfotest.c` will fail to compile. Add the system call `sysinfo`, following the same steps as in the previous assignment. To declare the prototype for `sysinfo()` in `user/user.h` you need predeclare the existence of struct sysinfo:

{% endnote %}

```c
struct sysinfo;
int sysinfo(struct sysinfo *);
```

在 `user/user.h` 中声明 `sysinfo` 函数, 在 `user/usys.pl` 中增加 `sysinfo` entry, 在 `kernel/syscall.h` 中定义 `sysinfo` 的系统调用号为23



{% gi 3 3%}
  ![pic1](Untitled%2011.png)
  ![pic1](Untitled%2012.png)

![pic1](Untitled%2013.png)

{% endgi %}

在 `kernel/syscall.c` 中修改如下三处信息

{% gi 3 3%}
  ![pic1](Untitled%2014.png)
  ![pic1](Untitled%2015.png)

![pic1](image-20201216215156011.png)

{% endgi %}



{% note success%}

- `sysinfo` needs to copy a struct sysinfo back to user space; see `sys_fstat()` ( `kernel/sysfile.c`) and `filestat()` ( `kernel/file.c`) for examples of how to do that using `copyout()`.  To collect the amount of free memory, add a function to `kernel/kalloc.c`。 To collect the number of processes, add a function to `kernel/proc.c`

{% endnote %}

<img src="Untitled%2017.png" alt="Lab2%20System%20calls%203e297d6823c44cf89443d35be3f382cf/Untitled%2017.png" style="zoom:67%;" />

其中两个函数 `nfree()` , `nproc()` 函数需要我们自己定义

`nfree()` 函数用来获取空闲内存的数量，以byte为单位返回。定义在 `kernel/kalloc.c` 中

`nproc()` 函数返回非 `UNUSED` state的进程数量。定义在 `kernel/proc.c` 中

`nfree()`的实现参考`kernel/kalloc.c/kalloc()`的代码，如下

```c
void *
kalloc(void)
{
  struct run *r;

  acquire(&kmem.lock);
  r = kmem.freelist;
  if(r)
    kmem.freelist = r->next;
  release(&kmem.lock);

  if(r)
    memset((char*)r, 5, PGSIZE); // fill with junk
  return (void*)r;
}
```

`nproc()`的实现参考`kernel/proc.c/procdump()`的代码，如下

```c
  for(p = proc; p < &proc[NPROC]; p++){
    if(p->state == UNUSED)
      continue;
    if(p->state >= 0 && p->state < NELEM(states) && states[p->state])
      state = states[p->state];
    else
      state = "???";
    printf("%d %s %s", p->pid, state, p->name);
    printf("\n");
  }
```



{% gi 2 2%}
  ![pic1](Untitled%2018.png)
  ![pic1](Untitled%2019.png)

{% endgi %}

为了使用这两个函数和 `sysinfo` 这个结构体，还需要在 `kernel/defs.h` 中声明函数。然后在 `kernel/sysproc.c` 中 `#include "sysinfo.h` 引入结构体信息，

{% gi 3 3%}
  ![pic1](Untitled%2020.png)
  ![pic1](Untitled%2021.png)

![pic1](image-20201216215452048.png)

{% endgi %}