## Docker安装RabbitMQ

```shell
# 拉取镜像 rabbitmq镜像tag没有management不带Web面板
docker pull rabbitmq:3.9.0-management

# 创建默认用户密码 -e RABBITMQ_DEFAULT_USER=admin -e RABBITMQ_DEFAULT_PASS=admin \

docker run -d \
--name rabbitmq2 \
--restart=always \
-m 256M \
-p 15673:15672 \
-p 5673:5672 \
rabbitmq:3.9.0-management

# 复制配置文件到挂载目录
docker cp rabbitmq:/etc/rabbitmq ./conf
```

```yaml
version: '3'
services:
  rabbitmq:
    image: rabbitmq:3.9.0-management
    container_name: rabbitmq
    hostname: rabbit
    restart: always
    ports:
      - 5672:5672
      - 15672:15672
    environment:
      TZ: Asia/Shanghai
      LANG: en_US.UTF-8
    volumes:
      # - ./data:/var/lib/rabbitmq
      - ./conf:/etc/rabbitmq
```

```
# 启动命令
docker-compose up -d
```

