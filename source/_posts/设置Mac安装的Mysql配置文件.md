---
title: 设置Mac安装的Mysql配置文件
date: 2022-09-15 10:14:23
tags:
- 笔记
---

### 1.背景

因为我安装的Mysql8默认开启了ONLY_FULL_GROUP_BY在group by某个字段时其他字段不在group by中就会出错，因此需要去my.cnf更改SQL_MODE。

Mac安装的Mysql8默认不会生成配置文件所以我需要创建个配置文件去让其加载。

### 2.配置文件

#### 2.1创建my.cnf

Mysql默认安装在/usr/local/mysql-8.0.29-macos12-arm64

![image-20220915101928256](http://image.hi-hufei.com/typora/image-20220915101928256.png)

在/usr/local/mysql/support-files文件夹下创建my.cnf 内容如下：

```bash
# Example MySQL config file for small systems.
#
# This is for a system with little memory (<= 64M) where MySQL is only used
# from time to time and it's important that the mysqld daemon
# doesn't use much resources.
#
# MySQL programs look for option files in a set of
# locations which depend on the deployment platform.
# You can copy this option file to one of those
# locations. For information about these locations, see:
# http://dev.mysql.com/doc/mysql/en/option-files.html
#
# In this file, you can use all long options that a program supports.
# If you want to know which options a program supports, run the program
# with the "--help" option.
# The following options will be passed to all MySQL clients
[client]
default-character-set=utf8
#password = your_password
port        = 3336
socket      = /tmp/mysql.sock
# Here follows entries for some specific programs
# The MySQL server
[mysqld]
default-storage-engine=INNODB
character-set-server=utf8
collation-server=utf8_general_ci
socket      = /tmp/mysql.sock
# lower_case_table_names = 1    # 是否对sql语句大小写敏感，1表示不敏感，即不区分大小写
# skip-external-locking
# key_buffer_size = 16K
# max_allowed_packet = 1M
# table_open_cache = 4
# sort_buffer_size = 64K
# read_buffer_size = 256K
# read_rnd_buffer_size = 256K
# net_buffer_length = 2K
# thread_stack = 256K   # 该字段根据需要修改，默认是128K，我的是因为启动报了这个字段的错导致启动失败，所以我改成256K了

# Don't listen on a TCP/IP port at all. This can be a security enhancement,
# if all processes that need to connect to mysqld run on the same host.
# All interaction with mysqld must be made via Unix sockets or named pipes.
# Note that using this option without enabling named pipes on Windows
# (using the "enable-named-pipe" option) will render mysqld useless!
#
#skip-networking
server-id   = 1
# Uncomment the following if you want to log updates
#log-bin=mysql-bin
# binary logging format - mixed recommended
#binlog_format=mixed
# Causes updates to non-transactional engines using statement format to be
# written directly to binary log. Before using this option make sure that
# there are no dependencies between transactional and non-transactional
# tables such as in the statement INSERT INTO t_myisam SELECT * FROM
# t_innodb; otherwise, slaves may diverge from the master.
#binlog_direct_non_transactional_updates=TRUE
# Uncomment the following if you are using InnoDB tables
#innodb_data_home_dir = /usr/local/mysql/data
#innodb_data_file_path = ibdata1:10M:autoextend
#innodb_log_group_home_dir = /usr/local/mysql/data
# You can set .._buffer_pool_size up to 50 - 80 %
# of RAM but beware of setting memory usage too high
#innodb_buffer_pool_size = 16M
#innodb_additional_mem_pool_size = 2M
# Set .._log_file_size to 25 % of buffer pool size
#innodb_log_file_size = 5M
#innodb_log_buffer_size = 8M
#innodb_flush_log_at_trx_commit = 1
#innodb_lock_wait_timeout = 50
[mysqldump]
quick
# max_allowed_packet = 16M
[mysql]
no-auto-rehash
# Remove the next comment character if you are not familiar with SQL
#safe-updates
[myisamchk]
# key_buffer_size = 8M
# sort_buffer_size = 8M
[mysqlhotcopy]
interactive-timeout
[mysqld]
sql_mode=STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION

```

#### 2.2指定my.cnf

`注：初始化数据库会删除之前的数据库中的表，请备份好！`

![image-20220915111149123](http://image.hi-hufei.com/typora/image-20220915111149123.png)

#### 2.3初始化数据库

![image-20220915111911940](http://image.hi-hufei.com/typora/image-20220915111911940.png)

![image-20220915111853987](http://image.hi-hufei.com/typora/image-20220915111853987.png)

![image-20220915111927297](http://image.hi-hufei.com/typora/image-20220915111927297.png)
