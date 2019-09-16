##### 进程与线程的区别

1. 进程是操作系统资源分配的最小单位，线程是CPU调度的最小单位。
2. 进程有独立的地址空间，相互不影响，线程没有独立的地址空间。
3. 进程切换开销大，线程切换开销相对小。
4. 进程可以独立存在，而线程脱离进程不能独立存在。

##### Java线程状态
Thread类的State枚举中定义了六种java线程状态  
`NEW`  
通过new Thread()创建线程，但是还没有执行start()  
`RUNNABLE`  
执行start()后线程进入RUNNABLE状态，执行start()后线程并不会立即执行，需要获取CPU执行时间，RUNNABLE包含READY和RUNNING两种状态。  
`WAITING`  
调用Object的无参wait()，Thread的无参join()，LockSupport.park()会使线程进入WAITING状态，线程只能被唤醒    
`TIMED_WAITING`  
调用Thread.sleep()，Object的有参wait()，Thread的有参join()，LockSupport.parkNanos()，LockSupport.parkUntil()会使线程进入TIMED_WAITING状态，超时后线程自动唤醒  
`BLOCKED`  
线程争抢互斥锁时会进入BLOCKED状态  
`TERMINATED`  
线程执行完成后进入TERMINATED


##### sleep与wait的区别
1. sleep是Object类中的方法，wait是Thread类中的方法。
2. sleep可以在任何地方使用，wait只能在synchronized代码块或synchronized方法中。
3. sleep会使线程进入TIMED_WAITING状态，无参的wait会使线程进入WAITING状态，有参的wait会使线程进入TIMED_WAITING状态。
4. sleep会使线程挂起，但是不会释放持有的锁，wait会释放锁。