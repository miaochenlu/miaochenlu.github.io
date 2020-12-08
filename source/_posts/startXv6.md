---
title: 启动xv6创建第一个进程
date: 2020-12-07 19:05:33
tags: [Operating System, xv6]
categories: Operating System
index_img: /img/operatingsystem.jpg
---



# Xv6启动过程

* xv6通电后先完成初始化

* 运行存储在ROM中的boot loader

* boot loader将xv6 kernel加载到memory中。kernel被加载到0x80000000处，之所以不加载到0x0处是因为在0x0:0x80000000之间包含了I/O设备。（此时分页硬件被disable了，虚拟地址直接对应物理地址)

* 在machine mode下，CPU从`_entry`(`kernel/entry.S:6`)开始执行xv6。`_entry`的作用是为C代码的运行指定栈。在`start.c`中, xv6声明了初始stack的空间`stack0`。`_entry`中的代码将地址`stack0+4096`加载到stack pointer register `sp`，也就是栈顶的地址。

```assembly
		# qemu -kernel loads the kernel at 0x80000000
        # and causes each CPU to jump there.
        # kernel.ld causes the following code to
        # be placed at 0x80000000.
.section .text
_entry:
		# set up a stack for C.
        # stack0 is declared in start.c,
        # with a 4096-byte stack per CPU.
        # sp = stack0 + (hartid * 4096)
        la sp, stack0
        li a0, 1024*4
		csrr a1, mhartid
        addi a1, a1, 1
        mul a0, a0, a1
        add sp, sp, a0
		# jump to start() in start.c
        call start
spin:
        j spin
```



* 接着，`_entry`跳转到`start`的C代码。`start`做了一些只能在machine mode下做的配置。他还初始化计时器中断。接着，他切换到supervisor mode。为了转换到supervisor mode, RISCV提供了`mret`指令。这个指令通常是用来从一个之前supervisor mode转换到machine mode的调用返回。但`start`并没有从一个这样的调用返回，他假装自己从这样的一个调用返回。他在register `mstatus`中将之前的privilege mode设置为supervisor mode，在register `mepc`中将main的地址设置为返回的地址，在page-table register `satp`中写入0来disable虚拟地址转换，并且将所有的interrupts和exceptions授权给supervisor mode

```c
#include "types.h"
#include "param.h"
#include "memlayout.h"
#include "riscv.h"
#include "defs.h"

void main();
void timerinit();

// entry.S needs one stack per CPU.
__attribute__ ((aligned (16))) char stack0[4096 * NCPU];

// scratch area for timer interrupt, one per CPU.
uint64 mscratch0[NCPU * 32];

// assembly code in kernelvec.S for machine-mode timer interrupt.
extern void timervec();

// entry.S jumps here in machine mode on stack0.
void
start()
{
  // set M Previous Privilege mode to Supervisor, for mret.
  unsigned long x = r_mstatus();
  x &= ~MSTATUS_MPP_MASK;
  x |= MSTATUS_MPP_S;
  w_mstatus(x);

  // set M Exception Program Counter to main, for mret.
  // requires gcc -mcmodel=medany
  w_mepc((uint64)main);

  // disable paging for now.
  w_satp(0);

  // delegate all interrupts and exceptions to supervisor mode.
  w_medeleg(0xffff);
  w_mideleg(0xffff);
  w_sie(r_sie() | SIE_SEIE | SIE_STIE | SIE_SSIE);

  // ask for clock interrupts.
  timerinit();

  // keep each CPU's hartid in its tp register, for cpuid().
  int id = r_mhartid();
  w_tp(id);

  // switch to supervisor mode and jump to main().
  asm volatile("mret");
}

// set up to receive timer interrupts in machine mode,
// which arrive at timervec in kernelvec.S,
// which turns them into software interrupts for
// devintr() in trap.c.
void
timerinit()
{
  // each CPU has a separate source of timer interrupts.
  int id = r_mhartid();

  // ask the CLINT for a timer interrupt.
  int interval = 1000000; // cycles; about 1/10th second in qemu.
  *(uint64*)CLINT_MTIMECMP(id) = *(uint64*)CLINT_MTIME + interval;

  // prepare information in scratch[] for timervec.
  // scratch[0..3] : space for timervec to save registers.
  // scratch[4] : address of CLINT MTIMECMP register.
  // scratch[5] : desired interval (in cycles) between timer interrupts.
  uint64 *scratch = &mscratch0[32 * id];
  scratch[4] = CLINT_MTIMECMP(id);
  scratch[5] = interval;
  w_mscratch((uint64)scratch);

  // set the machine-mode trap handler.
  w_mtvec((uint64)timervec);

  // enable machine-mode interrupts.
  w_mstatus(r_mstatus() | MSTATUS_MIE);

  // enable machine-mode timer interrupts.
  w_mie(r_mie() | MIE_MTIE);
}

```



* 调用`mret`后，program counter切到`main` (`kernel/main.c:11`), 在supervisor mode在运行。

```c
#include "types.h"
#include "param.h"
#include "memlayout.h"
#include "riscv.h"
#include "defs.h"

volatile static int started = 0;

// start() jumps here in supervisor mode on all CPUs.
void
main()
{
  if(cpuid() == 0){
    consoleinit();
    printfinit();
    printf("\n");
    printf("xv6 kernel is booting\n");
    printf("\n");
    kinit();         // physical page allocator
    kvminit();       // create kernel page table
    kvminithart();   // turn on paging
    procinit();      // process table
    trapinit();      // trap vectors
    trapinithart();  // install kernel trap vector
    plicinit();      // set up interrupt controller
    plicinithart();  // ask PLIC for device interrupts
    binit();         // buffer cache
    iinit();         // inode cache
    fileinit();      // file table
    virtio_disk_init(); // emulated hard disk
    userinit();      // first user process
    __sync_synchronize();
    started = 1;
  } else {
    while(started == 0)
      ;
    __sync_synchronize();
    printf("hart %d starting\n", cpuid());
    kvminithart();    // turn on paging
    trapinithart();   // install kernel trap vector
    plicinithart();   // ask PLIC for device interrupts
  }

  scheduler();        
}
```

* `main`初始化了一些设备和子系统，通过调用`userint`(`kernel/proc.c:212`)创建了第一个process

```c
// a user program that calls exec("/init")
// od -t xC initcode
uchar initcode[] = {
  0x17, 0x05, 0x00, 0x00, 0x13, 0x05, 0x45, 0x02,
  0x97, 0x05, 0x00, 0x00, 0x93, 0x85, 0x35, 0x02,
  0x93, 0x08, 0x70, 0x00, 0x73, 0x00, 0x00, 0x00,
  0x93, 0x08, 0x20, 0x00, 0x73, 0x00, 0x00, 0x00,
  0xef, 0xf0, 0x9f, 0xff, 0x2f, 0x69, 0x6e, 0x69,
  0x74, 0x00, 0x00, 0x24, 0x00, 0x00, 0x00, 0x00,
  0x00, 0x00, 0x00, 0x00
};

// Set up first user process.
void
userinit(void)
{
  struct proc *p;

  p = allocproc();
  initproc = p;
  
  // allocate one user page and copy init's instructions
  // and data into it.
  uvminit(p->pagetable, initcode, sizeof(initcode));
  p->sz = PGSIZE;

  // prepare for the very first "return" from kernel to user.
  p->trapframe->epc = 0;      // user program counter
  p->trapframe->sp = PGSIZE;  // user stack pointer

  safestrcpy(p->name, "initcode", sizeof(p->name));
  p->cwd = namei("/");

  p->state = RUNNABLE;

  release(&p->lock);
}
```

* 第一个进程执行了一个用RISC-V汇编写到小程序`initcode.S` (`user/initcode.S:1`)。他通过系统调用`exec`重新进入kernel。`exec`执行了一个新的程序`/init`。`/init`创建了一个控制台设备文件，并作为文件描述符0，1，2来打开它。然后在无限循环中，启动shell并处理僵尸进程。系统就这样启动了。

```assembly
# Initial process that execs /init.
# This code runs in user space.

#include "syscall.h"

# exec(init, argv)
.globl start
start:
        la a0, init
        la a1, argv
        li a7, SYS_exec
        ecall

# for(;;) exit();
exit:
        li a7, SYS_exit
        ecall
        jal exit

# char init[] = "/init\0";
init:
  .string "/init\0"

# char *argv[] = { init, 0 };
.p2align 2
argv:
  .long init
  .long 0

```





# GDB - 管中窥豹

首先，启动QEMU，并打开gdb。本质上来说QEMU内部有一个gdb server，启动之后，QEMU会等待gdb客户端连接。然后在计算机上再启动一个gdb客户端

{% gi 2 2%}
  ![pic1](image-20201207213216980.png)
  ![pic1](image-20201207213311003.png)

{% endgi %}

`b _entry`在程序的入口处设置一个断点。

<img src="image-20201207214718702.png" alt="image-20201207214718702" style="zoom:80%;" />

程序的起始位置应该是0x80000000, 但事实上，这里的breakpoint设置在了0x8000000a

`c` 运行程序，停在了0x8000000a, 此处的指令为`csrr a1, mhartid`, 这条指令读取了控制系统寄存器（Control System Register）`mhartid`，并将结果加载到`a1`寄存器。QEMU会模拟执行这条指令，之后执行下一条指令。



<img src="image-20201207215026892.png" alt="image-20201207215026892" style="zoom:80%;" />



XV6从`entry.s`开始启动，运行在machine mode下，这个时候没有内存分页，没有隔离性。XV6运行`start.c`后切换到supervisor mode, 然后准备运行`main.c`。我们在main函数设置一个断点，main函数已经运行在supervisor mode了。接下来运行程序，程序会在main函数的第一条指令停住。

<img src="image-20201207215406013.png" alt="image-20201207215406013" style="zoom:80%;" />



接下来，输入`layout split`在gdb的layout split模式下运行，从这个视图可以看出gdb要执行的下一条指令是什么，断点具体在什么位置。

<img src="image-20201207215518155.png" alt="image-20201207215518155" style="zoom:80%;" />

这里只在一个CPU上运行QEMU（见最初的make参数），这样会使得gdb调试更加简单。因为现在只指定了一个CPU核，QEMU只会仿真一个核，可以单步执行程序（因为在单核或者单线程场景下，单个断点就可以停止整个程序的运行）

在gdb中输入n，可以执行到下一条指令。这里调用了一个名为consoleinit的函数，他的功能是设置好console。一旦console设置好了，接下来可以向console打印输出（代码16、17行）。执行完16、17行之后，可以在QEMU看到相应的输出。

<img src="image-20201207215718648.png" alt="image-20201207215718648" style="zoom:80%;" />



除了console之外，还有许多代码来做初始化

- `kinit`：设置好页表分配器（page allocator）
- `kvminit`：设置好虚拟内存
- `kvminithart`：打开页表
- `processinit`：设置好初始进程或者说设置好进程表单
- `trapinit/trapinithart`：设置好user/kernel mode转换代码
- `plicinit/plicinithart`：设置好中断控制器PLIC（Platform Level Interrupt Controller），我们后面在介绍中断的时候会详细的介绍这部分，这是我们用来与磁盘和console交互方式
- `binit`：分配buffer cache
- `iinit`：初始化inode缓存
- `fileinit`：初始化文件系统
- `virtio_disk_init`：初始化磁盘
- `userinit`：最后当所有的设置都完成了，操作系统也运行起来了，会通过userinit运行第一个进程，这里有点意思，接下来我们看一下userinit

`userinit`函数启动了一个进程，调用`exec`系统调用运行了一个小程序 (定义在`initcode`数组中，左边所示，对应的汇编程序如右边所示)

<img src="image-20201207220205114.png" alt="image-20201207220205114" style="zoom:80%;" />

这个汇编程序中，它首先将init中的内容的指针加载到`a0`（`la a0, init`），`argv`中的地址加载到`a1`（`la a1, argv`），`exec`系统调用对应的数字加载到`a7`（`li a7, SYS_exec`），最后调用`ECALL。`所以这里执行了3条指令，之后在第4条指令将控制权交给了操作系统。

如果在syscall中设置一个断点，

<img src="image-20201207220443312.png" alt="image-20201207220443312" style="zoom:80%;" />

并让程序运行起来。`userinit`会创建初始进程，返回到用户空间，执行刚刚介绍的3条指令，再回到内核空间。这里是任何XV6用户会使用到的第一个系统调用。

通过在gdb中执行c，让程序运行起来，我们现在进入到了syscall函数。



<img src="image-20201207220543719.png" alt="image-20201207220543719" style="zoom:80%;" />

```c
void
syscall(void)
{
  int num;
  struct proc *p = myproc();

  num = p->trapframe->a7;
  if(num > 0 && num < NELEM(syscalls) && syscalls[num]) {
    p->trapframe->a0 = syscalls[num]();
  } else {
    printf("%d %s: unknown sys call %d\n",
            p->pid, p->name, num);
    p->trapframe->a0 = -1;
  }
}
```

`num = p->trapframe->a7` 会读取使用的系统调用对应的整数。当代码执行完这一行之后，我们可以在gdb中打印num，可以看到是7

<img src="image-20201207220710342.png" alt="image-20201207220710342" style="zoom:80%;" />

如果我们查看`syscall.h`，可以看到7对应的是`exec`这个系统调用。

```c
// System call numbers
#define SYS_fork    1
#define SYS_exit    2
#define SYS_wait    3
#define SYS_pipe    4
#define SYS_read    5
#define SYS_kill    6
#define SYS_exec    7
...
```

所以，这里本质上是告诉内核，某个用户应用程序执行了`ECALL`指令，并且想要调用`exec`系统调用。

`p->trapframe->a0 = syscall[num]()` 这一行是实际执行系统调用。这里可以看出，num用来索引一个数组，这个数组是一个函数指针数组，可以预期的是 `syscall[7]`对应了`exec`的入口函数。

跳到`sys_exec`函数中

<img src="image-20201207220847640.png" alt="image-20201207220847640" style="zoom:80%;" />

`sys_exec`中的第一件事情是从用户空间读取参数，它会读取path，也就是要执行程序的文件名。这里首先会为参数分配空间，然后从用户空间将参数拷贝到内核空间。之后我们打印path，

<img src="image-20201207220950356.png" alt="image-20201207220950356" style="zoom:80%;" />

可以看到传入的就是`init`程序。所以，综合来看，`initcode`完成了通过`exec`调用`init`程序。

再来看`init`程序，

```c
// init: The initial user-level program

#include "kernel/types.h"
#include "kernel/stat.h"
#include "kernel/spinlock.h"
#include "kernel/sleeplock.h"
#include "kernel/fs.h"
#include "kernel/file.h"
#include "user/user.h"
#include "kernel/fcntl.h"

char *argv[] = { "sh", 0 };

int
main(void)
{
  int pid, wpid;

  if(open("console", O_RDWR) < 0){
    mknod("console", CONSOLE, 0);
    open("console", O_RDWR);
  }
  dup(0);  // stdout
  dup(0);  // stderr

  for(;;){
    printf("init: starting sh\n");
    pid = fork();
    if(pid < 0){
      printf("init: fork failed\n");
      exit(1);
    }
    if(pid == 0){
      exec("sh", argv);
      printf("init: exec sh failed\n");
      exit(1);
    }

    for(;;){
      // this call to wait() returns if the shell exits,
      // or if a parentless process exits.
      wpid = wait((int *) 0);
      if(wpid == pid){
        // the shell exited; restart it.
        break;
      } else if(wpid < 0){
        printf("init: wait returned an error\n");
        exit(1);
      } else {
        // it was a parentless process; do nothing.
      }
    }
  }
}

```

`init`会为用户空间设置好一些东西，比如配置好console，调用`fork`，并在`fork`出的子进程中执行shell。

最终的效果就是Shell运行起来了。如果再次运行代码，还会陷入到`syscall`中的断点，并且同样也是调用`exec`系统调用，只是这次是通过`exec`运行Shell。当Shell运行起来之后，可以从QEMU看到Shell。

<img src="image-20201207221231563.png" alt="image-20201207221231563" style="zoom:80%;" />