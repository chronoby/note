# CPU Scheduling

## 基本概念

当CPU空闲是，操作系统从就绪队列中选择一个进程执行，进程选择由短期调度程序或CPU调度程序执行

### 抢占调度

CPU调度决策在四种情况下发生

- 进程从运行状态切换到等待状态
- 进程从运行状态切换到就绪状态
- 进程从等待状态切换到就绪状态
- 进程终止

当调度只能发生在第1或第4中情况下时，调度方案是非抢占的(nonpreemptive)或协作的(cooperative)；否则调度方案是抢占的(preemptive)

### 分派程序(dispatcher)

分派程序用来将CPU的控制交给由短期调度程序选择的进程，功能包括

- switching context
- switching to user mode
- 跳转到用户程序的合适位置，以重新启动程序

## 调度准则

用于比较算法的特征：

- CPU utilization (CPU利用率) – keep the CPU as busy as possible
- Throughput (吞吐率)– # of processes that complete their execution per time unit
- Turnaround time (周转时间)– amount of time to execute a particular process
- Waiting time (等待时间)– amount of time a process has been waiting in the ready queue
- Response time (响应时间)– amount of time it takes from when a request was submitted until the first response is produced, not output (for time-sharing environment)

## 调度算法

### First-Come, First-Served

### Shortest-Job-First (SJF) Scheduling

- SJF调度算法可证明为最佳的
- SJF算法的真正困难是如何知道下一个CPU区间的长度
- 对于批处理系统的长期调度，可以将用户提交作业时所指定的进程时间极限作为长度
- SJF调度经常用于长期调度

Two schemes:

- nonpreemptive – once CPU given to the process it cannot be preempted until completes its CPU burst
- preemptive – if a new process arrives with CPU burst length less than remaining time of current executing process, preempt. This scheme is known as the Shortest-Remaining-Time-First (SRTF)

### Priority Scheduling

优先级可通过内部或外部定义

- 内部定义优先级使用一些测量数据以计算进程优先级，如时间极限，内存要去，打开文件的数量和平均IO区间，平均CPU区间
- 外部定义则是通过操作系统外的准则来定义

优先调度可以是抢占的或者非抢占的

- 优先级调度算法的问题：无穷阻塞(indefinite blocking)或饥饿(starvation)
- 解决方法：老化(aging)，即逐渐增加在系统中等待很长时间的进程的优先级

### Round Robin (RR)

Each process gets a small unit of CPU time (time quantum), usually 10-100 milliseconds. After this time has elapsed, the process is preempted and added to the end of the ready queue.

### Multilevel Queue

将就绪队列分为多个独立队列，根据进程的属性，如内存大小，进程优先级，进程类型，一个进程被永久地分配到一个队列，每个队列有自己的调度算法

### Multilevel Feedback Queue

- 允许进程在队列间移动
- 主要思想是根据不同的CPU区间的特点以区分进程
- 最通用的CPU调度算法，也是最复杂的调度算法

多级反馈队列调度可由一下参数定义

- 队列数量
- 每个队列的调度算法
- 用以确定何时升级到更高优先级队列的方法
- 用以确定何时降级到更低优先级队列的方法
- 用以确定进程在需要服务时应进入哪个队列的方法

## 多处理器调度

- CPU scheduling more complex when multiple CPUs are available
- Homogeneous processors within a multiprocessor
- Load balancing
- Asymmetric multiprocessing – only one processor accesses the system data structures, alleviating the need for data sharing; others execute only user code.
- Symmetric multiprocessing (SMP) – each processor is self-scheduling. Multiple processors might access and update a common data structure.
