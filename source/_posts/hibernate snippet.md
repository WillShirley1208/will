---
title: hibernate snippet
date: 2018-5-1 10:01:30
tags: snippet
categories: hibernate
---

> ORM is an acronym for Object/Relational mapping. It is a programming strategy to map object with the data stored in the database. It simplifies data creation, data manipulation, and data access.

### The advantages of using ORM over JDBC

- Application development is fast.
- Management of transaction.
- Generates key automatically.
- Details of SQL queries are hidden.


### Hibernate Architecture

- Java application layer
- Hibernate framework layer
- Backhand api layer
- Database layer

![](https://www.javatpoint.com/images/hibernate/arc2.jpg)

Hibernate Application Architecture：

![](https://www.tutorialspoint.com/hibernate/images/hibernate_architecture.jpg)

> JDBC (Java Database Connectivity)
>
> JTA (Java Transaction API) 
>
> JNDI (Java Naming Directory Interface)

### Elements of Hibernate Architecture

- Configuration
  - **Database Connection** − This is handled through one or more configuration files supported by Hibernate. These files are **hibernate.properties** and **hibernate.cfg.xml**.
  - **Class Mapping Setup** − This component creates the connection between the Java classes and database tables.

- SessionFactory

  > SessionFactory is a thread-safe object, many threads cannot access it simultaneously.

- Session

  > - It maintains a connection between the hibernate application and database.It provides methods to store, update, delete or fetch data from the database such as persist(), update(), delete(), load(), get() etc.
  > - Session is not a thread-safe object, many threads can access it simultaneously. In other words, you can share it between threads.

- Transaction

- ConnectionProvider

- Query

  > Query objects use SQL or Hibernate Query Language (HQL) string to retrieve data from the database and create objects. A Query instance is used to bind query parameters, limit the number of results returned by the query, and finally to execute the query.

- Criteria

  > Criteria objects are used to create and execute object oriented criteria queries to retrieve objects.

### About SQL

- SQL query created in Hibernate

  > Session.createSQLQuery

  ```java
  Session.createSQLQuery()
  //The method createSQLQuery() creates Query object using the native SQL syntax. 
  Query query = session.createSQLQuery("Select * from Student");
  ```

- HQL query

  > Session.createQuery

  ```java
  Session.createQuery()
  //The method createQuery() creates Query object using the HQL syntax. 
  Query query = session.createQuery("from Student s where s.name like 'k%'");
  ```

- criteria query

  > Session.createCriteria

  ```java
  Session.createCriteria()
  //The method createCriteria() creates Criteria object for setting the query parameters. This is more useful feature for those who don't want to write the query in hand. You can specify any type of complicated syntax using the Criteria API.
  Criteria criteria = session.createCriteria(Student.class);
  ```

### HQL vs Criteria

- HQL is to perform both select and non-select operations on the data,  but Criteria is only for selecting the data, we cannot perform non-select operations using criteria
- HQL is suitable for executing Static Queries, where as Criteria is suitable for executing Dynamic Queries
- HQL doesn’t support pagination concept, but we can achieve pagination with Criteria
- Criteria used to take more time to execute then HQL
- With Criteria we are safe with SQL Injection because of its dynamic query generation but in HQL as your queries are either fixed or parametrized, there is no safe from SQL Injection.

###  types of association mapping

1. One to One

2. One to Many

   > （一对多）单向：会产生中间表，此时可以用@onetoMany @Joincolumn（name=" "）避免产生中间表**，并且指定了外键的名字（别看 @joincolumn在一中写着，但它存在在多的那个表中）

3. Many to One

   > （多对一）单向：不产生中间表，但可以用@Joincolumn（name="  "）来指定生成外键的名字，外键在多的一方表中产生！

4. Many to Many

---

可参考[JPA实体关系映射](https://www.jianshu.com/p/54108abb070f)

#### 为什么要有实体关系映射

>简化编程操作。把冗余的操作交给底层框架来处理。
 例如，如果我要给一位新入学的学生添加一位新的老师。而这个老师又是新来的，在学生数据库与教师数据库中均不存在对应的数据。那么我需要先在教师数据库中保存新来的老师的数据，同时在学生数据库中保存新学生的数据，然后再给两者建立关联。
 而如果我们使用了实体关系映射，我们只需要将该新教师实体交给该学生实体，然后保存该学生实体即可完成。