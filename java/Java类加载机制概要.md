### Java类加载机制概要


#### Java类加载过程
加载 验证 准备 解析 初始化
以上过程由“类加载器”实现


#### 类加载器层次关系
实用组合关系(Composition)实现的 双亲委派模型(Parents Delegation Module)
注：类加载器与其父类加载器不是继承关系(Inheritance)

```
        启动类加载器
    Bootstrap ClassLoader
            ↑
        扩展类加载器
    Extension ClassLoader
            ↑
       应用程序类加载器
   Application ClassLoader
     ↗                ↖
自定义类加载器1    自定义类加载器2 
User ClassLoader1 User ClassLoader2
```

* 启动类加载器(Bootstrap ClassLoader)：负责加载 JAVA_HOME\lib 目录中的，或通过-Xbootclasspath参数指定路径中的，且被虚拟机认可（按文件名识别，如rt.jar）的类。
* 扩展类加载器(Extension ClassLoader)：负责加载 JAVA_HOME\lib\ext 目录中的，或通过java.ext.dirs系统变量指定路径中的类库。
* 应用程序类加载器(Application ClassLoader)：负责加载用户路径（classpath）上的类库。


#### 类加载器的工作过程
当一个类加载器收到类加载任务，会先交给其父类加载器去完成，每一个层次的类加载器都是如此，因此最终加载任务都会传递到顶层的启动类加载器，只有当父类加载器无法完成加载任务时（他们的搜索范围种没有找到所需的类（参考上一点各个类加载器的搜索范围）），子加载器才会尝试执行加载任务。


#### 双亲委派模型解决的问题
系统安全性：Java类随着加载它的类加载器一起具备了一种带有优先级的层次关系。比如，Java中的Object类，它存放在rt.jar之中,无论哪一个类加载器要加载这个类，最终都是委派给处于模型最顶端的启动类加载器进行加载，因此Object在各种类加载环境中都是同一个类。如果不采用双亲委派模型，那么由各个类加载器自己取加载的话，那么系统中会存在多种不同的Object类。


#### 使用自定义类加载器的意义
不同类加载器加载的相同的类不相等，在类似tomcat服务器中实现Web服务器、各web应用间的类库的互相隔离、共享。