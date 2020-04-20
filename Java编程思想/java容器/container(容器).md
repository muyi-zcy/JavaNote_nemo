# 容器

> 如果一个程序只包含固定数量且其生命周期都是已知的对象，那么这时一个非常简单的程序。（Java编程思想）

很多时候，我们并不知道我们需要建立多大的一个容器来保存我们需要保存的但是数量未知的对象，如果创建一个固定数量的数组，就存在溢出和浪费的问题，Java作为一个成熟的语言，给出了一套相当完整的容器类作为解决办法。

## 1、泛型和类型安全的容器

既然是容器，是不是就是放东西的地方，对放的东西会不会带“有色眼镜”，答案是“因人而异”。那容器里可不可以什么都放？可以，很复杂。可不可以只放一种东西，可以，加限制。

下面看一个程序：

```java
class A{
    int a=0;
}
class  B{

}
public class Main {
    public static void main(String[] args) {        
        A a=new A();
        ArrayList arrayList=new ArrayList();
        arrayList.add(1);
        arrayList.add("1");
        arrayList.add(new Long(1));
        arrayList.add(a);
        arrayList.add(new B());
        for(Object o:arrayList){
            System.out.println(o);
        }
        ArrayList<A> as=new ArrayList<A>();
//        as.add("1"); //error
//        as.add(1);    //error
          as.add(a);
         for(A o:as){
            System.out.println(o);
        }
    }
}

输出：
1
1
1
Container.A@74a14482
Container.B@1540e19d
Container.A@74a14482
（注意看这里的第四个和第六个输出）
```

这里我们有两个ArrayList，差异很明显，一个有“<A>”,一个没有。

除此之外还有什么差异呢？没有加“<A>”的ArrayList可以add（添加）很多类型的对象，这些对象有一个共同的特点，继承自Object类。有“<A>”的ArrayList，只能add类型为A的对象，否则会异常。

为什么呢？知道泛型的都知道"<'参数类型'>"是泛型的标志，当然泛型也是一个很复杂的东西，这里的作用是限制了容器的保存类型，没有加即表示不加限制，也可以说缺省的指定该容器的保存类型为Object。

总结就是：**一个容器在声明的时候加上泛型，就是指定容器实例可以保存的类型，就可以在编译器防止将错误的对象保存在容器中，即类型安全。**

还有一个问题，容器到底保存的是什么呢？

不着急，下面我们先看一下两种情况下如何取出保存的对象：

```java
class A{
    int a=0;
}
class B{
    int a=1;
}
public class Main {
    public static void main(String[] args) {
        A a=new A();
        ArrayList<Object> arrayList=new ArrayList<Object>();
        arrayList.add(new A());
        for(Object o:arrayList){
            System.out.println(((A)o).a);
        }

        ArrayList<A> as=new ArrayList<A>();
        as.add(a);
        for(A o:as){
            System.out.println(o.a);
        }
    }
}
输出：
0
0
```

差异也很明显，((A)o).a和o.a的区别，上面的大家都知道这是进行了类型的转换，下面加了泛型的不需要类型转换就可以取出对象。

那如果把

```java
System.out.println(((A)o).a);
```

换成

```java
System.out.println(((B)o).a);
```

会怎么样？反正是类型转换，我换成B不也是可以的吗？运行一下：

```java
0
Exception in thread "main" java.lang.ClassCastException: Container.A cannot be cast to Container.B
```

重点是**Container.A cannot be cast to Container.B**

为什么会出现这种情况？

因为容器是保存了对象的引用，即对象的地址。当我们取出的时候进行类型转换，引用的对象和要转换的对象什么都对不上，当然要报错。

所以在使用了泛型的容器就指定了该容器类存放的都是指定类型对象的引用，既然都一样当然就不需要进行类型转换嘛。

这个时候我们回想上一块代码输出我加在括弧内的内容，两个输出是一样的，这里就明白了，存放在两个ArrayList内的都是引用，引用的是同一个对象，当然取出的是同样的东西。

## 2、Java容器类库

Java容器的基本作用是”保存对象“，Java容器类库把容器划分为两种类型：

- Collection：一个独立元素的序列，这些元素都服从一条或多条规则。
- Map：一组成对的”键值对“的对象，运行使用键来查找值。

这张图是《Java编程思想》第17章Java容器类库的简化图：

![](https://raw.githubusercontent.com/pomole/nemo_pic/master/20200120022043.png)

其中虚线框abstract类，众所周知，抽象类是不可以实例化对象的，所以需要使用具体实现子类去实现

## 3、Collection元素添加

### Collections

```java
public class Collections
extends Object
```

  此类仅由静态方法组合或返回集合。  它包含对集合进行操作的多态算法，“包装器”，返回由指定集合支持的新集合，以及其他一些可能的和最终的。 

```java
public static <T> boolean addAll(Collection<? super T> c, T... elements) {
     boolean result = false;
     for (T element : elements)
     	result |= c.add(element);
     return result;
}

//实例
Collection<Integer> collection=new ArrayList<Integer>(Arrays.asList(1,2,3,4,5,6));
Integer[] integer=new Integer[]{1,2,3,4,5,6};
System.out.println(Collections.addAll(collection));
System.out.println(Collections.addAll(collection,integer));
//输出
false
true
```

### Arrays

该类包含用于操作数组的各种方法（如排序和搜索）。 该类还包含一个静态工厂，可以将数组视为列表。

```java
Integer[] integer=new Integer[]{1,2,3,4,5,6};
Collection<Integer> collection=new ArrayList<Integer>(Arrays.asList(1,2,3,4,5,6));
collection.addAll(Arrays.asList(integer));
for(Integer i:collection){
    System.out.println(i);
}
//
1
2
3
4
5
6
1
2
3
4
5
6
```

但是Arrays.asList()的输出的List,其底层结果是数组,因此不能跳转尺寸,如果使用add(),delete()方法产生java.lang.UnsupportedOperationException异常.

```java
Collection<Integer> collection1=Arrays.asList(1,2,3);
collection1.add(2);
```

### Collection

#### 构造器

Collection的构造器可以接受另一个Collection,用来将自身初始化,

#### addAll

```java
boolean addAll(Collection<? extends E> c)将指定集合中的所有元素添加到此集合（可选操作）。 如果在操作进行中修改了指定的集合，则此操作的行为是未定义的。 （这意味着如果指定的集合是此集合，此调用的行为是未定义的，并且此集合是非空的。） 
参数 
c - 包含要添加到此集合的元素的集合 
结果 
true如果此收集因呼叫而更改 
```

Collection的构造器可以接受另一个Collection,用来做自身初始化,因此可以使用Arrays.List()来作为构造器的输入,但是Collection.addAll(),方法运行起来快很多;而且构造一个没有元素的Collection,然后调用Collections.addAll()的方法很方便,因此是首选方案.

Collection.addAll()成员方法只能接受另一个Collection对象作为参数,因此不如Arrays.List()和Collections.addAll()灵活,这两个方法使用的都是可变参数列表.

## 4、Map添加元素

Map的初始化除了使用另一个Map,Java标准类库没有提供任何自动化初始方法.

### 双括号初始化 （匿名内部类）
```java
HashMap<String, String > h = new HashMap<String, String>(){{
    put("a","b");
}};
```

慎用， 非静态内部类/ 匿名内部类包含了外围实例的引用， 如果拥有比外部类更长的生命周期，有内存泄露隐患

### Guava

```java
Map<String, Integer> left = ImmutableMap.of("a", 1, "b", 2, "c", 3);
//或者
Map<String, String> test = ImmutableMap.<String, String>builder()
    .put("k1", "v1")
    .put("k2", "v2")
    ...
    .build();
```

