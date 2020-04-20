# String StringBuffer StringBuilder

## 区别

1、String是字符串常量，而StringBuffer和StringBuilder是字符串变量。由String创建的字符内容是不可改变的，而由StringBuffer和StringBuidler创建的字符内容是可以改变的。

2、StringBuffer是线程安全的，而StringBuilder是非线程安全的。StringBuilder是从JDK 5开始，为StringBuffer类补充的一个单线程的等价类。我们在使用时应优先考虑使用StringBuilder，因为它支持StringBuffer的所有操作，但是因为它不执行同步，不会有线程安全带来额外的系统消耗，所以速度更快。	

## String为什么不可变

- String类被final修饰，不可被继承。
- String的成员变量char数组value被final修饰，初始化后不可更改引用。
- String的成员变量value访问修饰符为private，不对外界提供修改value数组值的方法。

虽然String、StringBuffer和StringBuilder都是final类，它们生成的对象都是不可变的，而且它们内部也都是靠char数组实现的，但是不同之处在于，String类中定义的char数组是final的，而StringBuffer和StringBuilder都是继承自AbstractStringBuilder类，它们的内部实现都是靠这个父类完成的，而这个父类中定义的char数组只是一个普通是私有变量，可以用append追加。因为AbstractStringBuilder实现了Appendable接口。

## 为什么String要设计成不可变

1. 字符串常量池的需要

   字符串常量池(String pool, String intern pool, String保留池) 是Java堆内存中一个特殊的存储区域, 当创建一个String对象时,假如此字符串值已经存在于常量池中,则不会创建一个新的对象,而是引用已经存在的对象。

   如下面的代码所示,将会在堆内存中只创建一个实际String对象.

      String s1 = "abcd";

      String s2 = "abcd"; 

   

   ![img](https://user-gold-cdn.xitu.io/2018/1/16/160fcb5d8d34d0a3?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

   假若字符串对象允许改变,那么将会导致各种逻辑错误,比如改变一个对象会影响到另一个独立对象. 严格来说，这种常量池的思想,是一种优化手段.

2. 允许String对象缓存HashCode

   Java中String对象的哈希码被频繁地使用, 比如在hashMap 等容器中。

   字符串不变性保证了hash码的唯一性,因此可以放心地进行缓存.这也是一种性能优化手段,意味着不必每次都去计算新的哈希码. 在String类的定义中有如下代码:

   private int hash;//用来缓存HashCode

3. 安全性

   String被许多的Java类(库)用来当做参数,例如 网络连接地址URL,文件路径path,还有反射机制所需要的String参数等, 假若String不是固定不变的,将会引起各种安全隐患。

   boolean connect(string s){

   if (!isSecure(s)) {

   throw new SecurityException();

   }

   // 如果在其他地方可以修改String,那么此处就会引起各种预料不到的问题/错误causeProblem(s);

   }

   总体来说, String不可变的原因包括 设计考虑,效率优化问题,以及安全性这三大方面. 事实上,这也是Java面试中的许多 "为什么" 的答案.

## String类不可变性的好处

1.只有当字符串是不可变的，字符串池才有可能实现。字符串池的实现可以在运行时节约很多heap空间，因为不同的字符串变量都指向池中的同一个字符串。但如果字符串是可变的，那么String interning将不能实现(译者注：String interning是指对不同的字符串仅仅只保存一个，即不会保存多个相同的字符串。)，因为这样的话，如果变量改变了它的值，那么其它指向这个值的变量的值也会一起改变。
2.如果字符串是可变的，那么会引起很严重的安全问题。譬如，数据库的用户名、密码都是以字符串的形式传入来获得数据库的连接，或者在socket编程中，主机名和端口都是以字符串的形式传入。因为字符串是不可变的，所以它的值是不可改变的，否则黑客们可以钻到空子，改变字符串指向的对象的值，造成安全漏洞。
3.因为字符串是不可变的，所以是多线程安全的，同一个字符串实例可以被多个线程共享。这样便不用因为线程安全问题而使用同步。字符串自己便是线程安全的。
4.类加载器要用到字符串，不可变性提供了安全性，以便正确的类被加载。譬如你想加载java.sql.Connection类，而这个值被改成了myhacked.Connection，那么会对你的数据库造成不可知的破坏。
5.因为字符串是不可变的，所以在它创建的时候hashcode就被缓存了，不需要重新计算。这就使得字符串很适合作为Map中的键，字符串的处理速度要快过其它的键对象。这就是HashMap中的键往往都使用字符串。