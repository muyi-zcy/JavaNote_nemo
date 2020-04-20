List

元素是有序的(怎么存的就怎么取出来，顺序不会乱)，元素可以重复,因为该集合体系有索引.

- ArrayList底层是一个数组，输出时需要foreach遍历，查询快，增删慢，线层安全,效率高
- Vector底层是一个实现自动增长的对象数组（数组长度是可变的百分之百延长），元素会自动添加到数组中，查询快，增删慢，线层安全，效率低
- LinkedList底层是一个链表，查询慢，增删快，线层不安全，效率高



1. 所有的List中只能容纳单个不同类型的对象组成的表，而不是Key－Value键值对。例如：[ tom,1,c ]
2. 所有的List中可以有相同的元素，例如Vector中可以有 [ tom,koo,too,koo ]
3. 所有的List中可以有null元素，例如[ tom,null,1 ]
4. 基于Array的List（Vector，ArrayList）适合查询，而LinkedList 适合添加，删除操作

## 遍历

```java
    //方法1 集合类的通用遍历方式, 从很早的版本就有, 用迭代器迭代
    Iterator it1 = list.iterator();
    while(it1.hasNext()){
        System.out.println(it1.next());
    }
```

```java
    //方法2 集合类的通用遍历方式, 从很早的版本就有, 用迭代器迭代
    for(Iterator it2 = list.iterator();it2.hasNext();){
        System.out.println(it2.next());
    }
```

```java
    //方法3 增强型for循环遍历
    for(String value:list){
        System.out.println(value);
    }
```

```java
    //方法4 一般型for循环遍历
    for(int i = 0;i < list.size(); i ++){
        System.out.println(list.get(i));
    }
```

## 排序

### 1. 利用Collections类的 java.util.Collections.sort(java.util.List, java.util.Comparator) 方法，自定义比较器对象对指定对象进行排序

```java
class user{
    public user(String name, Integer no, Integer cj) {
        this.name = name;
        this.no = no;
        this.cj = cj;
    }

    public String name;
    public Integer no;
    public Integer cj;
}

public class Main {
    public static void main(String[] args) {
        // 注意，要想改变默认的排列顺序，不能使用基本类型（int,double, char）
        // 而要使用它们对应的类
        user[] users=new user[]{new user("a",1,1),new user("b",3,3),new user("c",2,2)};

        // 定义一个自定义类MyComparator的对象
        Arrays.sort(users, new Comparator<user>() {
            @Override
            public int compare(user o1, user o2) {
                // 如果o1小于o2，我们就返回正值，如果n1大于n2我们就返回负值，
                if (o1.cj < o2.cj) {
                    return 1;
                } else if (o1.cj > o2.cj) {
                    return -1;
                } else {
                    return 0;
                }
            }
        });
        for (int i = 0; i < users.length; i++) {
            System.out.print(users[i].name + " ");
        }
    }
}
```

### 2. 通过实现Comparable接口来实现list的排序

假如现在我们有一个User类的list集合，要让其按照一个Order属性进行排序，我们可以让User类实现Comparable接口，重写其CompareTo方法即可,可以让程序按照我们想要的排列方式进行排序，如：这里我让User按照order属性升序排序,具体实现如下：

```java
class User implements Comparable<User>{
    private String name;
    private Integer order;
    public String getName() {
        return name;
    }
    public void setName(String name) {
        this.name = name;
    }
    public Integer getOrder() {
        return order;
    }
    public void setOrder(Integer order) {
        this.order = order;
    }
    public int compareTo(User arg0) {
        return this.getOrder().compareTo(arg0.getOrder());
    }
}
public class Main {

    public static void main(String[] args) {
        User user1 = new User();
        user1.setName("a");
        user1.setOrder(1);
        User user2 = new User();
        user2.setName("b");
        user2.setOrder(2);
        List<User> list = new ArrayList<User>();
        //此处add user2再add user1
        list.add(user2);
        list.add(user1);
        Collections.sort(list);
        for(User u : list){
            System.out.println(u.getName());
        }
    }
}
//
a
b
```

## 去重

方式一，利用HashSet不能添加重复数据的特性 由于HashSet不能保证添加顺序，所以只能作为判断条件：

```java
private static void removeDuplicate(List<String> list) {
    HashSet<String> set = new HashSet<String>(list.size());
    List<String> result = new ArrayList<String>(list.size());
    for (String str : list) {
        if (set.add(str)) {
            result.add(str);
        }
    }
    list.clear();
    list.addAll(result);
}
```

方式二，利用LinkedHashSet不能添加重复数据并能保证添加顺序的特性 ：

```java
private static void removeDuplicate(List<String> list) {
    LinkedHashSet<String> set = new LinkedHashSet<String>(list.size());
    set.addAll(list);
    list.clear();
    list.addAll(set);
}
```

方式三，利用List的contains方法循环遍历：

```java
private static void removeDuplicate(List<String> list) {
    List<String> result = new ArrayList<String>(list.size());
    for (String str : list) {
        if (!result.contains(str)) {
            result.add(str);
        }
    }
    list.clear();
    list.addAll(result);
}
```

方式二是最佳选择,效率最高



##  list.remove( )方法

```java
public static void main(String[] args) {
    List<Integer> list=new ArrayList<>();
    for(int i=0;i<10;i++){
        list.add(i);
    }
    for(Integer integer:list){
        System.out.print(integer+" ");
    }
    System.out.println();
    for(int i=0;i<4;i++){
        System.out.print(i+" ");
        list.remove(new Integer(i));
    }
    System.out.println();
    for(Integer integer:list){
        System.out.print(integer+" ");
    }
}
//
0 1 2 3 4 5 6 7 8 9 
0 1 2 3 
4 5 6 7 8 9 
    
    
 public static void main(String[] args) {
        List<Integer> list=new ArrayList<>();
        for(int i=0;i<10;i++){
            list.add(i);
        }
        for(Integer integer:list){
            System.out.print(integer+" ");
        }
        System.out.println();
        for(int i=0;i<4;i++){
            System.out.print(i+" ");
            list.remove(i);
        }
        System.out.println();
        for(Integer integer:list){
            System.out.print(integer+" ");
        }
    }
//
0 1 2 3 4 5 6 7 8 9 
0 1 2 3 
1 3 5 7 8 9
```

因为List的remove出现了重载，

```java
public boolean remove(Object o)

public E remove(int index)
```

## ArrayList

## Vector

## LinkedList

由于底层结构是链表的原因，LinkedList相比较ArrayList在插入和删除方面更加优秀，但是随机访问效率低。

LinkedList还拥有作为栈、队列、双端队列的方法。

