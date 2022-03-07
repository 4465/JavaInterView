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
- useGeneratedKey属性