### EhCache

Spring 与 EhCache整合：

1. 引入相关依赖
2. ehcache.xml文件配置（可以参考个人gitee项目 ehcache-demo的相关配置
3. spring配置文件配置：配置注解扫描
4. 将ehcache交给spring容器进行管理：

![image-20220808085924542](https://gitee.com/zhf19970510/image-server/raw/master/img/20220808085935.png)

5. 编码测试：

![image-20220809225750614](https://gitee.com/zhf19970510/image-server/raw/master/img/20220809225800.png)

6. 自定义可以生成策略：

![image-20220809230422183](https://gitee.com/zhf19970510/image-server/raw/master/img/20220809230424.png)

使用keyGenerator

![image-20220809230651178](https://gitee.com/zhf19970510/image-server/raw/master/img/20220809230654.png)

7. condition：只有满足条件的时候才会加入缓存。

![image-20220809230924469](https://gitee.com/zhf19970510/image-server/raw/master/img/20220809230927.png)

8. @CachePut：这玩意就是每次都会去执行代码块，并且将执行的结果缓存到cache中

![image-20220809231828527](https://gitee.com/zhf19970510/image-server/raw/master/img/20220809231831.png)

9. @CacheEvict

![image-20220809232610580](https://gitee.com/zhf19970510/image-server/raw/master/img/20220809232613.png)

![image-20220809232723149](https://gitee.com/zhf19970510/image-server/raw/master/img/20220809232725.png)

springBoot整合ehcache：

1. 配置pom：

![image-20220809235124905](https://gitee.com/zhf19970510/image-server/raw/master/img/20220809235133.png)

2. 配置ehcache.xml

3. 启动类开启缓存

![image-20220809235237002](https://gitee.com/zhf19970510/image-server/raw/master/img/20220809235240.png)

4. 其他和spirng中讲到的差不多，主要要活用注解。