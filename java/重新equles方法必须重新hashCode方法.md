JAVA中重写equals()方法的同时要重写hashcode()方法

object对象中的 public boolean equals(Object obj)，对于任何非空引用值 x 和 y，当且仅当 x 和 y 引用同一个对象时，此方法才返回 true；
注意：当此方法被重写时，通常有必要重写 hashCode 方法，以维护 hashCode 方法的**常规协定**，该协定声明相等对象必须具有相等的哈希码。如下：

* (1)当obj1.equals(obj2)为true时，obj1.hashCode() == obj2.hashCode()必须为true 
* (2)当obj1.hashCode() == obj2.hashCode()为false时，obj1.equals(obj2)必须为false

如果不重写equals，那么比较的将是对象的引用是否指向同一块内存地址，重写之后目的是为了比较两个对象的value值是否相等。特别指出利用equals比较八大包装对象
（如int，float等）和String类（因为该类已重写了equals和hashcode方法）对象时，默认比较的是值，在比较其它自定义对象时都是比较的引用地址
hashcode是用于散列数据的快速存取，如利用HashSet/HashMap/Hashtable类来存储数据时，都是根据存储对象的hashcode值来进行判断是否相同的。
这样如果我们对一个对象重写了euqals，意思是只要对象的成员变量值都相等那么euqals就等于true，但不重写hashcode，那么我们再new一个新的对象，
当原对象.equals（新对象）等于true时，两者的hashcode却是不一样的，由此将产生了理解的不一致，如在存储散列集合时（如Set类），将会存储了两个值一样的对象，
导致混淆，因此，就也需要重写hashcode()。