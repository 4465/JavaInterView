---
typora-root-url: ./
---

# Spring

[TOC]



## 1、Spring简介

### 1、简介

- 2002年interface21框架
- 2004年3月24日发布1.0版本
- Spring理念：是现有的技术更加容易使用，本事是一个大杂烩，整合了现有的技术框架



- SSH:Struct2+Spring+Hibernate
- SSM:SpringMVC+Spring+Mybatis



### 2、优点

- Spring是一个开源的免费的框架（容器）
- Spring是一个轻量级的、非入侵式的框架
- 控制反转（IOC）,面向切面编程（AOP）
- 支持事务的处理，对框架整合的支持

总结一句话：Spring就是一个轻量级的控制反转（IOC）和面向切面编程（AOP）的框架

弊端：发展了太久之后，违背了原来的理念，配置十分繁琐，人称”配置地狱“



### 3、组成

![](/img/Spring七大模块.png)

### 4、拓展

现代化的Java开发说白了就是基于Spring的开发

- SpringBoot
  - 一个快速开发的脚手架
  - 基于SpringBoot可以快捷的开发单个微服务
  - 约定大于配置
- SpringCloud
  - SoringCloud是基于SpringCloud实现的

现在大多数公司都在使用SpringBoot进行快速开发，学习SpringBoot的前提，需要完全掌握Spring和SpringMVC！

## 2、IOC理论推导

1. UserDao接口
2. UserDaoImpl实现类
3. UserService业务接口
4. UserServiceImpl业务实现类

![](/img/IOC推导01.png)

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

**控制反转是一种通过描述（XML或注解）并通过第三方去生产或获取特定对象的方式。在Spring中实现控制反转的是IoC容器，其实现方法是依赖注入（Dependency Injection,DI）。**

## 3、HelloSpring

### 1、创建MAVEN工程

![image-20220306104704145](/img/创建工程.png)

### 2、导入Spring依赖包

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-webmvc</artifactId>
        <version>5.3.1</version>
    </dependency>
</dependencies>
```

### 3、添加POJO类

```java
package com.xfnlp.pojo;

public class Hello {

    private String str;

    public String getStr() {
        return str;
    }

    public void setStr(String str) {
        this.str = str;
    }

    @Override
    public String toString() {
        return "Hello{" +
                "str='" + str + '\'' +
                '}';
    }
}
```

### 4、配置spring

在resources文件夹下添加beans.xml;

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">


<!--    使用Spring来创建对象，在Spring这些都称为Bean

        bean = 对象           new Hello()

-->
    <bean id="hello" class="com.xfnlp.pojo.Hello">
        <property name="str" value="Spring"></property>
    </bean>

</beans>
```

**bean对象：**

- id:变量名
- class:new的对象
- property:相当于给对象中的属性设置一个值

> **思考**
>
> - Hello对象是谁创建的？
>
>   Hello对象是由Spring创建的
>
> - Hello对象属性是怎么设置的？
>
>   对象的书香是由Spring容器设置的

以上这个过程就叫控制反转

控制：谁来控制对象的创建，传统应用程序的对象是由程序本身控制创建的，使用Spring后，对象是由Spring来创建的

反转：程序本身不创建对象，而变成被动的接收对象

依赖注入:就是利用ser方法来进行注入的，即POJO对象中必须要有set方法

IOC是一种编程思想，由主动的编程变成被动的接收

到了现在,我们彻底不用再程序中去改动了,要实现不同的操作,只需要在xmI配置文件中进行修改,所谓的IoC,- -句话搞定:对象由Spring来创建，管理,装配!

### 5、测试

```java
import com.xfnlp.pojo.Hello;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class MyTest {

    public static void main(String[] args) {
        //获取spring的上下文对象
        ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
        //我们的对象现在都在Spring中管理了，我们要使用，直接取里面取出来就可以
        Hello hello = (Hello) context.getBean("hello");
        System.out.println(hello.toString());
    }
}
```

## 4、IOC创建对象的方式

1. 默认使用无参构造创建对象
2. 
