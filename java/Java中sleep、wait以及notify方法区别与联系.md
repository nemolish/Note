### Java中sleep、wait以及notify方法区别与联系

#### 前言

 本文基于源码注释的解读，对sleep、wait、notify方法进行学习与了解。
另外，文章中出现了“持有对象锁，释放对象锁”的描述，之所以对对象进行加锁，是因为我们讨论问题的背景是多线程。如果多个线程同时对一个变量进行操作，有可能发生意想不到的情形，因此我们需要对对象先进行加锁，再进行操作。既然有加锁这一操作，当然也就有释放锁这一操作。



#### sleep方法

Thread类中的sleep方法：

```java
/** 
 * Causes the currently executing thread to sleep (temporarily cease 
 * execution) for the specified number of milliseconds, subject to 
 * the precision and accuracy of system timers and schedulers. The thread 
 * does not lose ownership of any monitors. 
 */  
public static native void sleep(long millis) throws InterruptedException; 
```

解析sleep方法的源码注释：
调用sleep方法可以让当前正在运行的线程进入睡眠状态，即暂时停止运行指定的单位时间。并且该线程在睡眠期间**不会释放对象锁**。
sleep方法的目的是让当前线程暂停运行一段时间，而与对象锁相关的信息无影响，如果执行sleep方法时是处于持有对象锁的状态，那么睡眠时依然持有对象锁，如果执行sleep方法时不是处于持有对象锁的状态，睡眠期间也不会持有对象锁。

#### wait方法

Object类中的wait方法：

```java
/** 
 * Causes the current thread to wait until another thread invokes the 
 * {@link java.lang.Object#notify()} method or the 
 * {@link java.lang.Object#notifyAll()} method for this object. 
 * In other words, this method behaves exactly as if it simply 
 * performs the call {@code wait(0)}. 
 * <p> 
 * The current thread must own this object's monitor. The thread 
 * releases ownership of this monitor and waits until another thread 
 * notifies threads waiting on this object's monitor to wake up 
 * either through a call to the {@code notify} method or the 
 * {@code notifyAll} method. The thread then waits until it can 
 * re-obtain ownership of the monitor and resumes execution. 
 * <p> 
 * As in the one argument version, interrupts and spurious wakeups are 
 * possible, and this method should always be used in a loop: 
 * <pre> 
 *     synchronized (obj) { 
 *         while (<condition does not hold>) 
 *             obj.wait(); 
 *         ... // Perform action appropriate to condition 
 *     } 
 * </pre> 
 * This method should only be called by a thread that is the owner 
 * of this object's monitor. See the {@code notify} method for a 
 * description of the ways in which a thread can become the owner of 
 * a monitor. 
 */  
public final native void wait(long timeout) throws InterruptedException;  
```

解析wait方法的源码注释：
调用wait方法可以让当前线程（即调用object.wait方法的那个线程）进入等待唤醒状态，该线程会处于等待唤醒状态直到另一个线程调用了object对象的notify方法或者notifyAll方法。该方法没有形参，相当于调用了参数为0的wait（long timeout）方法。
同时，要调用wait方法，前提是获取了这个对象的锁。在调用wait方法时，线程会释放锁并进入等待状态。在被唤醒后，该线程会一直处于等待获取锁的状态直到它重新获取到锁，然后才可以重新恢复运行状态。
注释也强调了，该方法应该只在获取了对象的锁之后才去调用，即wait方法应该放在synchronized(obj){}块中，否则运行期间会抛出IllegalMonitorStateException异常。

#### notify方法和notifyAll方法

既然介绍了wait方法，怎能少了notify方法和notifyAll方法。
Object类中的notify方法：

```java
/** 
 * Wakes up a single thread that is waiting on this object's 
 * monitor. If any threads are waiting on this object, one of them 
 * is chosen to be awakened. The choice is arbitrary and occurs at 
 * the discretion of the implementation. A thread waits on an object's 
 * monitor by calling one of the {@code wait} methods. 
 * <p> 
 * The awakened thread will not be able to proceed until the current 
 * thread relinquishes the lock on this object. The awakened thread will 
 * compete in the usual manner with any other threads that might be 
 * actively competing to synchronize on this object; for example, the 
 * awakened thread enjoys no reliable privilege or disadvantage in being 
 * the next thread to lock this object. 
 * <p> 
 * This method should only be called by a thread that is the owner 
 * of this object's monitor. A thread becomes the owner of the 
 * object's monitor in one of three ways: 
 * <ul> 
 * <li>By executing a synchronized instance method of that object. 
 * <li>By executing the body of a {@code synchronized} statement 
 *     that synchronizes on the object. 
 * <li>For objects of type {@code Class,} by executing a 
 *     synchronized static method of that class. 
 * </ul> 
 * <p> 
 * Only one thread at a time can own an object's monitor. 
 * 
 * @throws  IllegalMonitorStateException  if the current thread is not 
 *               the owner of this object's monitor. 
 * @see        java.lang.Object#notifyAll() 
 * @see        java.lang.Object#wait() 
 */  
public final native void notify(); 
```
解析notify方法的源码注释：
举例来解释，比方说多线程下，object对象是共享对象，那么对object对象的操作可以放在synchronized(object)同步块中，当线程A在同步块中执行object.wait方法，线程A就进入了等待状态。这时在线程Q中的同步块中执行object.notify()，就能唤醒线程A。另一种情形，假如线程ABC都执行了object.wait方法，那么当线程Q中执行了object.notify()，则只能唤醒其中一个，至于具体是哪一个，是任意的。
调用notify唤醒的线程，处于等待获取锁状态，且这个被唤醒的线程，相对于其他在等待获取锁的线程，没有任何特权，也没有任何劣势，即公平竞争。
如注释中所描述，同步块，可以是对共享对象进行同步处理，也可以直接对实例方法进行同步处理，还可以对类进行同步处理。
如果，当前调用notify方法的线程没有获取对象锁，则将抛出IllegalMonitorStateException异常。

notifyAll方法与notify方法很类似，不同之处是notify方法唤醒任意一个线程而notifyAll方法唤醒的是所有线程。

#### wait方法和sleep方法的对比

正如方法定义所描述，这两个方法都是native方法，且都会抛出InterruptedException，其中sleep方法是类方法，而wait方法是实例方法。
sleep方法是Thread类的方法，而wait方法是Object类的方法，由于一切类都是继承自Object类，因此Thread类中也有wait方法。
wait方法和sleep方法都可以通过interrupt方法打断线程的暂停状态，从而使得线程立刻抛出IntterruptedException。
sleep方法的作用是让当前线程暂停指定的时间，无关对象锁；而wait方法则是用于多个线程间的信息交互，与对象锁有关。
sleep方法是类方法，而锁是对象级别的。因此sleep方法并不影响锁的相关行为。因此如果在调用sleep方法时该线程是处理持有对象锁的状态，睡眠状态中仍然持有锁。而wait方法能安全使用的前提是获取了对象锁，wait方法之所以可以用于多个线程间的信息交流，正是它会释放对象锁。
这里引用知乎用户“孙立伟”的一段话：Thread.sleep和Object.wait都会暂停当前的线程，对于CPU资源来说，不管是哪种方式暂停的线程，都表示它暂时不再需要CPU的执行时间。操作系统会将执行时间分配给其它线程。区别是，调用wait后，需要别的线程执行notify/notifyAll才能够重新获得CPU执行时间。