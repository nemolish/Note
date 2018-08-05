### Java泛型及实现
[TOC]

#### 泛型定义

泛型编程是一种通过参数化的方式将数据处理与数据类型解耦的技术，通过对数据类型施加约束（比如Java中的有界类型）来保证数据处理的正确性，又称参数类型或参数多态性。 
泛型最著名的应用就是容器，C++的STL、Java的Collection Framework。
***范型程序设计***意味着编写的代码可以被不同类型的对象使用，典型的例子就是ArrayList可以聚集不同的类对象。

#### 范型出现之前
在Java增加范型之前，范型程序设计使用继承的方式实现，ArrayList内只维护着一个Object数组:
```java
public class ArrayList {
    private Object elementData;
    ...
    public Object get(int i){...};
    public void set(Object o){...};
}
```

这样的实现有两个问题：
* 获取指时必须做强制类型转换
```java
ArrayList al = new ArrayList();
al.set(new String("test"));
String t = (String)al.get(0); //强制类型转换
```

* 没有错误检查，可以向数组列表添加任何类型的对象
```java
ArrayList al = new ArrayList();
al.set(new String("test"));
al.set(new Integer(1));       //显然我们不希望如此，但是没有类型检查导致容易犯错
```

####  泛型的实现方式

不同的语言在实现泛型时采用的方式不同，C++的模板会在编译时根据参数类型的不同生成不同的代码，而Java的泛型是一种违反型，编译为字节码时参数类型会在代码中被擦除，单独记录在Class文件的attributes域内，而在使用泛型处做类型检查与类型转换。 
假设参数类型的占位符为T，擦除规则如下：

* <T>擦除后变为Obecjt
* <? extends A>擦除后变为A
* <？ super A>擦除后变为Object
上述擦除规则叫做保留上界。

#### 擦除带来的问题

对于<？ extends A>和<？ super A>的擦除，因为保留上界，所以擦除后并没有破坏里氏替换原则。 
设有类Super与Sub:

```java
class Super{}
class Sub extends Super{}
```

对于有界类型的协变性与逆变性：

```java
List<? extends Super> list = new ArrayList<Sub>();  //协变
List<? super Sub> list2 = new ArrayList<Super>();   //逆变
```

类型擦除后，等价于：

```java
List<Super> list = new ArrayList<Sub>();
List<Object> list2 = new ArrayList<Super>(); 
```

可以看出，参数类型的擦除并没有破坏里氏替换原则，这也是保留上界的原因。

感谢 Java中的逆变与协变这篇博文的作者，让我很好理解了协变与逆变、PECS规则。有机会我会再写一篇自己的总结。
对于<T>的擦除，根据T在类中出现位置的不同，分以下5种情况讨论：

* T是成员变量的类型
* T是泛型变量（无论成员变量还是局部变量）的类型参数，常见如Class<T>，List<T>。
* T是方法抛出的Exception（要求<T extends Exception>）
* T是方法的返回值
* T是方法的参数
情况1的擦除不会有任何影响，因为编译器会在泛型被调用的地方加上类型转换； 
情况2的擦除也不会有问题，这个问题有点像“要实现不可变类，就要保证成员变量中引用指向的类型也是不可变的”，是个递归定义； 
情况3的擦除，我认为讨论这种情况意义不大。想在方法中抛出T，那得先实例化T，而如何通过泛型进行实例化，原谅我不知道怎么能做到（有人说反射能做到，怪我反射学的不好……）；假设现在得到并可以抛出泛型T的实例，来看一下会出现什么情况。 
设有类Super与Sub：
```java
class Super<T extends SQLException>{
    public void test() throws T{}    //别怀疑，这段代码是可以编译通过的......
}
```