# Comparator

Comparator 是 java 的用于排序的接口，默认得实现一个 compare 函数，这个函数返回的参数有 -1 ， 1 ，0 正常来说按照返回值为 -1 进行排序。

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