---
title: 常用Linux命令总结
tags: Linux
categories: 笔记
abbrlink: 406cb8db
date: 2015-07-01 17:57:27
---

# 常用Linux命令总结



由于有的时候需要在Linux/Mac系统上进行工作，然而有的命令隔一段时间就会忘记，所以特此总结在此，方便查阅和记忆

文中很多内容都是网上参考别人的文章，具体链接在文末

<!--more-->

## 常用命令

```shell
ls     显示文件或目录
   -l  列出文件详细信息l(list)
   -a  列出当前目录下所有文件及目录，包括隐藏的a(all)
mkdir  创建目录
   -p  创建目录，若无父目录，则创建p(parent)   
cd     切换目录
touch  创建空文件
echo   创建带有内容的文件。
cat    查看文件内容
cp     拷贝
mv     移动或重命名
rm     删除文件
   -r  递归删除，可删除子目录及文件
   -f  强制删除
find   在文件系统中搜索某文件
wc     统计文本中行数、字数、字符数
grep   在文本文件中查找某个字符串
rmdir  删除空目录
pwd    显示当前目录
ln     创建链接文件
more、less   分页显示文本文件内容
head、tail   显示文件头、尾内容
```

## 系统管理命令

```shell
stat     显示指定文件的详细信息，比ls更详细
who      显示在线登陆用户
whoami   显示当前操作用户
hostname 显示主机名
uname    显示系统信息
top      动态显示当前耗费资源最多进程信息
ps       显示瞬间进程状态 ps -aux
du       查看目录大小 du -h /home带有单位显示目录信息
df       查看磁盘大小 df -h 带有单位显示磁盘信息
ifconfig 查看网络情况
ping     测试网络连通
netstat  显示网络状态信息
man      命令不会用了，找男人? 如：man ls
clear    清屏
alias    对命令重命名 如：alias showmeit=”ps -aux” ，另外解除使用unaliax showmeit
kill     杀死进程，可以先用ps 或 top命令查看进程的id，然后再用kill命令杀死进程。
```



## 关机/重启机器

```shell
shutdown
    -r      关机重启
    -h      关机不重启
    now     立刻关机
halt        关机
reboot      重启
```



## 参考链接

[http://www.weixuehao.com/archives/25](http://www.weixuehao.com/archives/25)
