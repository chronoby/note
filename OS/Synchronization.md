# Synchronization

## 临界区问题(critical section)

- 临界区问题是设计一个以便进程协作的协议，每个进程必须请求允许进入临界区
- 实现这一请求的代码成为进入区(entry section)，临界区之后可有退出区(exit section)，其他代码为剩余区(remainder section)

临界区问题的解答必须满足三个要求：

- 互斥(mutual exclusion)：如果进程$P_i$在其临界区内执行，那么其他进程都不能在其临界区内执行
- 前进(progress)：如果没有进程在其临界区内执行且有进程需进入临界区，那么只有那些不在剩余区内执行的进程可参加选择，以确定谁能下一个进入临界区，且这种选择不能无限推迟
- 有限等待(bounded waiting)：从一个进程作出进入临界区的请求，直到该请求允许为止，其他进程允许进入临界区的次数有上线

有两种方法处理操作系统内的临界区问题：

- 抢占内核(preemptive kernel)：允许处于内核模式的进程被抢占
- 非抢占内核(nonpreemptive kernel)：不允许。处于内核模式的进程会一直运行，直到它退出内核模式、阻塞或自动退出CPU的控制

## Peterson 算法

两个进程之间共享两个数据项

```c
int turn;
bool flag[2];
```

- turn表示那个进程可以进入临界区。如果turn == i，那么进程$P_i$允许在临界区内执行
- flag表示哪个进程想要进入临界区

算法：

```c
do
{
    flag[i] = true;
    turn = j;
    while(flag[j] && turn == j)
        ;
    flag[i] = false;
} while(true);

```

## 硬件同步

- 指令TestAndSet()能原子地执行，如果两个指令TestAndSet()同时执行在不同的CPU上，他们会按照任意顺序执行
- 指令Swap()操作两个数据，也原则地执行

## 信号量

- 信号量S是个整数变量
- 他只能通过两个标准原子操作 wait()和signal()来访问，这些操作被称为P

wait()定义为

```c
wait(S)
{
    while(S <= 0)
        ;
    S--;
}
```

signal()定义为

```c
signal(S)
{
    S++;
}
```

- 在wait()和signal()操作中，对信号量整型值的修改必须不可分地执行，即当一个进程修改信号量值是，不能有其他进程同时地修改同一信号量的值
- 对于wait()，对S的整型值的测试和对其可能的修改也必须不被中断地执行

### 用法

- 计数信号量的值域不受限制，而二进制信号量的值只能为0或1
- 有的系统将二进制信号量成为互斥锁
- 可以用二进制信号量来处理多处理器的临界区问题。这n个进程共享一个信号量mutex，并初始化为1.每个进程的程序结构如下

```c
do
{
    waiting(mutex);
    // critical section
    signal(mutex);
    // remainder section
} while (true)
```

- 计数信号量可以用来控制访问具有若干个实例的某种资源，改资源初始化为可用资源的数量
- 当每个进程需要使用资源时，需要对该信号量执行wait()操作
- 当进程释放资源时，需要对该信号量执行signal操作

也可以使用信号量来解决各种同步问题

### 实现

- 这里所定义的信号量的主要缺点是都要求忙等待(busy waiting)，即当一个进程位于其临界区内时，任何其他视图进入该临界区的进程都必须在其进入代码中连续地循环
- 这种类型的信号量也称为自旋锁(spinlock)

克服忙等待

- 当一个程序执行wait()时，发现信号量值补位正，则它阻塞自己。阻塞操作将一个进程放入到与信号量相关的等待队列中，并将该进程的状态切换成等待状态
- 一个被阻塞的进程，可以在其他进程执行signal()操作后重新执行，通过wakeup()操作进行

### 死锁和饥饿

- 死锁：两个或多个进程无限地等待一个事件，而这个时间只能有这些等待进程之一来产生
- 无限期阻塞(indefiinite blocking)或饥饿(starvation)，即进程在信号量内无限期等待

## 经典同步问题

### 有限缓冲问题

### 读者-写者问题