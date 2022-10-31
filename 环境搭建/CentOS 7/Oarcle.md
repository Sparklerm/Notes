# Oracle 19c

## 安装**oracle-database-priinstall**

```
yum -y localinstall ./oracle-database-preinstall-19c-1.0-1.el7.x86_64.rpm
```



## 安装Oracle数据库

```
yum -y localinstall ./oracle-database-ee-19c-1.0-1.x86_64.rpm
```

## 编辑Oracle配置文件

```
# 可修改字符集
vim /etc/init.d/oracledb_ORCLCDB-19c

# 可配置端口等信息
cat /etc/sysconfig/oracledb_ORCLCDB-19c.conf 
```

## Oracle 初始化

```bash
/etc/init.d/oracledb_ORCLCDB-19c configure
```

## 添加环境变量

```bash
vim /etc/profile.d/oracle19c.sh

export  ORACLE_HOME=/opt/oracle/product/19c/dbhome_1
export  PATH=$PATH:/opt/oracle/product/19c/dbhome_1/bin
export  ORACLE_SID=ORCLCDB
```

## 修改oracle用户密码 ora

```
[root@localhost profile.d]# passwd oracle
Changing password for user oracle.
New password: 
BAD PASSWORD: The password is shorter than 8 characters
Retype new password: 
passwd: all authentication tokens updated successfully.
```

## 防火墙开启端口

```
systemctl stop firewalld && systemctl disable firewalld
```

## 登陆Oracle控制台

```
sqlplus / as sysdba
```

## 创建表空间和用户

```
# 查看有哪些PDB
show pdbs;

# 查看当前连接的是哪个数据库 (CDB$ROOT是根容器）
show con_name;

# 切换到指定PDB
alter session set container=ORCLPDB1;
show con_name;

# 查看PDB的表空间
select * from v$tablespace;

# 开启表空间
ALTER PLUGGABLE DATABASE OPEN;

# 在PDB中新建一个表空间
CREATE BIGFILE TABLESPACE "表空间名称" DATAFILE '/opt/oracle/oradata/ORCLCDB/ORCLPDB1/sparkler.dbf' SIZE 100M AUTOEXTEND ON NEXT 100M MAXSIZE UNLIMITED LOGGING EXTENT MANAGEMENT LOCAL SEGMENT SPACE MANAGEMENT AUTO;
```



## **新建一个用户并授权**

```
# 创建用户
create user 用户名 identified by 密码;

# 表空间授权
alter user 用户名 default tablespace 表空间;

# 用户授权
grant connect,resource,dba to 用户名;
```

## 监听

### 服务端

```
LISTENER = 
    (DESCRIPTION_LIST = 
        (DESCRIPTION=
          (ADDRESS=(PROTOCOL = TCP)(HOST = 10.244.0.20)(PORT=1522))
      )
    )

SID_LIST_LISTENER =
  (SID_LIST =
    (SID_DESC =
      (GLOBAL_DBNAME = ORCLCDB)
      (SID_NAME = ORCLCDB)
      (ORACLE_HOME = /opt/oracle/product/19c/dbhome_1)
    )
   (SID_DESC =
      (GLOBAL_DBNAME = ORCLPDB1)      
      (SID_NAME = ORCLCDB)
      (ORACLE_HOME = /opt/oracle/product/19c/dbhome_1)
    )
  )

```

### 客户端

```
PDBORCL =
  (DESCRIPTION =
    (ADDRESS_LIST =
      (ADDRESS = (PROTOCOL = TCP)(HOST = oracle.19c)(PORT = 1522))
    )
    (CONNECT_DATA =
      (SERVICE_NAME = ORCLPDB1)
    )
  )

ORCL =
  (DESCRIPTION =
    (ADDRESS_LIST =
      (ADDRESS = (PROTOCOL = TCP)(HOST = oracle.19c)(PORT = 1522))
    )
    (CONNECT_DATA =
      (SERVICE_NAME = ORCLCDB)
    )
  )
```

## 开机启动Oracle

```
su oracle
lsnrctl stop;
lsnrctl start; --开户监听
sqlplus / as sysdba
startup;       --登录后开启数据库
alter session set container=ORCLPDB1;
ALTER PLUGGABLE DATABASE OPEN;
exit;
```



