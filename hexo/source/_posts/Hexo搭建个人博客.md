---
title: hexo搭建个人博客
date: 2017-04-21 10:43:23
tags: hexo
---

# hexo搭建个人博客


## 前言

最近突然心血来潮想要搭建自己的博客，就去调研了几种方案：

* 租个服务器。在上面搭建网站，数据库，优点是可以自己随时备份修改，很方便；缺点是花销比较大，不适合学生党，同时需要自己维护，而自己主要目的是去写文章，而没有精力去维护，所以这种方案Pass.
* 使用国内CSDN, 博客园都知名博客网站。这种方法的优点是随时随地都可以写文章，而且只要专注于文章就可以，不用考虑维护的事情，缺点的话就是他们的网站总是掺杂着各种广告，视觉效果。。。差强人意。
* 使用github, 利用一些现有的博客平台，比如node.js的Hexo，直接将本地.md文档生成静态的html文件作为静态页面，并将其部署到gihub pages服务器上。本文采用了这种方法。

<!--more-->

## 环境

* 安装git
* 安装node.js
* 安装hexo

#### 安装 hexo
1. 新建目录blog(此处存放你的博客工程)
2. 进入该目录下并执行命令
``` 
npm install -g hexo-cli
```
3. 在blog目录下新建目录hexo，并在里面进行hexo初始化
``` 
hexo init hexo
```
4. 等一切安装成功之后，执行命令
``` 
hexo server
```
出现下图所示，即为成功
![](/img/hexo1.png)
可以看到已经成功运行在了本地
``` 
http://localhost:4000/
```
打开网站即可预览到我们的页面

### Hexo 配置

#### _config.yml 文件配置
关于这个配置文件里的内容，官网有详细介绍，本人在这里只介绍比较重要的几个：

1. deploy, 配置这部分是为了能够将博客内容发布到线上。

        deploy:
            type: git
            repository: git@github.com:你的git用户名/你的git用户名.github.io.git 
            branch: master

2. url, 由于在blog下又建立了一个hexo,所以如果实在本地的话需要将url设置成

        url: http://你的git网址/hexo
        root: /hexo/ 
但是当你上传到服务器时，需要改回

        url: http://你的git网址/
        root: /

3. theme, 主题设置
在hexo路径下有一个叫做themes的文件夹，里面存放了你所拥有的themes主题模版文件，这些模版都可以从git上进行下载，刚开始创建项目的时候默认是是landscape。如果想要更改博客主题，只要把对应的主题模版下载到该目录并将_config.yml中的themes设置如下

        theme: landscape(你下载的模版文件夹名)

### 相关命令

#### 静态文件生成

每次修改博客或配置文件后记得执行以下命令进行更新

    hexo generate
    
#### 部署到服务器

部署到远程服务器执行以下命令

    hexo deploy
    
如果失败，有可能是因为没有安装git部署器，即hexo/node_moudles下没有hexo-deployer-git，如果是这种情况，可以进入hexo后执行以下命令

    npm install hexo-deployer-git –save


### 配置过程中遇到的坑

#### 权限问题
在往git上部署过程中一直出现

`Error: Permission denied (publickey). fatal: Could not read from remote repository.`

后来经过在网上查了很多资料发现有两种可能的原因：

1. git版本过高，将版本改成git 1.9版本的即可解决(但是本人没有解决，但是对其他很多人有效)
2. 由于当时生成公钥的命令是

        ssh-keygen -t rsa -C “你的git用户名@github.com”
    没有加sudo, 生成的公钥是当前用户的，然而，自己在部署到服务器时为了避免文件访问权限问题直接使用了
    
        sudo hexo deploy
    该命令在执行的时候会去读取root 用户的公钥，然而显然，root用户下还没有生成对应的公钥信息，所以一直会报公钥权限问题。
    解决方法是生成公钥的时候也在前面加上sudo
        
        sudo ssh-keygen -t rsa -C “你的git用户名@github.com”
     
#### hexo generate问题
在hexo generate中出现了        
	
`TypeError: Cannot read property 'offset' of null`

原因是因为hexo 目录下的_confing.yml 和 主题模版文件中的_confing.yml 有关时区配置不一致或者主题模版文件中没有配置时区

解决办法如下：
	
	timezone: Asia/Shanghai	        
    
### 建议

#### 备份
由于git上上传的是通过hexo生成的静态文件，并不是原生的.md文件，这意味着一旦你的本地工程文件丢失，很有可能无法找回你的文章。所以本人建议将/hexo/source目录下的文件进行备份，无论是自己本地备份还是上传的git备份都是可行的办法。
    
### 扩展

#### 打赏功能
在主题模版配置文件中，添加如下：

	reward_comment: 坚持原创技术分享，您的支持将鼓励我继续创作！
	wechatpay: /图片路径/微信支付二维码图片
	alipay: /图片路径/支付宝支付二维码图片
	
#### 阅读全文
有的时候我们希望在主页不要全部显示文章内容，希望在合适的地方截断，并提示显示阅读全文，如下图所示
	![](/img/hexo2.png)
我们可以在文章内部希望截断的地方添加

	//主页显示内容
	<!--more-->
	//隐藏内容

