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

## 2.1 

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

## 2.2

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

## 2.3 

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

...
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

```cpp
good_area:
	si_code = SEGV_ACCERR;
	write = 0;
	switch(error_code & 3) {
    default:
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



```cpp
survive:
fault = handle_mm_fault(mm, vma, address, write);
if(unlikely(fault & VM_FAULT_ERROR)) {
  if(fault & VM_FAULT_OOM)
    goto out_of_memory;
  else if(fault & VM_FAULT_SIGBUS)
    goto do_sigbus;
  BUG();
}
if(fault & VM_FAULT_MAJOR)
  tsk->maj_flt++;
else 
  tsk->min_flt++;
return;
```

`handle_mm_fault`，用于选择适当的异常恢复方法[按需调页、换入， 等等]，并应用选择的方法。

如果页成功建立，则例程返回`VM_FAULT_MINOR`（数据已经在内存中）或`VM_FAULT_MAJOR`（数据需要从块设备读取）。内核接下来更新进程的统计量。

但在创建页时，也可能发生异常。

* 如果用于加载页的物理内存不足`VM_FAULT_OOM`，内核会强制终止该进程，在最低限度上维持系统的运行。

* 如果对数据的访问已经允许，但由于其他的原因失败（例如，访问的映射已经在访问的同时被另一个进程收缩，不再存在于给定的地址），则将SIGBUS信号发送给进程。



