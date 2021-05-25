# guava的RateLimiter

https://zhuanlan.zhihu.com/p/60979444

漏桶算法：

漏桶算法可以将系统处理请求限定到恒定的速率，当请求过载时，漏桶将直接溢出。漏桶算法假定了系统处理请求的速率是恒定的，但是在现实环境中，往往我们的系统处理请求的速率不是恒定的。漏桶算法无法解决系统突发流量的情况。

令牌桶算法：

guava的RateLimiter使用的是令牌桶算法，就是以固定的频率向桶中放入令牌，例如一秒钟10枚令牌，实际业务在每次响应请求之前都从桶中获取令牌，只有取到令牌的请求才会被成功响应，获取的方式有两种：阻塞等待令牌或者取不到立即返回失败，下图来自网上：

![这里写图片描述](https://img-blog.csdn.net/20170715165032054?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYm9saW5nX2NhdmFscnk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

## Guava RateLimiter限流

Guava RateLimiter是一个谷歌提供的限流工具，RateLimiter基于令牌桶算法，可以有效限定单个JVM实例上某个接口的流量。

```
rateLimiter.tryAcquire()； //不阻塞
rateLimiter.acquire()	  //阻塞
```

### 平滑突发限流

使用 `RateLimiter`的静态方法创建一个限流器，设置每秒放置的令牌数为5个。返回的RateLimiter对象可以保证1秒内不会给超过5个令牌，并且以固定速率进行放置，达到平滑输出的效果。

```java
public void testSmoothBursty() {
 RateLimiter r = RateLimiter.create(5);
 while (true) {
 System.out.println("get 1 tokens: " + r.acquire() + "s");
 }
 /**
     * output: 基本上都是0.2s执行一次，符合一秒发放5个令牌的设定。
     * get 1 tokens: 0.0s 
     * get 1 tokens: 0.182014s
     * get 1 tokens: 0.188464s
     * get 1 tokens: 0.198072s
     * get 1 tokens: 0.196048s
     * get 1 tokens: 0.197538s
     * get 1 tokens: 0.196049s
     */
}
```



`RateLimiter`使用令牌桶算法，会进行令牌的累积，如果获取令牌的频率比较低，则不会导致等待，直接获取令牌。

```java
public void testSmoothBursty2() {
 RateLimiter r = RateLimiter.create(2);
 while (true)
 {
    System.out.println("get 1 tokens: " + r.acquire(1) + "s");
 try {
    Thread.sleep(2000);
 } catch (Exception e) {}
 System.out.println("get 1 tokens: " + r.acquire(1) + "s");
 System.out.println("get 1 tokens: " + r.acquire(1) + "s");
 System.out.println("get 1 tokens: " + r.acquire(1) + "s");
 System.out.println("end");
 /**
       * output:
       * get 1 tokens: 0.0s
       * get 1 tokens: 0.0s
       * get 1 tokens: 0.0s
       * get 1 tokens: 0.0s
       * end
       * get 1 tokens: 0.499796s
       * get 1 tokens: 0.0s
       * get 1 tokens: 0.0s
       * get 1 tokens: 0.0s
       */
 }
}
```



`RateLimiter`由于会累积令牌，所以可以应对突发流量。在下面代码中，有一个请求会直接请求5个令牌，但是由于此时令牌桶中有累积的令牌，足以快速响应。 `RateLimiter`在没有足够令牌发放时，采用滞后处理的方式，也就是前一个请求获取令牌所需等待的时间由下一次请求来承受，也就是代替前一个请求进行等待。

```java
public void testSmoothBursty3() {
 RateLimiter r = RateLimiter.create(5);
 while (true)
 {
    System.out.println("get 5 tokens: " + r.acquire(5) + "s");
    System.out.println("get 1 tokens: " + r.acquire(1) + "s");
    System.out.println("get 1 tokens: " + r.acquire(1) + "s");
    System.out.println("get 1 tokens: " + r.acquire(1) + "s");
    System.out.println("end");
 /**
       * output:
       * get 5 tokens: 0.0s
       * get 1 tokens: 0.996766s 滞后效应，需要替前一个请求进行等待
       * get 1 tokens: 0.194007s
       * get 1 tokens: 0.196267s
       * end
       * get 5 tokens: 0.195756s
       * get 1 tokens: 0.995625s 滞后效应，需要替前一个请求进行等待
       * get 1 tokens: 0.194603s
       * get 1 tokens: 0.196866s
       */
 }
}
```

### 平滑预热限流

`RateLimiter`的 `SmoothWarmingUp`是带有预热期的平滑限流，它启动后会有一段预热期，逐步将分发频率提升到配置的速率。 比如下面代码中的例子，创建一个平均分发令牌速率为2，预热期为3分钟。由于设置了预热时间是3秒，令牌桶一开始并不会0.5秒发一个令牌，而是形成一个平滑线性下降的坡度，频率越来越高，在3秒钟之内达到原本设置的频率，以后就以固定的频率输出。这种功能适合系统刚启动需要一点时间来“热身”的场景。

```java
public void testSmoothwarmingUp() {
 RateLimiter r = RateLimiter.create(2, 3, TimeUnit.SECONDS);
 while (true)
 {
   System.out.println("get 1 tokens: " + r.acquire(1) + "s");
   System.out.println("get 1 tokens: " + r.acquire(1) + "s");
   System.out.println("get 1 tokens: " + r.acquire(1) + "s");
   System.out.println("get 1 tokens: " + r.acquire(1) + "s");
   System.out.println("end");
 /**
       * output:
       * get 1 tokens: 0.0s
       * get 1 tokens: 1.329289s
       * get 1 tokens: 0.994375s
       * get 1 tokens: 0.662888s  上边三次获取的时间相加正好为3秒
       * end
       * get 1 tokens: 0.49764s  正常速率0.5秒一个令牌
       * get 1 tokens: 0.497828s
       * get 1 tokens: 0.49449s
       * get 1 tokens: 0.497522s
       */
 }
}
```

### 源码分析

看完了 `RateLimiter`的基本使用示例后，我们来学习一下它的实现原理。先了解一下几个比较重要的成员变量的含义。

```java
//SmoothRateLimiter.java
//当前存储令牌数
double storedPermits;
//最大存储令牌数
double maxPermits;
//添加令牌时间间隔
double stableIntervalMicros;
/**
 * 下一次请求可以获取令牌的起始时间
 * 由于RateLimiter允许预消费，上次请求预消费令牌后
 * 下次请求需要等待相应的时间到nextFreeTicketMicros时刻才可以获取令牌
 */
private long nextFreeTicketMicros = 0L;
```



### 平滑突发限流

`RateLimiter`的原理就是每次调用 `acquire`时用当前时间和 `nextFreeTicketMicros`进行比较，根据二者的间隔和添加单位令牌的时间间隔 `stableIntervalMicros`来刷新存储令牌数 `storedPermits`。然后acquire会进行休眠，直到 `nextFreeTicketMicros`。

`acquire`函数如下所示，它会调用 `reserve`函数计算获取目标令牌数所需等待的时间，然后使用 `SleepStopwatch`进行休眠，最后返回等待时间。

```java
public double acquire(int permits) {
 // 计算获取令牌所需等待的时间
 long microsToWait = reserve(permits);
 // 进行线程sleep
    stopwatch.sleepMicrosUninterruptibly(microsToWait);
 return 1.0 * microsToWait / SECONDS.toMicros(1L);
}
final long reserve(int permits) {
    checkPermits(permits);
 // 由于涉及并发操作，所以使用synchronized进行并发操作
 synchronized (mutex()) {
 return reserveAndGetWaitLength(permits, stopwatch.readMicros());
 }
}
final long reserveAndGetWaitLength(int permits, long nowMicros) {
 // 计算从当前时间开始，能够获取到目标数量令牌时的时间
 long momentAvailable = reserveEarliestAvailable(permits, nowMicros);
 // 两个时间相减，获得需要等待的时间
 return max(momentAvailable - nowMicros, 0);
}
```

`reserveEarliestAvailable`是刷新令牌数和下次获取令牌时间 `nextFreeTicketMicros`的关键函数。它有三个步骤，一是调用 `resync`函数增加令牌数，二是计算预支付令牌所需额外等待的时间，三是更新下次获取令牌时间 `nextFreeTicketMicros`和存储令牌数 `storedPermits`。

这里涉及 `RateLimiter`的一个特性，也就是可以预先支付令牌，并且所需等待的时间在下次获取令牌时再实际执行。详细的代码逻辑的解释请看注释。

```java
final long reserveEarliestAvailable(int requiredPermits, long nowMicros) {
 // 刷新令牌数，相当于每次acquire时在根据时间进行令牌的刷新
    resync(nowMicros);
 long returnValue = nextFreeTicketMicros;
 // 获取当前已有的令牌数和需要获取的目标令牌数进行比较，计算出可以目前即可得到的令牌数。
 double storedPermitsToSpend = min(requiredPermits, this.storedPermits);
 // freshPermits是需要预先支付的令牌，也就是目标令牌数减去目前即可得到的令牌数
 double freshPermits = requiredPermits - storedPermitsToSpend;
 // 因为会突然涌入大量请求，而现有令牌数又不够用，因此会预先支付一定的令牌数
 // waitMicros即是产生预先支付令牌的数量时间，则将下次要添加令牌的时间应该计算时间加上watiMicros
 long waitMicros = storedPermitsToWaitTime(this.storedPermits, storedPermitsToSpend)
 + (long) (freshPermits * stableIntervalMicros);
 // storedPermitsToWaitTime在SmoothWarmingUp和SmoothBuresty的实现不同，用于实现预热缓冲期
 // SmoothBuresty的storedPermitsToWaitTime直接返回0，所以watiMicros就是预先支付的令牌所需等待的时间
 try {
 // 更新nextFreeTicketMicros,本次预先支付的令牌所需等待的时间让下一次请求来实际等待。
 this.nextFreeTicketMicros = LongMath.checkedAdd(nextFreeTicketMicros, waitMicros);
 } catch (ArithmeticException e) {
 this.nextFreeTicketMicros = Long.MAX_VALUE;
 }
 // 更新令牌数，最低数量为0
 this.storedPermits -= storedPermitsToSpend;
 // 返回旧的nextFreeTicketMicros数值，无需为预支付的令牌多加等待时间。
 return returnValue;
}
// SmoothBurest
long storedPermitsToWaitTime(double storedPermits, double permitsToTake) {
 return 0L;
}
```

`resync`函数用于增加存储令牌，核心逻辑就是 `(nowMicros-nextFreeTicketMicros)/stableIntervalMicros`。当前时间大于 `nextFreeTicketMicros`时进行刷新，否则直接返回。

```java
void resync(long nowMicros) {
 // 当前时间晚于nextFreeTicketMicros，所以刷新令牌和nextFreeTicketMicros
 if (nowMicros > nextFreeTicketMicros) {
 // coolDownIntervalMicros函数获取每机秒生成一个令牌，SmoothWarmingUp和SmoothBuresty的实现不同
 // SmoothBuresty的coolDownIntervalMicros直接返回stableIntervalMicros
 // 当前时间减去要更新令牌的时间获取时间间隔，再除以添加令牌时间间隔获取这段时间内要添加的令牌数
      storedPermits = min(maxPermits,
          storedPermits
 + (nowMicros - nextFreeTicketMicros) / coolDownIntervalMicros());
      nextFreeTicketMicros = nowMicros;
 }
 // 如果当前时间早于nextFreeTicketMicros，则获取令牌的线程要一直等待到nextFreeTicketMicros,该线程获取令牌所需
 // 额外等待的时间由下一次获取的线程来代替等待。
}
double coolDownIntervalMicros() {
 return stableIntervalMicros;
}
```



下面我们举个例子，让大家更好的理解 `resync`和 `reserveEarliestAvailable`函数的逻辑。

比如 `RateLimiter`的 `stableIntervalMicros`为500，也就是1秒发两个令牌，storedPermits为0，nextFreeTicketMicros为155391849 `57`48。线程一acquire(2)，当前时间为155391849 `62`48，首先 `resync`函数计算，(1553918496248 - 1553918495748)/500 = 1，所以当前可获取令牌数为1，但是由于可以预支付，所以nextFreeTicketMicros= nextFreeTicketMicro + 1 * 500 = 155391849 `67`48。线程一无需等待。

紧接着，线程二也来acquire(2)，首先 `resync`函数发现当前时间早于 `nextFreeTicketMicros`，所以无法增加令牌数，所以需要预支付2个令牌，nextFreeTicketMicros= nextFreeTicketMicro + 2 * 500 = 155391849 `77`48。线程二需要等待155391849 `67`48时刻，也就是线程一获取时计算的nextFreeTicketMicros时刻。同样的，线程三获取令牌时也需要等待到线程二计算的nextFreeTicketMicros时刻。

### 平滑预热限流

上述就是平滑突发限流RateLimiter的实现，下面我们来看一下加上预热缓冲期的实现原理。 `SmoothWarmingUp`实现预热缓冲的关键在于其分发令牌的速率会随时间和令牌数而改变，速率会先慢后快。表现形式如下图所示，令牌刷新的时间间隔由长逐渐变短。等存储令牌数从maxPermits到达thresholdPermits时，发放令牌的时间价格也由coldInterval降低到了正常的stableInterval。



![img](https://pic4.zhimg.com/80/v2-ab13932c4fd594a753c99a69a0194b5f_720w.jpg)



`SmoothWarmingUp`的相关代码如下所示，相关的逻辑都写在注释中。

```java
// SmoothWarmingUp，等待时间就是计算上图中梯形或者正方形的面积。
long storedPermitsToWaitTime(double storedPermits, double permitsToTake) {
 /**
    * 当前permits超出阈值的部分
    */
 double availablePermitsAboveThreshold = storedPermits - thresholdPermits;
 long micros = 0;
 /**
    * 如果当前存储的令牌数超出thresholdPermits
    */
 if (availablePermitsAboveThreshold > 0.0) {
 /**
     * 在阈值右侧并且需要被消耗的令牌数量
     */
 double permitsAboveThresholdToTake = min(availablePermitsAboveThreshold, permitsToTake);

 /**
        * 梯形的面积
        *
        * 高 * (顶 * 底) / 2
        *
        * 高是 permitsAboveThresholdToTake 也就是右侧需要消费的令牌数
        * 底 较长 permitsToTime(availablePermitsAboveThreshold)
        * 顶 较短 permitsToTime(availablePermitsAboveThreshold - permitsAboveThresholdToTake)
        */
    micros = (long) (permitsAboveThresholdToTake
 * (permitsToTime(availablePermitsAboveThreshold)
 + permitsToTime(availablePermitsAboveThreshold - permitsAboveThresholdToTake)) / 2.0);
 /**
        * 减去已经获取的在阈值右侧的令牌数
        */
    permitsToTake -= permitsAboveThresholdToTake;
 }
 /**
    * 平稳时期的面积，正好是长乘宽
    */
    micros += (stableIntervalMicros * permitsToTake);
 return micros;
}

double coolDownIntervalMicros() {
 /**
    * 每秒增加的令牌数为 warmup时间/maxPermits. 这样的话，在warmuptime时间内，就就增张的令牌数量
    * 为 maxPermits
    */
 return warmupPeriodMicros / maxPermits;
}
```

### 后记

`RateLimiter`只能用于单机的限流，如果想要集群限流，则需要引入 `redis`或者阿里开源的 `sentinel`中间件，请大家继续关注。