# Mybatis 笔记

- JDBC连接
- mybatis框价

### 1从jdbc开始

#### 1.1JDBC连接数据库（以mysql数据库为例）

> JDBC的连接步骤
>
> 1. 注册驱动
> 2. 创建连接
> 3. 创建PreparedStatement对象 
> 4. 设置参数
> 5. 执行sql并返回值
> 6. 关闭连接

```java
public static void main(String[] args) throws Exception {
    Connection connection=null;
    PreparedStatement preparedStatement=null;
    ResultSet resultSet=null;
    //    1.注册驱动
    Class.forName("com.mysql.jdbc.Driver");
    //    2.获取连接
    String url="jdbc:mysql://localhost:3306/demo";
    String userName="root";
    String passWord="";
    connection= DriverManager.getConnection(url,userName,passWord);
    //    3.preparedStatement
    String sql="select * from user where loginName= ? ";
    preparedStatement=connection.prepareStatement(sql);
    //    4.设置参数
    preparedStatement.setString(1,"ceshi");
    //    5.执行sql并且返回值
    resultSet=preparedStatement.executeQuery();
    while(resultSet.next()){
        System.out.println(resultSet.getString("id"));
        System.out.println(resultSet.getString("realName"));
        System.out.println(resultSet.getString("loginName"));
        System.out.println(resultSet.getDate("createTime"));
    }
     //    6.关闭所有连接（释放资源）
       resultSet.close();
       preparedStatement.close();
       connection.close();
    
}
```

#### 1.2JDBC缺点分析

> 缺点
>
> 1. 每次都需要注册驱动
> 2. 代码太过耦合（sql 写在代码里面）
> 3. 参数类型需要手动判断
> 4. 需要手动设置参数
> 5. 结果集类型需要判断
> 6. 每次都需要打开和关闭连接

### 2.mybatis

#### 2.1Mybatis介绍

MyBatis 是一款优秀的持久层框架，它支持自定义 SQL、存储过程以及高级映射。MyBatis 免除了几乎所有的 JDBC 代码以及设置参数和获取结果集的工作。MyBatis 可以通过简单的 XML 或注解来配置和映射原始类型、接口和 Java POJO（Plain Old Java Objects，普通老式 Java 对象）为数据库中的记录。 

官网地址 <https://mybatis.org/mybatis-3/zh/getting-started.html> 

#### 2.2Mybatis的整体架构

![img](https://img-blog.csdn.net/20180701220233514?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3pwY2FuZHpoag==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70) 



![img](https://img-blog.csdn.net/20180701220241831?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3pwY2FuZHpoag==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70) 

#### 2.3Mybatis入门

##### 2.3.1引入依赖

```xml
<dependency> 
 <groupId>org.mybatis</groupId>
 <artifactId>mybatis</artifactId>
 <version>3.2.8</version> 
</dependency> 
```

##### 2.3.2全局配置文件（mybatis-config.xml）

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
  PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-config.dtd">
<!-- 根标签 -->
<configuration>
<properties>
	<property name="driver" value="com.mysql.jdbc.Driver"/>
	<property name="url" value="jdbc:mysql://127.0.0.1:3306/mybatis-110?useUnicode=true&amp;characterEncoding=utf-8&amp;allowMultiQueries=true"/>
	<property name="username" value="root"/>
    	<property name="password" value="123456"/>
   </properties>
   <!-- 环境，可以配置多个，default：指定采用哪个环境 -->
   <environments default="test">
      <!-- id：唯一标识 -->
      <environment id="test">
         <!-- 事务管理器，JDBC类型的事务管理器 -->
         <transactionManager type="JDBC" />
         <!-- 数据源，池类型的数据源 -->
         <dataSource type="POOLED">
            <property name="driver" value="com.mysql.jdbc.Driver" />
            <property name="url" value="jdbc:mysql://127.0.0.1:3306/mybatis-110" />
            <property name="username" value="root" />
            <property name="password" value="123456" />
         </dataSource>
      </environment>
      <environment id="development">
         <!-- 事务管理器，JDBC类型的事务管理器 -->
         <transactionManager type="JDBC" />
         <!-- 数据源，池类型的数据源 -->
         <dataSource type="POOLED">
            <property name="driver" value="${driver}" /> <!-- 配置了properties，所以可以直接引用 -->
            <property name="url" value="${url}" />
            <property name="username" value="${username}" />
            <property name="password" value="${password}" />
         </dataSource>
      </environment>
   </environments>
  </configuration>
```

