## 1. 环境准备

| 软件/系统      | 当前文章版本 | 下载链接                                                     |
| -------------- | ------------ | ------------------------------------------------------------ |
| CentOS         | 7.6          | [VM虚拟机](https://www.aliyundrive.com/s/ffN4inSTDda)        |
| Docker         | 20.10.10     | [Aliyun Docker镜像安装](https://developer.aliyun.com/mirror/docker-ce?spm=a2c6h.13651102.0.0.197d1b119ohM9e) |
| Docker-Compose | 1.29.2       | [docker-compose](「docker-compose」，点击链接保存，或者复制本段内容，打开「阿里云盘」APP ，无需下载极速在线查看，视频原画倍速播放。 链接：https://www.aliyundrive.com/s/VaMJgvGfrxV) |
| Redis          | 5.0.14       | Docker镜像 docker pull redis:5.0.14                          |

## 2. 配置文件参数

### 2.1 redis配置文件 主要修改项

```plain
################################## NETWORK #####################################
# bind 限制请求来源 默认127.0.0.1
bind 0.0.0.0

# 实例保护 默认 yes
protected-mode no

# Redis实例对外端口
port 6379

################################# GENERAL #####################################
# 允许Redis后台运行
daemonize yes

################################ SNAPSHOTTING  ################################
# 控制什么时候生成rdb文件，PS： “save 900 1”表示如果900秒内至少1个key发生变化（新增、修改和删除），则重写rdb文件；
save 900 1
save 300 10
save 60 10000

################################# REPLICATION #################################
# 指向作为那个节点的从节点
# replicaof <masterip> <masterport>

# 主节点密码 
# masterauth <master-password>
masterauth password

# 从节点只读，默认yes
replica-read-only yes

# 向主节点暴露IP 和 Port
# replica-announce-ip 5.5.5.5
# replica-announce-port 1234

replica-announce-ip 192.168.10.1
replica-announce-port 6379
################################## SECURITY ###################################
# 设置Redis实例的密码，每个节点密码设置一致
# requirepass foobared
requirepass password

################################### CLIENTS ####################################
# Redis实例最大连接数
# maxclients 10000

############################## MEMORY MANAGEMENT ################################
# maxmemory <bytes>
# 设置Redis允许最大内存 PS：256M 需要转换成字节
maxmemory  268435456

############################## APPEND ONLY MODE ###############################
# 开启持久化，默认 no
appendonly yes

# The name of the append only file (default: "appendonly.aof")
# 持久化输出文件名
appendfilename "appendonly.aof"
```

### 2.2 sentinel配置文件

```plain
# bind 127.0.0.1 192.168.1.1
bind 0.0.0.0

# protected-mode no
protected-mode no

# port <sentinel-port>
# RedisSentinel 运行端口
port 26379

# 允许后台运行
daemonize no

# 向其他Sentinel实例暴露IP和Port
# sentinel announce-ip <ip>
# sentinel announce-port <port>
sentinel announce-ip 192.168.20.1
sentinel announce-port 26379


# sentinel monitor <master-name> <ip> <redis-port> <quorum>
# Sentinel 监听的主节点
# sentinel monitor <Master节点名字> <Master节点IP> <Master节点端口号> <Sentinel投票数>
sentinel monitor mymaster 192.168.20.1 6379 2

# sentinel连接主节点密码
# sentinel auth-pass <master-name> <password>
sentinel auth-pass mymaster password

# sentinel down-after-milliseconds <master-name> <milliseconds>
# Default is 30 seconds.
# sentinel判断实例下线超时时间 默认30秒
sentinel down-after-milliseconds mymaster 30000

# sentinel parallel-syncs <master-name> <numreplicas>
#
# How many replicas we can reconfigure to point to the new replica simultaneously
# during the failover. Use a low number if you use the replicas to serve query
# to avoid that all the replicas will be unreachable at about the same
# time while performing the synchronization with the master.
sentinel parallel-syncs mymaster 1

# Default is 3 minutes.
# Redis故障转移超时，默认三分钟
sentinel failover-timeout mymaster 180000
```

## 3. 基于docker-compose单机部署Redis主从

### 3.1 redis主从docker-compose yaml文件

```yaml
version: '3'
services:
  master:
    image: redis:5.0.14
    container_name: redis-master
    restart: always
    environment: 
      - TZ=Asia/Shanghai
    privileged: true # 拥有容器内命令执行的权限
    ports: # 容器对外暴露端口
      - 6379:6379
    volumes: # 挂载数据卷
      - ./data/master/:/data
      - ./conf/master.conf:/etc/redis/redis.conf
    command: redis-server /etc/redis/redis.conf
      
  slave01:
    image: redis:5.0.14
    container_name: redis-slave-01
    restart: always
    environment: 
      - TZ=Asia/Shanghai
    privileged: true # 拥有容器内命令执行的权限
    ports:
      - 6002:6379
    volumes:
      - ./data/slave01/:/data
      - ./conf/slave.conf:/etc/redis/redis.conf
    command: redis-server /etc/redis/redis.conf

  slave02:
    image: redis:5.0.14
    container_name: redis-slave-02
    restart: always
    environment: 
      - TZ=Asia/Shanghai
    privileged: true # 拥有容器内命令执行的权限
    ports:
      - 6003:6379
    volumes:
      - ./data/slave02/:/data
      - ./conf/slave.conf:/etc/redis/redis.conf
    command: redis-server /etc/redis/redis.conf
```

### 3.2 sentinel docker-compose yaml文件

```yaml
version: '3'
services:
  sentinel-01:
    image: redis:5.0.14
    container_name: redis-sentinel-01
    restart: always
    environment: 
      - TZ=Asia/Shanghai
    privileged: true # 拥有容器内命令执行的权限
    ports:
      - 6401:26379
    volumes:
      - /<sentinel 配置文件路径>/redis5.0_sentinel_01.conf:/etc/redis/sentinel.conf
    command: redis-sentinel /etc/redis/sentinel.conf

  sentinel-02:
    image: redis:5.0.14
    container_name: redis-sentinel-02
    restart: always
    environment: 
      - TZ=Asia/Shanghai
    privileged: true # 拥有容器内命令执行的权限
    ports:
      - 6402:26379
    volumes:
      - /<sentinel 配置文件路径>/redis5.0_sentinel_02.conf:/etc/redis/sentinel.conf
    command: redis-sentinel /etc/redis/sentinel.conf

  sentinel-03:
    image: redis:5.0.14
    container_name: redis-sentinel-03
    restart: always
    environment: 
      - TZ=Asia/Shanghai
    privileged: true # 拥有容器内命令执行的权限
    ports:
      - 6403:26379
    volumes:
      - /<sentinel 配置文件路径>/redis5.0_sentinel_03.conf:/etc/redis/sentinel.conf
    command: redis-sentinel /etc/redis/sentinel.conf
```

### 3.3 注意事项

1. docker-compose.yaml 文件不要在同一文件包启动，虽然不影响但会有警告。
2. 每个节点使用独立的配置文件，因为sentinel在允许的过程中会对配置文件进行修改。

## 4.1 Redis主从搭建

### 4.1.1 Redis主从启动

```yaml
[root@redis-server docker-compose-files]# docker-compose -f redis-master-slave/redis-master-slave.yaml up -d
Creating network "redis-master-slave_default" with the default driver
Creating redis-master   ... done
Creating redis-slave-01 ... done
Creating redis-slave-02 ... done
[root@redis-server docker-compose-files]# docker ps
CONTAINER ID   IMAGE          COMMAND                  CREATED          STATUS          PORTS                                       NAMES
c02d43b1cf05   redis:5.0.14   "docker-entrypoint.s…"   37 seconds ago   Up 35 seconds   0.0.0.0:6003->6379/tcp, :::6003->6379/tcp   redis-slave-02
f41cd92de296   redis:5.0.14   "docker-entrypoint.s…"   37 seconds ago   Up 35 seconds   0.0.0.0:6002->6379/tcp, :::6002->6379/tcp   redis-slave-01
60689c869201   redis:5.0.14   "docker-entrypoint.s…"   37 seconds ago   Up 35 seconds   0.0.0.0:6001->6379/tcp, :::6001->6379/tcp   redis-master
```

### 4.1.2 验证Redis主从状态

```bash
[root@redis-server docker-compose-files]# docker exec -it redis-master redis-cli -a redis5
127.0.0.1:6379> info Replication
# Replication
role:master
connected_slaves:2
slave0:ip=192.168.26.131,port=6002,state=online,offset=182,lag=0
slave1:ip=192.168.26.131,port=6003,state=online,offset=182,lag=0
master_replid:01d8c71eabde442af17b1f5b873cceb4a332904a
master_replid2:0000000000000000000000000000000000000000
master_repl_offset:182
second_repl_offset:-1
repl_backlog_active:1
repl_backlog_size:1048576
repl_backlog_first_byte_offset:1
repl_backlog_histlen:182

[root@redis-server docker-compose-files]# docker exec -it redis-slave-01 redis-cli -a redis5
Warning: Using a password with '-a' or '-u' option on the command line interface may not be safe.
127.0.0.1:6379> info Replication
# Replication
role:slave
master_host:192.168.26.131
master_port:6001
master_link_status:up
master_last_io_seconds_ago:1
master_sync_in_progress:0
slave_repl_offset:294
slave_priority:100
slave_read_only:1
connected_slaves:0
master_replid:01d8c71eabde442af17b1f5b873cceb4a332904a
master_replid2:0000000000000000000000000000000000000000
master_repl_offset:294
second_repl_offset:-1
repl_backlog_active:1
repl_backlog_size:1048576
repl_backlog_first_byte_offset:1
repl_backlog_histlen:294
```

### 4.1.3 Redis主从复制状态校验

```bash
[root@redis-server docker-compose-files]# docker exec -it redis-master redis-cli -a redis5
Warning: Using a password with '-a' or '-u' option on the command line interface may not be safe.
127.0.0.1:6379> set link test
OK
127.0.0.1:6379> 
[root@redis-server docker-compose-files]# docker exec -it redis-slave-01 redis-cli -a redis5
Warning: Using a password with '-a' or '-u' option on the command line interface may not be safe.
127.0.0.1:6379> get link
"test"
```

## 4.2 Sentinel搭建

### 4.2.1 sentinel启动

```bash
[root@redis-server docker-compose-files]# docker-compose -f redis-sentinel/redis-sentinel.yaml up -d
Creating network "redis-sentinel_default" with the default driver
Creating redis-sentinel-01 ... done
Creating redis-sentinel-02 ... done
Creating redis-sentinel-03 ... done
[root@redis-server docker-compose-files]# docker ps
CONTAINER ID   IMAGE          COMMAND                  CREATED          STATUS          PORTS                                                   NAMES
0e9c1552cdb6   redis:5.0.14   "docker-entrypoint.s…"   5 minutes ago    Up 5 minutes    6379/tcp, 0.0.0.0:6403->26379/tcp, :::6403->26379/tcp   redis-sentinel-03
6ce3c9b75b78   redis:5.0.14   "docker-entrypoint.s…"   5 minutes ago    Up 5 minutes    6379/tcp, 0.0.0.0:6402->26379/tcp, :::6402->26379/tcp   redis-sentinel-02
1c3c2097477f   redis:5.0.14   "docker-entrypoint.s…"   5 minutes ago    Up 5 minutes    6379/tcp, 0.0.0.0:6401->26379/tcp, :::6401->26379/tcp   redis-sentinel-01
```

### 4.2.2 Sentinel集群状态

```bash
[root@redis-server docker-compose-files]# docker exec -it redis-sentinel-01 redis-cli -p 26379
127.0.0.1:26379> info Sentinel
# Sentinel
sentinel_masters:1
sentinel_tilt:0
sentinel_running_scripts:0
sentinel_scripts_queue_length:0
sentinel_simulate_failure_flags:0
master0:name=mymaster,status=ok,address=192.168.26.131:6001,slaves=2,sentinels=3
127.0.0.1:26379> 
# Sentinel log 初始化成功并发现主节点与从节点
1:X 30 Nov 2021 14:44:34.923 # oO0OoO0OoO0Oo Redis is starting oO0OoO0OoO0Oo
1:X 30 Nov 2021 14:44:34.923 # Redis version=5.0.14, bits=64, commit=00000000, modified=0, pid=1, just started
1:X 30 Nov 2021 14:44:34.923 # Configuration loaded
1:X 30 Nov 2021 14:44:34.924 * Running mode=sentinel, port=26379.
1:X 30 Nov 2021 14:44:34.924 # WARNING: The TCP backlog setting of 511 cannot be enforced because /proc/sys/net/core/somaxconn is set to the lower value of 128.
1:X 30 Nov 2021 14:44:34.938 # Sentinel ID is fbd6480eea4425b5435c0b9a52d3660884a8f210
1:X 30 Nov 2021 14:44:34.938 # +monitor master mymaster 192.168.26.131 6001 quorum 2
1:X 30 Nov 2021 14:44:34.948 * +slave slave 192.168.26.131:6002 192.168.26.131 6002 @ mymaster 192.168.26.131 6001
1:X 30 Nov 2021 14:44:34.951 * +slave slave 192.168.26.131:6003 192.168.26.131 6003 @ mymaster 192.168.26.131 6001
1:X 30 Nov 2021 14:44:37.202 * +sentinel sentinel 50fa1775787a77d4bfcff1bcdc611ec5e5b3bc0f 192.168.26.131 6403 @ mymaster 192.168.26.131 6001
1:X 30 Nov 2021 14:44:37.211 * +sentinel sentinel 2184e00073d856f122ee585ec7d753e5fdcdfed2 192.168.26.131 6402 @ mymaster 192.168.26.131 6001
```

## 4.3 主从切换

### 4.3.1 停止当前主节点

```plain
docker stop redis-master
```

### 4.3.2 查看Sentinel日志

```bash
# sentinel主观发现主节点宕机
1:X 30 Nov 2021 15:57:33.331 # +sdown master mymaster 192.168.26.131 6001
# sentinel开始投票
1:X 30 Nov 2021 15:57:33.409 # +odown master mymaster 192.168.26.131 6001 #quorum 2/2
# 开启新“纪元”
1:X 30 Nov 2021 15:57:33.409 # +new-epoch 1
# 尝试转移
1:X 30 Nov 2021 15:57:33.409 # +try-failover master mymaster 192.168.26.131 6001
1:X 30 Nov 2021 15:57:33.411 # +vote-for-leader fbd6480eea4425b5435c0b9a52d3660884a8f210 1
1:X 30 Nov 2021 15:57:33.413 # 2184e00073d856f122ee585ec7d753e5fdcdfed2 voted for fbd6480eea4425b5435c0b9a52d3660884a8f210 1
1:X 30 Nov 2021 15:57:33.413 # 50fa1775787a77d4bfcff1bcdc611ec5e5b3bc0f voted for fbd6480eea4425b5435c0b9a52d3660884a8f210 1
1:X 30 Nov 2021 15:57:33.470 # +elected-leader master mymaster 192.168.26.131 6001
1:X 30 Nov 2021 15:57:33.470 # +failover-state-select-slave master mymaster 192.168.26.131 6001
# 选择一个从节点作为新的master节点
1:X 30 Nov 2021 15:57:33.578 # +selected-slave slave 192.168.26.131:6002 192.168.26.131 6002 @ mymaster 192.168.26.131 6001
1:X 30 Nov 2021 15:57:33.578 * +failover-state-send-slaveof-noone slave 192.168.26.131:6002 192.168.26.131 6002 @ mymaster 192.168.26.131 6001
1:X 30 Nov 2021 15:57:33.656 * +failover-state-wait-promotion slave 192.168.26.131:6002 192.168.26.131 6002 @ mymaster 192.168.26.131 6001
1:X 30 Nov 2021 15:57:34.495 # +promoted-slave slave 192.168.26.131:6002 192.168.26.131 6002 @ mymaster 192.168.26.131 6001
1:X 30 Nov 2021 15:57:34.495 # +failover-state-reconf-slaves master mymaster 192.168.26.131 6001
1:X 30 Nov 2021 15:57:34.549 * +slave-reconf-sent slave 192.168.26.131:6003 192.168.26.131 6003 @ mymaster 192.168.26.131 6001
1:X 30 Nov 2021 15:57:35.571 # -odown master mymaster 192.168.26.131 6001
1:X 30 Nov 2021 15:57:35.571 * +slave-reconf-inprog slave 192.168.26.131:6003 192.168.26.131 6003 @ mymaster 192.168.26.131 6001
1:X 30 Nov 2021 15:57:35.571 * +slave-reconf-done slave 192.168.26.131:6003 192.168.26.131 6003 @ mymaster 192.168.26.131 6001
1:X 30 Nov 2021 15:57:35.663 # +failover-end master mymaster 192.168.26.131 6001
# 监听新的master节点，并将其他两个节点作为从节点加入master
1:X 30 Nov 2021 15:57:35.663 # +switch-master mymaster 192.168.26.131 6001 192.168.26.131 6002
1:X 30 Nov 2021 15:57:35.663 * +slave slave 192.168.26.131:6003 192.168.26.131 6003 @ mymaster 192.168.26.131 6002
1:X 30 Nov 2021 15:57:35.663 * +slave slave 192.168.26.131:6001 192.168.26.131 6001 @ mymaster 192.168.26.131 6002
1:X 30 Nov 2021 15:58:05.692 # +sdown slave 192.168.26.131:6001 192.168.26.131 6001 @ mymaster 192.168.26.131 6002
```

### 4.3.3 查看从节点信息

```bash
[root@redis-server redis-sentinel]# docker exec -it redis-slave-01 redis-cli -a redis5
Warning: Using a password with '-a' or '-u' option on the command line interface may not be safe.
127.0.0.1:6379> info Replication
# Replication
role:master
connected_slaves:1
slave0:ip=192.168.26.131,port=6003,state=online,offset=1100611,lag=0
master_replid:8202388d6de982843215783718188af95f783b3f
master_replid2:01d8c71eabde442af17b1f5b873cceb4a332904a
master_repl_offset:1100625
second_repl_offset:914089
repl_backlog_active:1
repl_backlog_size:1048576
repl_backlog_first_byte_offset:52050
repl_backlog_histlen:1048576
127.0.0.1:6379> 
# 从节点已从 slave -> master
```

### 4.3.4 上线原主节点后查看节点信息

```bash
[root@redis-server redis-sentinel]# docker start redis-master
redis-master
[root@redis-server redis-sentinel]# docker exec -it redis-slave-01 redis-cli -a redis5
Warning: Using a password with '-a' or '-u' option on the command line interface may not be safe.
127.0.0.1:6379> info Replication
# Replication
role:master
connected_slaves:2
slave0:ip=192.168.26.131,port=6003,state=online,offset=1116934,lag=1
slave1:ip=172.18.0.1,port=6379,state=online,offset=1117360,lag=0
master_replid:8202388d6de982843215783718188af95f783b3f
master_replid2:01d8c71eabde442af17b1f5b873cceb4a332904a
master_repl_offset:1117360
second_repl_offset:914089
repl_backlog_active:1
repl_backlog_size:1048576
repl_backlog_first_byte_offset:68785
repl_backlog_histlen:1048576
127.0.0.1:6379> 
# 原master节点作为现master节点的从节点

[root@redis-server redis-sentinel]# docker exec -it redis-master redis-cli -a redis5
Warning: Using a password with '-a' or '-u' option on the command line interface may not be safe.
127.0.0.1:6379> info Replication
# Replication
role:slave
master_host:192.168.26.131
master_port:6002
master_link_status:up
master_last_io_seconds_ago:1
master_sync_in_progress:0
slave_repl_offset:1124644
slave_priority:100
slave_read_only:1
connected_slaves:0
master_replid:8202388d6de982843215783718188af95f783b3f
master_replid2:0000000000000000000000000000000000000000
master_repl_offset:1124644
second_repl_offset:-1
repl_backlog_active:1
repl_backlog_size:1048576
repl_backlog_first_byte_offset:1115194
repl_backlog_histlen:9451
127.0.0.1:6379> 
# 现master节点出现两个从节点
```