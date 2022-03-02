# 1、Spring简介

## 1.1、简介

- 2002年interface21框架
- 2004年3月24日发布1.0版本
- Spring理念：是现有的技术更加容易使用，本事是一个大杂烩，整合了现有的技术框架



- SSH:Struct2+Spring+Hibernate
- SSM:SpringMVC+Spring+Mybatis



## 1.2、优点

- Spring是一个开源的免费的框架（容器）
- Spring是一个轻量级的、非入侵式的框架
- 控制反转（IOC）,面向切面编程（AOP）
- 支持事务的处理，对框架整合的支持

总结一句话：Spring就是一个轻量级的控制反转（IOC）和面向切面编程（AOP）的框架

弊端：发展了太久之后，违背了原来的理念，配置十分繁琐，人称”配置地狱“



## 1.3、组成

![img](F:\学习\java面试\Spring\img\Spring七大模块)

## 1.4、拓展

现代化的Java开发说白了就是基于Spring的开发

- SpringBoot
  - 一个快速开发的脚手架
  - 基于SpringBoot可以快捷的开发单个微服务
  - 约定大于配置
- SpringCloud
  - SoringCloud是基于SpringCloud实现的

现在大多数公司都在使用SpringBoot进行快速开发，学习SpringBoot的前提，需要完全掌握Spring和SpringMVC！

# 2、IOC理论推导

1. UserDao接口
2. UserDaoImpl实现类
3. UserService业务接口
4. UserServiceImpl业务实现类

![image-20220302135837023](F:\学习\java面试\Spring\img\IOC推导01)

背后增加类，程序不动，客户端自己做些事情去调用，上图

```java
 private UserDao userDao = new UserDaoMysqlImpl();
```

采用的是程序控制对象

在我们之前的业务中用户的需求可能会影响我们原来的代码，我们需要根据用户的需求去修改源代码！如果程序的代码量十分大，修改一次的成本代价十分昂贵！

我们使用一个Set接口实现，已经发生了革命性的变化

![image-20220302141006257](C:\Users\Liu\AppData\Roaming\Typora\typora-user-images\image-20220302141006257.png)

- 之前，程序是主动创建对象的，控制权在程序员手上！
- 使用了set注入后，程序不再具有主动性，而是被动的接收对象

这种思想，从本质上解决了问题，我们程序员不用再去管理对象的创建了，系统的耦合性大大降低，可以更多的关注再业务层的实现上，这是IOC的原型。

### IOC本质

控制反转IoC(Inversion of Control)，是一种设计思想，DI(依赖注入)是实现IoC的一种方法，也有人认为DI只是IoC的另一种说法。没有IoC的程序中 , 我们使用面向对象编程 , 对象的创建与对象间的依赖关系完全硬编码在程序中，对象的创建由程序自己控制，控制反转后将对象的创建转移给第三方，个人认为所谓控制反转就是：获得依赖对象的方式反转了。

**IoC是Spring框架的核心内容**，使用多种方式完美的实现了IoC，可以使用XML配置，也可以使用注解，新版本的Spring也可以零配置实现IoC。

Spring容器在初始化时先读取配置文件，根据配置文件或元数据创建与组织对象存入容器中，程序使用时再从Ioc容器中取出需要的对象。

采用XML方式配置Bean的时候，Bean的定义信息是和实现分离的，而采用注解的方式可以把两者合为一体，Bean的定义信息直接以注解的形式定义在实现类中，从而达到了零配置的目的。

控制反转是一种通过描述（XML或注解）并通过第三方去生产或获取特定对象的方式。在Spring中实现控制反转的是IoC容器，其实现方法是依赖注入（Dependency Injection,DI）。
