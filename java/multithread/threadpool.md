##### 为什么使用线程池
线程是系统资源，创建和销毁线程的开销很大，如果频繁的创建、销毁线程，可能影响系统稳定性。

ThreadPoolExecutor参数
```java
public ThreadPoolExecutor(int corePoolSize, 
                          int maximumPoolSize,
                          long keepAliveTime, 
                          TimeUnit timeUnit,
                          BlockingQueue<Runnable> workQueue,
                          ThreadFactory threadFactory,
                          RejectedExecutionrHandler handler
)
```
`corePoolSize`: 保留在线程池中的线程的数量，即使线程空闲(idle)也不会销毁，除非设置   allowCoreThreadTimeOut为true。如果调用prestartAllCoreThreads()会创建所有的core线程。  
`maximunPoolSize`: 线程最大数量。  
`keepAliveTime`: 线程空闲后保持时间。  
`timeUnit`: 保持时间单位。  
`workQueue`: 任务队列，用来存放等待执行的任务，有以下队列:
  * ArrayBlockingQueue: 基于数组的有界队列，队列内的任务先进先出
  * LinkedBlockingQueue: 基于链表的无界队列，队列内的任务先进先出，`Executors.newFixedThreadPool()`, `Executors.newSingleThreadExecutor()`使用这种队列
  * SynchronousQueue: 不存储任务的阻塞队列，如果队列中有任务未执行，将无法添加新的任务，一直处于阻塞状态，`Executors.newCachedThreadPool()`使用这种队列
  * PriorityBlockingQueue: 具有优先级的无界队列
  * DelayQueue
  * LinkedTransferQueue
 
`threadFactory`: 创建线程的工厂。  
`rejectedExecutionHandler`: 饱和策略，有几种饱和策略:
* CallerRunsPolicy: 在调用execute方法的线程中执行任务
* AbortPolicy: 丢弃任务并抛出RejectedExecutionException异常，是线程池默认的饱和策略
* DiscardPolicy: 丢弃新的任务
* DiscardOldestPolicy: 丢弃最早未执行的任务

##### 线程池原理
执行execute()提交新任务时，先判断当前线程数是否大于corePoolSize。如果小于corePoolSize，则创建新线程执行，否则将任务提交到workQueue中，空闲线程会从workQueue中取任务执行。如果workQueue已满，判断maximumPoolSize是否大于corePoolSize。如果maximumPoolSize大于corePoolSize，线程池会创建新线程执行任务，工作线程空闲时会在keepAliveTime后销毁。如果maximumPoolSize等于corePoolSize，则根据饱和策略进行处理。
execute()代码
```java
public void execute(Runnable command) {
        if (command == null)
            throw new NullPointerException();
        /*
         * Proceed in 3 steps:
         *
         * 1. If fewer than corePoolSize threads are running, try to
         * start a new thread with the given command as its first
         * task.  The call to addWorker atomically checks runState and
         * workerCount, and so prevents false alarms that would add
         * threads when it shouldn't, by returning false.
         *
         * 2. If a task can be successfully queued, then we still need
         * to double-check whether we should have added a thread
         * (because existing ones died since last checking) or that
         * the pool shut down since entry into this method. So we
         * recheck state and if necessary roll back the enqueuing if
         * stopped, or start a new thread if there are none.
         *
         * 3. If we cannot queue task, then we try to add a new
         * thread.  If it fails, we know we are shut down or saturated
         * and so reject the task.
         */
        int c = ctl.get();
        if (workerCountOf(c) < corePoolSize) {
            if (addWorker(command, true))
                return;
            c = ctl.get();
        }
        if (isRunning(c) && workQueue.offer(command)) {
            int recheck = ctl.get();
            if (! isRunning(recheck) && remove(command))
                reject(command);
            else if (workerCountOf(recheck) == 0)
                addWorker(null, false);
        }
        else if (!addWorker(command, false))
            reject(command);
    }
```

##### 使用线程池的风险
1. 产生死锁
2. 资源不足
3. 线程泄露
4. 请求过载