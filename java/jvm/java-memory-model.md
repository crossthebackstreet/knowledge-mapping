#### Java内存模型(Java Memory Model, JMM)
Java虚拟机规范定义了Java内存模型，用于屏蔽各种硬件和操作系统带来的内存访问差异，实现Java程序在各个平台上达到一致的内存访问效果。Java内存模型的主要目标是定义程序中各个变量的访问规则，规范了Java虚拟机与计算机内存是如何协同工作的。它规定所有的变量都存储在主存中，每个线程都有自己的工作内存。线程对变量的所有操作都在工作内存上进行，不能直接对主存进行操作。不同的线程不能访问其他线程的工作内存。线程间变量的传递需要通过主存完成。
![thread-workingmemory-mainmemory]

Java内存模型与计算机硬件有一定关系。所有的计算机指令都会在CPU上执行，CPU一般会从物理内存上读取数据到寄存器，再进行处理。然而CPU的处理速度远快于内存，为了解决这个问题，就在CPU与内存之间添加了CPU缓存，CPU缓存比内存快很多。CPU执行指令的过程就会变为先从缓存中读取，如果缓存命中（缓存中有要使用的数据），则直接使用缓存中的数据，如果没有命中则从内存中读取，并存入缓存中，执行完成后将数据刷新到缓冲中，再同步回内存中。在单核CPU中无论是单线程还是多线程都不会有问题，但是在多核多线程下，由于每个核都有自己的缓存，它们又共享同一物理内存，这样会引发[缓存一致性（Cache Coherence）](https://en.wikipedia.org/wiki/Cache_coherence)问题。为了解决缓存一致性问题，需要每个处理器在访问缓存时都遵循一些协议，在读写时要根据协议来操作。
![cpu-cache-mainmemory]

除了缓存一致性问题外，为了使处理器内部的运算单元尽量被充分利用，处理器可能会对指令进行乱序执行优化，程序中语句执行顺序可能与代码编写顺序不一致，但是会保证结果与顺序执行的结果是一致的。除了处理器优化外，Java虚拟机的即时编译器也会有指令重排序优化。

Java中工作内存对主存的操作和重排序优化会带来线程安全问题，解决线程安全问题就要解决并发中的三个特性: 原子性、有序性、可见性。Java内存模型定义了synchronized关键字实现原子性，synchronized、volatile、final实现可见性，synchronized、volatile实现有序性。仅仅依靠synchronized、volatile保证有序性将会变得很繁琐，为了解决这个问题，Java内存模型中定义先行发生（happens-before）原则。

#### happens-before
Java内存模型中定义了happens-before原则，保证一定的有序性
* 程序次序规则：一个线程内，按照代码顺序，书写在前面的操作先行发生于书写在后面的操作
* 管程锁定规则：一个unLock操作先行发生于后面对同一个锁的lock操作
* volatile变量规则：对一个变量的写操作先行发生于后面对这个变量的读操作
* 传递规则：如果操作A先行发生于操作B，而操作B又先行发生于操作C，则可以得出操作A先行发生于操作C
* 线程启动规则：Thread对象的start()方法先行发生于此线程的每个一个动作
* 线程终止规则：线程中所有操作都先行发生于对此线程的终止检测，可以通过Thread.join()方法结束、Thread.isAlive()的返回值等手段检测到线程已经终止执行
* 线程中断规则：对线程interrupt()方法的调用先行发生于被中断线程的代码检测到中断事件的发生
* 对象终结规则：一个对象的初始化完成先行发生于他的finalize()方法的开始
* 传递性：如果操作A先行发生于操作B，操作B先行发生于操作C，那么操作A先行发生于操作C


[thread-workingmemory-mainmemory]:<https://picabstract-preview-ftn.weiyun.com/ftn_pic_abs_v3/36dbdb5a556c6bf76e3b9b5fee016d36b6e60c7d9bc0801978eed6743d950a574bca343310bb9b1ad3d257f3aabda865?pictype=scale&from=30013&version=3.3.3.3&uin=595708313&fname=thread-workingmemory-mainmemory.png&size=750>

[cpu-cache-mainmemory]:<https://picabstract-preview-ftn.weiyun.com/ftn_pic_abs_v3/c22806f2332b91558b5e4ffdb526da550eaa3082be090cb1260cb4bed34477d207b7530043cecaeb2ca0c6e3c5d1f6f0?pictype=scale&from=30013&version=3.3.3.3&uin=595708313&fname=cpu-cache-mainmemory.png&size=750>