---
title: SpringBoot学习4之Logback使用
tags: SpringBoot
categories: 专题
abbrlink: 95f4c9d7
date: 2016-11-25 16:11:15
---





# SpringBoot学习4之Logback使用



在开发过程中，为了能够看到一些程序执行的中间结果，往往会在代码中加入一些标准输出到屏幕(Java中一般使用System.out.println)，但是由由于IO操作也是由当前线程执行，只有当当前输出语句执行完成之后，才会有继续执行下面的任务。所以会影响代码的执行效率。

而是用一些Log工具，不但可以控制日志输出，还可以控制日志输出的级别，同时不影响程序的正常执行。

本文在这里介绍了SpringBoot开发过程中经常使用到的Logback工具的简单使用

<!--more-->



## 基本使用



由于SpringBoot默认支持Logback，所以无需添加额外的引用，直接在配置文件里配置即可

在工程中新建类LogHelper，专门处理Log相关功能

```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class LogHelper {
    private final Logger logger = LoggerFactory.getLogger(this.getClass());  
    public void helpMethod(){  
        logger.debug("This is a debug message");  
        logger.info("This is an info message");  
        logger.warn("This is a warn message");  
        logger.error("This is an error message");  
    } 
}
```

在该类中，分别打印了debug , info , warn ,error 四种级别的的信息
调用该类，可以看到执行结果

![](/img/SpringBoot学习4之Logback使用/springboot1.png)

这是Logback默认配置下的显示信息，我们注意到debug级别的信息并没有打印下来

这个很容易设置，只要在application.yml中配置

```
logging:
  file: log.log  
  level:
    SpringBoot.SpringBoot : debug  
```


上面的**<font color = red>log.log</font>**是输出的日志文件名
SpringBoot.SpringBoot是包名，即你希望设置日志级别的包名，将其设置为debug
再次调用该类

![](/img/SpringBoot学习4之Logback使用/springboot2.png)

发现多出了debug信息
同时，工程目录下发现生成的日志文件

![](/img/SpringBoot学习4之Logback使用/springboot3.png)



## logback.xml文件配置



以上功能基本可以满足个人的开发，但是当我们需要更加丰富的功能时，则需要对logback.xml文件进行更加细致的配置

**//待续**

**<font color = red>如果在 logback.xml 和 application.yml 中定义了相同的配置（如都配置了 SpringBoot.SpringBoot）但是输出级别不同，则 application. yml 的优先级高于 logback.xml  </font>**



## 参考链接



[http://blog.csdn.net/sun_t89/article/details/52130839](http://blog.csdn.net/sun_t89/article/details/52130839)
[http://blog.csdn.net/sun_t89/article/details/52130839](http://412887952-qq-com.iteye.com/blog/2307244)


