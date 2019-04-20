---
title: SpringBoot学习5之自定义注解
tags: SpringBoot
categories: 专题
abbrlink: f3a05c35
date: 2016-12-01 11:54:30
---

# SpringBoot学习5之自定义注解

在springboot中经常用到一些注解，包括我们在前文中已经遇到的@Controller, @RestController, @RequestMapping等等，这些注解用起来简单，方便。
那我们能不能自定义一些注解呢？自定义注解的实现其实是springboot对aop的支持。



<!--more-->

## 自定义注解步骤

#### 1. 添加依赖

```xml
<dependency>
	<groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-aop</artifactId>
</dependency>
```

#### 2. 编写自定义注解类

```java
@Target({ ElementType.METHOD, ElementType.TYPE })
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface MyLog {
    String value();
}
```

#### 3. 编写注解类切面

```java
@Aspect
@Component
public class  OperateAspect {
    @Pointcut("@annotation(SpringBoot.SpringBoot.annotation.OperateLog)")
    public  void annotationPointCut() {
    }

    @Before("annotationPointCut()")
    public void before(JoinPoint joinPoint){
       MethodSignature sign =  (MethodSignature)joinPoint.getSignature();
       Method method = sign.getMethod();
       OperateLog annotation = method.getAnnotation(OperateLog.class);
       System.out.print("打印："+annotation.value()+" 前置日志");
    }
}

```


#### 4. 添加对aspect的支持

```java
@SpringBootApplication
@Controller
@EnableAspectJAutoProxy
public class App {
    public static void main(String[] args) {
        SpringApplication.run(App.class, args);
    }
}
```

#### 5. 在controler层使用注解

```java
@RequestMapping("/")
@ResponseBody
@OperateLog("测试")
public String getById() {
    return "hello";
}
```

#### 6. 打开浏览器，调用该接口，控制台输出

![](/img/SpringBoot学习5之自定义注解/springboot1.png)



## 注解解释



#### @Target

@Target  说明了Annotation所修饰的对象范围

取值(ElementType)有：
　　　　1.CONSTRUCTOR:用于描述构造器
　　　　2.FIELD:用于描述域
　　　　3.LOCAL_VARIABLE:用于描述局部变量
　　　　4.METHOD:用于描述方法
　　　　5.PACKAGE:用于描述包
　　　　6.PARAMETER:用于描述参数
　　　　7.TYPE:用于描述类、接口(包括注解类型) 或enum声明

#### @Retention

@Retention定义了该Annotation被保留的时间长短：某些Annotation仅出现在源代码中，而被编译器丢弃；而另一些却被编译在class文件中；编译在class文件中的Annotation可能会被虚拟机忽略，而另一些在class被装载时将被读取（请注意并不影响class的执行，因为Annotation与class在使用上是被分离的）。使用这个meta-Annotation可以对 Annotation的“生命周期”限制。
取值（RetentionPoicy）有：
　　　　1.SOURCE:在源文件中有效（即源文件保留）
　　　　2.CLASS:在class文件中有效（即class保留）
　　　　3.RUNTIME:在运行时有效（即运行时保留）

#### @Documented

@Documented用于描述其它类型的annotation应该被作为被标注的程序成员的公共API，因此可以被例如javadoc此类的工具文档化。Documented是一个标记注解，没有成员。

#### @Aspect

@Aspect作用是把当前类标识为一个切面供容器读取

#### @Pointcut

@Pointcut定义一个切点

#### @Before

@Before标识一个前置增强方法，相当于BeforeAdvice的功能

#### @component 

@component 把普通pojo实例化到spring容器中，相当于配置文件中的<bean id="" class=""/>


## 参考链接

[http://www.cnblogs.com/gmq-sh/p/4798194.html](http://www.cnblogs.com/gmq-sh/p/4798194.html)

[https://my.oschina.net/couples/blog/846732](https://my.oschina.net/couples/blog/846732)

