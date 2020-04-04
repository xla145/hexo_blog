---
title: 'Linux下离线安装mysql'
date: 2019-12-05 09:37:32
tags:
- mysql
---
#  下载mysql-5.7.28源码包（不需要编译）：

地址：https://dev.mysql.com/get/Downloads/MySQL-5.7/mysql-5.7.28-linux-glibc2.12-x86_64.tar.gz
<!-- more -->
#  创建MySQL组并添加MySQL用户，MySQL用户不存在则创建

    注意：执行以下命令需要在root权限下执行

    groupadd mysql -- 创建mysql 组

    useradd -r -g mysql mysql  创建MySQL用户并加入MySQL组

    useradd 命令参数如下：

   ![useradd命令](https://img.mupaie.com/useradd.png)

# 解压源码

解压包到指定目录下（/usr/local/mysql/）如果/usr/local/mysql/ 目录不存在则创建该目录

```
tar -zxvf mysql-5.7.28-linux-glibc2.12-x86_64.tar.gz -C /usr/local/mysql/
```

# 设置目录权限 

``` 
cd /usr/local/mysql/ 
chown -R mysql:mysql /usr/local/mysql/
chmod 775 /usr/local/mysql/
```
   
# 创建 my.cnf 文件 

如果/etc 下没有该文件，并填入以下内容

```
[mysqld] sql_mode=NO_ENGINE_SUBSTITUTION,STRICT_TRANS_TABLES
explicit_defaults_for_timestamp=true
basedir = /usr/local/mysql datadir= /usr/local/mysql/data
port = 3306
socket =/usr/local/mysql/mysqld/mysql.sock
pid-file =/usr/local/mysql/mysqld/mysql.pid
character-set-server=utf8
back_log =300
max_connections = 3000
max_connect_errors = 50
table_open_cache = 4096
max_allowed_packet = 32M
max_heap_table_size = 128M
read_rnd_buffer_size = 16M
sort_buffer_size = 16M
join_buffer_size = 16M
thread_cache_size = 16
query_cache_size = 128M
query_cache_limit = 4M
ft_min_word_len = 8
thread_stack = 512K
transaction_isolation = REPEATABLE-READ
tmp_table_size = 128M
long_query_time = 6
server_id=1
innodb_buffer_pool_size = 1G
innodb_thread_concurrency = 16
innodb_log_buffer_size = 16M
innodb_log_file_size = 512M
innodb_log_files_in_group = 3
innodb_max_dirty_pages_pct = 90
innodb_lock_wait_timeout = 120
innodb_file_per_table = on
[mysqldump]
quick max_allowed_packet = 32M
[mysql]
no-auto-rehash
default-character-set=utf8
safe-updates
[myisamchk]
key_buffer = 16M
sort_buffer_size = 16M
read_buffer = 8M
write_buffer = 8M
[mysqlhotcopy]
interactive-timeout
[mysqld_safe]
open-files-limit = 8192
[client]
port = 3306
socket = /usr/local/mysql/mysqld/mysql.sock
default-character-set = utf8
```
# 创建文件夹

```
 $ mkdir -p /usr/local/mysql/data
 $ chown -R mysql:mysql /usr/local/mysql/data
 $ chmod -R 755 /usr/local/mysql/data $ mkdir -p /usr/local/mysql/mysqld
 $ chown -R mysql:mysql /usr/local/mysql/mysqld
 $ chmod -R 755 /usr/local/mysql/mysqld
```
# 初始化数据库 

进入到/usr/local/mysql/bin 下执行以下命令

```
$ ./mysqld --user=mysql --basedir=/usr/local/mysql--datadir=/usr/local/mysql/data --initialize
```

执行结束后会有一个临时密码记入下来
A temporary password is generated for root@localhost: dsuIeduSe5>E

方便等下登录 比如 dsuIeduSe5 就是我的临时密码

# 创建软链接并配置环境变量
```
 $ln -fs /usr/local/mysql/bin/mysql
/usr/local/bin/mysql $ cp /usr/local/mysql/support-files/mysql.server
/etc/init.d/mysqld
```
打开 vim /etc/profile 文件填入以下内容：
```
MYSQL_HOME= /usr/local/mysq/bin PATH=$MYSQL_HOME/bin:$PATH export
MYSQL_HOME
```
# 启用Mysql 

```
/etc/init.d/mysqld start 或 service mysql start
```

# 登录mysql
    mysql -uroot -p  回车

    输入上述的临时密码

    登入成功后出现以下内容则表示成功：

   ![useradd命令](https://img.mupaie.com/mysql.png)

# 安装过程下如果出现以下问题
* 问题1：
启动 mysql 的时候出现
Can’t connect to local MySQL server through socket ‘/var/lib/mysql/mysql.sock’ 
解决方法：查看mysql.sock目录mysql用户是否有权限访问，如果没有权限则赋权

* 问题2：
启动 mysql 的时候出现
Starting MySQL...[ERROR] The server quit without updating PID file (/usr/local/mysql/data/mysqld.pid).
解决：
1. 查看该目录是否有权限
2. 查看 mysqld进程是否存在
3. 去mysql的数据目录/data看看，如果存在mysql-bin.index，就赶快把它删除掉吧，它就是罪魁祸首了
4. [mysqld]下指定数据目录(datadir) /usr/local/mysql/data 

* 问题3：

5. 忘记临时密码
上述执行mysqld命令会生成对应的日志文件，目录/var/log/mysqld.log 执行 grep
'temporary password' /var/log/mysqld.log 查看临时密码
如果日志中查不到密码使用以下方法： 进入/usr/local/mysql/bin
目录，操作前先关闭mysql服务 执行 mysqld_safe --skip-grant-tables
--user=mysql &

执行 mysql 命令 进入mysql
```
use mysql; update user set authentication_string=password("新密码") where user='root';
```
执行这段sql可能会出现以下错误：
```
Error Code: 1175. You are using safe update mode and you tried to
update a table without a WHERE that uses a KEY column To disable safe
mode, toggle the option in Preferences -> SQL Queries and reconnect.
```

由于安全模式下更新语句条件必须是主键 关闭安全模式：
```
 SET SQL_SAFE_UPDATES = 0;
```
修改下数据库模式 继续执行上述sql

* 问题4： 远程访问数据库 update user set host = '%' where user='root';
 让root用户可以远程登录，但是这样做有风险不建议使用

参考文档:

[安装：MySQL mysql-5.7.19-linux-glibc2.12-x86_64.tar.gz](https://www.jianshu.com/p/82790b6b4e08)

[【MySQL笔记】解除输入的安全模式](https://www.cnblogs.com/xuancaoyy/p/5814658.html)
