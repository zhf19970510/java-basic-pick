## Spring IOC 容器的初始化过程：

1. 先申明一个早期的应用程序监听器；

2. 资源定位过程中创建了环境对象configurableEnvironment，包含了JVM的profile配置信息、环境变量、JAVA进程变量、而且还创建了PropertyResolver、ProperplaceholderHelper对象来替换配置文件中的SPEL的值；

3. 之后主要有一个refresh方法，
    prepareRefresh():此方法完成一些基础的准备工作，例如设置时间、设置启动关闭标志、检查环境变量、并提供子类扩展，用来属性注入到ApplicationContext中、设置事件监听器集合.

  obtainFreshBeanFactory()：

  ​	refreshBeanFactory()：如果当前应用程序没有BeanFactory，那么就创建一个；如果已经存在，先销毁再创建一个新的BeanFactory。BeanFactory有两个非常关键的子接口：ListableBeanFactory、ConfigurableBeanFactory。通过createBeanFactory()创建Bean Factory，loadBeanDefinitions(beanFactory)   ,有多个loadBeanDefinitions()方法重载，加载配置文件，验证文件格式、文件约束、标签匹配、

  ![image-20211012130317152](https://gitee.com/zhf19970510/image-server/raw/master/img/20211012130328.png)

## Spring中用到了哪些设计模式？

代理模式 - 在AOP中用的比较多
单例模式 - 在spring配置文件中定义的bean默认为单例模式
模板方法 - 用来解决代码重复问题，比如：RestTemplate，JmsTemplate，JpaTemplate。
工厂模式 - BeanFactory用来创建对象的实例
适配器 - spring aop
装饰器 - spring data hashmapper
观察者 - spring 事件驱动模型
回调 - spring aware回调接口

## BeanFactory和FactoryBean的区别？

BeanFactory是个Factory，也就是IOC容器或对象工厂，在spring中，所有的Bean都是由BeanFactory(也就是IOC容器)来进行管理的，提供了实例化对象和拿对象的功能。

使用场景：

- 从IOC容器中获取Bean(byName or byType)
- 检索IOC容器中是否包含指定的Bean
- 判断Bean是否为单例

**FactoryBean是个Bean**，这个Bean不是简单的Bean，而是一个能生产或者修饰对象生成的工厂Bean，它的实现与设计模式中的工厂模式和修饰器模式类似。

使用场景

- ProxyFactoryBean

### Spring容器初始化过程

Spring容器初始化过程简单的可以分为三个过程：

第一个过程是Resource资源定位。这个Resource指的是BeanDefinition的资源定位。这个过程就是容器找数据的过程，就像水桶装水先找到水一样。

第二个过程是BeanDefinition的载入过程。这个载入过程是把用户定义好的Bean表示成IOC容器内部的数据结构，而这个容器内部的数据结构就是BeanDefinition。

第三个过程就是向IOC容器注册这些BeanDefinition的过程，这个过程就是将前面的BeanDefinition保存到HashMap的过程。

