### Redis - 底层c源码 核心设计原理

Redis : SDS， simple dynamic string

1. 二进制安全的数据结构
2. 提供了内存预分配机制，避免了频繁的内存分配
3. 兼容c语言的函数库



**List**是一个有序(按加入的时序排序)的数据结构，Redis采用quicklist（双端链表） 和 ziplist 作为List的底层实现。

可以通过设置每个ziplist的最大容量，quicklist的数据压缩范围，提升数据存取效率

list-max-ziplist-size  -2        //  单个ziplist节点最大能存储  8kb  ,超过则进行分裂,将数据存储在新的ziplist节点中
list-compress-depth  1        //  0 代表所有节点，都不进行压缩，1， 代表从头节点往后走一个，尾节点往前走一个不用压缩，其他的全部压缩，2，3，4 ... 以此类推



**Hash** 数据结构底层实现为一个字典( dict ),也是RedisBb用来存储K-V的数据结构,当数据量比较小，或者单个元素比较小时，底层用ziplist存储，数据大小和元素数量阈值可以通过如下参数设置。 

 ![image-20220612105808445](https://gitee.com/zhf19970510/image-server/raw/master/img/20220612105819.png)



hash-max-ziplist-entries  512    //  ziplist 元素个数超过 512 ，将改为hashtable编码 
hash-max-ziplist-value    64      //  单个元素大小超过 64 byte时，将改为hashtable编码



**ZSet**  为有序的，自动去重的集合数据类型，ZSet 数据结构底层实现为 字典(dict) + 跳表(skiplist) ,当数据比较少时，用ziplist编码结构存储。 参数设置如下：

zset-max-ziplist-entries  128    // 元素个数超过128 ，将用skiplist编码
zset-max-ziplist-value     64     //  单个元素大小超过 64 byte, 将用 skiplist编码



**GeoHash**是一种地理位置编码方法。 由Gustavo Niemeyer 和 G.M. Morton于2008年发明，它将地理位置编码为一串简短的字母和数字。它是一种分层的空间数据结构，将空间细分为网格形状的桶，这是所谓的z顺序曲线的众多应用之一，通常是空间填充曲线。

 

### redis 6.0 新特性

**redis 6.0 i/o 多线程模型**

io‐threads 4	# 三个IO 线程，还有一个线程是main线程，main线程负责IO读写和命令执行操作

默认情况下，如上配置，有三个IO线程， 这三个IO线程只会执行 IO中的write 操作，也就是说，read 和 命令执行 都由main线程执行。最后多线程将数据写回到客户端。

![image-20220707142222878](D:\zhfGitRepository\java-basic-pick\redis\Redis - 底层c源码 核心设计原理.assets\image-20220707142222878.png)

开启以下参数后，其他线程也可以执行read操作，但是命令执行还是由主线程执行：

io‐threads‐do‐reads yes // 将支持IO线程执行 读写任务。

![image-20220707142714504](D:\zhfGitRepository\java-basic-pick\redis\Redis - 底层c源码 核心设计原理.assets\image-20220707142714504.png)

**2. 客户端缓存**

**3.权限验证 ACLs**

https://redis.io/docs/manual/security/acl/