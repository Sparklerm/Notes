# DQL（Data Query Language）数据库查询语言

> DQL 用于对数据表中的数据进行查询。

在 [MySQL](http://c.biancheng.net/mysql/) 中，可以使用 SELECT 语句来查询数据。查询数据是指从数据库中根据需求，使用不同的查询方式来获取不同的数据，是使用频率最高、最重要的操作。

SELECT 的完整语法格式如下：

```sql
SELECT
	{* | <columnName>}
[
FROM 
    <table_1>, <table_2>...
[WHERE 
	<表达式>
[GROUP BY 
	<group by definition>
[HAVING 
	<expression> [{<operator> <expression>}…]]
[ORDER BY 
	<order by definition>]
[LIMIT[<offset>,] <row count>]
]
```

其中，各条子句的含义如下：

- `{*|<columnName>}`包含星号通配符的字段列表，表示所要查询字段的名称。
- `<table_1>,<table_2>...`，表 1 和表 2 表示查询数据的来源，可以是单个或多个。
- `WHERE <表达式>`是可选项，如果选择该项，将限定查询数据必须满足该查询条件。
- `GROUP BY<columnName>`，该子句告诉 MySQL 如何显示查询出来的数据，并按照指定的字段分组。
- `[ORDER BY<columnName>]`，该子句告诉 MySQL 按什么样的顺序显示查询出来的数据，可以进行的排序有升序（ASC）和降序（DESC），默认情况下是升序。
- `[LIMIT[<offset>，]<row count>]`，该子句告诉 MySQL 每次显示查询出来的数据条数。

```sql
-- sample sql
drop table if exists worker;
create table worker(
	id int primary key auto_increment,
    name varchar(32),
    age tinyint,
    gander char(2),
    salary decimal(10,2),
    job varchar(20) not null
);
insert into worker(name,age,gander,salary,job) values("Alice",20,"女",6000,"Java"),("Jammy",28,"男",12000,"C++"),("Bibo",23,"男",8000,"Go"),("Cat",21,"女",7000,"PHP"),("Joim",36,"女",25000,"DBA"),("Tim",28,"男",21000,"Java"),("Ret",24,"男",12000,"Go"),("Meic",21,"女",9000,"Go"),("Wss",25,"男",14000,"C"),("Fiore",27,"女",18000,"Python");

drop table if exists job;
create table job(
	id int primary key auto_increment,
    job varchar(32) not null
);
insert into job(job) values("Java"),("C"),("C++"),("Python"),("DBA"),("Go"),("PHP");
```

## 查询表中所有字段

```sql
select * from <tableName>;

select columnName_1,...,columnName_n from <tableName>;
```

```sql
-- sample sql
select * from worker;
select id,name,age,gander,salary from worker;

-- result
+----+-------+-----+--------+----------+
| id | name  | age | gander | salary   |
+----+-------+-----+--------+----------+
|  1 | Alice |  20 | 女     | 6000.00  |
|  2 | Jammy |  28 | 男     | 12000.00 |
|  3 | Bibo  |  23 | 男     | 8000.00  |
|  4 | Cat   |  21 | 女     | 7000.00  |
+----+-------+-----+--------+----------+
```



## 查询表中指定字段

```sql
select <columnName> from <tableName>
```

```sql
-- sample sql
select name , gander from worker;

-- result
+-------+--------+
| name  | gander |
+-------+--------+
| Alice | 女     |
| Jammy | 男     |
| Bibo  | 男     |
| Cat   | 女     |
+-------+--------+
```

## 使用DISTINCT去重

```sql
select distinct <columnName> from <tableName>; 
```

`<columnName>`为需要消除重复记录的字段名称，多个字段时用逗号隔开。

使用 DISTINCT 关键字时需要注意以下几点：

- DISTINCT 关键字只能在 SELECT 语句中使用。
- 在对一个或多个字段去重时，DISTINCT 关键字必须在所有字段的最前面。
- 如果 DISTINCT 关键字后有多个字段，则会对多个字段进行组合去重，也就是说，只有多个字段组合起来完全是一样的情况下才会被去重。

```sql
-- sample sql
select distinct gander from worker; 

-- result
+--------+
| gander |
+--------+
| 女     |
| 男     |
+--------+
```



## 别名

### 表别名

```sql
select * from <tableName> as <tableCName>;
```

```sql
-- sample sql
select * from worker as boss;
-- result
+----+-------+-----+--------+----------+
| id | name  | age | gander | salary   |
+----+-------+-----+--------+----------+
|  1 | Alice |  20 | 女     | 6000.00  |
|  2 | Jammy |  28 | 男     | 12000.00 |
|  3 | Bibo  |  23 | 男     | 8000.00  |
|  4 | Cat   |  21 | 女     | 7000.00  |
+----+-------+-----+--------+----------+
```



### 为字段指定别名

```
select <columnName> as <columnCName> ... from <tableName>;
```

```sql
-- sample sql
select id as no,name,gander as sex,salary from worker;
-- result
+----+-------+-----+----------+
| no | name  | sex | salary   |
+----+-------+-----+----------+
|  1 | Alice | 女  | 6000.00  |
|  2 | Jammy | 男  | 12000.00 |
|  3 | Bibo  | 男  | 8000.00  |
|  4 | Cat   | 女  | 7000.00  |
+----+-------+-----+----------+
```

## LIMIT：限制查询结果的条数

> LIMIT 是 MySQL 中的一个特殊关键字，用于指定查询结果从哪条记录开始显示，一共显示多少条记录。
>
> LIMIT 关键字有 3 种使用方式，即指定初始位置、不指定初始位置以及与 OFFSET 组合使用。

### 指定初始位置

LIMIT 关键字可以指定查询结果从哪条记录开始显示，显示多少条记录。

LIMIT 指定初始位置的基本语法格式如下：

```sql
-- 获取从第 "<initIndex>" 条记录开始的后面的 "<num>" 条记录
select * from <tableName> limit <startIndex>,<num>;
```

```sql
-- sample sql
select * from worker limit 1,5;

-- result
+----+-------+-----+--------+----------+------+
| id | name  | age | gander | salary   | job  |
+----+-------+-----+--------+----------+------+
|  2 | Jammy |  28 | 男     | 12000.00 | C++  |
|  3 | Bibo  |  23 | 男     | 8000.00  | Go   |
|  4 | Cat   |  21 | 女     | 7000.00  | PHP  |
|  5 | Joim  |  36 | 女     | 25000.00 | DBA  |
|  6 | Tim   |  28 | 男     | 21000.00 | Java |
+----+-------+-----+--------+----------+------+
```



### 不指定初始位置

LIMIT 关键字不指定初始位置时，记录从第一条记录开始显示。显示记录的条数由 LIMIT 关键字指定。

```sql
-- "<num>" 表示显示记录的条数。如果"<num>"的值小于查询结果的总数，则会从第一条记录开始，显示指定条数的记录。如果"<num>"的值大于查询结果的总数，则会直接显示查询出来的所有记录。
select * from <tableName> limit <num>;
```

```sql
-- sample sql
select * from worker limit 5;

-- result
+----+-------+-----+--------+----------+------+
| id | name  | age | gander | salary   | job  |
+----+-------+-----+--------+----------+------+
|  1 | Alice |  20 | 女     | 6000.00  | Java |
|  2 | Jammy |  28 | 男     | 12000.00 | C++  |
|  3 | Bibo  |  23 | 男     | 8000.00  | Go   |
|  4 | Cat   |  21 | 女     | 7000.00  | PHP  |
|  5 | Joim  |  36 | 女     | 25000.00 | DBA  |
+----+-------+-----+--------+----------+------+
```

### LIMIT和OFFSET组合使用

```sql
-- 获取从第 "<initIndex>" 条记录开始的后面的 "<num>" 条记录
select * from <tableName> limit <num> offset <initIndex>;
```

```sql
-- sample sql
select * from worker limit 5 offset 5;

-- result
+----+-------+-----+--------+----------+--------+
| id | name  | age | gander | salary   | job    |
+----+-------+-----+--------+----------+--------+
|  6 | Tim   |  28 | 男     | 21000.00 | Java   |
|  7 | Ret   |  24 | 男     | 12000.00 | Go     |
|  8 | Meic  |  21 | 女     | 9000.00  | Go     |
|  9 | Wss   |  25 | 男     | 14000.00 | C      |
| 10 | Fiore |  27 | 女     | 18000.00 | Python |
+----+-------+-----+--------+----------+--------+
```



## ORDER BY：查询结果排序

```sql
select * from <tableName> order by <columnName> [ASC|DESC];
```

语法说明如下。

- 字段名：表示需要排序的字段名称，多个字段时用逗号隔开。
- ASC|DESC：`ASC`表示字段按升序排序；`DESC`表示字段按降序排序。其中`ASC`为默认值。

使用 ORDER BY 关键字应该注意以下几个方面：

- ORDER BY 关键字后可以跟子查询（关于子查询后面教程会详细讲解，这里了解即可）。
- 当排序的字段中存在空值时，ORDER BY 会将该空值作为最小值来对待。
- ORDER BY 指定多个字段进行排序时，MySQL 会按照字段的顺序从左到右依次进行排序。

### 单字段排序

```sql
select * from <tableName> order by <columnName>;
```

```sql
-- sample sql
select * from worker order by id DESC limit 5;

-- result
+----+-------+-----+--------+----------+--------+
| id | name  | age | gander | salary   | job    |
+----+-------+-----+--------+----------+--------+
| 10 | Fiore |  27 | 女     | 18000.00 | Python |
|  9 | Wss   |  25 | 男     | 14000.00 | C      |
|  8 | Meic  |  21 | 女     | 9000.00  | Go     |
|  7 | Ret   |  24 | 男     | 12000.00 | Go     |
|  6 | Tim   |  28 | 男     | 21000.00 | Java   |
+----+-------+-----+--------+----------+--------+
```



### 多字段排序

> 多字段排序可对每个排序字段指定升序或降序，也可不指定使用默认排序。

```sql
select * from <tableName> order by <columnName_1> , <columnName_2>;
select * from <tableName> order by <columnName_1> ASC, <columnName_2> DESC;
```

```sql
-- sample sql
select * from worker order by age , salary limit 5;
-- result
+----+-------+-----+--------+----------+------+
| id | name  | age | gander | salary   | job  |
+----+-------+-----+--------+----------+------+
|  1 | Alice |  20 | 女     | 6000.00  | Java |
|  4 | Cat   |  21 | 女     | 7000.00  | PHP  |
|  8 | Meic  |  21 | 女     | 9000.00  | Go   |
|  3 | Bibo  |  23 | 男     | 8000.00  | Go   |
|  7 | Ret   |  24 | 男     | 12000.00 | Go   |
+----+-------+-----+--------+----------+------+

-- sample sql
select * from worker order by age DESC, salary ASC limit 5;
-- result
+----+-------+-----+--------+----------+--------+
| id | name  | age | gander | salary   | job    |
+----+-------+-----+--------+----------+--------+
|  5 | Joim  |  36 | 女     | 25000.00 | DBA    |
|  2 | Jammy |  28 | 男     | 12000.00 | C++    |
|  6 | Tim   |  28 | 男     | 21000.00 | Java   |
| 10 | Fiore |  27 | 女     | 18000.00 | Python |
|  9 | Wss   |  25 | 男     | 14000.00 | C      |
+----+-------+-----+--------+----------+--------+
```



## WHERE：条件查询数据

```sql
select * from <tableName> where [...];
```

查询条件可以是：

- 带比较运算符和逻辑运算符的查询条件
- 带 `BETWEEN AND` 关键字的查询条件
- 带 `IS NULL` 关键字的查询条件
- 带 IN 关键字的查询条件
- 带 LIKE 关键字的查询条件

### 单一条件的查询语句

```sql
select * from <tableName> where <columnName> <queryCondition>;
```

```sql
-- sample sql
select * from worker where age > 25;
-- result
+----+-------+-----+--------+----------+--------+
| id | name  | age | gander | salary   | job    |
+----+-------+-----+--------+----------+--------+
|  2 | Jammy |  28 | 男     | 12000.00 | C++    |
|  5 | Joim  |  36 | 女     | 25000.00 | DBA    |
|  6 | Tim   |  28 | 男     | 21000.00 | Java   |
| 10 | Fiore |  27 | 女     | 18000.00 | Python |
+----+-------+-----+--------+----------+--------+
```



### 多条件查询语句

```sql
select * from <tableName> where <columnName_1> <queryCondition_1> [and|or|xor] <columnName_2> <queryCondition_2>;
```

在 WHERE 关键词后可以有多个查询条件，这样能够使查询结果更加精确。多个查询条件时用逻辑运算符 AND（&&）、OR（||）或 XOR 隔开。

- AND：记录满足所有查询条件时，才会被查询出来。
- OR：记录满足任意一个查询条件时，才会被查询出来。
- XOR：记录满足其中一个条件，并且不满足另一个条件时，才会被查询出来。

**`OR`、`AND `和 `XOR `可以一起使用，但是在使用时要注意运算符的优先级。**

```sql
-- sample sql
select * from worker where age > 25 and salary > 20000;
-- result
+----+------+-----+--------+----------+------+
| id | name | age | gander | salary   | job  |
+----+------+-----+--------+----------+------+
|  5 | Joim |  36 | 女     | 25000.00 | DBA  |
|  6 | Tim  |  28 | 男     | 21000.00 | Java |
+----+------+-----+--------+----------+------+
```



## LIKE：模糊查询

> LIKE 关键字支持百分号`%`和下划线`_`通配符。
>
> ​	`%`：支持匹配任意长度的字符
>
> ​	`_`：支持匹配一个字符且字符的长度不能为 0；如 `a_c`：可以匹配`aac`，`abc`，`acc`但是不能匹配`ac`；
>
> 匹配的字符串必须加单引号或双引号。

```sql
select * from <tableName> where <columnName> [not] like '<str>'
```

### 带有“%”通配符的查询

```sql
select * from <tableName> where <columnName> [not] like '%<str>'
select * from <tableName> where <columnName> [not] like '<str>%'
select * from <tableName> where <columnName> [not] like '%<str>%'
select * from <tableName> where <columnName> [not] like '<str>%<str>'
```

### 带有“_”通配符的查询

```sql
select * from <tableName> where <columnName> [not] like '_<str>'
select * from <tableName> where <columnName> [not] like '<str>_'
select * from <tableName> where <columnName> [not] like '_<str>_'
select * from <tableName> where <columnName> [not] like '<str>_<str>'
```

**如果查询内容中包含通配符，可以使用“`\`”转义符**

```sql
-- <str> = AA% --> AA\%
select * from <tableName> where <columnName> like "%AA\%"
```

### 使LIKE区分大小写

> 默认情况下，LIKE 关键字匹配字符的时候是不区分大小写的。如果需要区分大小写，可以加入 BINARY 关键字。

```sql
select * from <tableName> where <columnName> like binary '<str>%';
```

```sql
-- sample sql 1
select * from worker where name like '%o';
-- result
+----+------+-----+--------+---------+-----+
| id | name | age | gander | salary  | job |
+----+------+-----+--------+---------+-----+
|  3 | Bibo |  23 | 男     | 8000.00 | Go  |
+----+------+-----+--------+---------+-----+

-- sample sql 2
select * from worker where name like '_im';
-- result
+----+------+-----+--------+----------+------+
| id | name | age | gander | salary   | job  |
+----+------+-----+--------+----------+------+
|  6 | Tim  |  28 | 男     | 21000.00 | Java |
+----+------+-----+--------+----------+------+

-- sample sql 3
select * from worker where name like binary 't%';
select * from worker where name like binary 'T%';
-- result
Empty set
+----+------+-----+--------+----------+------+
| id | name | age | gander | salary   | job  |
+----+------+-----+--------+----------+------+
|  6 | Tim  |  28 | 男     | 21000.00 | Java |
+----+------+-----+--------+----------+------+
```



### 使用通配符的注意事项和技巧

下面是使用通配符的一些注意事项：

- **注意大小写**。MySQL 默认是不区分大小写的。如果区分大小写，像“`Tom`”这样的数据就不能被“`t%`”所匹配到。
- **注意尾部空格**，尾部空格会干扰通配符的匹配。例如，“`T% `”就不能匹配到“`Tom`”。
- **注意 NULL**。“%”通配符可以到匹配任意字符，但是不能匹配 NULL。也就是说 “%”匹配不到数据表中值为 NULL 的记录。

下面是一些使用通配符要记住的技巧。

- 不要过度使用通配符，如果其它操作符能达到相同的目的，应该使用其它操作符。因为 MySQL 对通配符的处理一般会比其他操作符花费更长的时间。
- 在确定使用通配符后，除非绝对有必要，否则不要把它们用在字符串的开始处。把通配符置于搜索模式的开始处，搜索起来是最慢的。
- 仔细注意通配符的位置。如果放错地方，可能不会返回想要的数据。



## BETWEEN AND：范围查询

> BETWEEN AND 需要两个参数，即范围的起始值和终止值。如果字段值在指定的范围内，则这些记录被返回。如果不在指定范围内，则不会被返回。

```sql
select * from <tableName> where <columnName> [not] between <value_1> and <value_2>;
```

- `not`：可选参数，表示指定范围之外的值。如果字段值不满足指定范围内的值，则这些记录被返回。
- `<value_1>`：表示范围的起始值。
- `<value_2>`：表示范围的终止值。

```sql
-- sample sql
select * from worker where salary between 10000 and 20000;
-- result
+----+-------+-----+--------+----------+--------+
| id | name  | age | gander | salary   | job    |
+----+-------+-----+--------+----------+--------+
|  2 | Jammy |  28 | 男     | 12000.00 | C++    |
|  7 | Ret   |  24 | 男     | 12000.00 | Go     |
|  9 | Wss   |  25 | 男     | 14000.00 | C      |
| 10 | Fiore |  27 | 女     | 18000.00 | Python |
+----+-------+-----+--------+----------+--------+
```



## IS NULL：空值查询

> 空值不同于`0`和`""`，空值为`NULL`
>
> 如果字段的值是空值，则满足查询条件，该记录将被查询出来。如果字段的值不是空值，则不满足查询条件。
>
> **注意**：`IS NULL` 是一个整体，不能将` IS `换成“`=`”。如果将 IS 换成“=”将不能查询出任何结果，数据库系统会出现“Empty set(0.00 sec)”这样的提示。同理，`IS NOT NULL` 中的 `IS NOT` 不能换成“`!=`”或“`<>`”。

```sql
-- 查询值为空的
select * from <tableName> where <columnName> is null;
-- 查询值不为空的
select * from <tableName> where <columnName> is not null;
```

```sql
-- sample slq
select * from worker where name is not null;

-- result
+----+-------+-----+--------+----------+--------+
| id | name  | age | gander | salary   | job    |
+----+-------+-----+--------+----------+--------+
|  1 | Alice |  20 | 女     | 6000.00  | Java   |
|  2 | Jammy |  28 | 男     | 12000.00 | C++    |
|  3 | Bibo  |  23 | 男     | 8000.00  | Go     |
|  4 | Cat   |  21 | 女     | 7000.00  | PHP    |
|  5 | Joim  |  36 | 女     | 25000.00 | DBA    |
|  6 | Tim   |  28 | 男     | 21000.00 | Java   |
|  7 | Ret   |  24 | 男     | 12000.00 | Go     |
|  8 | Meic  |  21 | 女     | 9000.00  | Go     |
|  9 | Wss   |  25 | 男     | 14000.00 | C      |
| 10 | Fiore |  27 | 女     | 18000.00 | Python |
+----+-------+-----+--------+----------+--------+
```



## GROUP BY：分组查询

> GROUP BY 关键字可以根据一个或多个字段对查询结果进行分组。

```sql
select * from <tableName> group by <columnName>;
```

### GROUP BY单独使用

> 单独使用 GROUP BY 关键字时，查询结果会只显示每个分组的第一条记录。

```sql
select <columnName_1> from <tableName> group by <columnName_1>;
```

```sql
-- sample sql
select job from worker group by job;

-- reuslt
+--------+
| job    |
+--------+
| Java   |
| C++    |
| Go     |
| PHP    |
| DBA    |
| C      |
| Python |
+--------+
```

### GROUP BY 与 GROUP_CONCAT() 

> GROUP BY 关键字可以和 GROUP_CONCAT() 函数一起使用。GROUP_CONCAT() 函数会把每个分组的字段值都显示出来。

```sql
select <columnName_1> , GROUP_CONCAT(<columnName_2>) from <tableName> group by <columnName_1>;

select <columnName_1> , <columnName_2> , GROUP_CONCAT(<columnName_1>) from <tableName> group by <columnName_1> , <columnName_2>;
```

```sql
-- sample slq
select job,GROUP_CONCAT(name) from worker group by job;
-- result
+--------+--------------------+
| job    | GROUP_CONCAT(name) |
+--------+--------------------+
| C      | Wss                |
| C++    | Jammy              |
| DBA    | Joim               |
| Go     | Bibo,Ret,Meic      |
| Java   | Alice,Tim          |
| PHP    | Cat                |
| Python | Fiore              |
+--------+--------------------+
```



### GROUP BY 与聚合函数

> 在数据统计时，GROUP BY 关键字经常和聚合函数一起使用。
>
> 聚合函数包括 COUNT()，SUM()，AVG()，MAX() 和 MIN()。其中，COUNT() 用来统计记录的条数；SUM() 用来计算字段值的总和；AVG() 用来计算字段值的平均值；MAX() 用来查询字段的最大值；MIN() 用来查询字段的最小值。

```sql
select <columnName> [COUNT|SUM|MAX|...](<columnName>) from <tableName> group by <columnName>;
```

```sql
-- sample sql
SELECT job , COUNT(id) FROM worker GROUP BY job;
-- result
+--------+-----------+
| job    | COUNT(id) |
+--------+-----------+
| Java   |         2 |
| C++    |         1 |
| Go     |         3 |
| PHP    |         1 |
| DBA    |         1 |
| C      |         1 |
| Python |         1 |
+--------+-----------+
```



### GROUP BY 与 WITH ROLLUP

> WITH POLLUP 关键字用来在所有记录的最后加上一条记录，这条记录是上面所有记录的总和，即统计记录数量。

```sql
select <columnName> , GROUP_CONCAT(<columnName>) from <tableName> group by <columnName> WITH ROLLUP;
```

```sql
-- sample sql
SELECT job , GROUP_CONCAT(name) FROM worker GROUP BY job WITH ROLLUP;
-- result
+--------+--------------------------------------------------+
| job    | GROUP_CONCAT(name)                               |
+--------+--------------------------------------------------+
| C      | Wss                                              |
| C++    | Jammy                                            |
| DBA    | Joim                                             |
| Go     | Bibo,Ret,Meic                                    |
| Java   | Alice,Tim                                        |
| PHP    | Cat                                              |
| Python | Fiore                                            |
| NULL   | Wss,Jammy,Joim,Bibo,Ret,Meic,Alice,Tim,Cat,Fiore |
+--------+--------------------------------------------------+
```



## HAVING：过滤分组

> 使用 HAVING 关键字对分组后的数据进行过滤。
>
> HAVING 关键字和 WHERE 关键字都可以用来过滤数据，且 HAVING 支持 WHERE 关键字中所有的操作符和语法。
>
> `WHERE `和 `HAVING `的差异：
>
> - 一般情况下，**WHERE 用于过滤数据`行`**，**而 HAVING 用于过滤`分组`**。
> - **WHERE 查询条件中不可以使用聚合函数，而 HAVING 查询条件中可以使用聚合函数。**
> - **WHERE 在数据`分组前`进行过滤，而 HAVING 在数据`分组后`进行过滤 。**
> - **WHERE 针对数据库文件进行过滤，而 HAVING 针对查询结果进行过滤。也就是说，WHERE 根据数据表中的字段直接进行过滤，而 HAVING 是根据前面已经查询出的字段进行过滤。**
> - WHERE 查询条件中不可以使用字段别名，而 HAVING 查询条件中可以使用字段别名。

```sql
select * from <tableName> group by <columnName> having <queryCondition>;
```



```sql
-- sample sql
SELECT * FROM worker GROUP BY job HAVING job = 'Java';
-- result
+----+-------+-----+--------+---------+------+
| id | name  | age | gander | salary  | job  |
+----+-------+-----+--------+---------+------+
|  1 | Alice |  20 | 女     | 6000.00 | Java |
+----+-------+-----+--------+---------+------+
```



## 连表查询

### CROSS JOIN：交叉连接

> 交叉连接（CROSS JOIN）又称笛卡尔积。
>
> > ## 笛卡尔积
> >
> > 笛卡尔积（Cartesian product）是指两个集合 X 和 Y 的乘积。
> > 例如，有 A 和 B 两个集合，它们的值如下：
> > A = {1,2} B = {3,4,5}
> >
> > 集合 A×B 和 B×A 的结果集分别表示为：
> >
> > A×B={(1,3), (1,4), (1,5), (2,3), (2,4), (2,5) };
> > B×A={(3,1), (3,2), (4,1), (4,2), (5,1), (5,2) };
> >
> > 以上 A×B 和 B×A 的结果就叫做两个集合的笛卡尔积。
> >
> > 并且，从以上结果我们可以看出：
> >
> > - 两个集合相乘，不满足交换率，即 A×B≠B×A。
> > - A 集合和 B 集合的笛卡尔积是 A 集合的元素个数 × B 集合的元素个数。
> >
> > 多表查询遵循的算法就是以上提到的笛卡尔积，表与表之间的连接可以看成是在做乘法运算。在实际应用中，应避免使用笛卡尔积，因为笛卡尔积中容易存在大量的不合理数据，简单来说就是容易导致查询结果重复、混乱。

```sql
-- 交叉连接的语法格式如下：
select <columnName> from <tableName_1> cross join <tableName_2> [where ...];
select <columnName> from <tableName_1> , <tableName_2> [where ...];
-- 多个表交叉连接时，在 FROM 后连续使用 CROSS JOIN 或,即可。以上两种语法的返回结果是相同的，但是第一种语法才是官方建议的标准写法。
```

```sql
-- sample sql
select * from worker cross join job;

-- result --> count(worker) * count(job)  = count(result)
+----+-------+-----+--------+----------+--------+----+--------+
| id | name  | age | gander | salary   | job    | id | job    |
+----+-------+-----+--------+----------+--------+----+--------+
|  1 | Alice |  20 | 女     | 6000.00  | Java   |  7 | PHP    |
|  1 | Alice |  20 | 女     | 6000.00  | Java   |  6 | Go     |
|						......						          |
| 10 | Fiore |  27 | 女     | 18000.00 | Python |  2 | C      |
| 10 | Fiore |  27 | 女     | 18000.00 | Python |  1 | Java   |
+----+-------+-----+--------+----------+--------+----+--------+
70 rows in set (0.22 sec)
```

### INNER JOIN：内连接

> 内连接（INNER JOIN）主要通过设置连接条件的方式，来移除查询结果中某些数据行的交叉连接。简单来说，就是利用条件表达式来消除交叉连接的某些数据行。
> 内连接使用 **INNER JOIN** 关键字连接两张表，并使用 ON 子句来设置连接条件。如果没有连接条件，INNER JOIN 和 CROSS JOIN 在语法上是等同的，两者可以互换。

```sql
-- 内联查询语法
select <columnName>,... from <tableName_1> inner join <tableName_2> [on ...];
-- INNER JOIN 也可以使用 WHERE 子句指定连接条件，但是 INNER JOIN ... ON 语法是官方的标准写法，而且 WHERE 子句在某些时候会影响查询的性能。
-- 多个表内连接时，在 FROM 后连续使用 INNER JOIN 或 JOIN 即可。
select <columnName>,... from <tableName_1> inner join <tableName_2> [on ...] inner join <tableName_3> [on ...];
```

```sql
-- sample sql
select * from worker w inner join job j on w.job = j.job where j.job = 'Java';

-- result 
+----+-------+-----+--------+----------+------+----+------+
| id | name  | age | gander | salary   | job  | id | job  |
+----+-------+-----+--------+----------+------+----+------+
|  1 | Alice |  20 | 女     | 6000.00  | Java |  1 | Java |
|  6 | Tim   |  28 | 男     | 21000.00 | Java |  1 | Java |
+----+-------+-----+--------+----------+------+----+------+
```

### LEFT/RIGHT JOIN：外连接

> 外连接可以分为左外连接和右外连接，下面根据实例分别介绍左外连接和右外连接。

#### 左连接

> 左外连接又称为左连接，使用 **LEFT OUTER JOIN** 关键字连接两个表，并使用 ON 子句来设置连接条件。
>
> **特点：以`left outer join`左侧为`基表`，右侧为`参考表`，可以查询出`基表`和`参考表`中匹配连接条件的记录。如果`基表`的某条记录在参考表中没有匹配记录，则再返回结果中，`参考表`的字段值皆为空值（NULL）。**

```sql
select <columnName> from <table_1> left outer join <table_2> <on ...>;
```

#### 右连接

> 右外连接又称为右连接，右连接是左连接的反向连接。使用 **RIGHT OUTER JOIN** 关键字连接两个表，并使用 ON 子句来设置连接条件。
>
> **特点：以`right outer join`右侧为`基表`，左侧为`参考表`，可以查询出`基表`和`参考表`中匹配连接条件的记录。如果`基表`的某条记录在参考表中没有匹配记录，则再返回结果中，`参考表`的字段值皆为空值（NULL）。**

```sql
select <columnName> from <table_1> right outer join <table_2> <on ...>;
```

#### 子查询(DERIVED)

> 子查询是 MySQL 中比较常用的查询方法，通过子查询可以实现多表查询。子查询指将一个查询语句嵌套在另一个查询语句中。子查询可以在 SELECT、UPDATE 和 DELETE 语句中使用，而且可以进行多层嵌套。子查询经常出现在 WHERE 子句中。

##### 子查询语法

```sql
select * from <table_1> where <columnName_1> [IN|NOT IN|=|...] (DERIVED)
```

##### 子查询注意事项

1. **子查询语句可以嵌套在 SQL 语句中任何表达式出现的位置**

    > 在 SELECT 语句中，子查询可以被嵌套在 SELECT 语句的列、表和查询条件中，即 SELECT 子句，FROM 子句、WHERE 子句、GROUP BY 子句和 HAVING 子句。
    > 前面已经介绍了 WHERE 子句中嵌套子查询的使用方法，下面是子查询在 SELECT 子句和 FROM 子句中的使用语法。

    ```sql
    -- 嵌套在 SELECT 语句的 SELECT 子句中的子查询语法格式如下
    select (DERIVED) from <tableName>;
    
    -- 嵌套在 SELECT 语句的 FROM 子句中的子查询语法格式如下。
    select * from (DERIVED) as <cName>;
    ```

2. **只出现在子查询中而没有出现在父查询中的表不能包含在输出列中**

    > 多层嵌套子查询的最终数据集只包含父查询（即最外层的查询）的 SELECT 子句中出现的字段，而子查询的输出结果通常会作为其外层子查询数据源或用于数据判断匹配。

    ```sql
    -- 常见错误如下：
    select * from (select * from <tableName>);
    
    -- 这个子查询语句产生语法错误的原因在于主查询语句的 FROM 子句是一个子查询语句，因此应该为子查询结果集指定别名。正确代码如下。
    select * from (select * from <tableName>) as <tableCName>;
    ```


```sql
-- sample sql
select (select count(job) from job) from worker;
select * from (select * from job) as tempJob;
```



##### MySQL怎样将子查询修改为表连接？

```sql
-- 还没写呢
```

## REGEXP：正则表达式

> 正则表达式主要用来查询和替换符合某个模式（规则）的文本内容。例如，从一个文件中提取电话号码，查找一篇文章中重复的单词、替换文章中的敏感语汇等，这些地方都可以使用正则表达式。正则表达式强大且灵活，常用于非常复杂的查询。
>
> MySQL 中的正则表达式与 Java 语言、PHP 语言等编程语言中的正则表达式基本一致。

```sql
-- 使用 REGEXP 关键字指定正则表达式的字符匹配模式，其基本语法格式如下：
select * from <tableName> where <columnName> REGEXP '<switchType>'
```

| 选项         | 说明                                  | 例子                                       | 匹配值示例                 |
| ------------ | ------------------------------------- | ------------------------------------------ | -------------------------- |
| ^            | 匹配文本的开始字符                    | '^b' 匹配以字母 b 开头的字符串             | book、big、banana、bike    |
| $            | 匹配文本的结束字符                    | 'st$' 匹配以 st 结尾的字符串               | test、resist、persist      |
| .            | 匹配任何单个字符                      | 'b.t' 匹配任何 b 和 t 之间有一个字符       | bit、bat、but、bite        |
| *            | 匹配零个或多个在它前面的字符          | 'f*n' 匹配字符 n 前面有任意个字符 f        | fn、fan、faan、abcn        |
| +            | 匹配前面的字符 1 次或多次             | 'ba+' 匹配以 b 开头，后面至少紧跟一个 a    | ba、bay、bare、battle      |
| <字符串>     | 匹配包含指定字符的文本                | 'fa' 匹配包含‘fa’的文本                    | fan、afa、faad             |
| [字符集合]   | 匹配字符集合中的任何一个字符          | '[xz]' 匹配 x 或者 z                       | dizzy、zebra、x-ray、extra |
| [^]          | 匹配不在括号中的任何字符              | '[^abc]' 匹配任何不包含 a、b 或 c 的字符串 | desk、fox、f8ke            |
| 字符串{n,}   | 匹配前面的字符串至少 n 次             | 'b{2}' 匹配 2 个或更多的 b                 | bbb、bbbb、bbbbbbb         |
| 字符串 {n,m} | 匹配前面的字符串至少 n 次， 至多 m 次 | 'b{2,4}' 匹配最少 2 个，最多 4 个 b        | bbb、bbbb                  |

### 查询以特定字符或字符串开头的记录

```sql
select * from <tableName> where <columnName> regexp '^<str>';
```

### 查询以特定字符或字符串结尾的记录

```sql
select * from <tableName> where <columnName> regexp '<str>$';
```

### 替代字符串中的任意一个字符

```sql
select * from <tableName> where <columnName> regexp '<str_1>.<str_2>';
```

### 匹配多个字符

> 字符`*`和`+`都可以匹配多个该符号之前的字符。不同的是，`+`表示至少一个字符，而`*`可以表示 0 个字符。

```sql
select * from <tableName> where <columnName> regexp '<str>+';
select * from <tableName> where <columnName> regexp '<str>*';
```

### 匹配指定字符串

> 正则表达式可以匹配字符串。当表中的记录包含这个字符串时，就可以将该记录查询出来。指定多个字符串时，需要用`|`隔开。只要匹配这些字符串中的任意一个即可。
>
> 注意：字符串与`|`之间不能有空格。因为，查询过程中，数据库系统会将空格也当作一个字符，这样就查询不出想要的结果。

```sql
select * from <tableName> where <columnName> regexp '<str>';

select * from <tableName> where <columnName> regexp '<str_1>|<str_2>';
```

### 匹配指定字符串中的任意一个

> `[]`还可以指定集合的区间。
> 例如，“[a-z]”表示从 a~z 的所有字母；“[0-9]”表示从 0~9 的所有数字；“[a-z0-9]”表示包含所有的小写字母和数字；“[a-zA-Z]”表示匹配所有字符。

```sql
select * from <tableName> where <columnName> regexp '[<str>]';
```

### 匹配指定字符以外的字符

> `[^str...]`用来匹配不在指定集合中的任何字符。

```sql
select * from <tableName> where <columnName> regexp '[^<str>]';
```

### 使用{n,}或者{n,m}来指定字符串连续出现的次数

> `字符串{n,}`表示字符串连续出现 n 次；`字符串{n,m}`表示字符串连续出现至少 n 次，最多 m 次。

```sql
select * from <tableName> where <columnName> regexp '<str>{n,}';
select * from <tableName> where <columnName> regexp '<str>{n,m}';
```

