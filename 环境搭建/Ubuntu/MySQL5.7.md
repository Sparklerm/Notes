```
[mysql]
# 设置mysql客户端默认字符集
default-character-set=utf8
socket=/var/lib/mysql/mysql.sock
[mysqld]
skip-name-resolve
#设置3306端⼝
port = 3306
socket=/var/lib/mysql/mysql.sock
# 设置mysql的安装⽬录
basedir=/usr/local/soft/mysql
# 设置mysql数据库的数据的存放⽬录
datadir=/usr/local/soft/mysql/data
# 允许最⼤连接数
max_connections=200
# 服务端使⽤的字符集默认为8⽐特编码的latin1字符集
character-set-server=utf8
# 创建新表时将使⽤的默认存储引擎
default-storage-engine=INNODB
lower_case_table_names=1
max_allowed_packet=16M
```

```
cd /usr/local/soft/mysql
./bin/mysqld --initialize --user=mysql --basedir=/usr/local/mysql --datadir=/usr/local/mysql/data
```

```
cp ./support-files/mysql.server /etc/init.d/mysqld


./bin/mysqld --initialize --user=mysql --basedir=/usr/local/mysql/mysql-5.7.27-aarch64 --datadir=/usr/local/mysql/mysql-5.7.27-aarch64/data

cp ./support-files/mysql.server /etc/init.d/mysqld
```

