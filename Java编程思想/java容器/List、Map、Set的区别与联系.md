# List、Map、Set的区别与联系

**一、数组和集合的区别：**

 1.数组的大小是固定的，并且同一个数组只能是相同的数据类型

 2.集合的大小是不固定的，在不知道会有多少数据的情况下可使用集合。

**二、集合的三种类型：list(列表)、set(集)、map(映射)**

**List接口和Set接口属于Collection接口，Map接口和Collection接口并列存在（同级）。
**

 List(元素可重复性，有序性)

```java
 public static void main(String[] args) {
     List<Integer> arraylist=new ArrayList<>();
     arraylist.add(3);
     arraylist.add(22);
     arraylist.add(31);
     arraylist.add(345);
     arraylist.add(63);
     arraylist.add(3);
     //ArrayList底层是一个数组，输出时需要foreach遍历，查询快，增删慢，线层安全,效率高
     System.out.print("arraylist:");
     for (Integer test : arraylist) {
        System.out.print(test+" ");
    }
     
     System.out.println();   //换行
     
     //Vector底层是一个实现自动增长的对象数组，元素会自动添加到数组中，查询快，增删慢，线层安全，效率低
     Vector<Integer> vector=new Vector<>();
     vector.add(3);
     vector.add(5);
     vector.add(25);
     vector.add(51);
     vector.add(5);
     System.out.println("vector:"+vector);
     
     //LinkedList底层是一个链表，查询慢，增删快，线层不安全，效率高
     LinkedList<Integer> linkedList=new LinkedList<>();
     linkedList.add(30);
     linkedList.add(30);
     linkedList.add(34);
     linkedList.add(55);
     System.out.println("linkedlist"+linkedList);
```

运行结果：

![img](https://img2018.cnblogs.com/blog/1631256/201903/1631256-20190319111141259-1353607657.png)

 Set（具有唯一性，无序性）: 

```java
public static void main(String[] args) {
          HashSet<String> hashSet = new HashSet<>();
          LinkedHashSet<String> linkedHashSet = new LinkedHashSet<>();
          TreeSet<String> treeSet = new TreeSet<>();

          for (String data : Arrays.asList("afaE", "afaE", "asfweD", "hfasfae", "aefaeA")) {
              hashSet.add(data);
              linkedHashSet.add(data);
              treeSet.add(data);
          }

          //HashSet:无序，随机输出。底层数据结构是哈希表，依赖hashCode()和equals()两个方法来保证唯一性。
          System.out.println("Ordering in HashSet :" + hashSet);

          //LinkedHashSet:FIFO插入有序，先进先出。底层结构是链表和哈希表，由链表保证元素有序，哈希表保证元素唯一。
          System.out.println("Order of element in LinkedHashSet :" + linkedHashSet);

          //TreeSet：有序，唯一。底层结构是红黑树，排序采用自然排序和比较容器排序，根据比较的返回值是否为0来判断元素唯一。
          System.out.println("Order of objects in TreeSet :" + treeSet); 
    }
```

运行结果：

![img](https://img2018.cnblogs.com/blog/1631256/201903/1631256-20190319101712970-203905770.png)

  Map（采用键值对<key,value>存储元素，key键唯一）：

hashmap:底层结构是数组+链表，无序，线程不安全，效率高，允许有null（key和value都允许），父类是AbstractMap

treemap:底层结构是红黑树，有序，将数据按照key排序，默认是升序排序。

hashtable：底层结构是哈希表，无序，线程安全，效率低，不允许有null值，父类是Dictionary

LinkedHashMap：哈希表和链表实现的Map接口，具有可预测的迭代次序,允许有null（key和value都允许）。

```
public static void main(String[] args) {

        // HashMap采用键值对存储数据，采用put()方法存放数据
        Map<Integer, String> map = new HashMap<Integer, String>();
        map.put(1, "中国");
        map.put(2, "小日本");
        map.put(3, "美国佬");

        // foreach遍历方法,普遍使用，通过对key唯一的特性，进行key遍历
        for (Integer key : map.keySet()) {
            System.out.println(key + "-->" + map.get(key));
        }

        System.out.println("------------------------------------");

        // 因为key唯一，key重复时会自动覆盖value之前存储的数据
        map.put(3, "英国佬");
        for (Integer key1 : map.keySet()) {
            System.out.println(key1 + "-->" + map.get(key1));
        }

        System.out.println("------------------------------------");

        // Map.Entry遍历key和value，同样是foreach方法，推荐容量大的时候使用
        for (Map.Entry<Integer, String> entry : map.entrySet()) {
            System.out.println(entry.getKey() + "-->" + entry.getValue());

        }
    }
```



运行结果：

![img](https://img2018.cnblogs.com/blog/1631256/201903/1631256-20190319141804452-960866463.png)