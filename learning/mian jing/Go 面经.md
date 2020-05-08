#### 1.关于context包的用途

用来简化在多个goroutine中传递上下文数据，终止routine等操作。例如：官方http包中使用context传递请求上下文数据，grpc使用context来终止某个请求产生的routine树

#### 2.new和make的区别

- make用于内建类型(map/slice/channel)的内存分配.并返回一个有初始值的Ｔ类型
- new用于各种类型的内存分配，并且返回指向地址的指针．

#### 3.golang内存布局

![avatar](https://img-blog.csdn.net/2018032211404828)

从图中可以看出，Golang中内存布局可以拆分成三块：cache,central,heap

其中central属于heap子模块

Golang的内存管理本质上是一个**内存池**，内部做了很多优化，例如自动伸缩内存池大小，合理切割内存块等．

- heap:程序在启动之初会从操作系统那里申请一块大内存作为内存池.并将其放在一个叫做mheap的结构体中管理．mheap负责将整块内存切割，并将异步们切割的内存分配给用户使用
- central:用来管理内存块，相同用途的块会以链表形式组织在一起
- cache：线程私有的内存．用于线程中数据的存储，当此内存不够时，线程才会向central申请内存

优势：

- 内存分配大多时候是在用户态完成的，不需要频繁进入内核态
- 每一个P都有独立的span cache,多个cpu并不会并发读写同一块内存，进而减少Cpu cache中的脏数据情况，增加cpu cache命中率．
- 内存碎片问题：Go是自己在用户态管理内存，OS对于碎片管理的压力降低
- mcache存在使得内存分配不需要加锁，mcache扩充还是要加锁

缺点：go需要预先申请一大块内存

#### 4.goroutine调度器

目前存在三种常见的线程模型，N:1 多个用户线程运行在一个OS线程上；１:1一个用户线程运行在一个OS线程上；M:N任意数目的OS线程调用任意数目的goroutines．golang中选择M:N模式

Go调度器中存在3个实体：

![avatar](https://img-blog.csdn.net/20160418113332331?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

其中M代表线程，即处理机器machine，P代表调度器，G代表一个goroutine．运行方式如下：

![avatar](https://img-blog.csdn.net/20160418113347511?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

上面有两个线程(M)，每个线程都有一个上下文P,每个线程都运行一个goroutine(G)．外部灰色的goroutine没有运行，但是准备好被调度．它们放在一个runqueue的列表中．当一个goroutine执行一个go语句时，goroutine被添加到runqueue的末端，一旦一个goroutine运行完毕，那么会从runqueue中pop一个goroutine，设置栈和指令指针并开始运行该goroutine.

为了降低锁竞争，每一个上下文都有一个runqueue．当线程阻塞时，该线程上下文调度器P会尝试去寻找一个空闲的线程,或者新创建一个线程进行处理．

线程阻塞时，线程对应的调度器会尝试切换到其他线程执行，线程恢复后，会尝试获取一个调度器P继续执行未完成的goroutine,如果无法获取到P，线程则将G放到一个全局runqueue中，然后将自己放入线程池进行睡眠．

当一个P执行完自身的runqueue队列后，会尝试从全局runqueue获取待执行的goroutine,如果无法获取到，则会从其他P的runqueue中，获取goroutine去执行























