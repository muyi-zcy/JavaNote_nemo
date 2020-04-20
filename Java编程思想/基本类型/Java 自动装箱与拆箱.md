## 自动装箱与拆箱
说自动装箱与拆箱之前必须说java的基本数据类型和对应的封装类：
<table>
    <tbody><tr>
     <td> <p>序号</p> </td> 
     <td> <p>数据类型</p> </td> 
     <td> <p>大小/位</p> </td> 
     <td> <p>封装类</p> </td> 
     <td> <p>默认值（零值）</p> </td> 
     <td> <p>可表示数据范围</p> </td> 
    </tr>
    <tr>
     <td> <p>1</p> </td> 
     <td> <p>byte(位<span>)</span></p> </td> 
     <td> <p>8</p> </td> 
     <td> <p>Byte</p> </td> 
     <td> <p>(byte)0</p> </td> 
     <td> <p><span>-128~127</span></p> </td> 
    </tr>
    <tr>
     <td> <p>2</p> </td> 
     <td> <p>short(短整数<span>)</span></p> </td> 
     <td> <p>16</p> </td> 
     <td> <p>Short</p> </td> 
     <td> <p>(short)0</p> </td> 
     <td> <p><span>-32768~32767</span></p> </td> 
    </tr>
    <tr>
     <td> <p>3</p> </td> 
     <td> <p>int(整数<span>)</span></p> </td> 
     <td> <p>32</p> </td> 
     <td> <p>Integer</p> </td> 
     <td> <p>0</p> </td> 
     <td> <p><span>-2147483648~2147483647</span></p> </td> 
    </tr>
    <tr>
     <td> <p>4</p> </td> 
     <td> <p>long(长整数<span>)</span></p> </td> 
     <td> <p>64</p> </td> 
     <td> <p>Long</p> </td> 
     <td> <p>0L</p> </td> 
     <td> <p><span>-9223372036854775808~9223372036854775807</span></p> </td> 
    </tr>
    <tr>
     <td> <p>5</p> </td> 
     <td> <p>float(单精度<span>)</span></p> </td> 
     <td> <p>32</p> </td> 
     <td> <p>Float</p> </td> 
     <td> <p>0.0F</p> </td> 
     <td> <p><span>1.4E-45~3.4028235E38</span></p> </td> 
    </tr>
    <tr>
     <td> <p>6</p> </td> 
     <td> <p>double(双精度<span>)</span></p> </td> 
     <td> <p>64</p> </td> 
     <td> <p>Double</p> </td> 
     <td> <p>0.0D</p> </td> 
     <td> <p><span>4.9E-324~1.7976931348623157E308</span></p> </td> 
    </tr>
    <tr>
     <td> <p>7</p> </td> 
     <td> <p>char(字符<span>)</span></p> </td> 
     <td> <p>16</p> </td> 
     <td> <p>Character</p> </td> 
     <td> <p>空（'\u0000'）</p> </td> 
     <td> <p>0~65535</p> </td> 
    </tr>
    <tr>
     <td> <p>8</p> </td> 
     <td> <p>boolean</p> </td> 
     <td> <p>8</p> </td> 
     <td> <p>Boolean</p> </td> 
     <td> <p>flase</p> </td> 
     <td> <p>true或<span>false</span></p> </td> 
    </tr>
  </tbody></table>


所以自动装箱就是Java自动将原始类型值转换成对应的对象;同理为拆箱，例如：
```java
//        AutoBoxing
     	int a=100;
        Integer b=a;
        System.out.println("AutoBoxing:"+b);


//        AutoUnboxing
        Integer c=new Integer(100);
        int u=c;
        System.out.println("AutoUnboxing:"+u);
```
为什么可以把一个基本数据类型的变量直接赋值给一个对象，一个对象可以直接赋值给一个基本数据类型的变量？
打断点调试，在**Integer b=a;**的时候进入Force Step Into，执行了Integer的valueOf方法：

```java
static final int low = -128;
static final int high = 127;
/****......***/
public static Integer valueOf(int i) {
        if (i >= IntegerCache.low && i <= IntegerCache.high)
            return IntegerCache.cache[i + (-IntegerCache.low)];
        return new Integer(i);
    }
```
**int u=c;**,断点调试进入：
```java
 public Integer(int value) {
        this.value = value;
    }
```
## 一些问题
```java
 		Integer k1=100;
        Integer k2=100;

        Integer k3=200;
        Integer k4=200;

        System.out.println(k1==k2);
        System.out.println(k3==k4);
```
结果：
```java
true
false
```
继续调试：
![](https://img-blog.csdnimg.cn/20190725151626826.png)
答案很明显了，再看得更明显一点：
k1和k2的执行：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190725151824497.png)
k3和k4的执行：
![ 	](https://img-blog.csdnimg.cn/20190725151755401.png)
一目了然:在通过valueOf方法创建Integer对象的时候，如果数值在[-128,127]之间，便返回指向IntegerCache.cache中已经存在的对象的引用；否则创建一个新的Integer对象。
其中：

```java
    private static class IntegerCache {
        static final int low = -128;
        static final int high;
        static final Integer cache[];

        static {
            // high value may be configured by property
            int h = 127;
            String integerCacheHighPropValue =
                sun.misc.VM.getSavedProperty("java.lang.Integer.IntegerCache.high");
            if (integerCacheHighPropValue != null) {
                try {
                    int i = parseInt(integerCacheHighPropValue);
                    i = Math.max(i, 127);
                    // Maximum array size is Integer.MAX_VALUE
                    h = Math.min(i, Integer.MAX_VALUE - (-low) -1);
                } catch( NumberFormatException nfe) {
                    // If the property cannot be parsed into an int, ignore it.
                }
            }
            high = h;

            cache = new Integer[(high - low) + 1];
            int j = low;
            for(int k = 0; k < cache.length; k++)
                cache[k] = new Integer(j++);

            // range [-128, 127] must be interned (JLS7 5.1.7)
            assert IntegerCache.high >= 127;
        }

        private IntegerCache() {}
    }

```

如果换成其他基本类型：
# Double
```java
		Double d1=100.0;
        Double d2=100.0;
        Double d3=200.0;
        Double d4=200.0;

        System.out.println(d1==d2);
        System.out.println(d3==d4);

```
结果：

```java
false
false
```
调试找到Double内的valueOf：
```java
public static Double valueOf(double d) {
        return new Double(d);
    }
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190725153744538.png)


# Boolean:

```java
		Boolean b1=true;
        Boolean b2=true;
        Boolean b3=false;
        Boolean b4=false;
        System.out.println(b1==b2);
        System.out.println(b3==b4);
```
结果：

```java
true
true
```
调试：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190725154346969.png)
找到执行的valueOf，再往上面找看看TRUE和FALSE是什么：
```java

 /**
     * The {@code Boolean} object corresponding to the primitive
     * value {@code true}.
     */
    public static final Boolean TRUE = new Boolean(true);

    /**
     * The {@code Boolean} object corresponding to the primitive
     * value {@code false}.
     */
    public static final Boolean FALSE = new Boolean(false);
    
  public static Boolean valueOf(boolean b) {
        return (b ? TRUE : FALSE);
    }
    
```
同样的了然！！！




## “==”和“.equals”
```java
 		Integer k1=100;
        Integer k2=100;
        Integer k3=200;
        Integer k4=200;

        System.out.println(k1==k2);
        System.out.println(k3==k4);

        System.out.println(k1.equals(k2));
        System.out.println(k3.equals(k4));
```
结果:
```
true
false
true
true
```
why?
因为：

 - 基本数据类型（也称原始数据类型）
   byte,short,char,int,long,float,double,boolean。他们之间的比较，应用双等号（==）,比较的是他们的值。
 - 引用数据类型：当他们用（"\==“）进行比较的时候，比较的是他们在内存中的存放地址（确切的说，是堆内存地址）。


所以这里“\==”比较的是内存地址

而equals()方法是用来判断其他的对象是否和该对象相等。
```java
//equals()方法在object类中定义如下： 
public boolean equals(Object obj) {  
    return (this == obj);  
}  
```
但在一些诸如String,Integer,Date类中把Object中的这个方法覆盖了，作用被覆盖为比较内容是否相同。
```java
    // 比如在Integer类中如下：
     public boolean equals(Object obj) {
        if (obj instanceof Integer) {
            return value == ((Integer)obj).intValue();
        }
        return false;
    }
```
很明显，这是进行的内容比较，而已经不再是地址的比较。
了然！！！

# 如果是“==”两边的类型不一样
```java
	 	Integer k1=100;
        Integer k2=100;
        Integer k3=200;
        Integer k4=200;

        System.out.println(k1==k2);
        System.out.println(k3==k4);

        System.out.println(k1.equals(k2));
        System.out.println(k3.equals(k4));

        System.out.println(k3==(k1+k2));
        System.out.println(k3.equals(k1+k2));
          
        Integer a1=1;
        Integer a2=1;
        Long a3=2L;
        Integer a4=2;
        System.out.println(a3==(a1+a2));
        System.out.println(a3.equals(a1+a2));
        System.out.println(a3.equals(a4));

```
结果:
```
true
false

true
true

true
true

true
false
false
```
调试发现在k3==(k1+k2)，触发两次拆箱过程（调用intValue方法），因此是比较他们的值，和k3\==200是一样的，a3\==(a1+a2)同理，虽然属于不同类型，但是值是一样的,所以是 true;

k3.equals(k1+k2)触发了两次拆箱，一次装箱过程，所以和上面的k3.equals(k4)是一样的，同理：a3.equals(a1+a2)触发了两次拆箱，一次装箱过程，但是因为a3是Long，执行：
```java
    public boolean equals(Object obj) {
        if (obj instanceof Long) {
            return value == ((Long)obj).longValue();
        }
        return false;
    }
```
调试发现，直接跳过了 return value == ((Long)obj).longValue();因为（obj instanceof Long），所有直接返回false。