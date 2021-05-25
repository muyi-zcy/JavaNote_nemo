# Guava之Stopwatch 计时器

精确给出两次测量之间的持续时间，

```java
//创建新的计时器，但不启动 
public static Stopwatch createUnstarted() {
    return new Stopwatch();
  }

// 使用指定时间创建新的计时器，但不启动 
 public static Stopwatch createUnstarted(Ticker ticker) {
    return new Stopwatch(ticker);
  }

// 创建一个新的计时器，启动
 public static Stopwatch createStarted() {
    return new Stopwatch().start();
  }

//使用指定时间创建新的计时器，启动 
 public static Stopwatch createStarted(Ticker ticker) {
    return new Stopwatch(ticker).start();
  }

  Stopwatch() {
    this.ticker = Ticker.systemTicker();
  }

  Stopwatch(Ticker ticker) {
    this.ticker = checkNotNull(ticker, "ticker");
  }

// 计时器是否在计时运行
  public boolean isRunning() {
    return isRunning;
  }

//启动计时器
  public Stopwatch start() {
    checkState(!isRunning, "This stopwatch is already running.");
    isRunning = true;
    startTick = ticker.read();
    return this;
  }

// 暂停计时器
  public Stopwatch stop() {
    long tick = ticker.read();
    checkState(isRunning, "This stopwatch is already stopped.");
    isRunning = false;
    elapsedNanos += tick - startTick;
    return this;
  }

// 重启
  @CanIgnoreReturnValue
  public Stopwatch reset() {
    elapsedNanos = 0;
    isRunning = false;
    return this;
  }

  private long elapsedNanos() {
    return isRunning ? ticker.read() - startTick + elapsedNanos : elapsedNanos;
  }

//按照指定单位，返回计时器时间，采用四舍五入
  public long elapsed(TimeUnit desiredUnit) {
    return desiredUnit.convert(elapsedNanos(), NANOSECONDS);
  }

// 返回精确的计时器时间
  public Duration elapsed() {
    return Duration.ofNanos(elapsedNanos());
  }

```





```java
        Stopwatch stopwatch = Stopwatch.createStarted();
        Thread.sleep(900);
        System.out.println(stopwatch.elapsed());
        System.out.println(stopwatch.elapsed(TimeUnit.SECONDS));
        System.out.println(stopwatch.elapsed(TimeUnit.MILLISECONDS));

        Thread.sleep(100);
        // 停止计时
        stopwatch.stop();
        System.out.println(stopwatch.elapsed());
        System.out.println(stopwatch.elapsed(TimeUnit.SECONDS));
        System.out.println(stopwatch.elapsed(TimeUnit.MILLISECONDS));

        // 再次计时
        stopwatch.start();
        Thread.sleep(1000);
        System.out.println(stopwatch.elapsed());
        System.out.println(stopwatch.elapsed(TimeUnit.SECONDS));
        System.out.println(stopwatch.elapsed(TimeUnit.MILLISECONDS));
        // 重置并开始
        stopwatch.reset().start();
        Thread.sleep(500);

        // 检查是否运行
        System.out.println(stopwatch.isRunning());
        System.out.println(stopwatch.elapsed());
        System.out.println(stopwatch.elapsed(TimeUnit.SECONDS));
        System.out.println(stopwatch.elapsed(TimeUnit.MILLISECONDS));
        System.out.println(stopwatch.toString());


		// run 
        PT0.8997019S
        0
        904
        PT1.004915S
        1
        1004
        PT2.004595S
        2
        2004
        true
        PT0.5005017S
        0
        500
        500.6 ms
            
		Process finished with exit code 0

```

