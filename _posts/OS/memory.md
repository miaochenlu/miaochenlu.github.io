处理page fault时需要考虑的几个问题

* 缺页异常是由于访问用户地址空间中的有效地址而引起，还是应用程序试图访问内核的受保护区域？ 
* 目标地址对应于某个现存的映射吗？ 
* 获取该区域的数据，需要使用何种机制？

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20191217235904390.png" alt="image-20191217235904390" style="zoom:50%;" />

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20191218000057660.png" alt="image-20191218000057660" style="zoom:50%;" />



```cpp
fastcall void __kprobes do_page_fault(struct pt_regs * regs, unsigned long error_code);
```

# 1. 参数

* `struct pt_regs* regs`

> 发生异常时使用中的寄存器集合

* `unsigned long error_code`

> 错误代码

| 比特位 | 置位                   | 未置位                       |
| ------ | ---------------------- | ---------------------------- |
| 0      | 缺页                   | [保护异常]没有足够的访问权限 |
| 1      | 读访问                 | 写访问                       |
| 2      | 用户态                 | 核心态                       |
| 3      | 检测到保留位           |                              |
| 4      | 缺页异常在取指令时发生 |                              |



# 2. 代码

```cpp
struct task_struct* tsk;
struct mm_strut* mm;
struct vm_area_struct* vma;
unsigned long address;
unsigned long page;
int write, si_code;
int fault;

address = read_cr2();//读取CR2寄存器获取触发异常的访问地址
```

声明很多供后续使用的变量之后，内核在address中保存了触发异常的地址。

```cpp
if (notify_die(DIE_PAGE_FAULT, "page fault", regs, error_code, 14,
					SIGSEGV) == NOTIFY_STOP)
		return;
if (regs->eflags & (X86_EFLAGS_IF|VM_MASK))
		local_irq_enable();
```

如果发生异常时系统状态EFLAGS的状态，那么保存了cr2之后就可以允许中断发生，由`local_irq_enable()`函数控制

```cpp
tsk = current;
si_code = SEGV_MAPERR;

//检查该异常的触发地址是不是位于内核地址空间
if(unlikely(address >= TASK_SIZE)) {
  if(!(error_code & 0x0000000d) && vmalloc_fault(address) >= 0) 
    return;
//如果异常是在中断期间或内核线程中触发，也没有自身的上下文因而也没有独立的mm_struct实例，则内核会跳转到bad_area_nosemaphore标号
  goto bad_area_nosemaphore;
}
```

`address >= TASK_SIZE`用来判断是异常的触发地址是否位于内核地址空间，如果`unlikely(address >= TASK_SIZE)`为true, 则说明是的。

然后检查`error_code`看看来确认当前是否处于内核态也就是`!(error_code & 0x0000000d)`

如果异常发生于内核空间，则`error_code & 4 == 0`

而且异常不是保护错误`error_code & 9 == 0`

> 如果以上条件都满足，也就说明异常的触发地址位于内核地址空间，并且当前处于内核态

然后尝试用`vmalloc_fault()`来解决这个异常

如果地址属于vmalloc区，则从主内核页表同步数据到进程页表

否则，不应该产生page fault

```cpp
static noinline int vmalloc_fault(unsigned long address)
{
  /*pdg: page global directory
  	pmd: page middle directory
  	pte: page table entry
  */
	unsigned long pgd_paddr;
	pmd_t *pmd_k;
	pte_t *pte_k;
 
	//确定触发异常的地址是否处于VMALLOC区域
	if (!(address >= VMALLOC_START && address < VMALLOC_END))
		return -1;
 
	/*
	 * Synchronize this task's top level page-table
	 * with the 'reference' page table.
	 *
	 * Do _not_ use "current" here. We might be inside
	 * an interrupt in the middle of a task switch..
	 */
	pgd_paddr = read_cr3();//获取当前的PGD地址
	pmd_k = vmalloc_sync_one(__va(pgd_paddr), address);//将当前使用的页表和内核页表同步
	if (!pmd_k)
		return -1;
 
	/*到这里已经获取了内核页表对应于address的pmd,并且将该值设置给了当前使用页表的pmd，
	  最后一步就是判断pmd对应的pte项是否存在*/
	pte_k = pte_offset_kernel(pmd_k, address);//获取pmd对应address的pte项
	if (!pte_present(*pte_k))//判断pte项是否存在，不存在则失败
		return -1;
 
	return 0;
}
```



bad_area_nosemaphore表明这次异常是由于对非法的地址访问造成的

```cpp
mm = tsk->mm;

if(in_atomic() || !mm)
  goto bad_area_nosemaphore;
...
  
bad_area_nosemaphore:
	if(error_code & 4) {
    force_sig_info_fault(SIGSEGV, si_code, address, tsk);
    return;
  }

no_context:
	//准备好处理这个内核异常了吗
	if(fixup_exception(regs))
    return;
```



## 2.4

如果异常并非出现在中断期间，也有相关的上下文，则内核检查进程的地址空间是否包含异常地址所在的区域。它调用了`find_vma`函数

```cpp
vma = find_vma(mm, address);
if(!vma)
  goto bad_area;
if(vma->vm_start <= address)	//访问有效
  goto good_area;
if(!(vma->vm_flags & VM_GROWSDOWN))
  goto bad_area;
//确定是堆栈的情况，如果是在用户态下发生异常，则需要判断是否做了对比esp寄存器的地址还要低的地址访问操作。这是不允许的
if (error_code & 4) {
		if (address + 32 < regs->esp)//32考虑了特殊指令，做了宽限
			goto bad_area;
}
if (expand_stack(vma, address))
  goto bad_area;
//调用expand_stack适当地增大栈。如果成功，则结果返回0。不成功goto bad_area
if(expand_stack(vma, address))
  goto bad_area
```

在内核发现地址有效或无效时，会分别跳转到good_area和bad_area标号。 

搜索可能得到下面各种不同的结果。

* 没有找到结束地址在address之后的区域，在这种情况下访问是无效的。
* 异常地址在找到的区域内，在这种情况下访问是有效的，缺页异常由内核负责恢复。
*  找到一个结束地址在异常地址之后的区域，但异常地址不在该区域内。这可能有下面两种原因。
  * 该区域的VM_GROWSDOWN标志置位。这意味着区域是栈，自顶向下增长。接下来调用expand_stack适当地增大栈。如果成功，则结果返回0，内核在good_area标号恢复执行。否则，认为访问无效。
  *  找到的区域不是栈，访问无效。 

在上述代码之后，是good_area相关的处理逻辑。

### good_area

```cpp
good_area:
	si_code = SEGV_ACCERR;
	write = 0;
	switch(error_code & 3) {
    default:
#ifdef TEST_VERIFY_AREA
			if (regs->cs == KERNEL_CS)
				printk("WP fault at %08lx\n", regs->eip);
#endif
    case 2:
      if(!(vma->vm_flags & VM_WRITE))
        goto bad_area;
      write++;
      break;
    case 1:
      goto bad_area
        case 0:
      if(!(vma->vm_flags & (VM_READ | VM_EXEC)))
        goto bad_area;
  }
```

存在对应于异常地址的映射，并不一定意味着实际上可以允许访问。

内核必须检查访问权限，即比特位0和1。

可能出现的情况有

* 在写访问的情况下，必须置位VM_WRITE（比特位1置位，即3和2对应的情况）。否则，访问无效，跳转到bad_area标号恢复执行。
* 如果是读访问现存页（1对应的情况），该异常必定是硬件检测到的权限异常。接下来跳转到 bad_area恢复执行。
* 如果读取不存在的页，则内核必须检查是否置位VM_READ或VM_EXEC，在置位的情况下访问有效。否则拒绝读访问，内核跳转到bad_area。
*  如果内核没有显式跳转到 bad_area ， 则代码的执行将贯穿 case 语句， 到达下面给出的 handle_mm_fault调用。该函数负责校正缺页异常（即，读取所需数据）。

### Survive

```cpp
survive:
	switch (handle_mm_fault(mm, vma, address, write)) {
		case VM_FAULT_MINOR:
			tsk->min_flt++;
			break;
		case VM_FAULT_MAJOR:
			tsk->maj_flt++;
			break;
		case VM_FAULT_SIGBUS:
			goto do_sigbus;
		case VM_FAULT_OOM:
			goto out_of_memory;
		default:
			BUG();
	}

	/*
	 * Did it hit the DOS screen memory VA from vm86 mode?
	 */
	if (regs->eflags & VM_MASK) {
		unsigned long bit = (address - 0xA0000) >> PAGE_SHIFT;
		if (bit < 32)
			tsk->thread.screen_bitmap |= 1 << bit;
	}
	up_read(&mm->mmap_sem);
	return;
```

`handle_mm_fault`，用于选择适当的异常恢复方法[按需调页、换入， 等等]，并应用选择的方法。

如果页成功建立，则例程返回`VM_FAULT_MINOR`（数据已经在内存中）或`VM_FAULT_MAJOR`（数据需要从块设备读取）。内核接下来更新进程的统计量。

但在创建页时，也可能发生异常。

* 如果用于加载页的物理内存不足`VM_FAULT_OOM`，内核会强制终止该进程，在最低限度上维持系统的运行。

* 如果对数据的访问已经允许，但由于其他的原因失败（例如，访问的映射已经在访问的同时被另一个进程收缩，不再存在于给定的地址），则将SIGBUS信号发送给进程。

### bad_area

```cpp
//在函数do_page_fault中如果有出错情况出现，都会跳转到这里，准备返回。在这段过程中，主要是以信号和tsk内部数据系统报告出错原因
bad_area:
	up_read(&mm->mmap_sem);//释放信号量，因为以后的操作不会涉及mm数据的修改
```



```cpp
bad_area_nosemaphore:
	/* User mode accesses just cause a SIGSEGV */
	if (error_code & 4) {
		if (is_prefetch(regs, address, error_code))
			return;

		tsk->thread.cr2 = address;
		/* Kernel addresses are always protection faults */
		tsk->thread.error_code = error_code | (address >= TASK_SIZE);
		tsk->thread.trap_no = 14;
		force_sig_info_fault(SIGSEGV, si_code, address, tsk);
		return;
	}
```

如果`error_code`标记为用户态，直接返回给用户进程一个segmentation fault的信号`SIGSEGV`就可以了

```cpp
#ifdef CONFIG_X86_F00F_BUG
	/*
	 * Pentium F0 0F C7 C8 bug workaround.
	 */
	if (boot_cpu_data.f00f_bug) {
		unsigned long nr;
		
		nr = (address - idt_descr.address) >> 3;

		if (nr == 6) {
			do_invalid_op(regs, 0);
			return;
		}
	}
#endif
```

这边是一个pentium的bug修正

### no_context

在中断过程中或者内核线程运行过程中出现缺页异常时就会跳转到这里

```cpp
no_context:
	//从exception_table中根据当前的regs->eip查找是否存在对应的fixup函数，如果有，九江eip初始化为fixup的地址，然后返回。一般在系统调用中传递地址参数可能会出现这种异常。
	if (fixup_exception(regs))
		return;

 	if (is_prefetch(regs, address, error_code))
 		return;

//运行到这里都是内核试图访问一个不存在的页面而产生的，内核会产生一个oops信息打印在终端上
	bust_spinlocks(1);

#ifdef CONFIG_X86_PAE
	if (error_code & 16) {
		pte_t *pte = lookup_address(address);

		if (pte && pte_present(*pte) && !pte_exec_kernel(*pte))
			printk(KERN_CRIT "kernel tried to execute NX-protected page - exploit attempt? (uid: %d)\n", current->uid);
	}
#endif
```



```cpp
if (address < PAGE_SIZE)
		printk(KERN_ALERT "Unable to handle kernel NULL pointer dereference");
	else
		printk(KERN_ALERT "Unable to handle kernel paging request");
```

如果address<PAGE_SIZE, 被内核认为就相当于0地址，否则，内核打印无法处理页面请求的消息

```cpp
	printk(" at virtual address %08lx\n",address);
	printk(KERN_ALERT " printing eip:\n");
	printk("%08lx\n", regs->eip);
	page = read_cr3();
	page = ((unsigned long *) __va(page))[address >> 22];
	printk(KERN_ALERT "*pde = %08lx\n", page);
	/*
	 * We must not directly access the pte in the highpte
	 * case, the page table might be allocated in highmem.
	 * And lets rather not kmap-atomic the pte, just in case
	 * it's allocated already.
	 */
#ifndef CONFIG_HIGHPTE
	if (page & 1) {
		page &= PAGE_MASK;
		address &= 0x003ff000;
		page = ((unsigned long *) __va(page))[address >> PAGE_SHIFT];
		printk(KERN_ALERT "*pte = %08lx\n", page);
	}
#endif
	tsk->thread.cr2 = address;
	tsk->thread.trap_no = 14;
	tsk->thread.error_code = error_code;
	die("Oops", regs, error_code);
	bust_spinlocks(0);
	do_exit(SIGKILL);
```

这里会打印一些相关信息

### out_of_memory

在handle_mm_fault()返回负数的情况下，会到这里，说明出现了out of memory的情况

```cpp
out_of_memory:
	up_read(&mm->mmap_sem);
	if (tsk->pid == 1) {
		yield();
		down_read(&mm->mmap_sem);
		goto survive;
	}
	printk("VM: killing process %s\n", tsk->comm);
	if (error_code & 4)
		do_exit(SIGKILL);
	goto no_context;
```

* 如果出现异常的是1号init进程，不能kill。

修改init进程的调度策略为SCHED_YIELD, 等待其他进程的内存释放，然后调用schedule重新进入调度，之后转入survive, 再试一次。

* 如果是用户进程，直接kill。
* 如果是内核进程，跳转到no_context

### do_sigbus

在申请页面过程中因为I/O调度错误而无法申请页面，向进程发送SIGBUS信号，终止进程

```cpp
do_sigbus:
	up_read(&mm->mmap_sem);

	/* Kernel mode? Handle exceptions or die */
	if (!(error_code & 4))
		goto no_context;

	/* User space => ok to do another page fault */
	if (is_prefetch(regs, address, error_code))
		return;

	tsk->thread.cr2 = address;
	tsk->thread.error_code = error_code;
	tsk->thread.trap_no = 14;
	force_sig_info_fault(SIGBUS, BUS_ADRERR, address, tsk);
	return;
```

### vmalloc_fault

在内核态写不在内存中的page会跳转到这里

```cpp
vmalloc_fault:
	{
		/*
		 * Synchronize this task's top level page-table
		 * with the 'reference' page table.
		 *
		 * Do _not_ use "tsk" here. We might be inside
		 * an interrupt in the middle of a task switch..
		 */
		int index = pgd_index(address);
		unsigned long pgd_paddr;
		pgd_t *pgd, *pgd_k;
		pud_t *pud, *pud_k;
		pmd_t *pmd, *pmd_k;
		pte_t *pte_k;

		pgd_paddr = read_cr3();
		pgd = index + (pgd_t *)__va(pgd_paddr);
		pgd_k = init_mm.pgd + index;

		if (!pgd_present(*pgd_k))
			goto no_context;

		/*
		 * set_pgd(pgd, *pgd_k); here would be useless on PAE
		 * and redundant with the set_pmd() on non-PAE. As would
		 * set_pud.
		 */

		pud = pud_offset(pgd, address);
		pud_k = pud_offset(pgd_k, address);
		if (!pud_present(*pud_k))
			goto no_context;
		
		pmd = pmd_offset(pud, address);
		pmd_k = pmd_offset(pud_k, address);
		if (!pmd_present(*pmd_k))
			goto no_context;
		set_pmd(pmd, *pmd_k);

		pte_k = pte_offset_kernel(pmd_k, address);
		if (!pte_present(*pte_k))
			goto no_context;
		return;
	}
}
```



# 用户空间page fault

```cpp
static inline int handle_pte_fault(struct mm_struct *mm, struct vm_area_struct *vma, 
                                   unsigned long address, pte_t *pte, pmd_t *pmd, 
                                   int write_access) {
  pte_t entry;
  spinlock_t *ptl;
  entry = *pte;
  if (!pte_present(entry)) { 
    if (pte_none(entry)) { 
      if (vma->vm_ops) { 
        return do_linear_fault(mm, vma, address, pte, pmd, write_access, entry); 
      } 
      return do_anonymous_page(mm, vma, address, pte, pmd, write_access); 
    } 
    if (pte_file(entry)) 
      return do_nonlinear_fault(mm, vma, address, pte, pmd, write_access, entry);
    return do_swap_page(mm, vma, address, pte, pmd, write_access, entry);
  }
}

```

`!pte_present(entry)`说明page不在物理内存中

* 如果没有对应的page table entry, 内核从头开始加载这个页。在Linux虚拟内存中，如果页对应的vma映射的是文件，则称为映射页，如果不是映射的文件，则称为匿名页。
  * `vma->vm_ops`为true, 说明是映射页，调用`do_linear_fault`
  * `vma->vm_ops`为false, 说明是匿名页，调用`do_anonymous_fault`
* 如果该页标记为不存在，而页表中保存了相关的信息，则意味着该页已经换出，因而必须从 系统的某个交换区换入（换入或按需调页）。
* 非线性映射已经换出的部分不能像普通页那样换入， 因为必须正确地恢复非线性关联。 `pte_file`函数可以检查页表项是否属于非线性映射，`do_nonlinear_fault`在这种情况下可用于处理异常。