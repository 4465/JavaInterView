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

   ```xml
       <bean id="user" class="com.xfnlp.pojo.User">
           <property name="name" value="叶子"></property>
       </bean>
   ```

2. 假设我们使用有参构造创建对象
   1. 下标赋值

      ```xml
          <bean id="user" class="com.xfnlp.pojo.User">
              <constructor-arg index="0" value="狂神说java"></constructor-arg>
          </bean>
      ```

   2. 通过匹配变量类型创建

      ```xml
      <!-- 不建议使用，两个String类型参数的话就不能使用  !-->
      <bean id="user" class="com.xfnlp.pojo.User">
          <constructor-arg type="java.lang.String" value="刘威甫"></constructor-arg>
      </bean>
      ```

   3. 通过参数名创建对象

      ```xml
      <!-- 第三种，直接通过参数名来创建对象  !-->
      <bean id="user" class="com.xfnlp.pojo.User">
          <constructor-arg name="name" value="刘威甫"></constructor-arg>
      </bean>
      ```

   **总结：**

   IOC只有一种无参构造和三种有参构造

   我们在注册Bean的时候（即配置文件加载的时候），容器中管理的对象就已经被初始化了（即类就已经被Spring容器实例化了）

## 5、Spring配置

### 1、别名

```xml
<!--    别名，和对象名字等效-->
    <alias name="user" alias="lwf"></alias>
```

### 2、Bean的配置

```xml
<!--
is：bean的唯一标识符，也就是相当于我们学的对象名
class:bean对象所对应的全限定名：包名+类型
name:也是别名,可以取多个别名，alias是一对一的取别名
scope:作用域，默认单例模式
!-->
<bean id="user" class="com.xfnlp.pojo.User" name="user2,u3 u5;u8" scope="singleton">
    <property name="name" value="xianfeng"></property>
</bean>
```

### 3、import

这个import，一般用于团队开发使用，他可以将多个配置文件导入导入合并为一个

![image-20220306151728610](/img/import使用.png)

> applicationContext.xml
>
> ```xml
> <?xml version="1.0" encoding="UTF-8"?>
> <beans xmlns="http://www.springframework.org/schema/beans"
>        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
>        xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
> 
>     <import resource="beans.xml"></import>
>     <import resource="beans2.xml"></import>
>     <import resource="beans3.xml"></import>
> 
> </beans>
> ```
>
> MyTest.java
>
> ```java
> import com.xfnlp.pojo.User;
> import org.springframework.context.ApplicationContext;
> import org.springframework.context.support.ClassPathXmlApplicationContext;
> 
> public class MyTest {
>     public static void main(String[] args) {
> //        User user = new User();
> 
>         ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
> 
>         User user = (User) context.getBean("u8");
>         user.show();
> 
>     }
> }
> ```

## 6、依赖注入(DI)

### 1、构造器注入（见第四节）

### 2、Set方式注入（重点）

- 依赖注入：Set注入
- 依赖：bean对象的创建依赖于容器
- 注入：bean对象中的所有属性，由容器来注入

【环境搭建】

1. 复杂类型

   > Address.java
   >
   > ```java
   > package com.xfnlp.pojo;
   > 
   > public class Address {
   >     private String Address;
   > 
   >     public String getAddress() {
   >         return Address;
   >     }
   > 
   >     public void setAddress(String address) {
   >         Address = address;
   >     }
   > }
   > 
   > ```

2. 真实测试对象

   > Studnet.java
   >
   > ```
   > package com.xfnlp.pojo;
   > 
   > import java.util.*;
   > 
   > public class Student {
   >     private String name;
   >     private Address address;
   > 
   >     private String[] books;
   >     private List<String> hobbys;
   >     private Map<String,String> card;
   >     private Set<String> games;
   > 
   >     private String wife;
   >     private Properties info;
   > 
   >     public String getName() {
   >         return name;
   >     }
   > 
   >     public void setName(String name) {
   >         this.name = name;
   >     }
   > 
   >     public Address getAddress() {
   >         return address;
   >     }
   > 
   >     public void setAddress(Address address) {
   >         this.address = address;
   >     }
   > 
   >     public String[] getBooks() {
   >         return books;
   >     }
   > 
   >     public void setBooks(String[] books) {
   >         this.books = books;
   >     }
   > 
   >     public List<String> getHobbys() {
   >         return hobbys;
   >     }
   > 
   >     public void setHobbys(List<String> hobbys) {
   >         this.hobbys = hobbys;
   >     }
   > 
   >     public Map<String, String> getCard() {
   >         return card;
   >     }
   > 
   >     public void setCard(Map<String, String> card) {
   >         this.card = card;
   >     }
   > 
   >     public Set<String> getGames() {
   >         return games;
   >     }
   > 
   >     public void setGames(Set<String> games) {
   >         this.games = games;
   >     }
   > 
   >     public String getWife() {
   >         return wife;
   >     }
   > 
   >     public void setWife(String wife) {
   >         this.wife = wife;
   >     }
   > 
   >     public Properties getInfo() {
   >         return info;
   >     }
   > 
   >     public void setInfo(Properties info) {
   >         this.info = info;
   >     }
   > 
   >     @Override
   >     public String toString() {
   >         return "Student{" +
   >                 "name='" + name + '\'' +
   >                 ", address=" + address +
   >                 ", books=" + Arrays.toString(books) +
   >                 ", hobbys=" + hobbys +
   >                 ", card=" + card +
   >                 ", games=" + games +
   >                 ", wife='" + wife + '\'' +
   >                 ", info=" + info +
   >                 '}';
   >     }
   > }
   > ```

3. beans.xml

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
       <bean id="student" class="com.xfnlp.pojo.Student">
           <!--第一种，基本类型的注入-->
           <property name="name" value="白起"></property>
       </bean>
   </beans>
   ```

4. 测试类

   ```java
   import com.xfnlp.pojo.Student;
   import org.springframework.context.ApplicationContext;
   import org.springframework.context.support.ClassPathXmlApplicationContext;
   
   public class MyTest {
       public static void main(String[] args) {
           ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
   
           Student student = (Student) context.getBean("student");
           System.out.println(student.getName());
       }
   }
   ```

5. 完善注入信息

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
   
       <bean id="address" class="com.xfnlp.pojo.Address">
           <property name="address" value="湖北"></property>
       </bean>
   
       <bean id="student" class="com.xfnlp.pojo.Student">
           <!--第一种，基本类型的注入-->                                  
           <property name="name" value="白起"></property>
           <!--第二种，Bean(引    用类型)的注入  ref-->
           <property name="address" ref="address"></property>
   
           <!--数组注入， -->
           <property name="books">
               <array>
                   <value>红楼梦</value>
                   <value>水浒传</value>
                   <value>三国演义</value>
                   <value>西游记</value>
               </array>
           </property>
   
           <!--list -->
           <property name="hobbys">
               <list>
                   <value>听歌</value>
                   <value>敲代码</value>
                   <value>看电影</value>
               </list>
           </property>
   
           <!--map -->
           <property name="card">
               <map>
                   <entry key="身份证" value="12334865221558"></entry>
                   <entry key="银行卡" value="475589521255"></entry>
               </map>
           </property>
   
           <!--set -->
           <property name="games">
               <set>
                   <value>LOC</value>
                   <value>COC</value>
                   <value>COB</value>
               </set>
           </property>
   
           <!--空值注入 null 和”“ -->
           <property name="wife">
               <null></null>
           </property>
   
           <!--配置类-->
           <property name="info">
               <props>
                   <prop key="学号">1202021541</prop>
                   <prop key="性别">男</prop>
                   <prop key="母亲">刘素贞</prop>
               </props>
           </property>
   
       </bean>
   
   
   
   </beans>
   ```

   ```java
   import com.xfnlp.pojo.Student;
   import org.springframework.context.ApplicationContext;
   import org.springframework.context.support.ClassPathXmlApplicationContext;
   
   public class MyTest {
       public static void main(String[] args) {
           ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
   
           Student student = (Student) context.getBean("student");
           System.out.println(student.toString());
       }
   }
   
   
   //Student{name='白起', address=address{Address='湖北'}, books=[红楼梦, 水浒传, 三国演义, 西游记], hobbys=[听歌, 敲代码, 看电影], card={身份证=12334865221558, 银行卡=475589521255}, games=[LOC, COC, COB], wife='null', info={母亲=刘素贞, 学号=1202021541, 性别=男}}
   ```

### 3、扩展方式注入

我们可以使用p命名空间和c命名空间进行注入

#### 1、p命名空间

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:p="http://www.springframework.org/schema/p"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!--p命名空间注入，可以直接注入属性值-->
    <bean id="user" class="com.xfnlp.pojo.User" p:name="刘威甫" p:age="18">

    </bean>
</beans>
```

```java
@Test
public void test2(){
    ApplicationContext context = new ClassPathXmlApplicationContext("userBeans.xml");
    User user = context.getBean("user", User.class);
    System.out.println(user);
}

-------------------------------
User{name='刘威甫', age=18}
```

#### 2、c命名空间

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:p="http://www.springframework.org/schema/p"
       xmlns:c="http://www.springframework.org/schema/c"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!--p命名空间注入，可以直接注入属性值-->
    <bean id="user" class="com.xfnlp.pojo.User" p:name="刘威甫" p:age="18">

    </bean>

    <!--p命名空间注入，通过构造器注入：contructor-args-->
    <bean id="user2" class="com.xfnlp.pojo.User" c:name="白起" c:age="18"></bean>
</beans>xml

```

```java
@Test
public void test3(){
    ApplicationContext context = new ClassPathXmlApplicationContext("userBeans.xml");
    User user = context.getBean("user2", User.class);
    System.out.println(user);
}
---------------------------------------------
User{name='白起', age=18}
```

注意点：p命名空间和c命名空间，需要导入以下命令

```xml
xmlns:p="http://www.springframework.org/schema/p"
xmlns:c="http://www.springframework.org/schema/c"
```

## 7、Bean的作用域

![image-20220306162017046](/img/Bean作用域.png)

1.    单例模式（默认）

   ```xml
   <bean id="user2" class="com.xfnlp.pojo.User" c:name="白起" c:age="18" scope="singleton"></bean>
   ```

2. 原型模式

   每次从容器中get的时候，都会产生一个对象

   ```xml
   <bean id="user2" class="com.xfnlp.pojo.User" c:name="白起" c:age="18" scope="prototype"></bean>
   ```

3. 其余的request、session、application只能在web开发中使用到

## 8、Bean的自动装配

- 自动装配是Spring满足bean依赖的一种方式
- Spring会在上下文中自动寻找，并自动给bean装配属性

在Spring中由三种装配的方式：

- 在xml中显示的配置
- 在java中显示配置
- 隐式的自动装配【重要】

### 1、测试

- 一个人由两个宠物

  > Cat.java
  >
  > ```java
  > package com.xfnlp.pojo;
  > 
  > public class Cat {
  >      public void shout(){
  >          System.out.println("喵");
  >      }
  > }
  > ```
  >
  > Dog.java
  >
  > ```java
  > package com.xfnlp.pojo;
  > 
  > public class Dog {
  >     public void shout(){
  >         System.out.println("汪");
  >     }
  > }
  > ```
  >
  > People.java
  >
  > ```java
  > package com.xfnlp.pojo;
  > 
  > public class People {
  > 
  >     private Cat cat;
  >     private Dog dog;
  >     private String name;
  > 
  >     public Cat getCat() {
  >         return cat;
  >     }
  > 
  >     public void setCat(Cat cat) {
  >         this.cat = cat;
  >     }
  > 
  >     public Dog getDog() {
  >         return dog;
  >     }
  > 
  >     public void setDog(Dog dog) {
  >         this.dog = dog;
  >     }
  > 
  >     public String getName() {
  >         return name;
  >     }
  > 
  >     public void setName(String name) {
  >         this.name = name;
  >     }
  > 
  >     @Override
  >     public String toString() {
  >         return "People{" +
  >                 "cat=" + cat +
  >                 ", dog=" + dog +
  >                 ", name='" + name + '\'' +
  >                 '}';
  >     }
  > }
  > ```

  > bean.xml
  >
  > ```java
  > <?xml version="1.0" encoding="UTF-8"?>
  > <beans xmlns="http://www.springframework.org/schema/beans"
  >        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  >        xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
  > 
  >     <bean id="cat" class="com.xfnlp.pojo.Cat"></bean>
  >     <bean id="dog" class="com.xfnlp.pojo.Dog"></bean>
  > 
  >     <bean id="people" class="com.xfnlp.pojo.People">
  >         <property name="name" value="里斯"></property>
  >         <property name="dog" ref="dog"></property>
  >         <property name="cat" ref="cat"></property>
  >     </bean>
  > 
  > </beans>
  > ```

  > MyTest.java
  >
  > ```java
  > import com.xfnlp.pojo.People;
  > import org.junit.Test;
  > import org.springframework.context.ApplicationContext;
  > import org.springframework.context.support.ClassPathXmlApplicationContext;
  > 
  > public class MyTest {
  > 
  >     @Test
  >     public void test1(){
  >         ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
  >         People people = context.getBean("people", People.class);
  >         people.getCat().shout();
  >         people.getDog().shout();
  >     }
  > }
  > ```
  >
  > ![image-20220306211935823](/img/猫和狗.png)

2、byName自动装配

byName会自动在容器上下文中查找，和自己对象set方法后面的值对应的beanid

byName会自动在容器上下文中查找，和自己对象属性类型相同的bean

```java
    <bean id="cat" class="com.xfnlp.pojo.Cat"></bean>
    <bean id="dog" class="com.xfnlp.pojo.Dog"></bean>


<!--    byName会自动在容器上下文中查找，和自己对象set方法后面的值对应的beanid-->
    <bean id="people" class="com.xfnlp.pojo.People" autowire="byName">
        <property name="name" value="里斯"></property>
<!--        <property name="dog" ref="dog"></property>-->
<!--        <property name="cat" ref="cat"></property>-->
    </bean>
```

```java
<!--    byName会自动在容器上下文中查找，和自己对象set方法后面的值对应的beanid-->
    <bean id="people" class="com.xfnlp.pojo.People" autowire="byType">
        <property name="name" value="里斯"></property>
<!--        <property name="dog" ref="dog"></property>-->
<!--        <property name="cat" ref="cat"></property>-->
    </bean>
```

小结：

- byName的时候，需要保证所有bean的id唯一，并且这个bean需要和自动注入的属性的set()方法的值一致
- byType的时候，需要保证所有bean的class唯一，并且这个bean需要和自动注入的属性的类型一致

## 9、使用注解实现在自动装配

### 1、@AutoWired

使用@AutoWired(JDK1.5开始支持，Spring2.5就开始支持注解了)

要使用注解须知：

1. 导入约束

2. 配置注解的支持

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xmlns:context="http://www.springframework.org/schema/context"
          xsi:schemaLocation="http://www.springframework.org/schema/beans
          http://www.springframework.org/schema/beans/spring-beans.xsd
           http://www.springframework.org/schema/context
           http://www.springframework.org/schema/context/spring-context.xsd
          ">
       <context:annotation-config></context:annotation-config>
   </beans>
   ```

   > **使用注解后的代码**
   >
   > beans.xml
   >
   > ```xml
   > <?xml version="1.0" encoding="UTF-8"?>
   > <beans xmlns="http://www.springframework.org/schema/beans"
   >        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
   >        xmlns:context="http://www.springframework.org/schema/context"
   >        xsi:schemaLocation="http://www.springframework.org/schema/beans
   >        http://www.springframework.org/schema/beans/spring-beans.xsd
   >         http://www.springframework.org/schema/context
   >         http://www.springframework.org/schema/context/spring-context.xsd
   >        ">
   > 
   >     <context:annotation-config></context:annotation-config>
   > 
   >     <bean id="cat" class="com.xfnlp.pojo.Cat"></bean>
   >     <bean id="dog" class="com.xfnlp.pojo.Dog"></bean>
   > 
   >     <bean id="people" class="com.xfnlp.pojo.People"></bean>
   > 
   > </beans>
   > ```
   >
   > People.java
   >
   > ```java
   > package com.xfnlp.pojo;
   > 
   > import org.springframework.beans.factory.annotation.Autowired;
   > 
   > 
   > public class People {
   >     @Autowired
   >     private Cat cat;
   >     @Autowired
   >     private Dog dog;
   >     private String name;
   > 
   >     public Cat getCat() {
   >         return cat;
   >     }
   > 
   >     public void setCat(Cat cat) {
   >         this.cat = cat;
   >     }
   > 
   >     public Dog getDog() {
   >         return dog;
   >     }
   > 
   >     public void setDog(Dog dog) {
   >         this.dog = dog;
   >     }
   > 
   >     public String getName() {
   >         return name;
   >     }
   > 
   >     public void setName(String name) {
   >         this.name = name;
   >     }
   > 
   >     @Override
   >     public String toString() {
   >         return "People{" +
   >                 "cat=" + cat +
   >                 ", dog=" + dog +
   >                 ", name='" + name + '\'' +
   >                 '}';
   >     }
   > }
   > ```

@AutoWired直接在属性上使用即可，也可以在set方法上使用。

使用AutoWired我们可以不用编写Set方法了，前提是你这个自动装配的属性在IOC（Spring）容器中存在，且符合名字byName

```java
// @NUllable 字段标记了这个注解，说明这个字段可以为null
```

```java
@Target({ElementType.CONSTRUCTOR, ElementType.METHOD, ElementType.PARAMETER, ElementType.FIELD, ElementType.ANNOTATION_TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface Autowired {
    boolean required() default true;
}
//required为false说明这个对象可以为null
```

​		如果@AutoAired自动装配的环境比较复杂，自动装配无法通过一个注解@AutoWired完成的时候，我们可以使用@Qualifier(value=”xxxx“)配合@AutoWired使用指定一个唯一的bean对象

### 2、@Resource

@Resource是java的原生注解，能够通过byName自动装配，也能通过byType自动装配，只有在name和type都没由匹配到的时候才会报错

### 小结

@Resource和@AutoWired的区别：

- 都是用来自动装配的，都可以放在属性字段上
- @AutoWired通过byType的方式实现，而且必须要求这个对象存在
- @Resource默认通过nyName方式实现，如果找不到名字，则通过ByType实现，如果两个都找不到的情况下，就报错

![](/img/注解装配.png)

## 10、使用注解开发

在Spring4之后要使用注解开发必须要保证aop的包导入了

使用注解需要导入context约束，增加注解的支持

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd
       ">
    <context:annotation-config></context:annotation-config>
</beans>
```



| 注解       | 作用                                               |
| ---------- | -------------------------------------------------- |
| @Component | 组件，放在类上，说明这个类被Spring管理了，就是bean |
|            |                                                    |
|            |                                                    |

### 1、bean

### 2、属性如何注入

```java
package com.xfnlp.pojo;


import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Component;


//等价于<bean id="user" class="com.xfnlp.pojo.User"/>
@Component    //组件
public class User {

    /**
     *相当于<bean id="user" class="com.xfnlp.pojo.User">
     *         <property name="name" value="张三"></property>
     *     </bean>
     */
//    @Value("张三")
    public String name;

    @Value("张三")
    public void setName(String name){
        this.name = name;
    }
}
```

### 3、衍生的注解

@Component有几个衍生注解，我们在web开发中，会按照mvc三层架构分层

- dao    【@Repository】
- service  【@Service】
- controller 【@Controller】

这四个注解功能都是一样的，等价的，都是代表将某个类注册到Spring中，装配Bean

### 4、自动装配

![](/img/注解装配.png)

### 5、作用域

@Scope

### 6、小结

xml与注解

- xml更加万能，适用于任何场合，维护简单方便
- 注解不是自己的类是用不了，维护相对复杂

最佳实践：

- xml用来管理bean

- 注解只负责完成属性的注入

- 我们在使用的过程中，只需要注意一个问题，必须让注解生效，就需要开启注解的支持

  ```xml
  <!--指定要扫描的包，这个包下面的注解就会生效-->
  <context:component-scan base-package="com.xfnlp"></context:component-scan>
  <context:annotation-config></context:annotation-config>
  ```

  ## 11、使用java的方式配置Spring

我们现在要完全不是用户Spring的xml配置了，全权交给java来做

JavaConfig是Spring的一个子项目，在Spring4之后变成了核心功能

> User.java
>
> ```java
> package com.xfnlp.pojo;
> 
> 
> import org.springframework.beans.factory.annotation.Value;
> import org.springframework.stereotype.Component;
> 
> 
> //这里这个注解的意思，就是说明这个类被Spring接管了，注册到了容器中
> @Component
> public class User {
> 
>     private String name;
> 
>     public String getName() {
>         return name;
>     }
>     @Value("张三")
>     public void setName(String name) {
>         this.name = name;
>     }
> 
>     @Override
>     public String toString() {
>         return "User{" +
>                 "name='" + name + '\'' +
>                 '}';
>     }
> }
> ```
>
> MyConfig.java
>
> ```java
> package com.xfnlp.config;
> 
> 
> import com.xfnlp.pojo.User;
> import org.springframework.context.annotation.Bean;
> import org.springframework.context.annotation.ComponentScan;
> import org.springframework.context.annotation.Configuration;
> import org.springframework.context.annotation.Import;
> import org.springframework.stereotype.Indexed;
> 
> @Configuration   //这个也会被Spring容器托管，注册到容器中，因为他本来就是一个@Component
> //@Configuration 代表这是一个配置类，作用等价于beans.xml
> 
> @ComponentScan("com.xfnlp.pojo")
> @Import(MyConfig2.class)
> public class MyConfig {
>     @Bean
>     //注册一个bean，就相当于我们之前写的一个bean标签
>     //这个方法的名字，就相当于bean标签中的id属性
>     //这个方法的返回值，就相当于bean标签中的class属性
>     public User getUser(){
>         return new User();   //返回咬住的bean对象
>     }
> }
> ```
>
> MyConfig2.java
>
> ```java
> package com.xfnlp.config;
> 
> 
> import org.springframework.context.annotation.Configuration;
> 
> @Configuration
> 
> public class MyConfig2 {
> 
> }
> ```
>
> MyTest.java
>
> ```java
> import com.xfnlp.config.MyConfig;
> import com.xfnlp.pojo.User;
> import org.springframework.context.ApplicationContext;
> import org.springframework.context.annotation.AnnotationConfigApplicationContext;
> 
> public class MyTest {
> 
>     public static void main(String[] args) {
> 
>         //如果完全使用了配置类方式，我们就只能使用 AnnotationConfigApplicationContext  获取上下文来获取容器，通过配置类的class的对象加载
>         ApplicationContext context =  new AnnotationConfigApplicationContext(MyConfig.class);
>         User getUser = (User) context.getBean("getUser");
>         System.out.println(getUser.getName());
>     }
> }
> ```

这种纯java的配置方式，在SpringBoot中随处可见！

## 11、代理模式

代理模式是Spring APO的底层【SpringMVC和AOP面试必问】

代理模式的分类：

- 静态代理
- 动态代理

![image-20220307110559781](/img/代理模式.png)

### 1、静态代理

角色分析：

- 抽象对象：一般会使用接口和抽象类来解决
- 真实角色：被代理的角色
- 代理角色：代理真实角色，代理真实的角色后，我们一般会做一些附属操作
- 客户：访问代理对象的人

代码步骤：

1. 接口

   ```java
   package com.xfnlp.demo01;
   
   //租房
   public interface Rent {
       public void rent();
   }
   ```

2. 真实角色

   ```java
   package com.xfnlp.demo01;
   
   
   //房东
   public class Host implements Rent{
       public void rent(){
           System.out.println("房东要出租房子");
       }
   }
   ```

3. 代理角色

   ```java
   package com.xfnlp.demo01;
   
   public class Proxy implements Rent{
   
       private Host host;
   
       public Proxy() {
       }
   
       public Proxy(Host host) {
           this.host = host;
       }
   
       public void rent() {
           seeHouse();
           host.rent();
           hetong();
           fare();
       }
   
       //看房
       public void seeHouse(){
           System.out.println("中介带你看房");
       }
   
       //收中介费
       public void fare(){
           System.out.println("收中介费");
       }
   
       //签合同
       public void hetong(){
           System.out.println("签租赁合同");
       }
   }
   ```

4. 客户端访问代理角色

   ```java
   package com.xfnlp.demo01;
   
   public class Client {
   
       public static void main(String[] args) {
           //房东要出租房子
           Host host = new Host();
           //代理，中介帮房东子房子，但是中介会有一些额外的操作！
           Proxy proxy = new Proxy(host);
           //客户不用换面对房东，直接找中介即可
           proxy.rent();
       }
   }
   ```

代理模式的好处：

- 可以使真实角色的操作更加纯粹，不用取关注一些公共的业务
- 公共业务交给了代理角色，实现了业务的分工
- 公共业务发生扩展的时候，方便集中管理

缺点：

- 一个真实的角色就会产生一个代理角色，代码量会翻倍，开发效率会变低

![image-20220307131734136](/img/静态代理再理解.png)

### 2、动态代理

- 动态代理和静态代理角色一样
- 动态代理的代理类是动态生成的，不是我们直接写好的
- 动态代理分为两大类：基于接口的动态代理、基于类的动态代理
  - 基于接口---->JDK动态代理
  - 基于类 ---->cglib
  - java字节码实现  ---->javasist

需要了解两个类：Proxy(代理)，InvocationHandler(调用处理程序)

动态代理的好处

- 可以使真实角色的操作更加纯粹，不用取关注一些公共的业务
- 公共业务交给了代理角色，实现了业务的分工
- 公共业务发生扩展的时候，方便集中管理
- 一个动态代理类代理的是一个接口，一般就是对应一类业务

## 12、AOP

### 1、什么是AOP

AOP (Aspect Oriented Programming)意为:面向切面编程,通过预编译方式和运行期动态代理实现程序功能的统一维护一-种技术。AOP是OOP的延续,是软件开发中的一-个热点,也是Spring框架中的一一个重要内容,是函数式编程的一种衍生范型。利用AOP可以对业务逻辑的各个部分进行隔离，从而使得业务逻辑各部分之间的耦合度降低，提高程序的可重用性，同时提高了开发的效率。

![image-20220307134914027](/img/AOP业务.png)

### 2、AOP在Spring中的作用

![image-20220307140347001](/img/AOP名词解释.png)

![image-20220307140413010](/img/AOP.png)

![image-20220307140505711](/img/AOP2.png)

### 3、使用Spring实现AOP

【重点】使用AOP织入，需要导入一个依赖包

```xml
<dependencies>
    <dependency>
        <groupId>org.aspectj</groupId>
        <artifactId>aspectjweaver</artifactId>
        <version>1.9.4</version>
    </dependency>
</dependencies>
```

方式一：使用Spring的API接口【主要SpringAPI接口】

配置aop需要导入aop的约束

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd"
>
```

方法二：自定义类实现AOP【主要是切面定义】

方式三：使用注解实现

## 13、整合Mybatis

### 1、整合步骤

步骤：

1. 导入相关jar包

   - jubit
   - mybatis
   - mysql数据库
   - spring相关的
   - aop织入
   - mybatis-spring

   ```xml
   <dependencies>
       <dependency>
           <groupId>junit</groupId>
           <artifactId>junit</artifactId>
           <version>4.12</version>
           <scope>test</scope>
       </dependency>
   
       <dependency>
           <groupId>mysql</groupId>
           <artifactId>mysql-connector-java</artifactId>
           <version>8.0.28</version>
       </dependency>
   
       <dependency>
           <groupId>org.mybatis</groupId>
           <artifactId>mybatis</artifactId>
           <version>3.5.3</version>
       </dependency>
   
       <dependency>
           <groupId>org.springframework</groupId>
           <artifactId>spring-webmvc</artifactId>
           <version>5.3.1</version>
       </dependency>
   
       <dependency>
           <groupId>org.springframework</groupId>
           <artifactId>spring-jdbc</artifactId>
           <version>5.3.16</version>
       </dependency>
   
       <dependency>
           <groupId>org.aspectj</groupId>
           <artifactId>aspectjweaver</artifactId>
           <version>1.9.4</version>
       </dependency>
   
       <dependency>
           <groupId>org.mybatis</groupId>
           <artifactId>mybatis-spring</artifactId>
           <version>2.0.7</version>
       </dependency>
   
   </dependencies>
   ```

2. 编写配置文件

3. 测试

### 2、回顾MyBatis

1. 编写实体类
2. 编写核心配置文件
3. 编写接口
4. 编写Mapper.xml
5. 测试
