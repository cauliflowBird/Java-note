# 一、JPA

> **Java Persistence API**：用于对象持久化的API
>
> JavaEE5.0 平台标准的ORM规范，使应用程序以统一的方式访问持久层

![JPA是什么](images\JPA是什么.jpg)

- **JPA 是 hibernate 的一个抽象（就像JDBC和JDBC驱动的关系）**：

–**JPA** **是规范**：JPA 本质上就是一种  ORM 规范，不是ORM 框架 —— 因为 JPA 并未提供 ORM 实现，它只是制订了一些规范，提供了一些编程的 API 接口，但具体实现则由 ORM 厂商提供实现

–**Hibernate** **是实现**：Hibernate 除了作为 ORM 框架之外，它也是一种 JPA 实现

•从功能上来说， **JPA** **是** **Hibernate** **功能**的一个子集

