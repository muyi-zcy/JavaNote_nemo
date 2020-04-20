# synchronized

### synchronized是Java中的关键字，是一种同步锁。它修饰的对象有以下几种： 

1. 修饰一个代码块，被修饰的代码块称为同步语句块，其作用的范围是大括号{}括起来的代码，作用的对象是调用这个代码块的对象； 
2. 修饰一个方法，被修饰的方法称为同步方法，其作用的范围是整个方法，作用的对象是调用这个方法的对象； 
3. 修饰一个静态的方法，其作用的范围是整个静态方法，作用的对象是这个类的所有对象； 
4. 修饰一个类，其作用的范围是synchronized后面括号括起来的部分，作用主的对象是这个类的所有对象。

## 1.修饰一个代码块

### 1.1.  一个线程访问一个对象中的synchronized(this)同步代码块时，其他试图访问该对象的线程将被阻塞。

#### 使用synchronized

```java
class SyncThread implements Runnable {
    private static int count;
    public SyncThread() {
        count = 0;
    }
    public void run() {
        synchronized (this){//使用synchronized修饰整个代码块
            for (int i = 0; i < 5; i++) {
                try {
                    System.out.println("线程名:"+Thread.currentThread().getName() + ":" + (count++));
                    Thread.sleep(100);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }
    }
}
public class Main {
    public static void main(String[] args) {
        System.out.println("使用关键字synchronized");
        SyncThread syncThread = new SyncThread();
        Thread thread1 = new Thread(syncThread, "SyncThread1");
        Thread thread2 = new Thread(syncThread, "SyncThread2");
        thread1.start();
        thread2.start();
    }
}

//输出结果
使用关键字synchronized
线程名:SyncThread1:0
线程名:SyncThread1:1
线程名:SyncThread1:2
线程名:SyncThread1:3
线程名:SyncThread1:4
线程名:SyncThread2:5
线程名:SyncThread2:6
线程名:SyncThread2:7
线程名:SyncThread2:8
线程名:SyncThread2:9
```

#### 不使用synchronized

```java
class SyncThread implements Runnable {
    private static int count;
    public SyncThread() {
        count = 0;
    }
    public void run() {
//        synchronized (this){//使用synchronized修饰整个代码块
            for (int i = 0; i < 5; i++) {
                try {
                    System.out.println("线程名:"+Thread.currentThread().getName() + ":" + (count++));
                    Thread.sleep(100);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
//        }
    }
}
public class Main {
    public static void main(String[] args) {
        System.out.println("使用关键字synchronized");
        SyncThread syncThread = new SyncThread();
        Thread thread1 = new Thread(syncThread, "SyncThread1");
        Thread thread2 = new Thread(syncThread, "SyncThread2");
        thread1.start();
        thread2.start();
    }
}

//输出结果
使用关键字synchronized
线程名:SyncThread2:0
线程名:SyncThread1:1
线程名:SyncThread1:2
线程名:SyncThread2:3
线程名:SyncThread1:4
线程名:SyncThread2:5
线程名:SyncThread1:7
线程名:SyncThread2:6
线程名:SyncThread2:8
线程名:SyncThread1:9
```

当两个并发线程(thread1和thread2)访问同一个对象(syncThread)中的synchronized代码块时，在同一时刻只能有一个线程得到执行，另一个线程受阻塞，必须等待当前线程执行完这个代码块以后另一个线程才能执行该代码块。Thread1和thread2是互斥的，因为**在执行synchronized代码块时会锁定当前的对象，只有执行完该代码块才能释放该对象锁，下一个线程才能执行并锁定该对象**。 

修改代码:

```java
class SyncThread implements Runnable {
    private static int count;
    public SyncThread() {
        count = 0;
    }
    public void run() {
        synchronized (this){
            for (int i = 0; i < 5; i++) {
                try {
                    System.out.println("线程名:"+Thread.currentThread().getName() + ":" + (count++));
                    Thread.sleep(100);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }
    }
}
public class Main {
    public static void main(String[] args) {
        System.out.println("使用关键字synchronized每次调用进行new SyncThread()");
        SyncThread syncThread1 = new SyncThread();
        SyncThread syncThread2 = new SyncThread();
        Thread thread1 = new Thread(syncThread1, "SyncThread1");
        Thread thread2 = new Thread(syncThread2, "SyncThread2");
        thread1.start();
        thread2.start();
    }
}
//输出
使用关键字synchronized每次调用进行new SyncThread()
线程名:SyncThread1:0
线程名:SyncThread2:1
线程名:SyncThread1:2
线程名:SyncThread2:3
线程名:SyncThread2:4
线程名:SyncThread1:5
线程名:SyncThread1:6
线程名:SyncThread2:7
线程名:SyncThread2:8
线程名:SyncThread1:9
```

为什么上面的例子中thread1和thread2同时在执行。这是因为synchronized只锁定对象，每个对象只有一个锁（lock）与之相关. 

这时创建了两个SyncThread的对象syncThread1和syncThread2，线程thread1执行的是syncThread1对象中的synchronized代码(run)，而线程thread2执行的是syncThread2对象中的synchronized代码(run)；我们知道synchronized锁定的是对象，这时会有两把锁分别锁定syncThread1对象和syncThread2对象，而这两把锁是互不干扰的，不形成互斥，所以两个线程可以同时执行。

### 1.2. 当一个线程访问对象的一个synchronized(this)同步代码块时，另一个线程仍然可以访问该对象中的非synchronized(this)同步代码块。 

```java
public class Main {
    static class Mthreads implements Runnable {
        private int count;

        public Mthreads() {
            count = 0;
        }

        public void countAdd() {
            synchronized (this) {
                for (int i = 0; i < 5; i++) {
                    try {
                        System.out.println(Thread.currentThread().getName() + ":" + (count++));
                        Thread.sleep(100);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
            }
        }

        //非synchronized代码块，未对count进行读写操作，所以可以不用synchronized
        public void printCount() {
            for (int i = 0; i < 5; i++) {
                try {
                    System.out.println(Thread.currentThread().getName() + " count:" + count);
                    Thread.sleep(100);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }

        public void run() {
            String threadName = Thread.currentThread().getName();
            if (threadName.equals("mt1")) {
                countAdd();
            } else if (threadName.equals("mt2")) {
                printCount();
            }
        }
    }
    public static void main(String[] args) {
        System.out.println("使用关键字synchronized");
        Mthreads mt=new Mthreads();
        Thread thread1 = new Thread(mt, "mt1");
        Thread thread2 = new Thread(mt, "mt2");
        thread1.start();
        thread2.start();
    }
}

//输出
使用关键字synchronized
mt1:0
mt2 count:1
mt1:1
mt2 count:1
mt1:2
mt2 count:3
mt1:3
mt2 count:4
mt1:4
mt2 count:5
//输出
使用关键字synchronized
mt1:0
mt2 count:1
mt2 count:1
mt1:1
mt1:2
mt2 count:3
mt1:3
mt2 count:4
mt2 count:4
mt1:4
```

上面代码中countAdd是一个synchronized的，printCount是非synchronized的。从上面的结果中可以看出一个线程访问一个对象的synchronized代码块时，别的线程可以访问该对象的非synchronized代码块而不受阻塞。

## 2.修饰一个方法

Synchronized修饰一个方法很简单，就是在方法的前面加synchronized，public synchronized void method(){}; synchronized修饰方法和修饰一个代码块类似，只是作用范围不一样，修饰代码块是大括号括起来的范围，而修饰方法范围是整个函数。如将的run方法改成如下的方式，实现的效果一样。

```java
public class Main {
    static class Mthreads implements Runnable {
        private int count;

        public Mthreads() {
            count = 0;
        }

        public void countAdd() {
            synchronized (this) {
                for (int i = 0; i < 5; i++) {
                    try {
                        System.out.println(Thread.currentThread().getName() + ":" + (count++));
                        Thread.sleep(100);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
            }
        }

        //非synchronized代码块，未对count进行读写操作，所以可以不用synchronized
        public void printCount() {
            for (int i = 0; i < 5; i++) {
                try {
                    System.out.println(Thread.currentThread().getName() + " count:" + count);
                    Thread.sleep(100);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }

        public synchronized void run() {
            String threadName = Thread.currentThread().getName();
            if (threadName.equals("mt1")) {
                countAdd();
            } else if (threadName.equals("mt2")) {
                printCount();
            }
        }
    }
    public static void main(String[] args) {
        System.out.println("使用关键字synchronized");
        Mthreads mt=new Mthreads();
        Thread thread1 = new Thread(mt, "mt1");
        Thread thread2 = new Thread(mt, "mt2");
        thread1.start();
        thread2.start();
    }
}
//输出
使用关键字synchronized
mt1:0
mt1:1
mt1:2
mt1:3
mt1:4
mt2 count:5
mt2 count:5
mt2 count:5
mt2 count:5
mt2 count:5

```

1. 虽然可以使用synchronized来定义方法，但synchronized并不属于方法定义的一部分，因此，synchronized关键字不能被继承。如果在父类中的某个方法使用了synchronized关键字，而在子类中覆盖了这个方法，在子类中的这个方法默认情况下并不是同步的，而必须显式地在子类的这个方法中加上synchronized关键字才可以。当然，还可以在子类方法中调用父类中相应的方法，这样虽然子类中的方法不是同步的，但子类调用了父类的同步方法，因此，子类的方法也就相当于同步了。
2. 在定义接口方法时不能使用synchronized关键字。
3. 构造方法不能使用synchronized关键字，但可以使用synchronized代码块来进行同步。

## 3.修饰一个静态方法

Synchronized也可修饰一个静态方法，用法如下：

```java
public synchronized static void method() {
   
}
```

我们知道静态方法是属于类的而不属于对象的。同样的，synchronized修饰的静态方法锁定的是这个类的所有对象。

```java
class SyncThread implements Runnable {
    private static int count;

    public SyncThread() {
        count = 0;
    }

    public synchronized static void method() {
        for (int i = 0; i < 5; i ++) {
            try {
                System.out.println(Thread.currentThread().getName() + ":" + (count++));
                Thread.sleep(100);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }

    public synchronized void run() {
        method();
    }
}
public class Main {
    public static void main(String[] args) {
        System.out.println("使用关键字静态synchronized");
        SyncThread syncThread = new SyncThread();
        Thread thread1 = new Thread(syncThread, "SyncThread1");
        Thread thread2 = new Thread(syncThread, "SyncThread2");
        thread1.start();
        thread2.start();
    }
}
//输出
使用关键字静态synchronized
SyncThread1:0
SyncThread1:1
SyncThread1:2
SyncThread1:3
SyncThread1:4
SyncThread2:5
SyncThread2:6
SyncThread2:7
SyncThread2:8
SyncThread2:9
```

syncThread1和syncThread2是SyncThread的两个对象，但在thread1和thread2并发执行时却保持了线程同步。这是因为run中调用了静态方法method，而静态方法是属于类的，所以syncThread1和syncThread2相当于用了**同一把锁**。这与使用关键字synchronized运行结果相同

## 4.修饰一个类

```java
class ClassName {
    public void method() {
        synchronized(ClassName.class) {

        }
    }
}
class SyncThread implements Runnable {
    private static int count;

    public SyncThread() {
        count = 0;
    }

    public static void method() {
        synchronized(SyncThread.class) {
            for (int i = 0; i < 5; i ++) {
                try {
                    System.out.println(Thread.currentThread().getName() + ":" + (count++));
                    Thread.sleep(100);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }
    }

    public synchronized void run() {
        method();
    }
}
public class Main {
    public static void main(String[] args) {
        System.out.println("使用ClassName");
        SyncThread syncThread = new SyncThread();
        Thread thread1 = new Thread(syncThread, "SyncThread1");
        Thread thread2 = new Thread(syncThread, "SyncThread2");
        thread1.start();
        thread2.start();
    }
}
//输出
使用ClassName
SyncThread2:0
SyncThread2:1
SyncThread2:2
SyncThread2:3
SyncThread2:4
SyncThread1:5
SyncThread1:6
SyncThread1:7
SyncThread1:8
SyncThread1:9
```

效果和上面synchronized修饰静态方法是一样的，synchronized作用于一个类时，是给这个类加锁，类的所有对象用的是同一把锁。

## 5.总结

1、 无论synchronized关键字加在方法上还是对象上，如果它作用的对象是非静态的，则它取得的锁是对象；如果synchronized作用的对象是一个静态方法或一个类，则它取得的锁是对类，该类所有的对象同一把锁。 
2、每个对象只有一个锁（lock）与之相关联，谁拿到这个锁谁就可以运行它所控制的那段代码。 
3、实现同步是要很大的系统开销作为代价的，甚至可能造成死锁，所以尽量避免无谓的同步控制