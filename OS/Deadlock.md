# Deadlock

## 系统模型

进程使用资源：

- request
- use
- release

### 必要条件

- 互斥：至少有一个资源处于非共享模式，即一个时刻只有一个进程使用
- 占有并等待：一个进程必须占有至少一个资源，并等待另一资源，而该资源为其他进程所占有
- 非抢占
- 循环等待

### 资源分配图

## 死锁处理方法

- 使用协议预防或避免死锁，确保系统不会进入死锁状态
- 允许系统进入死锁状态，然后检测它，并家已恢复
- 忽略这个问题，认为死锁不可能在系统内发生 KISS(keep it simple and stupid)

## 死锁预防

确保至少一个必要条件不成立，就能预防死锁发生

### 互斥

- 取决于资源

### 占有并等待

必须保证，当一个进程申请一个资源时，它不能占有其他资源，有两种方式

- 在执行前申请并获得所有资源
- 申请更多资源之前，必须释放其现已分配的所有资源

### 非抢占

如果一个进程占有资源并申请另一个不能立即分配的资源，那么其现已分配的资源都可被抢占

### 循环等待

对所有资源类型进行全序排序，且要求每个进程按递增顺序来申请资源

## 死锁避免

获取以后如何申请资源的附加信息

- 每个进程说明可能需要的每种资源类型实例的最大需求
- 思索避免算法动态的检测资源分配状态以确保循环等待条件不可能成立
- 资源分配状态是由可用资源和已分配资源，及进程最大需求所决定的

### 安全状态

- 如果系统 能按某个顺序为每个进程分配资源并能避免死锁，那么系统状态是安全的
- 如果存在一个安全序列，那么系统处于安全状态
- 进程序列<P1, P2, ..., Pn>，如果对于每个Pi, Pi仍然可以申请的资源数小于当前可用资源加上所有进程Pj(j < i)所占用的资源，那么这一顺序称为安全序列
- 保证安全状态即可避免死锁

### 资源分配图算法

### 银行家算法

#### 假设

- 进程进入系统时，必须说明其可能需要的每种类型资源实例的最大数量，不能超过系统资源的综合
- 当用户申请一组资源时，系统必须确定这些资源的分配是否仍会是系统处于安全状态
  - 若是，就可分配资源
  - 否则，进程必须等待直到某个其他进程释放足够资源为止

#### Data structure

Let n = number of processes, and m = number of resources types.

- Available: Vector of length m. If available [j] = k, there are k instances of resource type Rj available.
- Max: n x m matrix. If Max [i,j] = k, then process Pi may request at most k instances of resource type Rj
- Allocation: n x m matrix. If Allocation[i,j] = k then Pi is currently allocated k instances of Rj.
- Need: n x m matrix. If Need[i,j] = k, then Pi may need k more instances of Rj to complete its task.

Need [i,j] = Max[i,j] – Allocation [i,j]

#### Safety Algorithm

1. Let Work and Finish be vectors of length m and n, respectively. Initialize:  
Work = Available  
Finish [i] = false for i = 0, 1, …, n- 1.  
2. Find an i such that both:  
(a) Finish [i] = false  
(b) Needi <= Work  
If no such i exists, go to step 4.  
3. Work = Work + Allocationi  
Finish[i] = true  
go to step 2.  
4. If Finish [i] == true for all i, then the system is in a safe state.

#### Resource-Request Algorithm

Request = request vector for process Pi. If Requesti[j] = k then process Pi wants k instances of resource type Rj.

1. If Requesti <= Needi go to step 2. Otherwise, raise error condition, since process has exceeded its maximum claim.
2. If Requesti <= Available, go to step 3. Otherwise Pi must wait, since resources are not available.
3. Pretend to allocate requested resources to Pi by modifying the state as follows:

   - Available = Available – Request
   - Allocationi= Allocationi + Requesti
   - Needi = Needi – Requesti

If safe => the resources are allocated to Pi.
If unsafe => Pi must wait, and the old resource-allocation state is restored

## 死锁检测

### Data Structure

- Available: A vector of length m indicates the number of available resources of each type.
- Allocation: An n x m matrix defines the number of resources of each type currently allocated to each process.
- Request: An n x m matrix indicates the current request of each process. If Request [i_j] = k, then process Pi is requesting k more instances of resource type Rj

### Detection Algorithm

1. Let Work and Finish be vectors of length m and n, respectively
Initialize:
(a) Work = Available
(b) For i = 1,2, ..., n, if Allocationi != 0, then Finish[i] = false;otherwise, Finish[i] = true.
2. Find an index i such that both:
(a) Finish[i] == false
(b) Requesti <= Work
If no such i exists, go to step 4.
3. Work = Work + Allocationi
Finish[i] = true
go to step 2.
4. If Finish[i] == false, for some i, 1 <= i <= n, then the system is in deadlock state. Moreover, if Finish[i] == false, then Pi is deadlocked.

O(m * n^2)

## 死锁恢复

- 终止所有死锁进程
- 一次只终止一个进程直到取消死锁循环为止
