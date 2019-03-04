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

![](https://www.javatpoint.com/images/hibernate/architecture.jpg)

> JDBC (Java Database Connectivity)
>
> JTA (Java Transaction API) 
>
> JNDI (Java Naming Directory Interface)

### Elements of Hibernate Architecture

- SessionFactory

  > SessionFactory is a thread-safe object, many threads cannot access it simultaneously.

- Session

  > - It maintains a connection between the hibernate application and database.It provides methods to store, update, delete or fetch data from the database such as persist(), update(), delete(), load(), get() etc.
  > - Session is not a thread-safe object, many threads can access it simultaneously. In other words, you can share it between threads.

- Transaction

- ConnectionProvider

- TransactionFactory

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
3. Many to One
4. Many to Many