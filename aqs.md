```
ReentrantLock:
    公平锁:FairLock
    非公平锁:NonFairLock
    区别是:公平锁,每个线程都要入队双向链表,等待被别人唤醒,从对头的线程尝试去获取锁
          非公平锁,新的线程会先尝试获取锁
          
    入队列的线程,会调用LockSupport.park(),卡住,等待持有锁的线程调用LockSupport.unpark(nextWaitor.thread)
    后,唤醒了队列的队列的头节点,头节点从卡住状态,变成执行状态后,继续执行代码,去尝试获取锁(cas,尝试改变state的值),修改state成功后,
    就把上一个线程node的释放掉,就代表获取锁成功,继续执行下面的代码了.
    
ReentrantReadWriteLock:
    读写锁,是通过state的值得高低16位,来表示是否持有了读写锁
    低16位表示写锁,高16位表示读锁,通过这个32位的值以及当前持有锁是哪个线程来判断是否可以重入,是否可以同时加锁
    
AbstractQueuedSynchronizer(AQS)

condition是用于实现类似于wait,notify的功能
    condition.await(); 

思路:
    只有一个线程时,查看源码的整个过程
    有两个线程时,看第二个线程,是如何入队的,如何卡住的,如何被唤醒的
    有三个线程时,看第三个线程,是如何入队的,如何卡住的,如何被唤醒的,唤醒后做了哪些操作
    
    
```