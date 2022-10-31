# MySQL 5.7

```bash
# 拉取镜像
docker pull mysql:5.7.35
# 提前准备好配置文件及挂载目录
mkdir /usr/local/server /usr/local/server/mysql5.7 /usr/local/server/mysql5.7/conf/ /usr/local/server/mysql5.7/data/
touch /usr/local/server/mysql5.7/conf/my.cnf
vi /usr/local/server/mysql5.7/conf/my.cnf
# 将配置文件写入my.cnf

# Docker启动命令
docker run -d \
--name mysql5.7 \
--restart=always \
-p 3306:3306 \
-m 1024M \
-v /usr/local/server/mysql5.7/conf:/etc/mysql \
-v /usr/local/server/mysql5.7/data:/var/lib/mysql \
-e MYSQL_ROOT_PASSWORD=123456 \
mysql:5.7.35

# 方式一：进入容器检查MySQL是否启动成功
[root@localhost local]# docker exec -it mysql5.7 bash
root@a77116498bc5:/# mysql -uroot -p123456
mysql: [Warning] Using a password on the command line interface can be insecure.
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 2
Server version: 5.7.25-log MySQL Community Server (GPL)

Copyright (c) 2000, 2019, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> 

# 方式二：使用数据库可视化工具（如Navicat）连接数据库进行测试
```



```properties
# MySQL 5.7 Config
[client]
#password	= your_password
port		= 3306
socket		= /var/lib/mysql/mysql.sock

[mysqld]
port		= 3306
socket		= /var/lib/mysql/mysql.sock
datadir = /var/lib/mysql
default_storage_engine = InnoDB
character-set-server = utf8mb4 
collation-server = utf8mb4_general_ci
performance_schema_max_table_instances = 400
table_definition_cache = 400
binlog_cache_size = 64K
thread_stack = 256K
join_buffer_size = 1024K
query_cache_type = 1
max_heap_table_size = 64M
skip-external-locking
key_buffer_size = 128M
max_allowed_packet = 100G
table_open_cache = 128
sort_buffer_size = 768K
net_buffer_length = 4K
read_buffer_size = 768K
read_rnd_buffer_size = 512K
myisam_sort_buffer_size = 8M
thread_cache_size = 64
query_cache_size = 64M
tmp_table_size = 64M
sql-mode=NO_ENGINE_SUBSTITUTION,STRICT_TRANS_TABLES

explicit_defaults_for_timestamp = true
#skip-name-resolve
max_connections = 100
max_connect_errors = 100
open_files_limit = 65535

log-bin=mysql-bin
binlog_format=mixed
server-id = 1
expire_logs_days = 10
slow_query_log=1
slow-query-log-file=/log/mysql/mysql-slow.log
long_query_time=3
#log_queries_not_using_indexes=on
early-plugin-load = ""


innodb_data_home_dir = /var/lib/mysql
innodb_data_file_path = ibdata1:10M:autoextend
innodb_log_group_home_dir = /var/lib/mysql
innodb_buffer_pool_size = 256M
innodb_log_file_size = 64M
innodb_log_buffer_size = 16M
innodb_flush_log_at_trx_commit = 1
innodb_lock_wait_timeout = 50
innodb_max_dirty_pages_pct = 90
innodb_read_io_threads = 1
innodb_write_io_threads = 1

[mysqldump]
quick
max_allowed_packet = 500M

[mysql]
no-auto-rehash

[myisamchk]
key_buffer_size = 32M
sort_buffer_size = 768K
read_buffer = 2M
write_buffer = 2M

[mysqlhotcopy]
interactive-timeout
```

# MySQL 8

```shell
# 拉取镜像
docker pull mysql:8.0.18

# 提前准备好配置文件及挂载目录
mkdir /usr/local/server /usr/local/server/mysql8 /usr/local/server/mysql8/conf/ /usr/local/server/mysql8/data/
touch /usr/local/server/mysql8/conf/my.cnf
vi /usr/local/server/mysql8/conf/my.cnf
# 将配置文件写入my.cnf

# Docker启动命令
docker run -d \
--name mysql8 \
--restart=always \
-p 3306:3306 \
-m 1024M \
-v /usr/local/server/mysql8/conf:/etc/mysql \
-v /usr/local/server/mysql8/data:/var/lib/mysql \
-e MYSQL_ROOT_PASSWORD=123456 \
mysql:8.0.18

# 方式一：进入容器检查MySQL是否启动成功
[root@localhost conf]# docker exec -it mysql8 bash
root@6d7f706f1ef4:/# mysql -uroot -p      
Enter password: 
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 9
Server version: 8.0.18 MySQL Community Server - GPL

Copyright (c) 2000, 2019, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> 

# 方式二：使用数据库可视化工具（如Navicat）连接数据库进行测试
```

```properties
# MySQL 8 Config
[client]
#password	= your_password
port		= 3306
socket		= /var/lib/mysql/mysql.sock

[mysqld]
secure_file_priv=
port		= 3306
socket		= /var/lib/mysql/mysql.sock
datadir = /var/lib/mysql
default_storage_engine = InnoDB
character-set-server = utf8mb4 
collation-server = utf8mb4_general_ci
performance_schema_max_table_instances = 400
table_definition_cache = 400
skip-external-locking
binlog_cache_size = 64K
thread_stack = 256K
join_buffer_size = 1024K
max_heap_table_size = 64M
key_buffer_size = 128M
max_allowed_packet = 100G
table_open_cache = 128
sort_buffer_size = 768K
net_buffer_length = 4K
read_buffer_size = 768K
read_rnd_buffer_size = 512K
myisam_sort_buffer_size = 32M
thread_cache_size = 64
tmp_table_size = 64M
default_authentication_plugin = mysql_native_password
lower_case_table_names = 1
sql-mode=NO_ENGINE_SUBSTITUTION,STRICT_TRANS_TABLES

explicit_defaults_for_timestamp = true
#skip-name-resolve
max_connections = 100
max_connect_errors = 100
open_files_limit = 65535

log-bin=mysql-bin
binlog_format=mixed
server-id = 1
binlog_expire_logs_seconds = 600000
slow_query_log=1
slow-query-log-file=/log/mysql/mysql-slow.log
long_query_time=3
#log_queries_not_using_indexes=on
early-plugin-load = ""

log-error=/var/log/mysqld.log

innodb_data_home_dir = /var/lib/mysql
innodb_data_file_path = ibdata1:10M:autoextend
innodb_log_group_home_dir = /var/lib/mysql
innodb_buffer_pool_size = 256M
innodb_log_file_size = 256M
innodb_log_buffer_size = 64M
innodb_flush_log_at_trx_commit = 1
innodb_lock_wait_timeout = 50
innodb_max_dirty_pages_pct = 90
innodb_read_io_threads = 4
innodb_write_io_threads = 4

[mysqldump]
quick
max_allowed_packet = 500M

[mysql]
no-auto-rehash

[myisamchk]
key_buffer_size = 128M
sort_buffer_size = 2M
read_buffer = 2M
write_buffer = 2M

[mysqlhotcopy]
interactive-timeout
```

