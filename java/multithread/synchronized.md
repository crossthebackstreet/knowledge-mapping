#### synchronized

#### synchronized与Lock区别
* 使用方式，synchronized可作用于方法和代码块，Lock只能作用于代码块
* 使用synchronized关键字如果发生异常，JVM会自动自动释放线程持有的锁，Lock必须手动释放锁，如果发生异常时没有释放锁，会造成死锁。
* synchronized是关键字，Lock是Java接口，它提供了更多了方法，如`tryLock()`，`newCondition()`等。
* synchronized线程处于BLOCKED状态时无法被中断，Lock线程可被中断。
* synchronized线程之间通信依赖于加锁对象，Lock通过Condition实现线程间通信。
* 实现原理不同，synchronized由JVM实现，Lock基于CAS，依赖于CPU CAS。
* synchronized是悲观锁，Lock是乐观锁。
