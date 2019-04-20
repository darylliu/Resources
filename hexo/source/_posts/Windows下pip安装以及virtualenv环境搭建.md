---
title: Windows下pip安装以及virtualenv环境搭建
tags: Python
categories: 笔记
abbrlink: b27d46ae
date: 2015-01-16 14:44:34
---

# Windows下pip安装以及virtualenv环境搭建

1. 确保已安装python环境
2. 进入python的官网下载工具，网址如下：https://pypi.python.org/pypi/setuptools

<!--more-->

![](/img/Windows下pip安装以及virtualenv环境搭建/windows1.png)

3. 找到ez_setup.py，点击下载，或者点击将其中内容进行复制，在本地创建文件ez_setup.py，将内容粘贴进去。

4. 双击运行下载好的文件，运行成功后，进入Python安装目录下Scripts文件夹下，发现easy_install以及pip相关文件已正常添加。

![](/img/Windows下pip安装以及virtualenv环境搭建/windows2.png)

5. 为了方便在cmd下使用常用命令，将Scripts文件夹路径添加到环境变量中，假设你在缺省路径安装了 Python 2.7 ，那么就应该添加如下内容:

	;C:\Python27\Scripts
	
6. 至此pip/easy_install安装完毕。

7. 打开cmd窗口，切换到合适路径下，输入命令:
	
	pip install virtualenv 
	
8. 安装完毕后发现Scripts目录下，virtualenv相关文件已正常添加

![](/img/Windows下pip安装以及virtualenv环境搭建/windows3.png)

9. 至此全部环境搭建成功~

10. 进入一个希望创建虚拟Python环境的文件夹下，输入命令：

	virtualenv venv
cmd 的当前目录下面多了一个 venv 文件夹,这个文件夹保存着当前Python虚拟环境。

11. virtualenv命令使用

	开启：

		\venv\Scripts\activate
	关闭：

		\venv\Scripts\deactivate
		
    查看当前虚拟环境配置信息

 		\venv\Scripts\activate
 		pip list
	
