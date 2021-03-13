# goroutine

- 同一地址空间
- 并行执行go function
- runtime.G结构体
- 栈：局部变量，1-8mb

# goroutine 和 thread区别

- 调度切换：上下文切换成本大，内核线程切换消耗1000-1500ns，等于12000-18000条指令
- 复杂性：线程创建和退出复杂
- 内存占用：goroutine 2kb
- 创建销毁

# gmp

- go创建m个线程，创建的n个goroutine会被调度到这m个内核线程上执行
- 同一个线程只能运行一个goroutine，当发生阻塞时，会进行gopark，让其他goroutine执行，尽可能的分\发任务
- GM
  - g: goroutine
  - m: os thread
  - 存在问题
    - 全局锁：goroutine的创建，调度，销毁需要加锁
    - 在多个m中传递goroutine，存在调度延迟
    - 创建m时会携带mcache，大部分情况都在执行syscall，只有执行go code都时候才会用mcache，造成内存浪费
    - 执行syscall时，os thread会取消阻塞和被阻塞，增加了很多开销
- GMP
  - g：struct runtime.G，包含goroutine的状态，堆栈，上下文
  - p：逻辑处理器，本地队列，与具体的m绑定
  - m：内核线程
  - m先去与之绑定的p本地队列获取goroutine，如果没有就去global queue获取 

# work-stealing调度算法




# go run main.go
- todo
- 创建gomax个processors
- 程序启动后，m0和g0关联
- 新创建的goroutine，唤醒idle processor，然后与找m绑定
- 创建好的goroutine，放到p本地的local queue，如果满了就把queue的前半部分和新创建的goroutine放到global queue
- m找到一个goroutine 任务，切换调用栈执行任务
  - 然后执行调度循环，获取任务执行，直至没有任务，进入休眠
- m自旋
  - m没有挂载p，找p挂载
  - m挂载p，找goroutine执行
  - goroutine创建，没有m在自旋，唤醒一个p，p再唤醒一个m
