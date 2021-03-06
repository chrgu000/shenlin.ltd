---
title: MySQL - InnoDB 存储引擎
categories:
  - MySQL
tags:
  - MySQL
---

MySQL InnoDB 存储引擎

<!--more-->

--------------------------------------数据库和数据库实例------------------------

数据库 database：是一个个文件的集合，是按照某种数据模型组织起来并存放在存储器中
的数据集合。

实例 instance：数据库实例是应用程序，由数据库后台进程/线程和共享内存区组成，数据
库实例是真正用来操作数据库文件的，其他程序或用户都只能通过数据库实例才能和数据库
打交道。

MySQL是单进程多线程的架构设计，所以MySQL数据库实例在系统上的的表现就是一个进程。

MySQL的实例和数据库通常是一一对应的，但在集群的情况下可能存在一个数据库被多个实
例使用的情况。


Default options are read from the following files in the given order:
C:\Windows\my.ini C:\Windows\my.cnf C:\my.ini C:\my.cnf C:\wamp\bin\mysql\mysql5
.6.12\my.ini C:\wamp\bin\mysql\mysql5.6.12\my.cnf

MVCC: 多版本并发控制


--------------------------------------MySQL体系结构-----------------------------



--------------------------------------MySQL表存储引擎---------------------------



--------------------------------------连接MySQL---------------------------------

连接MySQL是将连接进程和MySQL数据库实例进行通信，本质上是进程通信。

常用的进程通信方式：管道、命名管道、命名字、TCP/IP套接字、Unix域名套接字

    TCP/IP套接字：是网络中使用的最多的一种方式，也是MySQL在任何平台上都提供的连
    接方式，一般情况下客户端和服务器部署在不同的机器上。

                  mysql -h192.168.0.101 -u david -p

                  通过TCP/IP套接字连接MySQL实例时，MySQL会先检查mysql库下的user
                  表，确认权限

                        mysql> select host,user,password from use
                        +-----------+------+----------+
                        | host      | user | password |
                        +-----------+------+----------+
                        | localhost | root |          |
                        | 127.0.0.1 | root |          |
                        | ::1       | root |          |
                        | localhost |      |          |
                        +-----------+------+----------+
                        4 rows in set (0.00 sec)

    命名管道：windows2000以后的windows系统，如果需要通信的进程在同一台服务器上，
    可以使用命名管道。

              SQLServer默认安装后的本地连接也使用命名管道的通信方式，需在配置文
              件中启用--enable-named-pipe选项

    共享内存：MySQL4.1之后的版本，需要通信的进程在同一台服务器上，则可使用共享内
    存的通信方式。

              在配置文件中添加--shared-memory，客户端使用-protocol=memory选项

    Unix域套接字：Linux或Unix环境下，如果需要通信的进程在同一台服务器上，可以使
    用Unix域套接字。

                  Unix域套接字不是网络协议，只能是通信双方在同一台服务器上时使用
                  。

                  可以在配置文件中指定套接字文件的路径，如：
                  -socket=/tmp/mysql.sock

                  首先使用下列命令查找Unix域套接字文件：

                        mysql> show variables like 'socket';
                        +---------------+-----------------+
                        | Variable_name | Value           |
                        +---------------+-----------------+
                        | socket        | /tmp/mysql.sock |
                        +---------------+-----------------+
                        1 row in set (0.00 sec)
                    
                  然后使用文件进行连接：

                    C:\wamp\bin\mysql\mysql5.6.12\bin>mysql -uroot -S /tmp/mysql.sock
                    Welcome to the MySQL monitor.  Commands end with ; or \g.
                    Your MySQL connection id is 2
                    Server version: 5.6.12-log MySQL Community Server (GPL)                        

                    ???上面的是windows下也可以使用

                    ???上面的查找套接字文件命令是在已经登录mysql的情况下了

--------------------------------------备份与恢复--------------------------------

按备份方法分为：Hot Backup（热备）  Cold Backup（冷备） Warm Backup（温备）

    热备：指在数据库运行中直接备份，对正在运行的数据库没有任何影响，MySQL官方手
    册称为Online Backup（在线备份）

    冷备：指在数据库停止的情况下进行备份，这种备份最简单，一般只需要复制相关的数
    据库物理文件即可，MySQL官方手册称为Offline Backup（离线备份）

    温备：指在数据库运行中备份，但会对当前数据库的操作有所影响，如加一个全局读锁
    以保证备份数据的一致性

按备份文件的内容分为：逻辑备份  裸文件备份

按备份数据库的内容分：完全备份、增量备份、日志备份

--------------------------------------性能调优----------------------------------

数据应用一般分为两类：OLAP和OLTP

OLAP: Online Analytical Processing 在线分析处理

      OLAP多用在数据仓库或数据集市中，数据量大，一般需要执行复杂的SQL语句来进行
      查询

      OLAP的数据库应用对CPU的要求比较高，因为复杂的查询可能需要执行比较、排序，
      连接等非常耗CPU的操作

      OLAP是CPU密集型的操作

OLTP: Online Transaction Processing 在线事务处理

      OLTP多用在日常的实物处理应用中，数据量小，如银行交易，在线商品交易。

      OLTP的复杂查询较少，对CPU的要求不高

      OLTP是IO密集型的操作，采购设备时应注意提高IO的配置

InnoDB存储引擎一般应用于OLTP的数据库应用，特点：

      用户操作的并发量大

      事务处理的时间一般比较短

      查询的语句较为简单，一般都走索引

      复杂的查询较少

InnoDB Buffer Pool：

    InnoDB缓存池，大小直接影响数据库的性能，既缓存了数据，又缓存了索引

    当缓冲池容量超过数据文件和索引文件的总大小时，性能是最高的，再继续调大缓冲池
    并不能再提高性能

    在开发应用前应该预估“活跃”数据库的大小可能会是多少，并以此确定数据库服务器
    的内存大小（可能需要64位操作系统的支持）

