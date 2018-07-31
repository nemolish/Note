HashMap和Hashtable的区别
#### 主要区别
两者最主要的区别在于Hashtable是线程安全，而HashMap则非线程安全。Hashtable的实现方法里面都添加了synchronized关键字来确保线程同步，因此相对而言HashMap性能会高一些，我们平时使用时若无特殊需求建议使用HashMap，在多线程环境下若使用HashMap需要使用Collections.synchronizedMap()方法来获取一个线程安全的集合（Collections.synchronizedMap()实现原理是Collections定义了一个SynchronizedMap的内部类，这个类实现了Map接口，在调用方法时使用synchronized来保证线程同步,当然了实际上操作的还是我们传入的HashMap实例，简单的说就是Collections.synchronizedMap()方法帮我们在操作HashMap时自动添加了synchronized来实现线程同步，类似的其它Collections.synchronizedXX方法也是类似原理。
HashMap可以使用null作为key，不过建议还是尽量避免这样使用。HashMap以null作为key时，总是存储在table数组的第一个节点上。而Hashtable则不允许null作为key。
HashMap继承了AbstractMap，HashTable继承Dictionary抽象类，两者均实现Map接口。
HashMap的初始容量为16，Hashtable初始容量为11，两者的填充因子默认都是0.75。
HashMap扩容时是当前容量翻倍即:capacity*2，Hashtable扩容时是容量翻倍+1即:capacity*2+1。
HashMap和Hashtable的底层实现都是数组+链表结构实现。

#### 两者计算hash的方法不同
Hashtable计算hash是直接使用key的hashcode对table数组的长度直接进行取模：

```java
int hash = key.hashCode();
int index = (hash & 0x7FFFFFFF) % tab.length;
```

HashMap计算hash对key的hashcode进行了二次hash，以获得更好的散列值，然后对table数组长度取摸：

```java
static int hash(int h) {
     h ^= (h >>> 20) ^ (h >>> 12);
     return h ^ (h >>> 7) ^ (h >>> 4);
 }

static int indexFor(int h, int length) {
     return h & (length-1);
 }
```

#### 判断是否含有某个键 
在HashMap 中，null 可以作为键，这样的键只有一个；可以有一个或多个键所对 
应的值为null。当get()方法返回null 值时，既可以表示HashMap 中没有该键，也可 
以表示该键所对应的值为null。因此，在HashMap 中不能用get()方法来判断HashM 
ap 中是否存在某个键，而应该用containsKey()方法来判断。Hashtable 的键值都不能 
为null，所以可以用get()方法来判断是否含有某个键。

#### HashMap原理和rehash解释

采用hash结构的容器，除了采用hash算法来决定集合中元素的位置
并通过hash算法来控制集合的大小


#### HashMap的内部实现机制
HashMap的内部实现机制是对数据结构中哈希表（Hash Table）的实现,Hash表又叫散列表。
Hash表是根据关键码Key来访问其对应的值Value的数据结构，他是通过一个映射函数把关键码映射到表中一个位置
来访问该位置的值，从而加快查找的速度。这个映射函数叫做Hash函数，存放的数组叫做Hash表。


#### Hash的实现：
主要是 哈希算法 和 冲突的解决
计算出map中key的哈希值，在哈希表中找到<key,value>的位置，根据key找value
当多个哈希值冲突的时候，在哈希值的同一个位置上使用链表结构来保存多个对象，而hashMap访问map的时候
也是根据key的hashCode的值来快速定位，入伙HashMap中俩个以上的元素具有相同的HashCode值，将会导致性能下降


#### rehash
在创建hashMAP的时候可以设置来个参数，一般默认
初始化容量：创建hash表时桶的数量
负载因子：负载因子=map的size/初始化容量
当hash表中的负载因子达到负载极限的时候，hash表会自动成倍的增加容量（桶的数量），并将原有的对象
重新的分配并加入新的桶内，这称为rehash。这个过程是十分耗性能的，一般不要
一般建议设置比较大的初始化容量，防止rehash，但是也不能设置过大，初始化容量过大 浪费空间