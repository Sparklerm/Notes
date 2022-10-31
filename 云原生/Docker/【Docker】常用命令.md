# 映射端口号

```bash
# 1. Docker命令：端口映射端口
-p 8008:8008
# 2. Docker命令：批量映射
--expose=7000-8000
# 3. Dockerfile：批量映射端口
EXPOSE 7000-8000
```

```
    -p：公开指定的端口
    -P：公开EXPOSE指定的所有端口
```

# 同步宿主机时间

## 创建时同步时间

### docker run 添加时间参数

```bash
-v /etc/localtime:/etc/localtime

# 实例1
docker run -p 3306:3306 --name mysql -v /etc/localtime:/etc/localtime

# 实例2
docker run \
    --detach \
    --restart always \
    --name 'scribe' \
    --publish 11315:11315 \
    --mount type=bind,source=/data/gop/,destination=/data/gop/,consistency=consistent \
    -v /etc/localtime:/etc/localtime \
    wsgzao/facebook-scribe
```



### Dockerfile

```
# 方法1
# 添加时区环境变量，亚洲，上海
ENV TimeZone=Asia/Shanghai
# 使用软连接，并且将时区配置覆盖/etc/timezone
RUN ln -snf /usr/share/zoneinfo/$TimeZone /etc/localtime && echo $TimeZone > /etc/timezone

# 方法2
# CentOS
RUN echo "Asia/shanghai" > /etc/timezone
# Ubuntu
RUN cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
```



### docker-compose

```
#第一种方式(推荐)：
environment:
  TZ: Asia/Shanghai
  
#第二种方式：
environment:
  SET_CONTAINER_TIMEZONE=true
  CONTAINER_TIMEZONE=Asia/Shanghai

#第三种方式：
volumes:
  - /etc/timezone:/etc/timezone
  - /etc/localtime:/etc/localtime
```



## 容器创建后同步时间

```
# 1. 在宿主机操作
docker cp /etc/localtime 【容器ID或者NAME】:/etc/localtime
docker cp -L /usr/share/zoneinfo/Asia/Shanghai 【容器ID或者NAME】:/etc/localtime
# 2. 容器内操作
ln -sf /usr/share/zoneinfo/Asia/Singapore /etc/localtime
```

