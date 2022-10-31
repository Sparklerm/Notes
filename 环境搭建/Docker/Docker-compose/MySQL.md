```shell
cd /root
mkdir mysql mysql/mysql5.7 mysql/mysql5.7/conf/ mysql/mysql5.7/data/ mysql/mysql8 mysql/mysql8/conf/ mysql/mysql8/data/
cd mysql
rouch docker-compose.yaml
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
```

```yaml
version: '3'
services:
  mysql57:
    image: mysql:5.7.35
    restart: always
    container_name: mysql57
    environment:
      MYSQL_ROOT_PASSWORD: 123456
      TZ: Asia/Shanghai
    ports:
      - 3306:3306
    volumes:
      - ./mysql57/data:/var/lib/mysql
      - ./mysql57/conf/my.cnf:/etc/mysql/my.cnf
    command:
      --max_connections=1000
      --character-set-server=utf8mb4
      --collation-server=utf8mb4_general_ci
      --default-authentication-plugin=mysql_native_password

  mysql8:
    image: mysql:8.0.18
    restart: always
    container_name: mysql8
    environment:
      MYSQL_ROOT_PASSWORD: 123456
      TZ: Asia/Shanghai
    ports:
      - 3308:3306
    volumes:
      - ./mysql8/data:/var/lib/mysql
      - ./mysql8/conf/my.cnf:/etc/mysql/my.cnf
    command:
      --max_connections=1000
      --character-set-server=utf8mb4
      --collation-server=utf8mb4_general_ci
      --default-authentication-plugin=mysql_native_password
  
  phpmyadmin:
    image: phpmyadmin
    restart: always
    container_name: phpmyadmin
    ports:
      - 80:80
    environment:
      - PMA_ARBITRARY=1
```

