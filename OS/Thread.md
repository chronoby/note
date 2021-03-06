# 线程

- 线程是CPU使用的基本单元
- 由线程ID，程序计数器，寄存器集合和栈组成

## 多线程模型

### 多对一模型

- 将许多用户线程映射到一个内核线程
- 线程管理是由线程库在用户空间进行的，效率高
- 如果一个线程执行了阻塞系统调用，那个整个进程会阻塞

### 一对一模型

- 将每个用户线程映射到一个内核线程
- 提供了更好的并发功能
- 允许多个线程能并行地运行在多处理器系统上
- 唯一的缺点是每创建一个用户线程就要创建一个相应的内核线程，限制了系统所支持的线程数量

### 多对多模型

- 多路复用了许多用户线程到同样数量或更小数量的内核线程上
- 开发者可以创建任意多的用户线程，并且相应内核线程能在多处理器系统上并发运行
- 当一个线程执行阻塞系统调用时，内核能调度另一个线程来执行

## 多线程问题

### 系统调用fork()和exec()

- 如果程序中的一个线程调用fork()，有的unix系统有两种形式的fork()，一种复制所有线程，另一种只复制调用了系统调用fork()的线程
- exec()：如果一个线程调用了系统调用exec(),那么exec()参数所指定的程序会替换整个进程，包括所有线程

### 线程取消

线程完成之前终止线程的任务

- 异步取消：一个线程立即终止目标线程
- 延迟取消：目标线程不断地检查它是否应终止，这运行目标线程有机会以有序方式终止自己
