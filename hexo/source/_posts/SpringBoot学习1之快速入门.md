---
title: SpringBoot学习1之快速入门
tags: SpringBoot
categories: 专题
abbrlink: c3b398db
date: 2016-10-20 09:32:47
---
# SpringBoot学习1之快速入门

## 环境准备

Java环境
一个趁手的IDE(本文使用Eclipse)
Maven环境(可以自己安装或者使用IDE内置的maven)

<!--more-->

## 创建一个简单的Spring-boot应用

首先创建一个maven项目

![](/img/SpringBoot学习1之快速入门/springboot1.png)


![](/img/SpringBoot学习1之快速入门/springboot2.png)

创建好的项目结构如图所示

![](/img/SpringBoot学习1之快速入门/springboot3.png)

并在其pom.xml中引入如下所示：


```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <groupId>SpringBoot</groupId>

  <artifactId>SpringBoot</artifactId>
  <version>0.0.1-SNAPSHOT</version>
  <packaging>jar</packaging>

  <name>SpringBoot</name>
  <url>http://maven.apache.org</url>

  <parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>1.2.5.RELEASE</version>
    <relativePath/>
  </parent>

  <properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
  </properties>

  <dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>3.8.1</version>
      <scope>test</scope>
    </dependency>
    
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
  </dependencies>

  <build>
    <plugins>
      <plugin>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-maven-plugin</artifactId>
      </plugin>
    </plugins>
  </build>
</project>
```


重新构建项目，右键**项目**->**Maven**->**Update Project**


将**App.java**文件改写成如下所示


```java
package SpringBoot.SpringBoot;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;
@SpringBootApplication
@RestController
public class App {
	@RequestMapping("/")
	public String greeting() {
		return "Hello World!";
	}
	public static void main(String[] args) {
		SpringApplication.run(App.class, args);
	}
}
```


右键运行该项目（作为一个普通的java application运行，这也是springboot的魅力所在），亦或者输入命令：**<font color = red>mvn spring-boot:run</font>** 运行

运行成功后，访问浏览器**<font color = red>http://localhost:8080/</font>** 即可看到返回的结果


![](/img/SpringBoot学习1之快速入门/springboot4.png)


程序的入口是：**SpringApplication.run(App.class, args)**; 其中**SpringApplication**可以暂时当作一个主类来看，这里先不细说，Spring Boot会判断出这是一个web应用，并启动一个内嵌的Servlet容器，一般默认是Tomcat。



## 注解说明

### @Controller

@Controller 注解的类会被当作一个Controller，专门用来处理不同请求不同的URL，从而有针对性的返回内容。


### @RestController

@RestController 注解整合了**@Controller**和**@ResponseBody**。使用了这个注解的类会被当作一个Controller,而Controller中的方法**无法返回jsp页面**，视图解析器将不起作用，比如返回 return”success”;本来是应该返回到success.jsp页面的，但是现在只会**返回一个字符串**。

上文中的@RestController如果替换成@Controller则会提示找不到名为“Hello World!”的页面。


所以一般不要用@RestController注解修饰某个类，一般在实际使用时，可以选择使用@Controller注解类，在该类的方法中有选择的使用@ResponseBody,这样的话，当某个方法需要返回的是一个视图的时候就不需要添加@ResponseBody，当需要返回的是一个json数据或者Xml等时，可以选择添加@ResponseBody


### @RequestMapping


@RequestMapping 注解是用来处理请求地址映射的注解，可以用在类或方法上，如果用在类上，则表示所有该类中的响应方法都是以该地址作为父路径。


```java
@RestController
@RequestMapping("/root")
public class App {
	@RequestMapping("/")
	public String hello() {
		return " Hello World1!";
	}//该方法的访问路径是http://localhost:8080/root
	@RequestMapping("/hello2")
	public String hello2() {
		return "Hello World2!";
	}//该方法的访问路径是http://localhost:8080/root/hello2
}
```


在实际开发中，如果对于不同Id的用户进行不同的处理，常常需要在URL中加入变量以进行区分，@RequestMapping也提供类似的方法


```java
@RequestMapping("/hello/{id}")
public String hello(@PathVariable("id") int id) {
	return return “Hello :” + id;
}
```


其中**<font color = red>{id}</font>**就是一个占位符，可以替换成实际的id，当请求路由到该方法进行处理时，该方法根据注解**<font color = red>@PathVariable</font>**取出名为”id”的值，并赋予给 int id，这样你在方法中就可以将id作为一个局部变量进行使用


在实际应用中，不同的Http方法，在处理过程中可能也要进行分别处理，@RequestMapping也提供了相应的解决办法


```java
@RequestMapping(value = "/hello", method = RequestMethod.GET)
public String helloGet() {
	return "hello get";
}
@RequestMapping(value = "/hello", method = RequestMethod.POST)
public String helloPost() {
	return "hello post";
}
```


即使两个方法处理的URL是一样的，但是由于访问的http方法不一样，所以依然会路由到不同的方法分别进行处理


## 模板渲染


当使用@Controller注解某个类时，某个URL路由方法中的返回值字符串，将不再直接返回到前端进行显示，而是将寻找名为该字符串的模板进行渲染


```java
@Controller
public class App {
    @RequestMapping("/hello/{id}")
    public String hello(@PathVariable("id") String id, Model model) {
        model.addAttribute("id", id);
        return "hello"
    }
}
```


上述例子中将寻找名为hello的模板进行渲染。


在这里我们使用**<font color = red>thymeleaf</font>**模板引擎进行渲染。


首先在pom.xml中引入依赖


```xml
<dependency>
	<groupId>org.springframework.boot</groupId>
  	<artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>
```


接下来创建资源文件夹（source folder） **<font color = red>src/main/resources</font>**,并在该目录下创建普通文件夹templates，并在该文件夹下创建模板文件hello.html


```html
<!DOCTYPE HTML>
<html xmlns:th="http://www.thymeleaf.org">
<head>
	<title>Hello SomeOne</title>
	<meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
</head>
<body>
	<p th:text="'Hello, ' + ${id} + '!'" />
</body>
</html>
```

**<font color = red>th:text="'Hello, ' + ${id} + '!'" </font>**也就是将我们之前在@Controller方法里添加至Model的属性id进行渲染，并放入<p>标签中（因为th:text是<p>标签的属性）。
