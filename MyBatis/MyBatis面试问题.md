---
typora-root-url: ./
---

# MyBatis面试问题总结

## 1、MyBatis事务控制方式

MyBatis在执行增删改操作时，默认情况下事务在方法执行后自动回滚啦！通过控制台的log4j日志可以清楚地看到：

![image-20220307183517916](/img/MyBatis默认自动回滚.png)

针对这种情况，MyBatis有两种事务控制方式可以解决这个问题：

- 手动调用commit提交事务

  ![image-20220307183331575](/img/MyBatis事务控制方式1.png)

- 设置为自动提交
  - ![image-20220307183426916](/img/MyBatis事务控制方式2.png)

2、MyBatis怎么获取数据库自动生成的主键值

- 方式一：selectKey标签

  ```xml
  <!--1.方式一：通过selectkey标签获取自增主键值-->
          <insert id="save1" parameterType="com.yiidian.domain.Customer">
              <selectKey keyProperty="id" keyColumn="id" resultType="int" order="AFTER">
                  SELECT LAST_INSERT_ID()
              </selectKey>
              INSERT INTO t_customer(NAME,gender,telephone) VALUES(#{name},#{gender},#{telephone})
          </insert>x
  ```

- useGeneratedKey属性

  ```xml
   <!--2.方式二：通过useGeneratedKeys属性获取自增主键值-->
          <insert id="save1" parameterType="com.yiidian.domain.Customer" useGeneratedKeys="true" keyColumn="id" keyProperty="id">
              INSERT INTO t_customer(NAME,gender,telephone) VALUES(#{name},#{gender},#{telephone})
          </insert>
  ```

  

  ## 2、MyBatis输入参数类型有哪些

  MyBatis的Dao映射配置中有个叫`parameterType`的属性，该属性代表输入参数类型，对应Dao接口方法的参数类型。MyBatis的输入参数类型包含以下几种：

  1. 基本类型

     ![image-20220307211728234](/img/基本类型.png)

  2. Pojo类型

     ![image-20220307211806622](/img/POJO类型.png)

  3. 包装Pojo类型

     ![image-20220307211643751](/img/包装pojo.png)

## 3、MyBatis输出参数类型

MyBatis的Dao映射配置有个属性叫`resultType`，该属性代表输出参数类型，对应Dao方法的方法返回值。MyBatis的输出参数类型包含以下几种：

- 基本类型

  ![image-20220307212038290](/img/基本类型1.png)

- Pojo类型

  ![image-20220307212108616](/img/POJO类型1.png)

- ResultMap类型

  ![image-20220307212157578](/img/ResultMap类型.png)

## 4、MyBatis内置数据源类型

MyBatis支持三种内置的数据源类型：

- Pooled：实现dataSource接口，并且使用了池的思想。

  ```xml
  <!-- 数据库连接池 -->
          <dataSource type="POOLED">
             <property name="driver" value="${jdbc.driver}" />
             <property name="url" value="${jdbc.url}" />
             <property name="username" value="${jdbc.username}" />
             <property name="password" value="${jdbc.password}" />
          </dataSource>
  ```

  连接池就是用于存储连接对象的一个容器。而容器就是一个集合，且必须是线程安全的，即两个线程不能拿到同一个连接对象。同时还要具备队列的特性：先进先出原则。使用连接池的好处：**避免频繁创建和关闭数据库连接造成的开销，节省系统资源。**

  大致流程：

  1. 判断连接池中是否有空闲的连接对象，有则直接返回。
  2. 如果连接池没有空闲的连接，先判断活动连接池是否小于连接池承载的最大数量，小于则再创建新的连接对象。
  3. 但是如果连接池已经达到最大承载数量，那么在连接池中就把最先进来的连接（oldest）返回出去。

- UNPooled：同样也是实现了dataSource接口，但是该类型并没有使用池的思想。

  ```xml
  <dataSource type="unpooled">
              <property name="url" value="jdbc:mysql://localhost:3306/mybatis?characterEcndoing=utf8"/>
              <property name="driver" value="com.mysql.jdbc.Driver"/>
              <property name="username" value="root"/>
              <property name="password" value="root"/>
          </dataSource>
  ```

  这种方式是不具备连接池的思想，如果频繁的创建和销毁连接对象，会影响程序的运行效率。

  大致流程：

  1. 在获取连接对象时，调用initializeDriver()方法判断是否已经注册连接驱动。
  2. 完成驱动注册，使用DriverManager.getConnection获取一个连接对象。
  3. 将连接对象交给configureConnection()方法，并设置自动提交事务，及事务的隔离级别。

- JNDI：采用服务器提供的JNDI技术实现，并且在不同服务器之间获取的连接池不一样。

  JNDI（Java Naming and Directory Interface），是SUN公司推出的一套规范，属于JavaEE技术之一。目的是模仿windows系统中的注册表。

  如果将类型设置成JNDI，MyBatis从应用服务器（如：Tomcat）配置好的JNDI数据源获取数据源连接。

  注意：MyBatis需要使用JNDI作为数据源，必须为Web项目。

  ```xml
  <environments default="mysql">
              <environment id="mysql">
                  <transactionManager type="jdbc"></transactionManager>
                  <dataSource type="JNDI">
                      <property name="data_source" value="java:comp/env/jdbc/mybatis"/>
                  </dataSource>
              </environment>
          </environments>
      
  ```

  