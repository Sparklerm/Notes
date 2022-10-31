## Docker安装Nacos

```shell
# 拉取镜像
docker pull nacos/nacos-server:1.4.2

# -e NACOS_APPLICATION_PORT 指定Nacos启动端口
# SPRING_DATASOURCE_PLATFORM 指定Nacos数据源
# -e MYSQL_SERVICE_... 指定连接信息
- -Xmx: 指定JVM的最大堆大小，如:-Xmx=2g
- -Xms: 指定JVM的最小堆大小，如:-Xms=2g，高并发应用，建议和-Xmx一样，防止因为内存收缩／突然增大带来的性能影响。
- -Xmn: 指定JVM中NewGeneration的大小，如:-Xmn256m。这个参数很影响性能，如果程序需要比较多的临时内存，可以适当设置高点。

# 单机启动
docker run -d \
--name nacos \
--restart=always \
-p 8848:8848 \
-e MODE=standalone \
-e NACOS_APPLICATION_PORT=8848 \
-e SPRING_DATASOURCE_PLATFORM=mysql \
-e MYSQL_SERVICE_HOST=rds-aliyun.mysql.rds.aliyuncs.com \
-e MYSQL_SERVICE_PORT=9527 \
-e MYSQL_SERVICE_USER=develop \
-e MYSQL_SERVICE_PASSWORD=@Dev#123 \
-e MYSQL_SERVICE_DB_NAME=nacos \
-e JVM_XMS=256m \
-e JVM_XMX=512m \
-e JVM_XMN=256m \
nacos/nacos-server:1.4.2

# 集群启动
docker run -d \
--name nacos-node-1 \
--restart=always \
-p 8845:8845 \
-e MODE=cluster \
-e NACOS_APPLICATION_PORT=8845 \
-e NACOS_SERVERS="127.0.0.1:8845 127.0.0.1:8846 127.0.0.1:8847" \
-e NACOS_SERVER_IP=127.0.0.1 \
-e SPRING_DATASOURCE_PLATFORM=mysql \
-e MYSQL_SERVICE_HOST=127.0.0.1 \
-e MYSQL_SERVICE_PORT=3306 \
-e MYSQL_SERVICE_USER=root \
-e MYSQL_SERVICE_PASSWORD=123456 \
-e MYSQL_SERVICE_DB_NAME=nacos \
-e JVM_XMS=256m \
-e JVM_XMX=256m \
-e JVM_XMN=128m \
nacos/nacos-server:1.4.2

...
```