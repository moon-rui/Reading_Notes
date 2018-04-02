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
