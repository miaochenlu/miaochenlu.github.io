---
title: page tables [os lab3]
date: 2020-12-19 21:22:11
index_img: /img/xv6lab.jpg
tags: [Operating System, xv6]
categories: Operating System
---

# Print a page table

To help you learn about RISC-V page tables, and perhaps to aid future debugging, your first task is to write a function that prints the contents of a page table.

{% note primary%}

Define a function called `vmprint()`. It should take a `pagetable_t` argument, and print that pagetable in the format described below. Insert `if(p->pid==1) vmprint(p->pagetable)` in exec.c just before the `return argc`, to print the first process's page table. You receive full credit for this assignment if you pass the `pte printout` test of `make grade`.

{% endnote %}

Now when you start xv6 it should print output like this, describing the page table of the first process at the point when it has just finished `exec()`ing `init`:

```
page table 0x0000000087f6e000
..0: pte 0x0000000021fda801 pa 0x0000000087f6a000
.. ..0: pte 0x0000000021fda401 pa 0x0000000087f69000
.. .. ..0: pte 0x0000000021fdac1f pa 0x0000000087f6b000
.. .. ..1: pte 0x0000000021fda00f pa 0x0000000087f68000
.. .. ..2: pte 0x0000000021fd9c1f pa 0x0000000087f67000
..255: pte 0x0000000021fdb401 pa 0x0000000087f6d000
.. ..511: pte 0x0000000021fdb001 pa 0x0000000087f6c000
.. .. ..510: pte 0x0000000021fdd807 pa 0x0000000087f76000
.. .. ..511: pte 0x0000000020001c0b pa 0x0000000080007000
  
```

The first line displays the argument to `vmprint`. After that there is a line for each PTE, including PTEs that refer to page-table pages deeper in the tree. Each PTE line is indented by a number of `" .."` that indicates its depth in the tree. Each PTE line shows the PTE index in its page-table page, the pte bits, and the physical address extracted from the PTE. Don't print PTEs that are not valid. In the above example, the top-level page-table page has mappings for entries 0 and 255. The next level down for entry 0 has only index 0 mapped, and the bottom-level for that index 0 has entries 0, 1, and 2 mapped.

Your code might emit different physical addresses than those shown above. The number of entries and the virtual addresses should be the same.

## Some hints

{% note success%}

- You can put `vmprint()` in `kernel/vm.c`.

{% endnote %}

{% note success%}

- Use the macros at the end of the file `kernel/riscv.h`.

{% endnote %}

{% note success%}

- The function `freewalk` may be inspirational.

{% endnote %}

这个函数展示了如何去recursively访问page table

```c
void
freewalk(pagetable_t pagetable)
{
  // there are 2^9 = 512 PTEs in a page table.
  for(int i = 0; i < 512; i++){
    pte_t pte = pagetable[i];
    if((pte & PTE_V) && (pte & (PTE_R|PTE_W|PTE_X)) == 0){
      // this PTE points to a lower-level page table.
      uint64 child = PTE2PA(pte);
      freewalk((pagetable_t)child);
      pagetable[i] = 0;
    } else if(pte & PTE_V){
      panic("freewalk: leaf");
    }
  }
  kfree((void*)pagetable);
}
```



{% note success%}

- Define the prototype for `vmprint` in `kernel/defs.h` so that you can call it from `exec.c`.

{% endnote %}

<img src="image-20201219212505432.png" alt="image-20201219212505432" style="zoom:80%;" />

{% note success%}

- Use `%p` in your printf calls to print out full 64-bit hex PTEs and addresses as shown in the example.

{% endnote %}



## Final Code

想法比较自然，写一个递归函数`vmprintRecursive`去深度遍历page table, 因为打印`.. `的原因，还提供了一个函数参数level

这个深度遍历函数的逻辑是: 从0-511去查看page table entry

如果查到了pte并且他是valid的, 就通过`PTE2PA`获取指向的physical address。这个时候打印题目所要求的内容

更进一步，如果这个pte不是PTE_R, PTE_W, PTE_X没有被置位，说明这个pte指向了下一层page table的physical address, 递归去访问。

```c
void vmprintRecursive(pagetable_t pagetable, int level) {
  for(int i = 0; i < 512; i++){
    pte_t pte = pagetable[i];
    if(pte & PTE_V) {
      uint64 child = PTE2PA(pte);
      for(int i = 0; i <= level; i++) {
        printf("..");
        if(i != level) printf(" ");
      }

      printf("%d: pte %p pa %p\n", i, pte, child);
      if((pte & (PTE_R | PTE_W | PTE_X)) == 0)
          vmprintRecursive((pagetable_t)child, level + 1);
    } 
  }
}

void
vmprint(pagetable_t pagetable) {
  printf("page table %p\n", pagetable);
  vmprintRecursive(pagetable, 0);
}
```



# A kernel page table per process

{% note primary%}

Xv6 has a single kernel page table that's used whenever it executes in the kernel. The kernel page table is a direct mapping to physical addresses, so that kernel virtual address *x* maps to physical address *x*. Xv6 also has a separate page table for each process's user address space, containing only mappings for that process's user memory, starting at virtual address zero. Because the kernel page table doesn't contain these mappings, user addresses are not valid in the kernel. Thus, when the kernel needs to use a user pointer passed in a system call (e.g., the buffer pointer passed to `write()`), the kernel must first translate the pointer to a physical address. The goal of this section and the next is to allow the kernel to directly dereference user pointers.



Xv6 has a single kernel page table that's used whenever it executes in the kernel. The kernel page table is a direct mapping to physical addresses, so that kernel virtual address *x* maps to physical address *x*. Xv6 also has a separate page table for each process's user address space, containing only mappings for that process's user memory, starting at virtual address zero. Because the kernel page table doesn't contain these mappings, user addresses are not valid in the kernel. Thus, when the kernel needs to use a user pointer passed in a system call (e.g., the buffer pointer passed to `write()`), the kernel must first translate the pointer to a physical address. The goal of this section and the next is to allow the kernel to directly dereference user pointers.

{% endnote %}



这个实验中修改的部分有`vm.c, proc.h, proc.c, defs.h`

## Some hints

{%note success%}

- Add a field to `struct proc` for the process's kernel page table.

{% endnote %}

<img src="image-20201222200731803.png" alt="image-20201222200731803" style="zoom: 50%;" />

{% note success %}

- A reasonable way to produce a kernel page table for a new process is to implement a modified version of `kvminit` that makes a new page table instead of modifying `kernel_pagetable`. You'll want to call this function from `allocproc`.

{% endnote %}

`allocproc`函数分配并且初始化struct proc, 因为我们在struct proc中增加了kernel page table, 因此需要在这里进行初始化。

在`kernel/vm.c`中`kvminit`函数创建了direct-map kernel page table。在`kernel/proc.c`中仿照`kvminit`函数新写一个`proc_kpagetable`函数来创建process中的kernel page table

```c
pagetable_t
proc_kpagetable(struct proc *p)
{
  pagetable_t kpagetable;

  kpagetable = uvmcreate();
  if (kpagetable == 0)
    return 0;
  
  // Fill in the process's kernel page table, the same as kernel_pagetable
  ukvmmap(kpagetable, UART0, UART0, PGSIZE, PTE_R | PTE_W);
  ukvmmap(kpagetable, VIRTIO0, VIRTIO0, PGSIZE, PTE_R | PTE_W);
  ukvmmap(kpagetable, PLIC, PLIC, 0x400000, PTE_R | PTE_W);
  ukvmmap(kpagetable, KERNBASE, KERNBASE, (uint64)etext-KERNBASE, PTE_R | PTE_X);
  ukvmmap(kpagetable, (uint64)etext, (uint64)etext, PHYSTOP-(uint64)etext, PTE_R | PTE_W);
  ukvmmap(kpagetable, TRAMPOLINE, (uint64)trampoline, PGSIZE, PTE_R | PTE_X);
  ukvmmap(kpagetable, TRAPFRAME, (uint64)(p->trapframe), PGSIZE, PTE_R | PTE_W);
  return kpagetable;
}
```

在`kernel/proc.c`的`allocproc`函数中调用`proc_kpagetable`函数

```c
  // An empty user page table.
  p->pagetable = proc_pagetable(p);
  if(p->pagetable == 0){
    freeproc(p);
    release(&p->lock);
    return 0;
  }

  p->kpagetable = proc_kpagetable(p);
  if(p->kpagetable == 0){
    freeproc(p);
    release(&p->lock);
    return 0;
  }
```



{% note success %}

- Make sure that each process's kernel page table has a mapping for that process's kernel stack. In unmodified xv6, all the kernel stacks are set up in `procinit`. You will need to move some or all of this functionality to `allocproc`.

{% endnote %}

修改`kernel/proc.c`中的`procinit`函数, 注释掉分配kernel stack的部分

```c
void
procinit(void)
{
  struct proc *p;
  
  initlock(&pid_lock, "nextpid");
  for(p = proc; p < &proc[NPROC]; p++) {
      initlock(&p->lock, "proc");
      // Allocate a page for the process's kernel stack.
      // Map it high in memory, followed by an invalid
      // guard page.
      //char *pa = kalloc();
      //if(pa == 0)
      //    panic("kalloc");
      //uint64 va = KSTACK((int) (p - proc));
      //kvmmap(p->kpagetable, va, (uint64)pa, PGSIZE, PTE_R | PTE_W);
      //p->kstack = va;
  }
  kvminithart();
}
```

将这部分代码移动到`allocproc`中

```c
  // Allocate a page for the process's kernel stack.
  // Map it high in memory, followed by an invalid
  // guard page.
  char *pa = kalloc();
  if(pa == 0)
    panic("kalloc");
  uint64 va = KSTACK((int) (p - proc));
  ukvmmap(p->kpagetable, va, (uint64)pa, PGSIZE, PTE_R | PTE_W);
  p->kstack = va;
```

注意: 这里将`kvmmap`函数改成了`ukvmmap`函数, `kvmmap`函数默认操作`kernel_pagetable`, 而这里我们想要在process的`kpagetable`中进行映射， 因此增加了一个`ukvmmap`函数，多提供了一个`pagetable`的参数

```c
// add a mapping to the kernel page table.
// only used when booting.
// does not flush TLB or enable paging.
void
kvmmap(uint64 va, uint64 pa, uint64 sz, int perm)
{
  if(mappages(kernel_pagetable, va, sz, pa, perm) != 0)
    panic("kvmmap");
}

void
ukvmmap(pagetable_t kpagetable, uint64 va, uint64 pa, uint64 sz, int perm)
{
  if(mappages(kpagetable, va, sz, pa, perm) != 0)
    panic("ukvmmap");
}
```

{% note success %}

- Modify `scheduler()` to load the process's kernel page table into the core's `satp` register (see `kvminithart` for inspiration). Don't forget to call `sfence_vma()` after calling `w_satp()`.

{% endnote %}

<img src="image-20201222202345524.png" alt="image-20201222202345524" style="zoom:50%;" />

{% note success %}

- `scheduler()` should use `kernel_pagetable` when no process is running.

{% endnote %}

<img src="image-20201222202559087.png" alt="image-20201222202559087" style="zoom:50%;" />

{% note warning %}

注意, 在`kernel/vm.c`中，`kvmpa`函数会在进程执行期间调用，这个函数不应调用全局kernel page table, 而应调用进程对应的kernel page table

{% endnote %}

```c
// translate a kernel virtual address to
// a physical address. only needed for
// addresses on the stack.
// assumes va is page aligned.
uint64
kvmpa(uint64 va)
{
  uint64 off = va % PGSIZE;
  pte_t *pte;
  uint64 pa;
  
  pte = walk(myproc()->kpagetable, va, 0);
  if(pte == 0)
    panic("kvmpa");
  if((*pte & PTE_V) == 0)
    panic("kvmpa");
  pa = PTE2PA(*pte);
  return pa+off;
}
```

为了使用`myproc()`函数 ，还需要在头文件中

```c
#include "spinlock.h"
#include "proc.h"
```



{% note success %}

- Free a process's kernel page table in `freeproc`.
- You'll need a way to free a page table without also freeing the leaf physical memory pages.

{% endnote %}

修改`kernel/proc.c`中的`freeproc`函数。free kernel stack, free kernel page table

* free kstack是通过kpage table去查找kstack对应的物理地址，然后free
* free kernel page table是通过`kernel/proc.c`中一个新写的函数`proc_freekpagetable`来实现的。这个函数借鉴`kernel/vm.c`中的`freewalk`函数 。注意这里只free kernel page table, 不能把page table指向的地址free掉，也就是不能free leaves

```c
void
proc_freekpagetable(pagetable_t kpagetable)
{
  // there are 2^9 = 512 PTEs in a page table.
  for(int i = 0; i < 512; i++) {
    pte_t pte = kpagetable[i];
    if(pte & PTE_V) {
      kpagetable[i] = 0;

      if((pte & (PTE_R | PTE_W | PTE_X)) == 0) {
        uint64 child = PTE2PA(pte);
        proc_freekpagetable((pagetable_t)child);
      }
    }
  }
  kfree((void*)kpagetable);
}
```



```c
// free a proc structure and the data hanging from it,
// including user pages.
// p->lock must be held.
static void
freeproc(struct proc *p)
{
  if(p->trapframe)
    kfree((void*)p->trapframe);
  p->trapframe = 0;
  // free kernel stack
  if(p->kstack) {
    pte_t* pte = walk(p->kpagetable, p->kstack, 0);
    if (pte == 0)
      panic("freeproc: walk");
    kfree((void*)PTE2PA(*pte));
  }
  p->kstack = 0;

  if(p->pagetable)
    proc_freepagetable(p->pagetable, p->sz);

  if(p->kpagetable)
    proc_freekpagetable(p->kpagetable);
  p->pagetable = 0;
  p->kpagetable = 0;
  p->sz = 0;
  p->pid = 0;
  p->parent = 0;
  p->name[0] = 0;
  p->chan = 0;
  p->killed = 0;
  p->xstate = 0;
  p->state = UNUSED;
}
```

注意这里要先free kstack。如果先free pagetable, 再free kstack, 则会出现error

{% note success %}

- `vmprint` may come in handy to debug page tables.

{% endnote %}

{% note success %}

- It's OK to modify xv6 functions or add new functions; you'll probably need to do this in at least `kernel/vm.c` and `kernel/proc.c`. (But, don't modify `kernel/vmcopyin.c`, `kernel/stats.c`, `user/usertests.c`, and `user/stats.c`.)

{% endnote %}

{% note success %}

- A missing page table mapping will likely cause the kernel to encounter a page fault. It will print an error that includes `sepc=0x00000000XXXXXXXX`. You can find out where the fault occurred by searching for `XXXXXXXX` in `kernel/kernel.asm`.

{% endnote %}

## Final Code

* 在`kernel/defs.h`中补上一些函数的声明

{% gi 2 2%}
  ![pic1](image-20201222204213555.png)
  ![pic1](image-20201222204251376.png)

{% endgi %}

* `kernel/proc.h`

```c
struct proc {
  struct spinlock lock;

  // p->lock must be held when using these:
  enum procstate state;        // Process state
  struct proc *parent;         // Parent process
  void *chan;                  // If non-zero, sleeping on chan
  int killed;                  // If non-zero, have been killed
  int xstate;                  // Exit status to be returned to parent's wait
  int pid;                     // Process ID

  // these are private to the process, so p->lock need not be held.
  uint64 kstack;               // Virtual address of kernel stack
  uint64 sz;                   // Size of process memory (bytes)
  pagetable_t kpagetable;      // Kernel page table
  pagetable_t pagetable;       // User page table
  struct trapframe *trapframe; // data page for trampoline.S
  struct context context;      // swtch() here to run process
  struct file *ofile[NOFILE];  // Open files
  struct inode *cwd;           // Current directory
  char name[16];               // Process name (debugging)
};

```

* `kernel/vm.c`

```c
#include "spinlock.h"
#include "proc.h"
```

```c
void
ukvmmap(pagetable_t kpagetable, uint64 va, uint64 pa, uint64 sz, int perm)
{
  if(mappages(kpagetable, va, sz, pa, perm) != 0)
    panic("ukvmmap");
}
```

```c
uint64
kvmpa(uint64 va)
{
  uint64 off = va % PGSIZE;
  pte_t *pte;
  uint64 pa;
  
  pte = walk(myproc()->kpagetable, va, 0);
  if(pte == 0)
    panic("kvmpa");
  if((*pte & PTE_V) == 0)
    panic("kvmpa");
  pa = PTE2PA(*pte);
  return pa+off;
}
```

* `kernel/proc.c`

```c
extern char etext[];  // kernel.ld sets this to end of kernel code.
extern pagetable_t kernel_pagetable;
```

```c
void
procinit(void)
{
  struct proc *p;
  
  initlock(&pid_lock, "nextpid");
  for(p = proc; p < &proc[NPROC]; p++) {
      initlock(&p->lock, "proc");
  }
  kvminithart();
}
```

```c
static struct proc*
allocproc(void)
{
  struct proc *p;

  for(p = proc; p < &proc[NPROC]; p++) {
    acquire(&p->lock);
    if(p->state == UNUSED) {
      goto found;
    } else {
      release(&p->lock);
    }
  }
  return 0;

found:
  p->pid = allocpid();

  // Allocate a trapframe page.
  if((p->trapframe = (struct trapframe *)kalloc()) == 0){
    release(&p->lock);
    return 0;
  }

  // An empty user page table.
  p->pagetable = proc_pagetable(p);
  if(p->pagetable == 0){
    freeproc(p);
    release(&p->lock);
    return 0;
  }

  p->kpagetable = proc_kpagetable(p);
  if(p->kpagetable == 0){
    freeproc(p);
    release(&p->lock);
    return 0;
  }


  // Allocate a page for the process's kernel stack.
  // Map it high in memory, followed by an invalid
  // guard page.
  char *pa = kalloc();
  if(pa == 0)
    panic("kalloc");
  uint64 va = KSTACK((int) (p - proc));
  ukvmmap(p->kpagetable, va, (uint64)pa, PGSIZE, PTE_R | PTE_W);
  p->kstack = va;

  // Set up new context to start executing at forkret,
  // which returns to user space.
  memset(&p->context, 0, sizeof(p->context));
  p->context.ra = (uint64)forkret;
  p->context.sp = p->kstack + PGSIZE;

  return p;
}
```

```c
static void
freeproc(struct proc *p)
{
  if(p->trapframe)
    kfree((void*)p->trapframe);
  p->trapframe = 0;
  // free kernel stack
  if(p->kstack) {
    pte_t* pte = walk(p->kpagetable, p->kstack, 0);
    if (pte == 0)
      panic("freeproc: walk");
    kfree((void*)PTE2PA(*pte));
  }
  p->kstack = 0;

  if(p->pagetable)
    proc_freepagetable(p->pagetable, p->sz);

  if(p->kpagetable)
    proc_freekpagetable(p->kpagetable);
  p->pagetable = 0;
  p->kpagetable = 0;
  p->sz = 0;
  p->pid = 0;
  p->parent = 0;
  p->name[0] = 0;
  p->chan = 0;
  p->killed = 0;
  p->xstate = 0;
  p->state = UNUSED;
}
```

```c
pagetable_t
proc_kpagetable(struct proc *p)
{
  pagetable_t kpagetable;

  kpagetable = uvmcreate();
  if (kpagetable == 0)
    return 0;
  
  // Fill in the process's kernel page table, the same as kernel_pagetable
  ukvmmap(kpagetable, UART0, UART0, PGSIZE, PTE_R | PTE_W);
  ukvmmap(kpagetable, VIRTIO0, VIRTIO0, PGSIZE, PTE_R | PTE_W);
  ukvmmap(kpagetable, PLIC, PLIC, 0x400000, PTE_R | PTE_W);
  ukvmmap(kpagetable, KERNBASE, KERNBASE, (uint64)etext-KERNBASE, PTE_R | PTE_X);
  ukvmmap(kpagetable, (uint64)etext, (uint64)etext, PHYSTOP-(uint64)etext, PTE_R | PTE_W);
  ukvmmap(kpagetable, TRAMPOLINE, (uint64)trampoline, PGSIZE, PTE_R | PTE_X);
  ukvmmap(kpagetable, TRAPFRAME, (uint64)(p->trapframe), PGSIZE, PTE_R | PTE_W);
  return kpagetable;
}
```

```c
void
proc_freekpagetable(pagetable_t kpagetable)
{
  // there are 2^9 = 512 PTEs in a page table.
  for(int i = 0; i < 512; i++) {
    pte_t pte = kpagetable[i];
    if(pte & PTE_V) {
      kpagetable[i] = 0;

      if((pte & (PTE_R | PTE_W | PTE_X)) == 0) {
        uint64 child = PTE2PA(pte);
        proc_freekpagetable((pagetable_t)child);
      }
    }
  }
  kfree((void*)kpagetable);
}
```

```c
void
scheduler(void)
{
  struct proc *p;
  struct cpu *c = mycpu();
  
  c->proc = 0;
  for(;;){
    // Avoid deadlock by ensuring that devices can interrupt.
    intr_on();
    
    int found = 0;
    for(p = proc; p < &proc[NPROC]; p++) {
      acquire(&p->lock);
      if(p->state == RUNNABLE) {
        // Switch to chosen process.  It is the process's job
        // to release its lock and then reacquire it
        // before jumping back to us.
        p->state = RUNNING;
        c->proc = p;

        // load process's kernel page table 
        w_satp(MAKE_SATP(p->kpagetable));
        sfence_vma();

        swtch(&c->context, &p->context);

        // Process is done running for now.
        // It should have changed its p->state before coming back.
        c->proc = 0;

        found = 1;
      }
      release(&p->lock);
    }
#if !defined (LAB_FS)
    if(found == 0) {
      w_satp(MAKE_SATP(kernel_pagetable));
      sfence_vma();
      intr_on();
      asm volatile("wfi");
    }
#else
    ;
#endif
  }
}
```





# Simplify `copyin/copyinstr`

{% note primary %}

The kernel's `copyin` function reads memory pointed to by user pointers. It does this by translating them to physical addresses, which the kernel can directly dereference. It performs this translation by walking the process page-table in software. Your job in this part of the lab is to add user mappings to each process's kernel page table (created in the previous section) that allow`copyin` (and the related string function `copyinstr`) to directly dereference user pointers.

Replace the body of `copyin` in `kernel/vm.c` with a call to `copyin_new` (defined in `kernel/vmcopyin.c`); do the same for `copyinstr` and `copyinstr_new`. Add mappings for user addresses to each process's kernel page table so that `copyin_new` and `copyinstr_new` work. You pass this assignment if `usertests`runs correctly and all the `make grade` tests pass.

This scheme relies on the user virtual address range not overlapping the range of virtual addresses that the kernel uses for its own instructions and data. Xv6 uses virtual addresses that start at zero for user address spaces, and luckily the kernel's memory starts at higher addresses. However, this scheme does limit the maximum size of a user process to be less than the kernel's lowest virtual address. After the kernel has booted, that address is `0xC000000` in xv6, the address of the PLIC registers; see `kvminit()` in `kernel/vm.c`, `kernel/memlayout.h`, and Figure 3-4 in the text. You'll need to modify xv6 to prevent user processes from growing larger than the PLIC address.

{% endnote %}



为了让kernel page table直接能够translate user space address, 需要把user pagetable的信息复制到kernel pagetable中。

这里要回答两个问题

* 怎么将user pagetable的信息复制到kernel page table (通过`ukvmcopy`函数来解决这个问题)
* 哪些函数需要生成了/改变了address mapping, 需要将user pagetable的信息复制到kernel page table (`userinit`, `fork`, `exec`, `sbrk`)



## Some hints

{%note success%}

- Replace `copyin()` with a call to `copyin_new` first, and make it work, before moving on to `copyinstr`.

{% endnote %}

修改`kernel/vm.c`中的`copyin`和`copyin_new`函数

<img src="image-20201227210648285.png" alt="image-20201227210648285" style="zoom:80%;" />

{% note success%}

- At each point where the kernel changes a process's user mappings, change the process's kernel page table in the same way. Such points include `fork()`, `exec()`, and `sbrk()`.
- What permissions do the PTEs for user addresses need in a process's kernel page table? (A page with `PTE_U` set cannot be accessed in kernel mode.)
- Don't forget about the above-mentioned PLIC limit

{%endnote%}

这几个函数都需要将user space中的地址映射拷贝到kernel page table。因此在`kernel/vm.c`中增加一个函数`ukvmcopy`来完成这项工作。

从user space的virtual address的起始地址开始，通过`walk`函数在pagetable中找到地址对应的page table entry, 然后在kernel page table中为这个page分配一个kernel page table entry。要注意对flag的处理，如果PTE_U置位，在kernel mode下是无法访问的。

```c
void ukvmcopy(pagetable_t pagetable, pagetable_t kpagetable, uint64 beginsz, uint64 endsz) {
  pte_t *upte, *kpte;
  uint64 pa;
  uint flags;
  
  if(beginsz > endsz) return;
  for(uint64 i = beginsz; i < endsz; i += PGSIZE) {
    if((upte = walk(pagetable, i, 0)) == 0)
      panic("ukvmcopy: pte should exist");
    if((kpte = walk(kpagetable, i, 1)) == 0)
      panic("ukvmcopy: walk fails");
    pa = PTE2PA(*upte);
    flags = (PTE_FLAGS(*upte) & (~PTE_U));
    *kpte = PA2PTE(pa) | flags;
  }
}
```



接下来在`fork`, `exec`, `sbrk`的代码使用这个函数，将user page table的信息记录到kernel page table.

对于`fork()`, 修改`kernel/proc.c`中的`fork`函数

<img src="image-20201227203900952.png" alt="image-20201227203900952" style="zoom:80%;" />

对于`exec`, 修改`kernel/exec.c`中的`exec`函数

<img src="image-20201227205027041.png" alt="image-20201227205027041" style="zoom:80%;" />

对于`sbrk`

`kernel/sysproc.c`中的`sys_sbrk`是这样定义的

```c
uint64
sys_sbrk(void)
{
  int addr;
  int n;

  if(argint(0, &n) < 0)
    return -1;
  addr = myproc()->sz;
  if(growproc(n) < 0)
    return -1;
  return addr;
}
```

主要工作是在`kernel/proc.c`的`growproc`函数中完成的， 因此把代码加在`growproc`中

`growproc`函数在扩张的时候要注意不能超过PLIC。

使用`ukvmcopy`的时候要注意这里的`sz`是扩张或者收缩n bytes之后的`sz`

<img src="image-20201227210053052.png" alt="image-20201227210053052" style="zoom:80%;" />



{%note success%}

- Don't forget that to include the first process's user page table in its kernel page table in `userinit`.

{% endnote %}

在创建第一个user process的时候就要将user page table复制到kernel page table中

<img src="image-20201227210335814.png" alt="image-20201227210335814" style="zoom:80%;" />



对了！不要忘记在`kernel/defs.h`中声明`ukvmcopy`, `copyin_new`, `copyinstr_new`函数

<img src="image-20201227211533018.png" alt="image-20201227211533018" style="zoom:80%;" />

# Reference

[1]https://github.com/gaofanfei/xv6-riscv-fall20/tree/pgtbl

[2]https://blog.csdn.net/u013577996/article/details/109582932

[3]https://zhuanlan.zhihu.com/p/336091300