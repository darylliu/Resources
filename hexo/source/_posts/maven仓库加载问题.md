---
title: maven仓库加载问题
date: 2016-03-12 10:43:23
tags: maven
categories: 笔记
---

# maven仓库加载问题


## 前言

maven是当前非常流行的项目管理工具，各大公司几乎都在使用，然而本人在使用maven的过程中总是或多或少遇到一些问题，特此整理并记录下来，方便以后再次遇到这些问题时能够及时解决。

<!--more-->

## maven仓库介绍

### 本地仓库

本地仓库在本人看来就是一个类似于缓存的地方，当构建新的项目时，根据pom.xml文件中的依赖去远程仓库下载jar包，然后将其存放在本地仓库，当下次再去构建项目时，首先会到本地仓库中查找是否已存在相关依赖，如果有则直接添加依赖，如果没有则再去远程仓库下载。

本地仓库的路径

```
用户路径/.m2/repository
```

该路径可以通过maven的配置文件setting.xml进行修改

```
<settings>
	<localRepository>新的本地仓库地址</localRepository>  
</settings> 
```

### 远程仓库

#### 中央仓库

中央仓库就是在安装maven默认的远程仓库，当构建新的项目时，会去该仓库地址进行下载相关依赖。该仓库是在maven的超级pom文件中进行配置。

```
<repositories>
	<repository>
		<id>central</id>
		<url>http://repo1.maven.org/maven2</url>
	</repository>
</repositories>
```

这里要记住的一点是中央仓库的id是**central**, url是**http://repo1.maven.org/maven2**
这是一个国外的地址，下载的时候速度会比较慢。

#### 私服

假如你到了一个公司，参与到他们的项目中进行开发，他们内部往往也有一些jar包需要依赖，而这些依赖由于并未对外开放，放在了公司内部的局域网上，所以在中央仓库中肯定是找不到的，在这种情况下你不得不添加该局域网远程仓库的地址。

在项目pom.xml文件中

```
<repositories>
	<repository>
		<id>公司仓库id</id>
		<name>公司仓库名称</name>
		<url>公司仓库地址</url>
		<releases>
			<enabled>true</enabled>
		</releases>
		<snapshots>
			<enabled>false</enabled>
		</snapshots>
	</repository>
</repositories>
```

## maven使用中的问题

### 1. 速度太慢	

正如前文所说，由于maven默认的中央仓库是国外的地址，由于某些原因，访问起来速度奇慢无比，如果加载的依赖比较少，还可以勉强使用，但是项目一旦稍微大点，则非常影响使用（本人深有体会），所以这里推荐使用镜像。

使用国内的一些镜像仓库，可以在setting.xml中进行如下设置

```
<mirrors>
	<mirror>
		<id>alimaven</id>
		<name>aliyun maven</name>
		<url>http://maven.aliyun.com/nexus/content/groups/public/</url>
		<mirrorOf>central</mirrorOf>
	</mirror>
</mirrors>
```

其中的**<mirrorOf>central</mirrorOf>**意思是指对于所有访问central(默认中央仓库的id，前文有提到)的请求，都将被拦截，并从本镜像仓库寻找相关的依赖包返还给用户。

在这里**<mirrorOf>central</mirrorOf>** 也可以写成**<mirrorOf>*</mirrorOf>**,表示的是对于所有的请求，都将被拦截。

### 2. Missing artifact

问题  **Cound not transfer artifact xxxxxxx......**

同样是由于maven默认的中央仓库是国外的地址，导致在下载过程中网络不佳，经常出现一些超时或者其他问题，按说这种情况进行重试即可，然而实际会在本地仓库生成一份以lastUpdated结尾的文件。

解决办法：打开~/.m2/repository文件夹，找到该artifact文件夹，删除，然后重新构建。
本人建议：由于经常会出现很多的这种错误，所以往往几次删除之内很难解决问题，所以本人在使用最初都是直接把repository文件夹整个的删除（太年轻），后来发现其实只要换成国内镜像，几乎就再也不会出现这个问题，推荐更换镜像。

## maven仓库编译顺序

本地仓库 > pom中的repository > mirror


