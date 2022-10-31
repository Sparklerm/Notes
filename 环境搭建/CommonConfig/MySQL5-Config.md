## 参考宝塔面板

### （1-2G实例）最大使用内存 859.25MB

```bash
[client]
#password	= your_password
port		= 3306
socket		= /tmp/mysql.sock

[mysqld]
port		= 3306
socket		= /tmp/mysql.sock
datadir = /var/lib/mysql
default_storage_engine = InnoDB
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

### （2-4G实例）最大使用内存 2030.50MB

```
[client]
#password	= your_password
port		= 3306
socket		= /tmp/mysql.sock

[mysqld]
binlog_cache_size = 64K
thread_stack = 256K
join_buffer_size = 2048K
query_cache_type = 1
max_heap_table_size = 384M
port		= 3306
socket		= /tmp/mysql.sock
datadir = /var/lib/mysql
default_storage_engine = InnoDB
performance_schema_max_table_instances = 400
table_definition_cache = 400
skip-external-locking
key_buffer_size = 256M
max_allowed_packet = 100G
table_open_cache = 192
sort_buffer_size = 768K
net_buffer_length = 4K
read_buffer_size = 768K
read_rnd_buffer_size = 512K
myisam_sort_buffer_size = 8M
thread_cache_size = 96
query_cache_size = 128M
tmp_table_size = 384M
sql-mode=NO_ENGINE_SUBSTITUTION,STRICT_TRANS_TABLES

explicit_defaults_for_timestamp = true
#skip-name-resolve
max_connections = 200
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
innodb_buffer_pool_size = 384M
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