### redis数据类型
#### 数据结构体
　Redis使用前面说的五大数据类型来表示键和值，每次在Redis数据库中创建一个键值对时，至少会创建两个对象，一个是键对象，一个是值对象，而Redis中的每个对象都是由 redisObject 结构来表示：
```c
typedef struct redisObject{
     //类型
     unsigned type:4;
     //编码
     unsigned encoding:4;
     //指向底层数据结构的指针
     void *ptr;
     //引用计数
     int refcount;
     //记录最后一次被程序访问的时间
     unsigned lru:22;
 
}robj
```

#### 数据类型
对象的type属性记录了对象的类型，这个类型就是前面讲的五大数据类型，
可以通过如下命令来判断对象类型：
```redis
type key
```
返回值：
|对象|对象type属性值|type命令输出|
|---|---|---|
|key不存在|-|none|
|字符串|REDIS_STRING|string|
|列表|REDIS_LIST|list|
|集合|REDIS_SET|set|
|有序集|REDIS_ZSET|zset|
|哈希表|REDIS_HASH|hash|
