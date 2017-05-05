---
title: SpringBoot学习2之配置文件
date: 2016-10-22 11:47:27
tags: SpringBoot
categories: 专题
---
# SpringBoot学习2之配置文件

虽然SpringBoot号称零配置，但是在实际开发过程中，我们常常需要进行一些额外信息的配置，比如数据库连接的一些参数，或者分布式服务的相关配置等等，那么这个时候就有可能用到我们本章的内容。

<!--more-->

## SpringBoot 添加自定义属性

当我们在创建一个springboot项目时，可以在其src/main/java/resources目录下创建一个名为**application.properties**的文件或者**application.yml**文件作为配置文件，springboot会默认对该名称的文件进行读取。

在application.yml中添加如下属性

```
test:
    id: 1
    name: bob
```

**注意，不要使用tab，制表符等进行缩进，请使用空格**

如果想要在程序中读取以上属性，可以使用**<font color = red>@Value(“${属性名}”)</font>**注解

```java
@Controller
public class App {

    @Value("${test.id}")
    private String id;

    @RequestMapping("/")
    public String hello(Model model) {
        model.addAttribute("id", id);
        return "hello";
    }

    public static void main(String[] args) {
        SpringApplication.run(App.class, args);
    }
}
```

## 将属性赋值给实体类

当属性非常多的时候，如果每一个都使用**@Value**注解，则过于麻烦，这个时候，我们可以根据这些属性，给他们创造一个统一的实体类，比如

```java
@ConfigurationProperties(prefix = "test")
@Component
public class ConfigBean {
    private String name;
    private int id;
    public String getName() {
        return name;
    }
    public void setName(String name) {
        this.name = name;
    }
    public int getId() {
        return id;
    }
    public void setId(int id) {
        this.id = id;
    }   
}
```

同时需要在application类添加@EnableConfigurationProperties

```java
@SpringBootApplication
@Controller
@EnableConfigurationProperties({ConfigBean.class})  
public class App {
    @Autowired
    ConfigBean configBean;

    @RequestMapping("/")
    public String hello(Model model) {
        model.addAttribute("id", configBean.getId());
        return "hello";
    }

    public static void main(String[] args) {
        SpringApplication.run(App.class, args);
    }
}
```

启动工程，信息读取成功

**@EnableConfigurationProperties**注解中可以存放多个类，用,分割，比如
```java
@EnableConfigurationProperties({ConfigBean1.class，ConfigBean2.class })
```
同时，如果你的配置信息不想放到**application.yml**这个默认的文件中，你也可以将其放到自己的配置文件中，比如该自定义配置文件叫做**”myapplication.yml”**，然后在配置实体类上额外添加注解即可。
```java
@PropertySource(value = "classpath: myapplication.yml")
```

## 多环境配置文件

在实际开发过程中，为了保证线上程序的正确性，常常使用多套配置，比如开发一套配置，测试一套配置，实际生产一套配置，而我们需要在这些多个配置之间来回切换。
我们可以通过在代码中更改@PropertySource 中的内容来进行切换，但是这样过于麻烦

其实我们可以采用如下手段：

首先，给各个开发环境进行命名
1. **application-test.yml：测试环境**
2. **application-dev.yml：开发环境**
3. **application-prod.yml：生产环境**

然后只需要在application.yml中配置

```
spring:
  profiles:
    active: dev
```

当需要切换的时候直接将active这一属性进行切换即可（test/dev/prod）

比如我们在test 中

```
server:
  port: 8081
```

在dev中
```
server:
  port: 8082
```

然后现在运行就会发现，浏览器访问地址变为了**http://localhost:8082/**

说明：只要格式按照**<font color = red>application-{环境标识}.yml</font>**或者**<font color = red>application-{环境标识}.properties</font>**即可，不一定必须是**dev/test/prod/**，以上命名只是为了方便区分。

