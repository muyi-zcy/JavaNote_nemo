# Java单例模式的不同写法

> 单例模式，顾名思义，单个类自己负责创建自己的对象，且当前进程中有且只实例化一次。
>
> 单例模式的写法通常包括：
>
> - 饿汉模式
> - 懒汉模式
> - 双重检测锁
> - 静态内部类
> - 枚举



## 饿汉模式

```java
public class HungryManMode {
    private static HungryManMode hungryManMode = new HungryManMode();
    private HungryManMode(){};
    public static HungryManMode getHungryManMode(){
        return hungryManMode;
    }
}
```

**饿汉模式在类加载的时候就对实例进行创建，避免了多线程重复创建实例的问题，但是类加载就创建一定程度造成了内存的浪费。**

## 懒汉模式

```java
public class LazyManMode {
    private static LazyManMode lazyManMode = null;
    private LazyManMode(){};
    public static synchronized LazyManMode getLazyManMode(){
        if(lazyManMode == null){
            lazyManMode = new LazyManMode();
        }
        return lazyManMode;
    }
}
```

**懒汉模式中在单例需要的时候才进行创建，为了解决存在的并发问题，使用synchronized关键字 进行约束，也就造成了一定的性能浪费 **

## 双重检测锁

```java
public class MutexMode  implements Serializable {
    private static MutexMode mutexMode = null;
    private MutexMode(){};
    private static MutexMode mutex(){
        if(mutexMode == null){
            synchronized (MutexMode.class){
                if(mutexMode == null){
                    mutexMode = new MutexMode();
                }
            }
        }
        return mutexMode;
    }
}
```

**第一次见到双重检测锁实在guava的限流ReteLimiter.class，即实现了延迟加载，又解决了并发问题（一定程度解决了并发的性能浪费）**

但是由于jvm的指令重排优化，导致双重检测锁失效，jdk1.5之后增加了**volatile**关键字，**volatile的一个语义是禁止指令重排序优化**

## 静态内部类

```java
public class StaticClass  implements Serializable {
    private StaticClass(){};
    private static class staticClass{
        private static Object object = new Object();
    }
    public static Object getObject(){
        return staticClass.object;
    }
}
```

通过在内部类中创建实例，利用类加载机制，保证只创建一次实例，保证了延迟加载和线程安全。

## 枚举

```java
// Singledto.class
public class Singledto implements Serializable {}

// EnumSingleton.class
public enum EnumSingleton {
    ;
    private Singledto singledto;

    private EnumSingleton(){
        singledto = new Singledto();
    }

    public Singledto getSingledto(){
        return singledto;
    }
}
```

枚举中的实例被保证只会被实例化一次，所以需要的实例也会保证实例化一次，以上五种方法，**枚举**最为推荐

## 单例模式的破坏

以懒汉模式为例：

```java
public class LazyManMode  implements Serializable {
    private static LazyManMode lazyManMode = null;
    private LazyManMode(){};
    public static synchronized LazyManMode getLazyManMode(){
        if(lazyManMode == null){
            lazyManMode = new LazyManMode();
        }
        return lazyManMode;
    }

    public static void main(String[] args) throws Exception {
        LazyManMode o1 = LazyManMode.getLazyManMode();
        // 反射破坏单例
        Constructor<LazyManMode> constructor = LazyManMode.class.getDeclaredConstructor();
        constructor.setAccessible(true);
        LazyManMode o2 = (LazyManMode) constructor.newInstance();

        System.out.println(o1 == o2);

        //序列化破坏单例
        FileOutputStream fos = new FileOutputStream("SerSingleton.obj");
        ObjectOutputStream oos = new ObjectOutputStream(fos);
        oos.writeObject(o1);
        oos.flush();
        oos.close();

        FileInputStream fis = new FileInputStream("SerSingleton.obj");
        ObjectInputStream ois = new ObjectInputStream(fis);
        LazyManMode o3 = (LazyManMode) ois.readObject();
        System.out.println(o1==o3);
    }
}

//run
false
false
```



```java
public class LazyManMode  implements Serializable {
    private static LazyManMode lazyManMode = null;
    private LazyManMode(){};
    public static synchronized LazyManMode getLazyManMode(){
        if(lazyManMode == null){
            lazyManMode = new LazyManMode();
        }
        return lazyManMode;
    }
    private Object readResolve() {
        return lazyManMode;
    }

    public static void main(String[] args) throws Exception {
        LazyManMode o1 = LazyManMode.getLazyManMode();
        // 反射破坏单例
        Constructor<LazyManMode> constructor = LazyManMode.class.getDeclaredConstructor();
        constructor.setAccessible(true);
        LazyManMode o2 = (LazyManMode) constructor.newInstance();

        System.out.println(o1 == o2);

        //序列化破坏单例
        FileOutputStream fos = new FileOutputStream("SerSingleton.obj");
        ObjectOutputStream oos = new ObjectOutputStream(fos);
        oos.writeObject(o1);
        oos.flush();
        oos.close();

        FileInputStream fis = new FileInputStream("SerSingleton.obj");
        ObjectInputStream ois = new ObjectInputStream(fis);
        LazyManMode o3 = (LazyManMode) ois.readObject();
        // readObject深入源码查找会发现，反序列化时若目标类有readResolve方法，
        // 那就通过反射的方式调用要被反序列化的类中的readResolve方法，返回一个对象，
        // 然后把这个新的对象复制给之前创建的obj（即最终返回的对象）（https://zhuanlan.zhihu.com/p/136769959）
        System.out.println(o1==o3);
    }
}
// run 
false
true
```

