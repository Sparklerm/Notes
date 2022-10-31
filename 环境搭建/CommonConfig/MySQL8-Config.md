## 参考Aliyun RDS MySQL（1核1G）

### 适用1G内存的MySQL实例配置文件：

```properties
[client]
port		= 3306
socket		= /tmp/mysql.sock

[mysqld]
secure_file_priv=
socket		= /tmp/mysql.sock
datadir = /var/lib/mysql

## 设置server_id，注意要唯一
#server-id=1
## 开启二进制日志功能，可以随便取
#log-bin= replicas-mysql-bin
## 复制过滤：也就是指定哪个数据库不用同步（mysql库一般不同步）
#binlog-ignore-db=mysql
## 主从复制的格式（mixed,statement,row，默认格式是statement）
#binlog_format=mixed
## 二进制日志自动删除/过期的天数。默认值为0，表示不自动删除。
#expire_logs_days=7
## 跳过主从复制中遇到的所有错误或指定类型的错误，避免slave端复制中断。
## 如：1062错误是指一些主键重复，1032错误是因为主从数据库数据不一致
# lave_skip_errors=1062

loose_performance_schema_max_table_instances=1000
innodb_buffer_pool_load_at_startup=ON
loose_performance_schema_max_index_stat=10000
bulk_insert_buffer_size=4194304
show_old_temporals=OFF
ft_query_expansion_limit=20
innodb_old_blocks_time=1000
thread_stack=262144
lc_time_names=en_US
innodb_thread_concurrency=0
sync_master_info=10000
default_time_zone= '+8:00'
optimizer_search_depth=62
innodb_compression_level=6
loose_performance_schema_max_prepared_statements_instances=1000
max_sort_length=1024
max_binlog_cache_size=18446744073709547520
loose_innodb_log_write_ahead_size=4096
innodb_online_alter_log_max_size=134217728
key_cache_block_size=1024
mysql_native_password_proxy_users=OFF
innodb_adaptive_max_sleep_delay=150000
innodb_purge_rseg_truncate_frequency=128
query_alloc_block_size=8192
loose_performance_schema_max_socket_instances=1000
innodb_lock_wait_timeout=50
innodb_purge_threads=1
innodb_compression_failure_threshold_pct=5
innodb_compression_pad_pct_max=50
loose_performance_schema_error_size=10000
binlog_rows_query_log_events=OFF
max_execution_time=0
innodb_stats_persistent_sample_pages=20
innodb_ft_total_cache_size=640000000
event_scheduler=OFF
innodb_flush_method=O_DIRECT
loose_performance_schema_accounts_size=10000
eq_range_index_dive_limit=100
loose_optimizer_trace_features=greedy_search=on,range_optimizer=on,dynamic_range=on,repeated_subselect=on
connect_timeout=10
innodb_purge_batch_size=300
div_precision_increment=4
avoid_temporal_upgrade=OFF
innodb_sync_array_size=1
innodb_stats_method=nulls_equal
lock_wait_timeout=31536000
net_read_timeout=30
innodb_deadlock_detect=ON
innodb_write_io_threads=4
end_markers_in_json=OFF
loose_performance_schema_hosts_size=10000
ngram_token_size=2
loose_innodb_numa_interleave=ON
loose_performance_schema_max_cond_instances=10000
max_binlog_stmt_cache_size=18446744073709547520
loose_performance_schema_max_table_lock_stat=10000
loose_performance_schema_max_table_handles=10000
innodb_checksum_algorithm=crc32
innodb_ft_enable_diag_print=OFF
innodb_ft_enable_stopword=ON
innodb_io_capacity=20000
slow_launch_time=2
innodb_table_locks=ON
innodb_stats_persistent=ON
tmp_table_size=2097152
disconnect_on_expired_password=ON
default_storage_engine=InnoDB
net_retry_count=10
innodb_ft_cache_size=8000000
binlog_cache_size=2097152
innodb_max_dirty_pages_pct=75
innodb_disable_sort_file_cache=ON
innodb_lru_scan_depth=1024
loose_performance_schema_max_mutex_instances=10000
innodb_ft_result_cache_limit=2000000000
long_query_time=1
interactive_timeout=7200
innodb_read_io_threads=4
transaction_prealloc_size=4096
open_files_limit=65535
loose_performance_schema_max_metadata_locks=10000
temptable_max_ram=1073741824
innodb_open_files=3000
max_heap_table_size=67108864
loose_performance_schema_digests_size=10000
automatic_sp_privileges=ON
explicit_defaults_for_timestamp=OFF
ft_max_word_len=84
innodb_autoextend_increment=64
sql_mode=ONLY_FULL_GROUP_BY,STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_ENGINE_SUBSTITUTION
innodb_stats_transient_sample_pages=8
innodb_random_read_ahead=OFF
table_open_cache=256
range_optimizer_max_mem_size=8388608
innodb_status_output=OFF
innodb_log_compressed_pages=OFF
slave_net_timeout=60
delay_key_write=ON
max_points_in_geometry=65536
general_log=OFF
max_prepared_stmt_count=16382
wait_timeout=86400
log_bin_use_v1_row_events=1
loose_performance_schema_setup_actors_size=10000
innodb_print_all_deadlocks=OFF
binlog_stmt_cache_size=32768
transaction_isolation=READ-COMMITTED
innodb_buffer_pool_dump_at_shutdown=ON
query_prealloc_size=8192
key_cache_age_threshold=300
loose_performance_schema_setup_objects_size=10000
transaction_alloc_block_size=8192
optimizer_trace_limit=1
optimizer_prune_level=1
loose_performance_schema_max_file_instances=1000
innodb_max_purge_lag=0
innodb_buffer_pool_dump_pct=25
innodb_max_dirty_pages_pct_lwm=0
max_sp_recursion_depth=0
innodb_status_output_locks=OFF
updatable_views_with_limit=YES
binlog_row_image=full
innodb_change_buffer_max_size=25
innodb_optimize_fulltext_only=OFF
local_infile=ON
innodb_ft_max_token_size=84
loose_performance_schema_users_size=10000
innodb_max_undo_log_size=1073741824
slave_parallel_type=LOGICAL_CLOCK
innodb_adaptive_hash_index=OFF
innodb_sync_spin_loops=30
net_write_timeout=60
flush_time=0
loose_internal_tmp_mem_storage_engine=MEMORY
sha256_password_proxy_users=OFF
character_set_filesystem=binary
innodb_flush_sync=ON
tls_version=TLSv1,TLSv1.1,TLSv1.2
key_cache_division_limit=100
loose_performance_schema_max_rwlock_instances=10000
delayed_insert_timeout=300
preload_buffer_size=32768
innodb_read_ahead_threshold=56
loose_optimizer_switch=index_merge=on,index_merge_union=on,index_merge_sort_union=on,index_merge_intersection=on,engine_condition_pushdown=on,index_condition_pushdown=on,mrr=on,mrr_cost_based=on,block_nested_loop=on,batched_key_access=off,materialization=on,semijoin=on,loosescan=on,firstmatch=on,subquery_materialization_cost_based=on,use_index_extensions=on
concurrent_insert=1
block_encryption_mode="aes-128-ecb"
slow_query_log=ON
net_buffer_length=16384
innodb_buffer_pool_size=805306368
delayed_insert_limit=100
innodb_monitor_disable=
loose_performance_schema_max_program_instances=10000
innodb_adaptive_flushing_lwm=10
innodb_log_checksums=ON
delayed_queue_size=1000
session_track_gtids=OFF
innodb_thread_sleep_delay=10000
innodb_old_blocks_pct=37
innodb_ft_sort_pll_degree=2
log_slow_admin_statements=OFF
innodb_stats_on_metadata=OFF
stored_program_cache=256
group_concat_max_len=1024
innodb_sort_buffer_size=1048576
innodb_page_cleaners=1
loose_innodb_parallel_read_threads=1
innodb_spin_wait_delay=6
myisam_sort_buffer_size=262144
innodb_rollback_segments=128
innodb_commit_concurrency=0
innodb_concurrency_tickets=5000
table_definition_cache=512
auto_increment_increment=1
binlog_checksum=CRC32
max_seeks_for_key=18446744073709500000
max_length_for_sort_data=1024
back_log=3000
max_error_count=64
innodb_io_capacity_max=40000
innodb_strict_mode=OFF
binlog_order_commits=ON
min_examined_row_limit=0
innodb_ft_min_token_size=3
sync_relay_log_info=10000
innodb_stats_auto_recalc=ON
max_connect_errors=100
join_buffer_size=131072
innodb_change_buffering=all
optimizer_trace_max_mem_size=16384
innodb_autoinc_lock_mode=2
innodb_rollback_on_timeout=OFF
loose_performance_schema_max_thread_instances=10000
max_write_lock_count=102400
master_verify_checksum=OFF
innodb_ft_num_word_optimize=2000
max_join_size=18446744073709551615
log_error_verbosity=2
log_throttle_queries_not_using_indexes=0
innodb_max_purge_lag_delay=0
loose_optimizer_trace=enabled=off,one_line=off
default_week_format=0
innodb_cmp_per_index_enabled=OFF
host_cache_size=644
low_priority_updates=0
auto_increment_offset=1
range_alloc_block_size=4096
ft_min_word_len=4
sort_buffer_size=262144
slave_type_conversions=
max_allowed_packet=1073741824
read_buffer_size=131072
thread_cache_size=100
optimizer_trace_offset=-1
character_set_server=utf8
innodb_adaptive_flushing=ON
log_queries_not_using_indexes=OFF
innodb_monitor_enable=
table_open_cache_instances=1
innodb_flush_neighbors=0
innodb_buffer_pool_instances=1

# 字符集设定，如果前台有连social mobile application一类包括wechat，并且允许有使用emoji表情的，请开启成utf8mb4
character_set_server = utf8mb4
```

### 参数计算规则：

```bash
table_open_cache={LEAST(DBInstanceClassMemory/1073741824*256, 2048)}
innodb_buffer_pool_size={DBInstanceClassMemory*3/4}
innodb_page_cleaners={LEAST(DBInstanceClassMemory/1073741824, 8)}
table_definition_cache={LEAST(DBInstanceClassMemory/1073741824*512, 2048)}
join_buffer_size={LEAST(DBInstanceClassMemory/1048576*128, 262144)}
read_buffer_size={LEAST(DBInstanceClassMemory/1048576*128, 262144)}
table_open_cache_instances={LEAST(DBInstanceClassMemory/1073741824, 16)}
innodb_buffer_pool_instances={LEAST(DBInstanceClassMemory/1073741824, 8)}
```

## 参考宝塔配置

### （1-2G实例）最大使用内存约 843.25MB

```properties
[client]
#password	= your_password
port		= 3306
socket		= /tmp/mysql.sock

[mysqld]
binlog_cache_size = 64K
thread_stack = 256K
join_buffer_size = 1024K
max_heap_table_size = 64M
port		= 3306
socket		= /tmp/mysql.sock
datadir = /var/lib/mysql
default_storage_engine = InnoDB
performance_schema_max_table_instances = 400
table_definition_cache = 400
skip-external-locking
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

### （2-4G实例）最大使用内存约 1950.50MB

```properties
[client]
#password	= your_password
port		= 3306
socket		= /tmp/mysql.sock

[mysqld]
binlog_cache_size = 64K
thread_stack = 256K
join_buffer_size = 2048K
max_heap_table_size = 384M
port		= 3306
socket		= /tmp/mysql.sock
datadir = /www/server/data
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
myisam_sort_buffer_size = 32M
thread_cache_size = 96
tmp_table_size = 384M
default_authentication_plugin = mysql_native_password
lower_case_table_names = 1
sql-mode=NO_ENGINE_SUBSTITUTION,STRICT_TRANS_TABLES

explicit_defaults_for_timestamp = true
#skip-name-resolve
max_connections = 200
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


innodb_data_home_dir = /var/lib/mysql
innodb_data_file_path = ibdata1:10M:autoextend
innodb_log_group_home_dir = /var/lib/mysql
innodb_buffer_pool_size = 384M
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