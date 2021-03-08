```
volatile:
    保证可见性,有序性

底层原理:
    lock前缀指令+mesi缓存一致性协议保证

如何保证可见性/有序性:
    当被volatile修饰的成员变量,在修改时(assign),会立即执行store,write操作
    其他cpu,根据MESI缓存一致性协议,实现的嗅探机制,嗅探到该变量在执行修改操作了,就立即让自己的缓存中的数据失效

    当被volatile修改的成员变量,如下所示的代码:
    volatile int field = 0;

    public void print(){
        int localVariable = field;//load操作
        field = 1;//store操作
    }

    当执行load操作时,查看汇编时,会发现这样一句代码 lock addl xxx,这就是lock前缀指令,这样其他CPU通过嗅探机制就让自己的缓存失效
    重新从主内存中获取
    
    int localVariable = field;这句指令就是load操作,就是读操作,就会在代码前加上loadload内存屏障,在代码后加上loadstore内存屏障
    field = 1;这句指令就是store操作,就是写操作,就会在代码前机上storestore内存屏障,在代码后加上storeload内存屏障
    
    loadload:确保load1数据转载先于load2,以及后面的指令不能重排
    storestore: 当执行store时,会立即刷会主内存,同时先于其他的store操作执行
    loadstore: 确保load1的指令先执行,先于后store2以及后续指令
    storeload: 确保store1刷会主内存,同时先于load2指令及其他后续指令


    MESI只是CPU层面的重排序和可见性问题
    volatile是在JVM层面上取消了volatile变量的缓存,也禁用了编译器层面的重排序(取消编译层面的缓存和重排序。)
    
    处理器执行时的乱序优化:不影响单线程下的执行结果,以及操作之间无关联,就可以乱排序

    volatile解决编译器层面的可见性和重排序问题
    内存屏障解决了硬件层面的可见性和重排序问题
    
    不同的处理器,在对volatile变量修饰的变量进行读写操作时,实现不一样,
    但是都叫做内存屏障,都能实现变量在硬件级别的可见性和有序性
    
实现了Happens-before中的volatile表达的语义:
    比如X86处理器的sfence,就是对volatile修饰的变量的写操作之后,插入一个ifence,之前的所有写操作(包括volatile修饰的变量)
    不会重排序在sfence之后,sfence之后的所有store(写操作)不会被重排序到sfence之前,且sfence之前修改的数据都会被写会缓存
    并标记其他cpu中的缓存失效

    在volatile修饰的变量的读操作前,


    
```