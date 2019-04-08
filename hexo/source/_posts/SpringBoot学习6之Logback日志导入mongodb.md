---
title: SpringBoot学习6之Logback日志导入mongodb
date: 2016-12-16 14:27:17
tags: [SpringBoot, Logback, mongodb]
categories: 专题
---

# SpringBoot学习6之Logback日志导入mongodb

正如前文所说，springboot自带logback作为其日志新系统，但是在实际工作中，我们常常需要对日志进行管理或分析，如果只是单纯的将日志导入文本文件，则在查询时操作过于繁琐，如果将其导入mysql等关系型数据库进行存储，又太影响系统性能，同时由于Mysql其结构化的信息存储结构，导致在存储时不够灵活。因此，本文在此考虑将springboot系统中产出的日志(logback) 存入mongodb中。

<!--more-->

## 实现步骤

#### 1. 添加依赖

```xml
<dependency>
  <groupId>ch.qos.logback</groupId>
  <artifactId>logback-core</artifactId>
  <version>0.9.28</version>
</dependency>
<dependency>
  <groupId>ch.qos.logback</groupId>
  <artifactId>logback-classic</artifactId>
  <version>0.9.28</version>
</dependency>
<dependency>
  <groupId>org.mongodb</groupId>
  <artifactId>mongo-java-driver</artifactId>
  <version>3.4.2</version>
</dependency>
```

#### 2. 在项目资源文件下src/main/resources下添加logback.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE xml>
<configuration>
    <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <charset>UTF-8</charset>
            <pattern>%d [%thread] %-5level %logger{68} %line - logId[[%X{client}][%X{request_id}]] - %msg%n</pattern>
        </encoder>
    </appender>

    <appender name="MONGODB" class="SpringBoot.SpringBoot.log.MongoDBAppender">
    </appender>

     <root level="INFO">
         <appender-ref ref="STDOUT" />
         <appender-ref ref="MONGODB" />
     </root>

</configuration>
```

上文中分别配置了mongodb的appender, 并起名为MONGODB，并启用

#### 3. 在项目Springboot.Springboot包下创建类MongoDBAppender

```java
public class MongoDBAppender extends UnsynchronizedAppenderBase<LoggingEvent> {
    private MongoClient  mongo;
    private String dbHost = "localhost";
    private int dbPort = 27017;
    private String dbName = "logging";
    private String colName = "logs";
    private MongoCollection<Document> _collection;

    @Override
    public void start() {
        try {
            mongo = new MongoClient (dbHost, dbPort);
            MongoDatabase  db = mongo.getDatabase(dbName);
            _collection = db.getCollection(colName);
        } catch (Exception e) {
            addStatus(new ErrorStatus("Failed to initialize MondoDB", this, e));
            return;
        }
        super.start();
    }
    public void setDbHost(String dbHost) {
        dbHost = this.dbHost;
    }
    public void setDbName(String dbName) {
        dbName = this.dbName;
    }
    public void setDbPort(int dbPort) {
        dbPort = this.dbPort;
    }
    @Override
    public void stop() {
        mongo.close();
        super.stop();
    }

    @Override
    protected void append(LoggingEvent e) {
        Document document = new Document().
                append("ts", new Date(e.getTimeStamp())).
                append("msg", e.getFormattedMessage()).
                append("level", e.getLevel().toString()).
                append("logger", e.getLoggerName()).
                append("thread", e.getThreadName());
        if(e.hasCallerData()) {
            StackTraceElement st = e.getCallerData()[0];
            String callerData = String.format("%s.%s:%d", st.getClassName(), st.getMethodName(), st.getLineNumber());
            document.append("caller", callerData);
        }
        _collection.insertOne(document);
    }
}
```

#### 4. 启动Mongodb服务器，运行项目

![](/img/SpringBoot学习6之Logback日志导入mongodb/springboot1.png)

访问数据库发现，已经成功将日志插入


## 有待解决问题

1. 系统启动速度巨慢无比，有待解决
2. 有关数据库配置的问题，可以使用springboot的文件配置标签进行配置，将相关信息统一放在application.yml文件中，这样避免将数据库配置信息直接写在代码中，降低代码耦合性（具体详情请查阅[SpringBoot学习2之配置文件](https://darylliu.github.io/2016/10/22/SpringBoot%E5%AD%A6%E4%B9%A02%E4%B9%8B%E9%85%8D%E7%BD%AE%E6%96%87%E4%BB%B6/#more)）

## 问题解决

为了解决以上出现的两个问题，本人通过调研一些资料发现了解决办法，但是并没有弄清具体问题出现的原因。

### 问题1：启动速度慢

因为之前调用Mongodb是自己添加的java驱动，实际上springboot有自己内置的mongodb接口，可以直接使用， 我们使用这套方法，执行效率几乎不受任何影响

#### 1.	替换依赖，将之前的依赖

```xml
<dependency>
  <groupId>org.mongodb</groupId>
  <artifactId>mongo-java-driver</artifactId>
  <version>3.4.2</version>
</dependency>
```

替换成

```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-data-mongodb</artifactId>
</dependency>
```


#### 2.	添加实体类

```java
public class MyLog {
    @Id
    private String id;
    private Date ts;
    private String level;
    private String msg;
    private String thread;
    
	public String getId() {
		return id;
	}
	public void setId(String id) {
		this.id = id;
	}
	public Date getTs() {
		return ts;
	}
	public void setTs(Date date) {
		this.ts = date;
	}
	public String getLevel() {
		return level;
	}
	public void setLevel(String level) {
		this.level = level;
	}
	public String getMsg() {
		return msg;
	}
	public void setMsg(String msg) {
		this.msg = msg;
	}
	public String getThread() {
		return thread;
	}
	public void setThread(String thread) {
		this.thread = thread;
	}   
}
```

@Id  该标签对应Mongodb中的_id

#### 3. 添加数据访问接口

```java
public interface LogRepository extends MongoRepository<MyLog, String> {
}
```

#### 4.	修改appender类

```java
@Component
public class MongoDBAppender extends UnsynchronizedAppenderBase<LoggingEvent>  implements ApplicationContextAware {
    private static LogRepository logRepository;
    @Override
    public void start() {
        super.start();
    }

    @Override
    public void stop() {
        super.stop();
    }

    @Override
    protected void append(LoggingEvent e) {

        MyLog myLog = new MyLog();
        myLog.setLevel(e.getLevel().toString());
        myLog.setMsg(e.getFormattedMessage());
        myLog.setThread(e.getThreadName());
        myLog.setTs(new Date(e.getTimeStamp()));
        logRepository.save(myLog);
    }
    @Override
    public void setApplicationContext(ApplicationContext applicationContext) {
        if (applicationContext.getAutowireCapableBeanFactory().getBean(LogRepository.class) != null) {
        	logRepository = (LogRepository) applicationContext.getAutowireCapableBeanFactory().getBean(LogRepository.class);
        }
    }
}
```

有的人看到这里可能疑惑了，为什么不直接使用@Autowired注解，直接将LogRepository注入到代码里，而非要使用实现ApplicationContextAware 接口的方法，重写public void setApplicationContext(ApplicationContext applicationContext) 手动注入呢?

这里本人遇到了一个坑，刚开始确实采用了@Autowired方法尝试注入，后来发现在日志读取过程中，logRepository一直是null,后来经过调查发现，**<font color = red>logback 的appender 在spring加载类之前创建，所以也即是说，在spring加载类之前该类已经投入使用，所以没有办法注入logRepository</font>**

为了解决这个问题，只能“手动”注入了，将其声明为静态变量，并采用懒加载的方式进行加载。

### 问题2：数据库信息配置

在application.yml中配置如下即可
```
spring:
  data:
    mongodb:
      uri: mongodb://localhost:27017/logging
```

这里也可以使用带密码形式的设置
```
spring:
  data:
    mongodb:
      uri: mongodb://username:password@localhost:27017/logging
```

但是由于Mongdb进入3.0版本后更改了其默认认证方式，而springboot依然停留在老版本，所以虽然账号密码都输入正确，但是依然无法登陆成功。
在这里我们可以修改mongodb的默认认证方式
解决办法参考了解
[http://blog.csdn.net/wlzx120/article/details/52311777](http://blog.csdn.net/wlzx120/article/details/52311777)

