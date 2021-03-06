#### 什么是线程？
线程（threads，台湾称 执行绪），也被称为轻量进程（lightweight processes）。计算机科学术语，指运行中的程序的调度单位。
线程是进程中的实体，一个进程可以拥有多个线程，一个线程必须有一个父进程。线程不拥有系统资源，只有运行必须的一些数据结构；它与父进程的其它线程共享该进程所拥有的全部资源。线程可以创建和撤消线程，从而实现程序的并发执行。一般，线程具有就绪、阻塞和运行三种基本状态。
在多中央处理器的系统里，不同线程可以同时在不同的中央处理器上运行，甚至当它们属于同一个进程时也是如此。大多数支持多处理器的操作系统都提供编程接口来让进程可以控制自己的线程与各处理器之间的关联度（affinity）。
#### 线程安全？
如果你的代码所在的进程中有多个线程在同时运行，而这些线程可能会同时运行这段代码。如果每次运行结果和单线程运行的结果是一样的，而且其他的变量的值也和预期的是一样的，就是线程安全的。
或者说:一个类或者程序所提供的接口对于线程来说是原子操作或者多个线程之间的切换不会导致该接口的执行结果存在二义性,也就是说我们不用考虑同步的问题。
线程安全问题都是由全局变量及静态变量引起的。
若每个线程中对全局变量、静态变量只有读操作，而无写操作，一般来说，这个全局变量是线程安全的；若有多个线程同时执行写操作，一般都需要考虑线程同步，否则就可能影响线程安全。
#### 线程池
java的线程池是通过HashMap获取当前的线程，保持线程同步。
#### 线程池功能　
应用程序可以有多个线程，这些线程在休眠状态中需要耗费大量时间来等待事件发生。其他线程可能进入睡眠状态，并且仅定期被唤醒以轮循更改或更新状态信息，然后再次进入休眠状态。为了简化对这些线程的管理，.NET框架为每个进程提供了一个线程池，一个线程池有若干个等待操作状态，当一个等待操作完成时，线程池中的辅助线程会执行回调函数。线程池中的线程由系统管理，程序员不需要费力于线程管理，可以集中精力处理应用程序任务。
线程池是一种多线程处理形式，处理过程中将任务添加到队列，然后在创建线程后自动启动这些任务。线程池线程都是后台线程.每个线程都使用默认的堆栈大小,以默认的优先级运行,并处于多线程单元中.如果某个线程在托管代码中空闲(如正在等待某个事件),则线程池将插入另一个辅助线程来使所有处理器保持繁忙.如果所有线程池线程都始终保持繁忙,但队列中包含挂起的工作,则线程池将在一段时间后创建另一个辅助线程但线程的数目永远不会超过最大值.超过最大值的线程可以排队,但他们要等到其他线程完成后才启动
#### 什么情况下不能使用线程池　
1、如果需要使一个任务具有特定优先级 
2、如果具有可能会长时间运行（并因此阻塞其他任务）的任务
3、如果需要将线程放置到单线程单元中（线程池中的线程均处于多线程单元中）
4、如果需要永久标识来标识和控制线程，比如想使用专用线程来终止该线程，将其挂起或按名称发现它
System.ThreadingPool类实现了线程池，这是一个静态类，它提供了管理线程的一系列方法  
Threading.QueueUserItem方法在线程池中创建一个线程池线程来执行指定方法（用委托WaitCallBack表示），并将该线程排入线程池的队列等待执行。
```  
public static Boolean QueueUserWorkItem(WaitCallback wc,Object state);
```
#### 线程同步的方式和机制
临界区、互斥区、事件、信号量四种方式
临界区（Critical Section）、互斥量（Mutex）、信号量（Semaphore）、事件（Event）的区别：
1、临界区：通过对多线程的串行化来访问公共资源或一段代码，速度快，适合控制数据访问。在任意时刻只允许一个线程对共享资源进行访问，如果有多个线程试图访问公共资源，那么在有一个线程进入后，其他试图访问公共资源的线程将被挂起，并一直等到进入临界区的线程离开，临界区在被释放后，其他线程才可以抢占。 
2、互斥量：采用互斥对象机制。 只有拥有互斥对象的线程才有访问公共资源的权限，因为互斥对象只有一个，所以能保证公共资源不会同时被多个线程访问。互斥不仅能实现同一应用程序的公共资源安全共享，还能实现不同应用程序的公共资源安全共享。
3、信号量：它允许多个线程在同一时刻访问同一资源，但是需要限制在同一时刻访问此资源的最大线程数目。
4、事 件： 通过通知操作的方式来保持线程的同步，还可以方便实现对多个线程的优先级比较的操作。