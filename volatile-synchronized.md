```
java内存模型中的lock,read,load,use,assign,store,write,unlock
中每个操作时原子性的,lock和unlock对应monitorenter,monitorexit
synchronized的字节码实现就是monitorenter,monitorexit指令
monitorenter执行时,会保证代码块中的成员变量会是最新的,也就是和主内存的变量(不一定)看不同的硬件
monitorexit执行时,会保证代码中的写操作会被刷新回主内存,保证可见性

无锁-->偏向锁-->轻量级锁-->重量级锁,锁只能升级,不能降级
偏向锁:是当一个线程来获取锁时,会在对象头的markword中记录线程id,以后只需要一次cas操作,来修改线程id,能修改cas操作就表示是同一个线程,无需进行多次
cas操作,修改成功表示是同一个线程,直接执行代码块,如果修改失败(因为偏向锁不会自动释放偏向锁),再去判断持有这个偏向锁的线程是否存活,如果不存活,就直接
修改对象头为无锁状态,然后偏向新的这个线程,如果存活就去cas自旋,改成轻量级锁

关闭偏向锁:-XX:-UseBiasedLocking=false


轻量级锁:同时只有两个线程在交替的执行(获取锁),在进入轻量级锁后,如果发现自选操作一段时间或者一个线程持有锁,一个线程cas,又有一个线程来访问,就自动升级为重量级锁.释放锁时,将复制的Displaced Mark Word替换当前对象头中的
mark word,替换成功,表示同步过程完成了,如果不成功,就认为锁已经自动转为重量级锁,释放锁的同时,唤醒挂机的线程,这些挂起的线程在wait set队列中

重量级锁:就是利用了操作系统的mutex lock是需要切换用户态和核心态,很耗时

锁消除:认为没必要加锁
锁粗化:一系列操作反复加锁和解锁,就扩大同步块的范围





每个对象(实例对象和类对象)java对象头


synchronized的阻塞和唤醒一条线程是映射到操作系统的原生线程上的,需要由用户态到核心态,比较耗时,所以是重量级锁
jdk1.6之后,加入了自旋,避免频繁进入核心态中,基本上保证了synchronized和ReentrantLock的性能相当,因为ReentrantLock就是自旋加阻塞的方式
```