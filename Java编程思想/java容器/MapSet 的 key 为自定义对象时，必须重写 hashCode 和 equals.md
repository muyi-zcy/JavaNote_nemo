# Map/Set 的 key 为自定义对象时，必须重写 hashCode 和 equals  

HashMap:链表+数组方式实现。

HashMap的存储： 先从通过key的hascode计算出位置，然后存入到链表。

HashMap查找key: hashMap会先根据key值的hashcode经过运算定位其所在数组的位置，再根据key的equals方法匹配相同key值获取对应相应的对象.

也即是说，一个Key的查找是由hashcode和equals方法，共同来决定的。如果只实现equals, 而不实现hashcode。那么必然存着问题。

equals 和 hascode是Java对象的两个方法。默认实现是：equals,比较两个对象的内存地址。hashcode,通过对象的内存地址计算出的散列值。 如果两个对象相等，hashcode一定相等。

当对某类equals重写之后，两个对象实例的内存地址不一定相同，而hashcode也不一定相同。 根据hashcode的规则，两个对象相等其hashcode一定相等，所以矛盾就产生了，因此重写equals一定要重写hashcode。

（set同理）