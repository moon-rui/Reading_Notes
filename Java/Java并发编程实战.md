# 第2章 线程安全性
## 2.3 加锁机制
### 2.3.2 重入
如果某个线程试图获得一个已经由它自己持有的锁，这种请求是可以成功的。这种“重入”机制表明获取锁的操作的粒度是“线程”，而不是“调用”。
可为每个锁关联一个获取计数值和一个所有者线程来实现重入机制：当计数值为0，表明这个锁没有被任何线程持有。当有线程请求时，JVM将记下锁的持有者，并且将获取计数值置为1。而当同一个线程再次获取这个锁时，计数值递增。当线程退出同步代码块时，计数器会相应地递减。计数值为0时，这个锁将被释放。
如下所示的程序中，子类改写了父类的synchronized方法，然后调用父类的方法，如果没有重入机制，这段代码会产生死锁。
```java
public class Widget {
    public synchronized void doSomething() {
        ...
    }
}

public class LoggingWidget extends Widget {
    public synchronized void doSomething() {
        System.out.println(toString() + ": calling doSomething");
        super.doSomething();
    }
}
```

# 第3章 对象的共享

## 3.1 可见性

### 3.1.2 非原子的64位操作

Java内存模型要求变量的读取和写入操作都必须是原子操作，但是对于非volatile类型的long和double变量，JVM允许将64位的读操作或写操作分解为两个32位的操作。因此如果在不同线程中执行该类变量的读写操作，很可能会读取到某个值的高32位和另一个值的低32位。