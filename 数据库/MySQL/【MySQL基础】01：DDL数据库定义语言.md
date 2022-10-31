## DDL（Data Definition Language）数据库定义语言

### DDL-数据库操作

>   使用DDL语句可以创建数据库、查询数据库、修改数据库、删除数据库

**查询数据库**

```SQL
-- 列出所有的数据库
show databases;
-- 显示指定名称的数据库创建的SQL指令
show create database <database_name>;
```

**创建数据库**

```sql
-- 创建数据库
create database <database_name>;
-- 创建数据库同时指定编码格式
create database <database_name> character set utf8;
-- 如果指定名称的数据库不存在则创建数据库
create database if not exists <database_name>;
```

**使用/切换数据库**

```sql
use <database_name>;
```

**修改数据库**

```sql
-- 修改数据库的字符集
alter database <database_name> character set utf8;
```

**删除数据库**

```sql
-- 删除指定数据库
drop database <database_name>;
-- 如果指定数据库存在则删除
drop database if exists <database_name>;
```

### DDL-数据表操作

>   数据表可以看作一个二维的表格，由多个列组成，表格中的每个列可以称为一个字段。

| id   | name  | age  | gender |
| ---- | ----- | ---- | ------ |
| 1    | Alan  | 20   | 男     |
| 2    | Alice | 19   | 女     |

以如上表格为例：`id`，`name`，`age`，`gender`就是列，每一行就称为记录。

**创建数据表**

```sql
create table tableName(
	column_1 <dataType> [constraint],
    column_2 <dataType> [constraint],
    column_3 <dataType> [constraint]
);
```

**查询数据表**

```SQL
show tables;
```

**查询表结构**

```sql
desc <table_name>;
```

**删除数据表**

```sql
-- 删除数据表
drop table <table_name>;
-- 如果指定数据表存在则删除
drop table if exists <table_name>;
```

**修改数据表**

```sql
-- 修改表名
alter table <table_name> rename to <new_table_name>;
-- 修改数据表字符集
alter table <table_name> character set utf8;
-- 添加列
alter table <table_name> add <column_name> <dataType>;
-- 修改列的列名与数据类型
alter table <table_name> change <old_column_name> <new_column_name> <data_type>;
-- 只修改列的数据类型
alter table <table_name> modify <column_name> <new_data_type>;
-- 删除列
alter table <table_name> drop <column_name>;
```

##  sample sql

```sql
-- 创建数据库
create database dome character set utf8;
-- 查看表信息
show create database dome ;
-- 修改数据库字符集
alter database dome character set utf8mb4;
show create database dome ;
-- 切换数据库
use dome;
-- 创建表
create table sample_table(
	id int(7) not null unique,
	name varchar(32) not null,
	age int(2) not null,
	gender char(2) not null
);
show tables;
-- 查看表结构
desc sample_table;
-- 修改表名
alter table sample_table rename to sample;
show tables;
-- 修改数据表字符集
alter table sample character set utf8;
-- 添加列
alter table sample add test int;
desc sample;
-- 修改列的列名与数据类型
alter table sample change test text_context varchar(64);
desc sample;
-- 只修改列的数据类型
alter table sample modify text_context text;
desc sample;
-- 删除列
alter table sample drop text_context;
desc sample;
-- 删除表
drop table if exists sample;
show tables;
-- 移除数据库
drop database dome;
```

