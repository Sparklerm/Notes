# 什么是索引

>   索引，就是将数据表中的某一列或多列的值取出来构造成更便于查找的结构进行存储，生成数据表的目录；当我们进行数据查询的时候，则先回在目录中进行查找得到对应的数据的地址，然后再到数据表中快速过去数据记录，避免全表扫描。
>
>   **索引的作用：`排序`和`查找`**

# 索引的分类

MySQL中的索引，更具创建索引的列的不同，可以分为：

-   `主键索引`：在数据表的主键字段创建索引，这个字段必须被`primary key`修饰，每张表只能有一个主键。
-   `唯一索引`：在数据表中的唯一列（`unique`）创建索引，此列的所有制只能出现一次，值允许为`null`。
-   `普通索引`：在普通字段上建立的索引，没有特殊约束。
-   `组合索引`：两个及两个以上字段联合主键的索引。

说明：

>   1.   **在创建数据表并同时将列声明为`主键`时，会自动在主键列创建`主键索引`；**
>   2.   **在创建数据表并同时将列声明`唯一约束`，会自动在唯一键列创建`唯一索引`；**



# 索引操作

## 创建索引

### 唯一索引

```sql
-- unique：声明为唯一索引， <index_name> : 索引名，<table_name>：数据表名，<column_name>：需要创建索引的列名
create unique index <index_name> on <table_name>(<column_name>);
```

### 普通索引

```sql
create index <index_name> on <table_name>(<column_name>);
```

### 组合索引

```sql
create index <index_name> on <table_name>(<column_name_1>,<column_name_2>,...,<column_name_n>);
```

### 全文索引

>   全文索引是MySQL5.6版本新增的索引，可以通过此索引进行全文检索，但是MySQL全文检索不支持`中文`，在应用开发中通常是通过搜索引擎<es...>实现全文检索。

```sql
create fulltext index <index_name> on <table_name>(<column_name>);
```

## 使用索引

>   索引生效的条件: where 字句中使用创建了索引的列作为条件。

```sql
-- sample sql
DROP TABLE IF EXISTS `worker`;
CREATE TABLE `worker` (
  `id` int(10) NOT NULL AUTO_INCREMENT,
  `name` varchar(20) DEFAULT NULL,
  `age` int(10) DEFAULT '0',
  `job` varchar(30) DEFAULT NULL,
  `phone` varchar(11) DEFAULT NULL,
  `created_time` datetime DEFAULT NULL,
  PRIMARY KEY (`id`),
  KEY `idx_name_age_job_phone` (`name`,`age`,`job`,`phone`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci;

insert into worker(name,age,job,phone,created_time) values('Bile',22,'Java Developer','13412345678',SYSDATE()),('Reox',26,'Go Developer','13401234567',SYSDATE()),('Wrood',33,'C++ Developer','15512345678',SYSDATE()),('Kiols',24,'Java Developer','13512345678',SYSDATE()),('Qrtus',31,'Python Developer','18812345678',SYSDATE());
```

### 通过执行过程查看索引使用详细

```sql
explain select_statement;
```

```sql
-- sample sql-1 索引生效
explain select * from worker where name = 'Bile' and age  = 22 and job = 'Java Developer' and phone = '13412345678';
-- result
mysql> explain select * from worker where name = 'Bile' and age  = 22 and job = 'Java Developer' and phone = '13412345678';
+----+-------------+--------+------------+------+------------------------+------------------------+---------+-------------------------+------+----------+-------+
| id | select_type | table  | partitions | type | possible_keys          | key                    | key_len | ref                     | rows | filtered | Extra |
+----+-------------+--------+------------+------+------------------------+------------------------+---------+-------------------------+------+----------+-------+
|  1 | SIMPLE      | worker | NULL       | ref  | idx_name_age_job_phone | idx_name_age_job_phone | 258     | const,const,const,const |    1 |   100.00 | NULL  |
+----+-------------+--------+------------+------+------------------------+------------------------+---------+-------------------------+------+----------+-------+
1 row in set (0.03 sec)

-- sample sql-2 索引生效
explain select * from worker where age  = 22 and job = 'Java Developer' and phone = '13412345678' and name = 'Bile';
-- result
mysql> explain select * from worker where age  = 22 and job = 'Java Developer' and phone = '13412345678' and name = 'Bile';
+----+-------------+--------+------------+------+------------------------+------------------------+---------+-------------------------+------+----------+-------+
| id | select_type | table  | partitions | type | possible_keys          | key                    | key_len | ref                     | rows | filtered | Extra |
+----+-------------+--------+------------+------+------------------------+------------------------+---------+-------------------------+------+----------+-------+
|  1 | SIMPLE      | worker | NULL       | ref  | idx_name_age_job_phone | idx_name_age_job_phone | 258     | const,const,const,const |    1 |   100.00 | NULL  |
+----+-------------+--------+------------+------+------------------------+------------------------+---------+-------------------------+------+----------+-------+
1 row in set (0.03 sec)

-- sample sql-3 索引生效
explain select * from worker where name = 'Bile' and age  = 22 and job = 'Java Developer';
-- result
mysql> explain select * from worker where name = 'Bile' and age  = 22 and job = 'Java Developer';
+----+-------------+--------+------------+------+------------------------+------------------------+---------+-------------------+------+----------+-------+
| id | select_type | table  | partitions | type | possible_keys          | key                    | key_len | ref               | rows | filtered | Extra |
+----+-------------+--------+------------+------+------------------------+------------------------+---------+-------------------+------+----------+-------+
|  1 | SIMPLE      | worker | NULL       | ref  | idx_name_age_job_phone | idx_name_age_job_phone | 211     | const,const,const |    1 |   100.00 | NULL  |
+----+-------------+--------+------------+------+------------------------+------------------------+---------+-------------------+------+----------+-------+
1 row in set (0.04 sec)

-- sample sql-4 索引失效
explain select * from worker where age  = 22 and job = 'Java Developer' and phone = '13412345678';
-- result
mysql> explain select * from worker where age  = 22 and job = 'Java Developer' and phone = '13412345678';
+----+-------------+--------+------------+------+---------------+------+---------+------+------+----------+-------------+
| id | select_type | table  | partitions | type | possible_keys | key  | key_len | ref  | rows | filtered | Extra       |
+----+-------------+--------+------------+------+---------------+------+---------+------+------+----------+-------------+
|  1 | SIMPLE      | worker | NULL       | ALL  | NULL          | NULL | NULL    | NULL |    5 |    20.00 | Using where |
+----+-------------+--------+------------+------+---------------+------+---------+------+------+----------+-------------+
1 row in set (0.04 sec)
```

-   `possible_keys`：可能用到的索引
-   `key`：实际使用的索引
-   **组合索引生效与`顺序无关`，MySQL底层的优化器会进行优化，调整索引的顺序**

## 查看索引

```sql
show create table <table_name>\G;

show indexes from <table_name>;

show keys from <table_name>;
```

## 删除索引

```sql
drop index <index_name> on <table_name>;
```

## 索引失效

### 1. 违反最左前缀法则

>   如果索引有多列，要遵守最左前缀法则，即查询从索引的最左前列开始并且不跳过索引中的列

```sql
-- sample sql
explain select * from worker where age  = 22 and job = 'Java Developer' and phone = '13412345678';
-- result
mysql> explain select * from worker where age  = 22 and job = 'Java Developer' and phone = '13412345678';
+----+-------------+--------+------------+------+---------------+------+---------+------+------+----------+-------------+
| id | select_type | table  | partitions | type | possible_keys | key  | key_len | ref  | rows | filtered | Extra       |
+----+-------------+--------+------------+------+---------------+------+---------+------+------+----------+-------------+
|  1 | SIMPLE      | worker | NULL       | ALL  | NULL          | NULL | NULL    | NULL |    5 |    20.00 | Using where |
+----+-------------+--------+------------+------+---------------+------+---------+------+------+----------+-------------+
1 row in set (0.03 sec)
```

### 2. 在索引列上做任何操作

>   如计算、函数、（自动or手动）类型转换等操作，会导致索引失效从而全表扫描

```sql
-- sample sql
explain select * from worker where left(name,5) = 'zhangsan' and age  = 22 and job = 'Java Developer' and phone = '13412345678';
-- result
mysql> explain select * from worker where left(name,5) = 'zhangsan' and age  = 22 and job = 'Java Developer' and phone = '13412345678';
+----+-------------+--------+------------+------+---------------+------+---------+------+------+----------+-------------+
| id | select_type | table  | partitions | type | possible_keys | key  | key_len | ref  | rows | filtered | Extra       |
+----+-------------+--------+------------+------+---------------+------+---------+------+------+----------+-------------+
|  1 | SIMPLE      | worker | NULL       | ALL  | NULL          | NULL | NULL    | NULL |    5 |    20.00 | Using where |
+----+-------------+--------+------------+------+---------------+------+---------+------+------+----------+-------------+
1 row in set (0.04 sec)
```

### 3. 索引范围条件右边的列

>   索引范围条件右边的索引列会失效，

```sql
drop index idx_name_age_job_phone on worker;
create index idx_name on worker(name); 
-- sample sql -1
explain select * from worker where age > 10 and id > 20 and name = 'Bile';
-- result
mysql> explain select * from worker where age > 20 and name = 'Bile' and job = 'Java Developer';
+----+-------------+--------+------------+-------+------------------------+------------------------+---------+------+------+----------+-----------------------+
| id | select_type | table  | partitions | type  | possible_keys          | key                    | key_len | ref  | rows | filtered | Extra                 |
+----+-------------+--------+------------+-------+------------------------+------------------------+---------+------+------+----------+-----------------------+
|  1 | SIMPLE      | worker | NULL       | range | idx_name_age_job_phone | idx_name_age_job_phone | 88      | NULL |    1 |    20.00 | Using index condition |
+----+-------------+--------+------------+-------+------------------------+------------------------+---------+------+------+----------+-----------------------+
1 row in set (0.06 sec)

```

### 4. 尽量使用覆盖索引

>   只访问索引查询（索引列和查询列一致），减少select *

```sql
-- sample sql 1 索引生效
explain select name,age,job,phone from worker where age = 20;
-- result
mysql> explain select name,age,job,phone from worker where age = 20;
+----+-------------+--------+------------+-------+------------------------+------------------------+---------+------+------+----------+--------------------------+
| id | select_type | table  | partitions | type  | possible_keys          | key                    | key_len | ref  | rows | filtered | Extra                    |
+----+-------------+--------+------------+-------+------------------------+------------------------+---------+------+------+----------+--------------------------+
|  1 | SIMPLE      | worker | NULL       | index | idx_name_age_job_phone | idx_name_age_job_phone | 258     | NULL |    5 |    20.00 | Using where; Using index |
+----+-------------+--------+------------+-------+------------------------+------------------------+---------+------+------+----------+--------------------------+
1 row in set (0.06 sec)

-- sample sql 2 索引失效
explain select * from worker where age = 20;
-- result
mysql> explain select * from worker where age = 20;
+----+-------------+--------+------------+------+---------------+------+---------+------+------+----------+-------------+
| id | select_type | table  | partitions | type | possible_keys | key  | key_len | ref  | rows | filtered | Extra       |
+----+-------------+--------+------------+------+---------------+------+---------+------+------+----------+-------------+
|  1 | SIMPLE      | worker | NULL       | ALL  | NULL          | NULL | NULL    | NULL |    5 |    20.00 | Using where |
+----+-------------+--------+------------+------+---------------+------+---------+------+------+----------+-------------+
1 row in set (0.06 sec)
```

### 5. 使用不等于（!=、<>）

>   mysql在使用不等于（!=、<>）的时候无法使用索引会导致全表扫描（除覆盖索引外）

```sql
-- sample sql 1 索引生效
explain select age from worker where age != 20;
explain select age from worker where age <> 20;
-- result
mysql> explain select age from worker where age != 20;
+----+-------------+--------+------------+-------+---------------+------------------------+---------+------+------+----------+--------------------------+
| id | select_type | table  | partitions | type  | possible_keys | key                    | key_len | ref  | rows | filtered | Extra                    |
+----+-------------+--------+------------+-------+---------------+------------------------+---------+------+------+----------+--------------------------+
|  1 | SIMPLE      | worker | NULL       | index | NULL          | idx_name_age_job_phone | 258     | NULL |    5 |    80.00 | Using where; Using index |
+----+-------------+--------+------------+-------+---------------+------------------------+---------+------+------+----------+--------------------------+
1 row in set (0.06 sec)

mysql> explain select age from worker where age <> 20;
+----+-------------+--------+------------+-------+---------------+------------------------+---------+------+------+----------+--------------------------+
| id | select_type | table  | partitions | type  | possible_keys | key                    | key_len | ref  | rows | filtered | Extra                    |
+----+-------------+--------+------------+-------+---------------+------------------------+---------+------+------+----------+--------------------------+
|  1 | SIMPLE      | worker | NULL       | index | NULL          | idx_name_age_job_phone | 258     | NULL |    5 |    80.00 | Using where; Using index |
+----+-------------+--------+------------+-------+---------------+------------------------+---------+------+------+----------+--------------------------+
1 row in set (0.06 sec)

-- sample sql 2 索引失效
explain select * from worker where age != 20;
explain select * from worker where age <> 20;
-- result
mysql> explain select * from worker where age != 20;
+----+-------------+--------+------------+------+---------------+------+---------+------+------+----------+-------------+
| id | select_type | table  | partitions | type | possible_keys | key  | key_len | ref  | rows | filtered | Extra       |
+----+-------------+--------+------------+------+---------------+------+---------+------+------+----------+-------------+
|  1 | SIMPLE      | worker | NULL       | ALL  | NULL          | NULL | NULL    | NULL |    5 |    80.00 | Using where |
+----+-------------+--------+------------+------+---------------+------+---------+------+------+----------+-------------+
1 row in set (0.06 sec)

mysql> explain select * from worker where age <> 20;
+----+-------------+--------+------------+------+---------------+------+---------+------+------+----------+-------------+
| id | select_type | table  | partitions | type | possible_keys | key  | key_len | ref  | rows | filtered | Extra       |
+----+-------------+--------+------------+------+---------------+------+---------+------+------+----------+-------------+
|  1 | SIMPLE      | worker | NULL       | ALL  | NULL          | NULL | NULL    | NULL |    5 |    80.00 | Using where |
+----+-------------+--------+------------+------+---------------+------+---------+------+------+----------+-------------+
1 row in set (0.06 sec)
```

### 6. like以通配符开头

```sql
-- sample sql 1 索引生效
explain select * from worker where name like 'Bile%' and age  = 22 and job = 'Java Developer' and phone = '13412345678';
-- result
mysql> explain select * from worker where name like 'Bile%' and age  = 22 and job = 'Java Developer' and phone = '13412345678';
+----+-------------+--------+------------+-------+------------------------+------------------------+---------+------+------+----------+-----------------------+
| id | select_type | table  | partitions | type  | possible_keys          | key                    | key_len | ref  | rows | filtered | Extra                 |
+----+-------------+--------+------------+-------+------------------------+------------------------+---------+------+------+----------+-----------------------+
|  1 | SIMPLE      | worker | NULL       | range | idx_name_age_job_phone | idx_name_age_job_phone | 258     | NULL |    1 |    20.00 | Using index condition |
+----+-------------+--------+------------+-------+------------------------+------------------------+---------+------+------+----------+-----------------------+
1 row in set (0.07 sec)

-- sample sql 2 索引失效
explain select * from worker where name like '%Bile' and age  = 22 and job = 'Java Developer' and phone = '13412345678';
-- result 
mysql> explain select * from worker where name like '%Bile' and age  = 22 and job = 'Java Developer' and phone = '13412345678';
+----+-------------+--------+------------+------+---------------+------+---------+------+------+----------+-------------+
| id | select_type | table  | partitions | type | possible_keys | key  | key_len | ref  | rows | filtered | Extra       |
+----+-------------+--------+------------+------+---------------+------+---------+------+------+----------+-------------+
|  1 | SIMPLE      | worker | NULL       | ALL  | NULL          | NULL | NULL    | NULL |    5 |    20.00 | Using where |
+----+-------------+--------+------------+------+---------------+------+---------+------+------+----------+-------------+
1 row in set (0.06 sec)
```

### 7. 字符串不加单引号索引失效

```sql
-- sample sql 1 索引生效
explain select * from worker where name = 'Bile';
-- result
mysql> explain select * from worker where name = 'Bile';
+----+-------------+--------+------------+------+------------------------+------------------------+---------+-------+------+----------+-------+
| id | select_type | table  | partitions | type | possible_keys          | key                    | key_len | ref   | rows | filtered | Extra |
+----+-------------+--------+------------+------+------------------------+------------------------+---------+-------+------+----------+-------+
|  1 | SIMPLE      | worker | NULL       | ref  | idx_name_age_job_phone | idx_name_age_job_phone | 83      | const |    1 |   100.00 | NULL  |
+----+-------------+--------+------------+------+------------------------+------------------------+---------+-------+------+----------+-------+
1 row in set (0.06 sec)

-- sample sql 2 索引失效
explain select * from worker where name = 000;
-- result
mysql> explain select * from worker where name = 000;
+----+-------------+--------+------------+------+------------------------+------+---------+------+------+----------+-------------+
| id | select_type | table  | partitions | type | possible_keys          | key  | key_len | ref  | rows | filtered | Extra       |
+----+-------------+--------+------------+------+------------------------+------+---------+------+------+----------+-------------+
|  1 | SIMPLE      | worker | NULL       | ALL  | idx_name_age_job_phone | NULL | NULL    | NULL |    5 |    20.00 | Using where |
+----+-------------+--------+------------+------+------------------------+------+---------+------+------+----------+-------------+
1 row in set (0.06 sec)
```

### 8. or连接

```sql
-- sample sql 1 索引生效
explain select * from worker where name = 'Bile' and age > 20;
-- result
mysql> explain select * from worker where name = 'Bile' and age > 20;
+----+-------------+--------+------------+-------+------------------------+------------------------+---------+------+------+----------+-----------------------+
| id | select_type | table  | partitions | type  | possible_keys          | key                    | key_len | ref  | rows | filtered | Extra                 |
+----+-------------+--------+------------+-------+------------------------+------------------------+---------+------+------+----------+-----------------------+
|  1 | SIMPLE      | worker | NULL       | range | idx_name_age_job_phone | idx_name_age_job_phone | 88      | NULL |    1 |   100.00 | Using index condition |
+----+-------------+--------+------------+-------+------------------------+------------------------+---------+------+------+----------+-----------------------+
1 row in set (0.07 sec)

-- sample sql 2 索引失效
explain select * from worker where name = 'Bile' or age > 20;
-- result
mysql> explain select * from worker where name = 'Bile' or age > 20;
+----+-------------+--------+------------+------+------------------------+------+---------+------+------+----------+-------------+
| id | select_type | table  | partitions | type | possible_keys          | key  | key_len | ref  | rows | filtered | Extra       |
+----+-------------+--------+------------+------+------------------------+------+---------+------+------+----------+-------------+
|  1 | SIMPLE      | worker | NULL       | ALL  | idx_name_age_job_phone | NULL | NULL    | NULL |    5 |    46.66 | Using where |
+----+-------------+--------+------------+------+------------------------+------+---------+------+------+----------+-------------+
1 row in set (0.07 sec)
```

### 9. order by

>   正常（索引参与了排序）
>   explain select * from user where name = 'zhangsan' and age = 20 order by age,pos;
>   备注：索引有两个作用：排序和查找
>
>   ```pgsql
>   explain select name,age from user where name = 'zhangsan' order by pos;//
>   explain select name,age from user where name = 'zhangsan' order by pos,age;//违反最左前缀法则
>   explain select * from user where name = 'zhangsan' and age = 20 order by created_time,age;//含非索引字段
>   ```

```sql
-- sample sql 1 索引参与了排序
explain select * from worker where name = 'Bile' and age  = 22 order by age;
-- result
mysql> explain select * from worker where name = 'Bile' and age  = 22 order by age;
+----+-------------+--------+------------+------+------------------------+------------------------+---------+-------------+------+----------+-------+
| id | select_type | table  | partitions | type | possible_keys          | key                    | key_len | ref         | rows | filtered | Extra |
+----+-------------+--------+------------+------+------------------------+------------------------+---------+-------------+------+----------+-------+
|  1 | SIMPLE      | worker | NULL       | ref  | idx_name_age_job_phone | idx_name_age_job_phone | 88      | const,const |    1 |   100.00 | NULL  |
+----+-------------+--------+------------+------+------------------------+------------------------+---------+-------------+------+----------+-------+
1 row in set (0.06 sec)

-- 导致额外的文件排序
-- sample sql 2 违反最左前缀法则
explain select name,age from worker where name = 'Bile' order by job,age;
-- result
mysql> explain select name,age from worker where name = 'Bile' order by job,age;
+----+-------------+--------+------------+------+------------------------+------------------------+---------+-------+------+----------+-----------------------------+
| id | select_type | table  | partitions | type | possible_keys          | key                    | key_len | ref   | rows | filtered | Extra                       |
+----+-------------+--------+------------+------+------------------------+------------------------+---------+-------+------+----------+-----------------------------+
|  1 | SIMPLE      | worker | NULL       | ref  | idx_name_age_job_phone | idx_name_age_job_phone | 83      | const |    1 |   100.00 | Using index; Using filesort |
+----+-------------+--------+------------+------+------------------------+------------------------+---------+-------+------+----------+-----------------------------+
1 row in set (0.07 sec)

-- sample sql 3 含非索引字段
explain select * from worker where name = 'Bile' and age  = 22 order by created_time;
-- result
mysql> explain select * from worker where name = 'Bile' and age  = 22 order by created_time;
+----+-------------+--------+------------+------+------------------------+------------------------+---------+-------------+------+----------+----------------+
| id | select_type | table  | partitions | type | possible_keys          | key                    | key_len | ref         | rows | filtered | Extra          |
+----+-------------+--------+------------+------+------------------------+------------------------+---------+-------------+------+----------+----------------+
|  1 | SIMPLE      | worker | NULL       | ref  | idx_name_age_job_phone | idx_name_age_job_phone | 88      | const,const |    1 |   100.00 | Using filesort |
+----+-------------+--------+------------+------+------------------------+------------------------+---------+-------------+------+----------+----------------+
1 row in set (0.07 sec)
```

### 10. group by

>   正常（索引参与了排序）
>   `explain select name,age from worker where name = 'Bile' group by age;`
>   **分组之前必排序（排序同order by）**

```sql
-- sample sql 1 索引正常
explain select name,age from worker where name = 'Bile' group by age;
-- result
mysql> explain select name,age from worker where name = 'Bile' group by age;
+----+-------------+--------+------------+------+------------------------+------------------------+---------+-------+------+----------+-------------+
| id | select_type | table  | partitions | type | possible_keys          | key                    | key_len | ref   | rows | filtered | Extra       |
+----+-------------+--------+------------+------+------------------------+------------------------+---------+-------+------+----------+-------------+
|  1 | SIMPLE      | worker | NULL       | ref  | idx_name_age_job_phone | idx_name_age_job_phone | 83      | const |    1 |   100.00 | Using index |
+----+-------------+--------+------------+------+------------------------+------------------------+---------+-------+------+----------+-------------+
1 row in set (0.07 sec)

-- 导致产生临时表（会降低性能）
-- sample sql 2 违反最左前缀法则
explain select name,job from worker where name = 'Bile' group by job;
-- result
mysql> explain select name,job from worker where name = 'Bile' group by job;
+----+-------------+--------+------------+------+------------------------+------------------------+---------+-------+------+----------+------------------------------+
| id | select_type | table  | partitions | type | possible_keys          | key                    | key_len | ref   | rows | filtered | Extra                        |
+----+-------------+--------+------------+------+------------------------+------------------------+---------+-------+------+----------+------------------------------+
|  1 | SIMPLE      | worker | NULL       | ref  | idx_name_age_job_phone | idx_name_age_job_phone | 83      | const |    1 |   100.00 | Using index; Using temporary |
+----+-------------+--------+------------+------+------------------------+------------------------+---------+-------+------+----------+------------------------------+
1 row in set (0.06 sec)

-- sample sql 3 违反最左前缀法则
explain select name,age from worker where name = 'Bile' group by job,age;
-- result
mysql> explain select name,age from worker where name = 'Bile' group by job,age;
+----+-------------+--------+------------+------+------------------------+------------------------+---------+-------+------+----------+------------------------------+
| id | select_type | table  | partitions | type | possible_keys          | key                    | key_len | ref   | rows | filtered | Extra                        |
+----+-------------+--------+------------+------+------------------------+------------------------+---------+-------+------+----------+------------------------------+
|  1 | SIMPLE      | worker | NULL       | ref  | idx_name_age_job_phone | idx_name_age_job_phone | 83      | const |    1 |   100.00 | Using index; Using temporary |
+----+-------------+--------+------------+------+------------------------+------------------------+---------+-------+------+----------+------------------------------+
1 row in set (0.06 sec)

-- sample sql 4 含非索引字段
explain select name,age from worker where name = 'Bile' group by age,created_time;
-- result
mysql> explain select name,age from worker where name = 'Bile' group by age,created_time;
+----+-------------+--------+------------+------+------------------------+------------------------+---------+-------+------+----------+-----------------+
| id | select_type | table  | partitions | type | possible_keys          | key                    | key_len | ref   | rows | filtered | Extra           |
+----+-------------+--------+------------+------+------------------------+------------------------+---------+-------+------+----------+-----------------+
|  1 | SIMPLE      | worker | NULL       | ref  | idx_name_age_job_phone | idx_name_age_job_phone | 83      | const |    1 |   100.00 | Using temporary |
+----+-------------+--------+------------+------+------------------------+------------------------+---------+-------+------+----------+-----------------+
1 row in set (0.07 sec)
```

# 索引小结

## 索引优点

-   提升检索效率，避免全表扫描
-   索引可以避免数据库服务器进行排序，将随机IO转换为顺序IO

## 索引缺点

-   索引是根据数据表中列的数据创建的，当数据表中创建索引的列发生DML操作时，索引也需要更新
-   索引也是数据文件，同样会占用磁盘空间

## 使用注意

-   数据表中数据量不大时，添加索引对检索的提升不明显
-   数据表中数据量大但是DML操作频繁时，不建议使用索引
-   避免在数据重复度高的列中建立索引，如性别等
-   创建索引之后，要注意查询SQL语句的编写，避免索引失效或使用索引外的条件导致SQL性能下降
