title: 读写锁ReentrantReadWriteLock的疑问
tags:
  - Java
date: 2011-10-31 22:12:28
---

Jdk5中的concurrent包中有一个ReadWriteLock接口以及它的实现（ReentrantReadWriteLock）。通过这个名字，以及它提供的两个方法：

1.  readLock()
2.  writeLock()

可以猜测，它提供了两个锁，一个用来写入，一个用来读取。因为读取不改变数据，可假设读取锁可同时被多个线程持有；而写入锁只能被一个线程持有，因为写入将改变数据。

由于该类的文档写得异常难懂，所以这里用代码来测试。

<span id="more-543"></span>
<p>**一、读取锁可同时被多个线程持有**

> public class ReadLockCanBeHoldByMultiThreadTest { 
> 
>     public static void main(String[] args) {     
>         ReentrantReadWriteLock lock = new ReentrantReadWriteLock();      
>         final ReadLock readLock = lock.readLock();      
>         for (int i = 0; i < 3; i++) {      
>             new Thread() {      
>                 public void run() {      
>                     readLock.lock();      
>                     System.out.println(Thread.currentThread().getName() + " holds the read lock");      
>                     try {      
>                         Thread.sleep(3000);      
>                     } catch (InterruptedException e) {      
>                         e.printStackTrace();      
>                     }      
>                     System.out.println(Thread.currentThread().getName() + " exits");      
>                 };      
>             }.start();      
>         }      
>     }      
> }
> 
>  

输出结果如下：

> Thread-0 holds the read lock     
> Thread-2 holds the read lock      
> Thread-1 holds the read lock      
> Thread-0 exits      
> Thread-1 exits      
> Thread-2 exits
> 
>  

可见读取锁的确可同时被多个线程持有。

**二、写入锁不可同时被多个线程持有**

> public class WriteLockCannotBeHeldByMultiThreads { 
> 
>     public static void main(String[] args) {     
>         ReentrantReadWriteLock lock = new ReentrantReadWriteLock();      
>         final WriteLock writeLock = lock.writeLock();      
>         for (int i = 0; i < 3; i++) {      
>             new Thread() {      
>                 public void run() {      
>                     writeLock.lock();      
>                     System.out.println(Thread.currentThread().getName() + " holds the read lock");      
>                     try {      
>                         Thread.sleep(3000);      
>                     } catch (InterruptedException e) {      
>                         e.printStackTrace();      
>                     }      
>                     writeLock.unlock();      
>                     System.out.println(Thread.currentThread().getName() + " exits");      
>                 };      
>             }.start();      
>         }      
>     } 
> 
> }
> 
>  

结果如下：

> Thread-0 holds the read lock     
> Thread-0 exits      
> Thread-1 holds the read lock      
> Thread-1 exits      
> Thread-2 holds the read lock      
> Thread-2 exits
> 
>  

可见写入锁必须在释放后，才能被另一个线程拿到。

**三、写入锁与读取锁不可同时被不同线程持有**

> public class ReadWriteLockCannotBeHeldAtTheSameTime { 
> 
>     public static void main(String[] args) throws Exception {     
>         ReentrantReadWriteLock lock = new ReentrantReadWriteLock();      
>         final ReadLock readLock = lock.readLock();      
>         final WriteLock writeLock = lock.writeLock(); 
> 
>         new Thread() {     
>             public void run() {      
>                 readLock.lock();      
>                 System.out.println(Thread.currentThread().getName() + " holds the read lock");      
>                 try {      
>                     Thread.sleep(3000);      
>                 } catch (InterruptedException e) {      
>                     e.printStackTrace();      
>                 }      
>                 readLock.unlock();      
>                 System.out.println(Thread.currentThread().getName() + " released the read lock");      
>             };      
>         }.start(); 
> 
>         // let the first thread have enough time to hold the read lock     
>         Thread.sleep(1000); 
> 
>         new Thread() {     
>             public void run() {      
>                 writeLock.lock();      
>                 System.out.println(Thread.currentThread().getName() + " holds the write lock");      
>                 try {      
>                     Thread.sleep(3000);      
>                 } catch (InterruptedException e) {      
>                     e.printStackTrace();      
>                 }      
>                 writeLock.unlock();      
>                 System.out.println(Thread.currentThread().getName() + " released the write lock");      
>             };      
>         }.start(); 
> 
>     }     
> }
> 
>  

结果如下：

> Thread-0 holds the read lock     
> Thread-0 released the read lock      
> Thread-1 holds the write lock      
> Thread-1 released the write lock
> 
>  

可见读取写被一线程持有后，其它线程必须等它释放后，才能拿到写入锁。反过来也一样，如果写入锁被拿了，读取锁也就拿不到了。

**四、某线程拿到写入锁后，还能再拿读取锁**

> public class AThreadCanHoldWriteAndReadLock {     
>     public static void main(String[] args) {      
>         ReentrantReadWriteLock lock = new ReentrantReadWriteLock();      
>         final ReadLock readLock = lock.readLock();      
>         final WriteLock writeLock = lock.writeLock();      
>         writeLock.lock();      
>         System.out.println("Hold the write lock");      
>         readLock.lock();      
>         System.out.println("Hold the read lock");      
>     }      
> }
> 
>  

<font style="background-color: #ffffff">输出：</font>

> Hold the write lock     
> Hold the read lock
> 
>  

**五、反过来不行，拿到读取锁后，就拿不到写入锁了**

> public class AThreadCannotHoldReadAndWriteLock {     
>     public static void main(String[] args) {      
>         ReentrantReadWriteLock lock = new ReentrantReadWriteLock();      
>         final ReadLock readLock = lock.readLock();      
>         final WriteLock writeLock = lock.writeLock();      
>         readLock.lock();      
>         System.out.println("Hold the read lock");      
>         writeLock.lock();      
>         System.out.println("Hold the write lock");      
>     }      
> }
> 
>  

输出如下：

> Hold the read lock
> 
>  

程序永远卡在这里了，因为拿不到写入锁而陷入等待。

（待续）