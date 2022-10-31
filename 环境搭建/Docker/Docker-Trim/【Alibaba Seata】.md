## Docker安装Seata

```shell
# 上传Seata配置到Nacos
sh nacos-config.sh [-h host] [-p port] [-g group] [-t tenant] [-u username] [-w password]

sh nacos-config.p -h 127.0.0.1 -p 8848 -g SEATA_GROUP -u nacos -w nacos
-h nacos地址
-p nacos端口，默认 8848
-g nacos配置组
-t naocos的命名空间id
-u -p 登录nacos的账号、密码
# 在..\config-center\nacos 目录下执行
```

```bash
docker pull seataio/seata-server:1.3.0

docker run -d \
--name seata \
--restart=always \
-p 1210:8091 \
-e SEATA_IP=101.35.190.150 \
-e SEATA_PORT=8091 \
-e STORE_MODE=db \
-e SEATA_CONFIG_NAME=file:/root/seata-config/registry \
-v /opt/seata/conf:/root/seata-config \
seataio/seata-server:1.3.0


docker run -d --name seata-1.3.0 -p 8091:8091 -e SEATA_IP=127.0.0.1 -e SEATA_PORT=8091 -e STORE_MODE=db -e SEATA_CONFIG_NAME=file:/root/seata-config/registry -v D:\LocalData\Docker\seata\conf:/root/seata-config seataio/seata-server:1.3.0
```

