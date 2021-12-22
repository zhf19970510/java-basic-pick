## Nginx和apache的优缺点

### nginx相对于apache的优点

轻量级：同样起web服务，比起apache占用更少的内存及资源

抗并发：nginx处理请求示异步非阻塞的，而apache是阻塞型的，在高并发下nginx能保持低资源低消耗高性能

高度模块化的设计，编写模块相对简单

社区活跃，各种高性能模块出品迅速

### apache相对于nginx的优点：

rewrite：比nginx的rewrite强大

模块超多，基本想到的都可以找到

少bug，nginx的bug相对较多



Nginx配置简介，Apache复杂

最核心的区别在于apache是同步多进程模型，一个连接对应一个进程；nginx是异步的，多个连接可以对应一个进程

## Nginx解决的问题

高并发

负债均衡

高可用

虚拟主机

伪静态

动态分离



