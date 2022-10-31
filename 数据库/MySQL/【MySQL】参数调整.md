# MySQL 参数调整

## back_log

- 适用版本：8.0、5.7、5.6、5.5

- 默认值：3000

- 修改完后是否需要重启：是

- 作用：MySQL每处理一个连接请求时都会创建一个新线程与之对应。在主线程创建新线程期间，如果前端应用有大量的短连接请求到达数据库，MySQL会限制这些新的连接进入请求队列，由参数back_log控制。如果等待的连接数量超过back_log的值，则不会接受新的连接请求，所以如果需要MySQL能够处理大量的短连接，需要提高此参数的大小。

- 现象：如果参数过小，应用可能出现如下错误：

  ```sql
  SQLSTATE[HY000] [2002] Connection timed out;
  ```

- 修改建议：提高此参数值的大小。

## innodb_autoinc_lock_mode

- 适用版本：8.0、5.7、5.6、5.5

- 默认值：1

- 修改完后是否需要重启：是

- 作用：在MySQL 5.1.22后，InnoDB为了解决自增主键锁表的问题，引入了参数innodb_autoinc_lock_mode，用于控制自增主键的锁机制。该参数可以设置的值为0、1、2，RDS默认的参数值为1，表示InnoDB使用轻量级别的mutex锁来获取自增锁，替代最原始的表级锁。但是在load data（包括`INSERT … SELECT`和`REPLACE … SELECT`）场景下若使用自增表锁，则可能导致应用在并发导入数据时出现死锁。

- 现象：在load data（包括 INSERT … SELECT 和 REPLACE … SELECT ）场景下若使用自增表锁，在并发导入数据时出现如下死锁：

  ```sql
  RECORD LOCKS space id xx page no xx n bits xx index PRIMARY of table xx.xx trx id xxx lock_mode X insert intention waiting. TABLE LOCK table xxx.xxx trx id xxxx lock mode AUTO-INC waiting；
  ```

- 修改建议：建议将该参数值改为2，表示所有情况插入都使用轻量级别的mutex锁（只针对row模式），这样就可以避免auto_inc的死锁，同时在 INSERT … SELECT 的场景下性能会有很大提升。

  **说明** 当该参数值为2时，binlog的格式需要被设置为row。

## query_cache_size

- 适用版本：5.7、5.6、5.5
- 默认值：3145728
- 修改完后是否需要重启：否
- 作用：该参数用于控制MySQL query cache的内存大小。如果MySQL开启query cache，在执行每一个query的时候会先锁住query cache，然后判断是否存在于query cache中，如果存在则直接返回结果，如果不存在，则再进行引擎查询等操作。同时，INSERT、UPDATE和DELETE等操作都会导致query cache失效，这种失效还包括结构或者索引的任何变化。但是cache失效的维护代价较高，会给MySQL带来较大的压力。所以，当数据库不会频繁更新时，query cache是很有用的，但如果写入操作非常频繁并集中在某几张表上，那么query cache lock的锁机制就会造成很频繁的锁冲突，对于这一张表的写和读会互相等待query cache lock解锁，从而导致SELECT的查询效率下降。
- 现象：数据库中有大量的连接状态为`checking query cache for query`、`Waiting for query cache lock`、`storing result in query cache`。
- 修改建议：RDS默认是关闭query cache功能的，如果您的实例打开了query cache，当出现上述情况后可以关闭query cache。

## net_write_timeout

- 适用版本：8.0、5.7、5.6、5.5

- 默认值：60

- 修改完后是否需要重启：否

- 作用：等待将一个block发送给客户端的超时时间。

- 现象：若参数设置过小，可能会导致客户端出现如下错误：

  ```sql
  the last packet successfully received from the server was milliseconds ago或the last packet sent successfully to the server was milliseconds ago.
  ```

- 修改建议：该参数在RDS中默认设置为60秒，一般在网络条件比较差时或者客户端处理每个block耗时较长时，由于net_write_timeout设置过小导致的连接中断很容易发生，建议增加该参数的大小。

## tmp_table_size

- 适用版本：8.0、5.7、5.6、5.5
- 默认值：2097152
- 修改完后是否需要重启：否
- 作用：该参数用于决定内部内存临时表的最大值，每个线程都要分配，实际起限制作用的是tmp_table_size和max_heap_table_size的最小值。如果内存临时表超出了限制，MySQL就会自动地把它转化为基于磁盘的MyISAM表。优化查询语句的时候，要避免使用临时表，如果实在避免不了的话，要保证这些临时表是存在内存中的。
- 现象：如果复杂的SQL语句中包含了GROUP BY、DISTINCT等不能通过索引进行优化而使用了临时表，则会导致SQL执行时间加长。
- 修改建议：如果应用中有很多GROUP BY、DISTINCT等语句，同时数据库有足够的内存，可以增大tmp_table_size（max_heap_table_size）的值提升查询性能。

## loose_rds_max_tmp_disk_space

- 适用版本：5.6、5.5

- 默认值：10737418240

- 修改完后是否需要重启：否

- 作用：用于控制MySQL能够使用的临时文件的大小。

- 现象：如果临时文件超出loose_rds_max_tmp_disk_space的取值，则会导致应用出现如下错误：

  ```sql
  The table ‘/home/mysql/dataxxx/tmp/#sql_2db3_1’ is full
  ```

- 修改建议：首先需要分析一下导致临时文件增加的SQL语句是否能够通过索引或者其它方式进行优化。其次，如果确定实例的空间足够，则可以提升此参数的值，以保证SQL能够正常执行。

## loose_tokudb_buffer_pool_ratio

- 适用版本：5.6
- 默认值：0
- 修改完后是否需要重启：是
- 作用：用于控制TokuDB引擎能够使用的buffer内存大小，比如innodb_buffer_pool_size设置为1000MB，tokudb_buffer_pool_ratio设置为50（代表50%），那么TokuDB引擎的表能够使用的buffer内存大小则为500MB。
- 修改建议：如果RDS中使用TokuDB引擎，建议调大该参数，以此来提升TokuDB引擎表的访问性能。

## loose_max_statement_time

- 适用版本：5.6

- 默认值：0

- 修改完后是否需要重启：否

- 作用：用于控制查询（QUERY）在数据库中的最长执行时间。如果超过该参数设置的时间，查询将会失败，默认是不限制。

- 现象：若查询时间超过了该参数的值，则会出现如下错误：

  ```yaml
  ERROR 3006 (HY000): Query execution was interrupted, max_statement_time exceeded
  ```

  **说明** 参数修改后仅对新连接生效，保持中的连接需要断开重连才能生效。

- 修改建议：如果您想要控制数据库中SQL的执行时间，则可以开启该参数，单位是毫秒。

## loose_rds_threads_running_high_watermark

- 适用版本：5.6、5.5
- 默认值：50000
- 修改完后是否需要重启：否
- 作用：用于控制MySQL并发的查询数目，比如将rds_threads_running_high_watermark的值设置为100，则允许MySQL同时进行的并发查询为100个，超过限制数量的查询将会被拒绝掉。
- 修改建议：该参数通常在秒杀或者大并发的场景下使用，对数据库具有较好的保护作用。

## innodb_buffer_pool_instances

- 适用版本：8.0、5.7、5.6、5.5
- 默认值：{LEAST(DBInstanceClassMemory/1073741824, 8)}
- 修改完后是否需要重启：是
- 作用：将innodb_buffer_pool_size大于1 GB的内存缓冲池拆分成多个实例进行维护，每个实例都有自己的锁、信号量、物理块（Buffer chunks）以及逻辑链表，各实例之间没有竞争关系，可以并发读取与写入。
- 修改建议：对于一些较老的实例，可能存在innodb_buffer_pool_size大于1 GB但是innodb_buffer_pool_instances值为1的情况，建议按当前参数模板默认值重新设置。

## table_open_cache_instances

- 适用版本：8.0、5.7、5.6
- 默认值：{LEAST(DBInstanceClassMemory/1073741824, 16)}
- 修改完后是否需要重启：是
- 作用：将打开的表缓存划分为几个大小为`table_open_cache / table_open_cache_instances`的较小缓存实例，减少会话（Session）间表缓存的争用。
- 修改建议：对于一些较老的实例，可能存在innodb_buffer_pool_size大于1 GB但是table_open_cache_instances值为1的情况，建议按当前参数模板默认值重新设置。

## table_open_cache

- 适用版本：8.0、5.7、5.6
- 默认值：{LEAST(DBInstanceClassMemory/1073741824*256, 2048)
- 修改完后是否需要重启：否
- 作用：表缓存的数量，表缓存用于将表加在到缓存中，实现快速访问该表。如果该值偏小，则有可能在高并发时引起SQL性能问题。
- 修改建议：执行`SHOW GLOBAL STATUS LIKE 'Open_tables'`，如果返回的值接近或等于当前**table_open_cache**设置的值时，建议适当调大本参数。

## innodb_adaptive_hash_index

- 适用版本：8.0、5.7、5.6
- 默认值：OFF
- 修改完后是否需要重启：否
- 作用：是否开启自适应哈希索引。自适应哈希索引可以根据您提供的查询条件加速定位到叶子节点，减少IO次数。
- 修改建议：开启本参数是否带来性能提升和业务SQL有关系，部分操作可能引发自适应哈希索引维护（例如对表执行DDL操作时自适应哈希索引被内存清理），导致执行SQL被阻塞或性能下降。建议评估业务SQL，如非必要请将该参数设置为OFF。





## Aliyun RDS MySQL8

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

