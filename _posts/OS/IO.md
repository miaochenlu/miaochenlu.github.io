# I/O输入系统

# 1. Overview

I/O设备在功能和速度上存在很大差异，所以需要采用多种方法来控制设备。这些方法构成了I/O子系统的核心，该子系统使内核其他部分不必设计复杂的I/O设备管理。



I/O设备技术呈现两个矛盾的趋势

* 硬件与软件接口日益增长的标准化
* I/O设备日益增长的多样性

为了封装不同设备的细节和特点，操作系统内核设计成使用设备驱动程序模块的结构。***设备驱动程序[device drivers]***为I/O子系统提供了统一设备访问接口。



# 2. I/O硬件

* 设备与计算机通信通过一个连接点[***端口port***], 例如串形端口。

* 如果一个或者多个设备使用一组共同的线，那么这种连接则称为***总线[bus]***。总线是***一组线***和一组严格定义的可以描述在线上传输信息的***协议***

* 如果设备A通过电缆连接到设备B，设备B又通过电缆连接到设备C，设备C通过端口连接到计算机上，那么这种方式称为***链环[daisy chain]***。链环常常按总线方式工作



下图是一个典型的PC总线结构。

* bus
  * PCI总线：连接处理器-内存子系统与快速设备
  * expansion bus: 连接串行、并行端口和相对较慢的设备[如键盘]
  * SCSI bus: 如右上角，4块硬盘一起连接到与SCSI控制器相连的SCSI总线
* controller: 用于操作端口、总线或设备的一组电子器件
  * 串行端口控制器：是计算机上的一块或者部分芯片，用以控制串行端口线上的信号
  * SCSI controller: 实现为与计算机相连接的独立的线路板或主机适配器[host adapter]。这个适配器通常有处理器、微码以及一部分私有内存，以便能粗粒SCSI协议信息。

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20191221101815223.png" alt="image-20191221101815223" style="zoom:40%;" />

* 处理器如何向控制器发送命令和数据以完成I/O传输
  * 控制器有一个或者多个用于数据和控制信号的寄存器，处理器通过读写这些寄存器的位模式来与控制器通信。有以下两种技术：
    * 使用特殊的***<u>I/O指令</u>***来向指定的I/O端口地址传输一个字节或字。I/O指令出发总线线路来选择合适设备并将位信息传入或传出设备寄存器。
    * 设备控制器也可以支持***<u>内存映射I/O</u>***。这时，设备控制寄存器被映射到处理器的地址空间。处理器执行I/O请求是通过标准数据传输指令来完成对设备控制器的读写。

I/O端口通常有四种寄存器：状态寄存器，控制寄存器，数据输入寄存器，数据输出寄存器

* data-in register：被主机读出以获取数据
* data-out register：被主机写入以发送数据
* status register：包含一些主机可以读取的位[bit]。这些位指示各种状态。例如：当前任务是否完成，数据输入寄存器中是否有数据可以读取，是否出现设备故障
* control register：可以被主机用来向设备发送命令或改变设备状态。例如：一个串行端口的控制寄存器中的一个确定的位选择全双工通信或者单工通信，另一个位控制器启动奇偶校验检查，第三个位设置字长位7或8位，其他位选择串行端口通信所支持的速度。

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20191221110859768.png" alt="image-20191221110859768" style="zoom:40%;" />

## 2.1 Polling轮询

主机与控制器之间交互的完成协议可能很复杂，但基本握手概念比较简单。

假定有两个位来协调controller和主机之间的生产者与消费者的关系。控制器通过status register的`busy bit`来显示其状态。

controller工作忙时就set `busy bit`，而可以接受下一命令时就clear `busy bit`

The host signals its wishes via the `command-ready bit` in the command register。

下面来看一下主机需要通过端口来写输出数据时，主机与控制器之间握手协调如下：

> 主机不断地读取busy bit，直到该位被清除 (这个过程称为轮询，亦称忙等待-busy waiting)
>
> 主机设置command register中的write bit并向data-out register中写入一个字节。
>
> 主机设置command-ready bit
>
> 当控制器注意到command-ready bit已被设置，则设置busy bit
>
> controller读取command register，并看到write command。它从data-out register中读取一个字节，并向设备执行I/O操作。
>
> controller清除command-ready bit，清除status register的error bit以表示设备I/O成功，清除busy bit以表示完成。



<img src="/Users/jones/Library/Application Support/typora-user-images/image-20191221110841772.png" alt="image-20191221110841772" style="zoom:50%;" />



## 2.2 Interrupts

nCPU硬件有一条中断请求线（interrupt-request line, IRL），由I/O设备触发

l设备控制器通过中断请求线发送信号而引起中断，CPU捕获中断并派遣到中断处理程序，中断处理程序通过处理设备来清除中断。

n两种中断请求

l**非屏蔽中断**：主要用来处理如不可恢复内存错误等事件

l**可屏蔽中断**：由CPU在执行关键的不可中断的指令序列前加以屏蔽

n**中断向量**

n**中断优先级**：能够使CPU延迟处理低优先级中断而不屏蔽所有中断，这也可以让高优先级中断抢占低优先级中断处理。

n中断的用途

l中断机制用于处理各种异常，如被零除，访问一个受保护的或不存在的内存地址

l系统调用的实现需要用到中断（软中断）

l中断也可以用来管理内核的控制流

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20191221112517406.png" alt="image-20191221112517406" style="zoom:50%;" />

