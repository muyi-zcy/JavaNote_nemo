# Java final关键字

> 在Java中，final可以修饰修饰类、方法和变量（包括成员变量和局部变量）

## 1、final数据

> 对于基本类型，final 使得数值恒定不变；而对于对象引用，final使引用恒定不变，但是对象自身是可以被修改的。

### 1.1 实例



```java
//程序修改自《Java编程思想》
class Value {
    int i; // Package access
    public Value(int i) { this.i = i; }
}
public class FinalData {
    private static Random rand = new Random(47);
    private String id;

    public FinalData(String id) {
        this.id = id;
    }


    //    非静态和静态
    private final int valueOne = 9;
    private static final int VALUE_TWO = 99;

    //    public 静态
    public static final int VALUE_THREE = 39;


    // 使用随机生成数来初始化，说明final修饰的常量不一定要在编译器知道其值，可以在运行时确定
    private final int i4 = rand.nextInt(20);
    static final int INT_5 = rand.nextInt(20);

    //
    private Value v1 = new Value(11);
    private final Value v2 = new Value(22);
    private static final Value VAL_3 = new Value(33);

    // 被final修饰的数组：
    private final int[] a = { 1, 2, 3, 4, 5, 6 };

    public String toString() {
        return id + ": " + "i4 = " + i4 + ", INT_5 = " + INT_5;
    }
    public static void main(String[] args) {

        FinalData fd1 = new FinalData("fd1");    //fd1指向一个对象
//        fd1.valueOne++;                           //  valueOne被final修饰,不可以再次被修改
//        fd1.v2=new Value(33);                     //v2被final修饰
        fd1.v2.i++;                                 // v2被修饰但是Value类的i没有被final修饰，所以可以修改
        fd1.v1 = new Value(9);                    // v1没有被修饰


        for(int i = 0; i < fd1.a.length; i++)
            fd1.a[i]++;             //数组也是一种引用，可以修改数组的值，但是不可以修改数组的引用指向
//        fd1.a = new int[3];

        //! fd1.v2 = new Value(0);
        //! fd1.VAL_3 = new Value(1);

        System.out.println(fd1);
        System.out.println("Creating new FinalData");

        // final修饰对象不可以再次指向另一个对象
        final FinalData fd2 = new FinalData("fd2");
//        fd2=new FinalData("fd3");
        System.out.println(fd1);
        System.out.println(fd2);
        //但是可以修改对象本身,但是也仅限于非final域
        fd2.id="new id";

    }
}
//输出：
//        fd1: i4 = 15, INT_5 = 18
//        Creating new FinalData
//        fd1: i4 = 15, INT_5 = 18
//        fd2: i4 = 13, INT_5 = 18

```



### 1.2、静态和非静态final数值

JAVA中常量就是变量同时标记为static和final

### 1.3、空白final 

Java允许生成“空白final”，即被final修饰但是没有进行初始化的域，但是在使用前必须初始化：

必须保证被final修饰的域在使用前被初始化，初始化进行在域的定义处和每个构造器内。

```java
class Poppet {
  private int i;
  Poppet(int ii) { i = ii; }
}

public class BlankFinal {
  private final int i = 0; // Initialized final
  private final int j; // Blank final
  private final Poppet p; // Blank final reference
    
  //构造器内必须对空白final初始化
  public BlankFinal() {
    j = 1; // Initialize blank final
    p = new Poppet(1); // Initialize blank final reference
  }
    
  public BlankFinal(int x) {
    j = x; // Initialize blank final
    p = new Poppet(x); // Initialize blank final reference
  }
    
  public static void main(String[] args) {
    new BlankFinal();
    new BlankFinal(47);
  }
}
```



### 1.4、类的final变量和普通变量有什么区别？

```java
 public static void main(String[] args)  { 
        String a = "hello2";
        final String b = "hello";
        String d = "hello";
        String c = b + 2;
        String e = d + 2;
        System.out.println((a == c));
        System.out.println((a == e));
        
        System.out.println((a.equals(c)));
        System.out.println((a.equals(e)));
        
        System.out.println((c.equals(a)));
        System.out.println((e.equals(a)));
    } 
结果：
    true
    false
    true
    true
    true
    true
```



```java
  public static void main(String[] args)  {
        String a = "hello2";
        final String b = getHello(); //b在运行时初始化
        String c = b + 2;
        final String d="hello2";
        System.out.println((a == c));
        System.out.println(a.equals(c));
        System.out.println(a==d);
    }

    public static String getHello() {
        return "hello";
    }
结果：
    false
    true
    true
```



当final修饰域时基本数据类型以及String类型时,如果在编译期间能知道他的确切值，则编译器会把它当作编译器常量使用。

### 1.5、final参数

Java允许在参数列表内以声明的形式将参数指明为final，即无法再在方法内对final修饰的对象进行指向修改，只能进行读取和修改指向的对象的内容。

## 2、final方法

- 锁定方法，不可再次被修改，防止继承类修改其含义

- 提高效率（早期版本）、在早期的Java实现版本中，会将final方法转为内嵌调用。但是如果方法过于庞大，可能看不到内嵌调用带来的任何性能提升。在最近的Java版本中，不需要使用final方法进行这些优化了。

  （选自《Java编程思想》）

### 2.1、private 和final

因为其他类无法取用类的private方法，所以也就无法覆盖他，所以类的private方法会隐式地被指定为final方法。

```java
class father{
    int i=0;
    private father(){}
    public father(int i){}
}

public class test extends father{
    public test(int i) {
        super(i);
    }
//    private father(){
//        
//    }         //error
    
}
```



## 3、final类

当某一个类被整体定义为final时，就表明该类不可以再次被继承。所以该类内所有方法被隐式的定义为被final修饰，因为无法覆盖他们。