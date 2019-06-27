[TOC]

## [MySQL安装配置](https://www.runoob.com/mysql/mysql-install.html)

按照网上教程顺下来就装好了

**重置密码：**非常重要

在初始化后，系统自动为我们创建了一个password，但是当我们操作数据库时，系统要求我们重置登录密码，否则无法访问。

重置方法：初始密码登陆后直接输入以下代码，就完成了！！**新密码：123456**

```
Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> ALTER USER 'root'@'localhost' IDENTIFIED BY '123456';
Query OK, 0 rows affected (0.02 sec)
```

接下来就可以与爱的操作数据库啦！~~~

参考文章：https://blog.csdn.net/baidu_32363401/article/details/81544573

## 可视化界面 安装

https://blog.csdn.net/jsnhux/article/details/80921454

## [Navicat连接MySQL问题](https://www.jianshu.com/p/81681e52da0a)

**查看MySQL连接：**select host,user,plugin,authentication_string from mysql.user;

```
mysql> select host,user,plugin,authentication_string from mysql.user;
+-----------+------------------+-----------------------+------------------------------------------------------------------------+
| host      | user             | plugin                | authentication_string                                                  |
+-----------+------------------+-----------------------+------------------------------------------------------------------------+
| localhost | mysql.infoschema | caching_sha2_password | $A$005$THISISACOMBINATIONOFINVALIDSALTANDPASSWORDTHATMUSTNEVERBRBEUSED |
| localhost | mysql.session    | caching_sha2_password | $A$005$THISISACOMBINATIONOFINVALIDSALTANDPASSWORDTHATMUSTNEVERBRBEUSED |
| localhost | mysql.sys        | caching_sha2_password | $A$005$THISISACOMBINATIONOFINVALIDSALTANDPASSWORDTHATMUSTNEVERBRBEUSED |
| localhost | root             | caching_sha2_password | $A$005$.q"OoTA!       e3L4U{NvwJJCAhXJCkBgrUeH73PEKvcxUpA0A2vQ8FmyGWy5cA7 |
+-----------+------------------+-----------------------+------------------------------------------------------------------------+
4 rows in set (0.00 sec)
```

:heavy_exclamation_mark:在安装mysql8的时候如果选择了密码加密，之后用客户端连接比如navicate，会提示客户端连接caching-sha2-password,是由于客户端不支持这种插件，可以通过如下方式进行修改： 

```
 #修改加密规则  
ALTER USER 'root'@'localhost' IDENTIFIED BY 'password' PASSWORD EXPIRE NEVER;  
 #更新密码（mysql_native_password模式）    
 ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '{NewPassword}';
```

**然后就连接成功啦！！~~~**



## 如何将数据库文件导入数据库

[查看链接](https://jingyan.baidu.com/article/a17d5285c164cc8098c8f23e.html)















