---
title: SpringBoot学习3之数据库集成mybatis
date: 2016-11-03 14:05:39
tags: SpringBoot
categories: 专题
---
# SpringBoot学习3之数据库集成mybatis



作为一个Web框架，必然要与数据库打交道，这里介绍了如何将SpringBoot与mybatis进行集成的方法

<!--more-->

## 添加相关依赖

pom.xml如下

```xml
<!-- 使用数据源 -->
<dependency>
  <groupId>com.alibaba</groupId>
  <artifactId>druid</artifactId>
  <version>1.0.14</version>
</dependency>

<!-- mysql -->
<dependency>
  <groupId>mysql</groupId>
  <artifactId>mysql-connector-java</artifactId>
  <scope>runtime</scope>
</dependency>

<!-- mybatis -->
<dependency>  
  <groupId>org.mybatis.spring.boot</groupId>  
  <artifactId>mybatis-spring-boot-starter</artifactId>  
  <version>1.1.1</version>  
</dependency>
```

## 在配置文件application-dev.yml中配置

```
spring:
  datasource:
    url: jdbc:mysql://10.31.85.33:3306/School?useUnicode=true&characterEncoding=utf-8&autoReconnect=true&zeroDateTimeBehavior=convertToNull
    username: root
    password: 123456
    driver-class-name: com.mysql.jdbc.Driver
```

## 创建实体类

```java
public class User {
    private Integer id;
    private String name;
    public Integer getId() {
        return id;
    }
    public void setId(Integer id) {
        this.id = id;
    }
    public String getName() {
        return name;
    }
    public void setName(String name) {
        this.name = name;
    }
}
```

## 创建Mapper接口

```java
@Mapper 
public interface UserMapper {
    @Select("SELECT * FROM user WHERE user.user_id = #{userId}")
    @Results({
        @Result(column = "user_id", property = "id"),
        @Result(column = "user_name", property = "name")
    })
    User getById(@Param("userId") long houseId);
}
```

## 创建Service

```java
@Service
public class UserService {
    @Autowired
    private UserMapper userMapper;

	public User getById(long id) {
        return userMapper.getById(id);
    }
}
```

## Controller中调用

```java
@Autowired
private UserService userService;
@RequestMapping("/{id}")
public String getById(@PathVariable("id") int id, Model model) {
    model.addAttribute("user", userService.getById(id));
    return "show";
}
```

查询成功!


