### Zookeeper特性与节点数据类型详解

针对节点的监听：一定事件触发，对应的注册立刻被移除，所以事件监听是一次性的

> get ‐w /path // 注册监听的同时获取数据
> stat ‐w /path // 对节点进行监听，且获取元数据信息

![image-20220710064014957](C:\Users\zhf\AppData\Roaming\Typora\typora-user-images\image-20220710064014957.png)

针对目录的监听，如下图，目录的变化，会触发事件，且一旦触发，对应的监听也会被移除，后续对节点的创建没有触发监听事件

> ls ‐w /path

![image-20220710064108521](C:\Users\zhf\AppData\Roaming\Typora\typora-user-images\image-20220710064108521.png)

针对递归子目录的监听

> ls ‐R ‐w /path ： ‐R 区分大小写，一定用大写

如下对/test 节点进行递归监听，但是每个目录下的目录监听也是一次性的，如第一次在/test 目录下创建节点时，触发监听事件，第二次则没有，同样，因为时递归的目录监听，所以在/test/sub0下进行节点创建时，触发事件，但是再次创建/test/sub0/subsub1节点时，没有触发事件。

![image-20220710064220361](C:\Users\zhf\AppData\Roaming\Typora\typora-user-images\image-20220710064220361.png)

Zookeeper事件类型：

> None: 连接建立事件
> NodeCreated： 节点创建
> NodeDeleted： 节点删除
> NodeDataChanged：节点数据变化
> NodeChildrenChanged：子节点列表变化
> DataWatchRemoved：节点监听被移除
> ChildWatchRemoved：子节点监听被移除

### Zookeeper 的 ACL 权限控制( Access Control List )

Zookeeper 的ACL 权限控制,可以控制节点的读写操作,保证数据的安全性，Zookeeper ACL 权限设置分为 3 部分组成，分别是：权限模式（Scheme）、授权对象（ID）、权限信息（Permission）。最终组成一条例如“scheme:id:permission”格式的 ACL 请求信息。下面我们具体看一下这 3 部分代表什么意思：

**Scheme（权限模式）**：用来设置 ZooKeeper 服务器进行权限验证的方式。ZooKeeper 的权限验证方式大体分为两种类型：

一种是**范围验证**。所谓的范围验证就是说 ZooKeeper 可以针对一个 IP 或者一段 IP 地址授予某种权限。比如我们可以让一个 IP 地址为“ip：192.168.0.110”的机器对服务器上的某个数据节点具有写入的权限。或者也可以通过“ip:192.168.0.1/24”给一段 IP 地址的机器赋权。

另一种权限模式就是**口令验证**，也可以理解为用户名密码的方式。在 ZooKeeper 中这种验证方式是 Digest 认证，而 Digest 这种认证方式首先在客户端传送“username:password”这种形式的权限表示符后，ZooKeeper 服务端会对密码 部分使用 SHA-1 和 BASE64 算法进行加密，以保证安全性。

还有一种**Super权限模式**, Super可以认为是一种特殊的 Digest 认证。具有 Super 权限的客户端可以对 ZooKeeper 上的任意数据节点进行任意操作。

**授权对象（ID）**

授权对象就是说我们要把权限赋予谁，而对应于 4 种不同的权限模式来说，如果我们选择采用 IP方式，使用的授权对象可以是一个 IP 地址或 IP 地址段；而如果使用 Digest 或 Super 方式，则对应于一个用户名。如果是 World 模式，是授权系统中所有的用户。

**权限信息（Permission）**

权限就是指我们可以在数据节点上执行的操作种类，如下所示：在 ZooKeeper 中已经定义好的权限有 5 种：

> 数据节点（c: create）创建权限，授予权限的对象可以在数据节点下创建子节点；
> 数据节点（w: wirte）更新权限，授予权限的对象可以更新该数据节点；
> 数据节点（r: read）读取权限，授予权限的对象可以读取该节点的内容以及子节点的列表信息；
> 数据节点（d: delete）删除权限，授予权限的对象可以删除该数据节点的子节点；
> 数据节点（a: admin）管理者权限，授予权限的对象可以对该数据节点体进行 ACL 权限设置。

命令：

> getAcl：获取某个节点的acl权限信息
> setAcl：设置某个节点的acl权限信息
> addauth: 输入认证授权信息，相当于注册用户信息，注册时输入明文密码，zk将以密文的形式存储

可以通过系统参数zookeeper.skipACL=yes进行配置，默认是no,可以配置为true, 则配置过的ACL将不再进行权限检测

生成授权ID的两种方式:

a.代码生成ID:

> @Test
> public void generateSuperDigest() throws NoSuchAlgorithmException {
> 	String sId = DigestAuthenticationProvider.generateDigest("gj:test");
> 	System.out.println(sId);// gj:X/NSthOB0fD/OT6iilJ55WJVado=
> }

b.在xshell 中生成

```bash
echo -n <user>:<password> | openssl dgst -binary -sha1 | openssl base64
```

例如：

![image-20220710072639033](C:\Users\zhf\AppData\Roaming\Typora\typora-user-images\image-20220710072639033.png)

设置ACL有两种方式

节点创建的同时设置ACL

create [-s] [-e] [-c] path [data] [**acl**]

例如：

![image-20220710072802912](C:\Users\zhf\AppData\Roaming\Typora\typora-user-images\image-20220710072802912.png)

或者用setAcl 设置

> setAcl /zk‐node digest:gj:X/NSthOB0fD/OT6iilJ55WJVado=:cdrwa

设置好权限之后如果直接访问是会报错的：（因为没有指定用户名和密码进行访问）

![image-20220710072856799](C:\Users\zhf\AppData\Roaming\Typora\typora-user-images\image-20220710072856799.png)

访问前需要添加授权信息

![image-20220710073154320](D:\zhfGitRepository\java-basic-pick\zookeeper\Zookeeper特性与节点数据类型详解.assets\image-20220710073154320.png)

另一种授权模式： auth 明文授权

使用之前需要先 addauth digest username:password 注册用户信息，后续可以直接用明文授权
如：

> addauth digest u100:p100
> create /node‐1 node1data auth:u100:p100:cdwra
> 这是u100用户授权信息会被zk保存，可以认为当前的授权用户为u100
> get /node‐1
> node1data

IP授权模式：

>  setAcl /node‐ip ip:192.168.109.128:cdwra
> create /node‐ip data ip:192.168.109.128:cdwra

多个指定IP可以通过逗号分隔， 如 setAcl /node-ip ip:IP1:rw,ip:IP2:a

Super 超级管理员模式
这是一种特殊的Digest模式， 在Super模式下超级管理员用户可以对Zookeeper上的节点进行任何的操作。
需要在启动了上通过JVM 系统参数开启：

>  DigestAuthenticationProvider中定义
> ‐Dzookeeper.DigestAuthenticationProvider.superDigest=super:<base64encoded(SHA1(password))

### ZooKeeper 内存数据和持久化

```java
public class DataTree {
	private final ConcurrentHashMap<String, DataNode> nodes = new ConcurrentHashMap<String, DataNode>();
	private final WatchManager dataWatches = new WatchManager();
	private final WatchManager childWatches = new WatchManager();
}
```

DataNode 是Zookeeper存储节点数据的最小单位

```java
public class DataNode implements Record {
	byte data[];
	Long acl;
	public StatPersisted stat;
	private Set<String> children = null;
}
```

### 事务日志

针对每一次客户端的事务操作，Zookeeper都会将他们记录到事务日志中，当然，Zookeeper也会将数据变更应用到内存数据库中。我们可以在zookeeper的主配置文件zoo.cfg 中配置内存中的数据持久化目录，也就是事务日志的存储路径 dataLogDir. 如果没有配置dataLogDir（非必填）, 事务日志将存储到dataDir （必填项）目录，zookeeper提供了格式化工具可以进行数据查看事务日志数据 org.apache.zookeeper.server.LogFormatter

> java ‐classpath .:slf4j‐api‐1.7.25.jar:zookeeper‐3.5.8.jar:zookeeper‐jute‐3.5.8.jar org.apache.zookeeper.server.LogFormatter /usr/local/zookeeper/apache‐zookeeper‐3.5.8‐bin/data/version‐2/log.1

如下是我本地的日志文件格式化效果

![image-20220710100131937](D:\zhfGitRepository\java-basic-pick\zookeeper\Zookeeper特性与节点数据类型详解.assets\image-20220710100131937.png)

从左到右分别记录了操作时间，客户端会话ID，CXID,ZXID,操作类型，节点路径，节点数据（用#+ascii 码表示），节点版本。

Zookeeper进行事务日志文件操作的时候会频繁进行磁盘IO操作，事务日志的不断追加写操作会触发底层磁盘IO为文件开辟新的磁盘块，即磁盘Seek。因此，为了提升磁盘IO的效率，Zookeeper在创建事务日志文件的时候就进行文件空间的预分配- 即在创建文件的时候，就向操作系统申请一块大一点的磁盘块。这个预分配的磁盘大小可以通过系统参数 zookeeper.preAllocSize 进行配置。

事务日志文件名为： log.<当时最大事务ID>，应为日志文件时顺序写入的，所以这个最大事务ID也将是整个事务日志文件中，最小的事务ID，日志满了即进行下一次事务日志文件的创建。

### 数据快照

数据快照用于记录Zookeeper服务器上某一时刻的全量数据，并将其写入到指定的磁盘文件中。可以通过配置snapCount配置每间隔事务请求个数，生成快照，数据存储在dataDir 指定的目录中，可以通过如下方式进行查看快照数据（ 为了避免集群中所有机器在同一时间进行快照，实际的快照生成时机为事务数达到 [snapCount/2 + 随机数(随机数范围为1 ~ snapCount/2 )] 个数时开始快照）、

> java ‐classpath .:slf4j‐api‐1.7.25.jar:zookeeper‐3.5.8.jar:zookeeper‐jute‐3.5.8.jar org.apache.zookeeper.server.**SnapshotFormatter** /usr/local/zookeeper/apache‐zookeeper‐3.5.8‐bin/data‐dir/version‐2/snapshot.0

![image-20220710101024647](D:\zhfGitRepository\java-basic-pick\zookeeper\Zookeeper特性与节点数据类型详解.assets\image-20220710101024647.png)

快照事务日志文件名为： snapshot.<当时最大事务ID>，日志满了即进行下一次事务日志文件的创建。

有了事务日志，为啥还要快照数据。

快照数据主要时为了快速恢复， 事务日志文件是每次事务请求都会进行追加的操作，而快照是达到某种设定条件下的内存全量数据。所以通常快照数据是反应当时内存数据的状态。事务日志是更全面的数据，所以恢复数据的时候，可以先恢复快照数据，再通过增量恢复事务日志中的数据即可。