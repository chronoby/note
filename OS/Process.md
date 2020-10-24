# Process

## Process concept

    a program in execution; process execution must progress in sequential fashion

### A process includes

- text section (code) 文本段（代码段）
- program counter 程序及所属期
- stack (function parameters, local vars, return addresses) 堆栈段
- data section (global vars) 数据段
- heap (dynamically allocated memory) 堆

程序和进程

- 程序本身不是进程，程序只是被动实体，如存储在磁盘上包含一系列指令的文件内容
- 而进程是活动实体，他有一个程序计数器用来表示下一个要执行的命令和相关资源几何
- 当一个可执行文件被装入内存时，一个程序才能成为进程

### Process state

- new:  The process is being created
- running:  Instructions are being executed
- waiting:  The process is waiting/blocked for some event to occur
- ready:  The process is waiting to be assigned to a processor
- terminated:  The process has finished execution

### 进程控制块

进程控制块(process control block)，包含了与一个进程相关的信息

- 进程状态
- 程序计数器
- CPU寄存器
- CPU调度信息
- 内存管理信息
- 记账信息
- I/O状态信息

## 进程调度

### 调度队列

- Job queue – set of all processes in the system
- Ready queue – set of all processes residing in main memory, ready and waiting to execute
- Device queues – set of processes waiting for an I/O device

Processes migrate among the various queues

### 调度程序(scheduler)

- 长期调度程序（作业调度程序）
- 短期调度程序（CPU调度程序）

## 进程操作

### 进程创建

### 进程终止

## 进程间通信

进程间通信的两种基本模式：

- 共享内存：通信速度更快
- 消息传递：更易于实现。因此对于交换数量较少的数据有用，因为不需要避免冲突

可以使用两种缓冲：无限缓冲和有限缓冲

- unbounded-buffer places no practical limit on the size of the buffer. Consumer has to wait if no new item.
- bounded-buffer assumes that there is a fixed buffer size. Producer must wait if buffer full.
