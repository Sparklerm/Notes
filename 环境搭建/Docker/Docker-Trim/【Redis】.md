# Docker安装Redis

```shell
# 拉取镜像
docker pull redis:5.0.14
docker pull redis:6.0.16

# 提前准备好配置文件及挂载目录
docker run -d \
--name redis \
--restart=always \
-m 256m \
-p 6379:6379 \
-v /opt/redis/conf/redis.conf:/etc/redis/redis.conf \
-v /opt/redis/data:/data \
redis:5.0.14 redis-server /etc/redis/redis.conf
```

```
#  --appendonly yes 开启持久化,可作用于配置文件没有配置持久化时
# Redis配置文件取官方默认配置文件，按需修改以下配置项
#端口
port 6379
# 访问来源限制 默认bind 127.0.0.1
bind 0.0.0.0
# appendonly 默认no ,yes 开启持久化
appendonly yes 
# requirepass 访问密码 默认关闭
requirepass 123456

# 256M
maxmemory 268435456
maxclients 10000
```