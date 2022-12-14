# MySQL数据表中的字段约束

-   主键约束（`primary key`）：非空+唯一，数据表中的唯一标识
-   非空约束（`not null`）：限制列的值不能为null
-   唯一约束（`unique`）：在数据表中，此列的数据不允许重复
-   外键约束（`foreign key`）：建立不同表之间的关联关系
-   检查约束（`check(<约束条件>)`）：对列的值存入前进行检查，如果不符合约束条件在不允许存入
-   默认值约束（`default <默认值>`）：当当前列没有值存入时，自动为其添加默认值约束设定的默认值

**MySQL查看表中的约束：`SHOW CREATE TABLE <tableName>;`**

```sql
create table stu(
	id int(7) not null unique,
	name varchar(32) not null,
	age int(2) not null,
	gender char(2) not null
);
```

## 主键约束

**创建主键约束**

> 1. 主键是数据表中数据的唯一标识，一个数据表中只能存在一个主键（主键可以是一个列，也可以是多个列的组合）；
>
> 2. 通常情况下，当在修改表时要设置表中某个字段的主键约束时，要确保设置成主键约束的字段中值不能够有重复的，并且要保证是非空的。否则，无法设置主键约束。

```sql
-- 1. 声明列时同时声明主键约束
create table tableName(
	column_1 <dataType> primary key,
	......
);
create table tableName(
	column_1 <dataType> primary key,
	...... ,
    primary key (column_1)
);
-- 2. 在修改表的时候添加主键约束
alter table <tableName> add primary key(<columnName>);
```

**设置联合主键**

```sql
-- 1. 声明列时同时创建联合主键
create table tableName(
	column_1 <dataType>,
    column_2 <dataType>,
	...... ,
    primary key (column_1,column_2)
);
-- 2. 在修改表的时候添加联合主键
alter table <tableName> add primary key(<column_1,column_2>);
```

**移除主键约束**

```sql
alter table <tableName> drop primary key;
```

**设置主键自增长（数据类型为整形）**

```sql
-- 设置主键自增长
create table tableName(
	column_1 <dataType> primary key auto_increment,
	......
);
-- 设置主键自增长起始值
create table tableName(
	column_1 <dataType> primary key auto_increment,
	......
)auto_increment=<startValue>;
```

## 外键约束

**创建外键约束**

> 在为已经创建好的数据表添加外键约束时，要确保添加外键约束的列的值全部来源于主键列，并且外键列不能为空。
>
> 外键约束也可以在修改表时添加，但是添加外键约束的前提是：从表中外键列中的数据必须与主表中主键列中的数据一致或者是没有数据。

```sql
create table table_1(
	table_1_pk <dataType> primary key,
    ......
);  
-- 第一种：添加关键字
create table table_2(
	table_2_pk <dataType> primary key,
    table_2_column <dataType>,
    ......
    CONSTRAINT fk_table_1 FOREIGN KEY(table_2_column) REFERENCES table_1(table_1_pk)
);
-- 以上语句执行成功之后，在表 table_2 上添加了名称为 fk_table_1 的外键约束，外键名称为 table_2_column，其依赖于表 table_1 的主键 table_1_pk。

-- 第二种：额外声明
alter table <tableName> ADD CONSTRAINT <FK_Name> FOREIGN KEY(<columnName>) REFERENCES <masterTableName> (<masterTableColumnName>);
```

**删除外检约束**

```sql
alter table <tableName> DROP FOREIGN KEY <FK_Name>;
```

## 唯一约束

**创建唯一约束**

```sql
-- 1. 声明列时同时声明主键约束
create table tableName(
	column_1 <dataType> unique,
	......
);
-- 2
alter table <tableName> add constraint <uniqueName> UNIQUE(<columnName>);
```

**删除唯一约束**

```sql
alter table <tableName> drop index <uniqueName>;
```

**查看唯一约束**

```sql
show index from <tableName>;
```

## 非空约束

**创建非空约束**

```sql
-- 1
create table tableName(
	column_1 <dataType> not null,
	......
);
-- 2
alter table <tableName> change column <columnName> <columnName> <dataType> NOT NULL;
```

**移除非空约束**

```sql
alter table <tableName> change column <columnName> <columnName> <dataType> NULL;
```

## 检查约束

**创建检查约束**

```sql
-- 1
create table tableName(
	column_1 <dataType> unique,
	......
);
-- 2
alter table <tableName> add constraint <checkName> CHECK(<[checkConstraint]>)
```

**删除检查约束**

```sql
alter table <tableName> drop constraint <checkName>;
```

## 默认值约束

**创建默认值约束**

```sql
-- 1
create table tableName(
	column_1 <dataType> default <defaultValue>,
	......
);
-- 2
alter table <tableName> change column <columnName> <dataType> default <defaultValue>;
```

**删除默认值约束**

```sql
alter table <tableName> change column <columnName> <columnName> <dataType> default null;
```



# MySQL中的运算符

## 算术运算符

| 运算符     | 作用               | 使用方法                           |
| ---------- | ------------------ | ---------------------------------- |
| `+`        | 加法运算           | 用于获得一个或多个值的和           |
| `-`        | 减法运算           | 用于从一个值中减去另一个值         |
| `*`        | 乘法运算           | 使数字相乘，得到两个或多个值的乘积 |
| `/`        | 除法运算，返回商   | 用一个值除以另一个值得到商         |
| `%`，`MOD` | 求余运算，返回余数 | 用一个值除以另一个值得到余数       |

```sql
-- Sample table
create table sample(
	num int(11)
);
insert into sample values(10);
-- sample sql
select num , num+10 , num-10 , num*10 , num/10 , num/0 , num%10 , num%0 from sample;
-- result
+------+--------+--------+--------+--------+-------+--------+-------+
| num  | num+10 | num-10 | num*10 | num/10 | num/0 | num%10 | num%0 |
+------+--------+--------+--------+--------+-------+--------+-------+
|   10 |     20 |      0 |    100 | 1.0000 |  NULL |      0 |  NULL |
+------+--------+--------+--------+--------+-------+--------+-------+
```

> 数学运算中，除数为 0 的除法是没有意义的。所以在除法运算和取余运算中，如果除数为 0，那么返回结果为 NULL.

## 比较运算符

| 运算符                  | 作用                         |
| ----------------------- | ---------------------------- |
| `=`                     | 等于                         |
| `<=>`                   | 安全的等于                   |
| `<> `或者 `!=`          | 不等于                       |
| `<=`                    | 小于等于                     |
| `>=`                    | 大于等于                     |
| `>`                     | 大于                         |
| `IS NULL` 或者 `ISNULL` | 判断一个值是否为空           |
| `IS NOT NULL`           | 判断一个值是否不为空         |
| `BETWEEN AND`           | 判断一个值是否落在两个值之间 |

### 等于运算（=）

`= `运算符用来比较两边的操作数是否相等，**`相等的话返回 1，不相等的话返回 0`**。具体的语法规则如下：

- **若有一个或两个操作数为 NULL，则比较运算的结果为 `NULL`。**
- 若两个操作数都是字符串，则按照字符串进行比较。
- 若两个操作数均为整数，则按照整数进行比较。
- 若一个操作数为字符串，另一个操作数为数字，则 MySQL 可以自动将字符串转换为数字。

注意：`NULL `不能用于 `=` 比较。

```sql
-- sample sql
SELECT (1 = 0) , ('2' = 2) , (2 = 2) , ('0.02'=0) , ('b'='b') , ((1+3)=(2+2)) , (NULL=null);
-- result
+---------+-----------+---------+------------+-----------+---------------+-------------+
| (1 = 0) | ('2' = 2) | (2 = 2) | ('0.02'=0) | ('b'='b') | ((1+3)=(2+2)) | (NULL=null) |
+---------+-----------+---------+------------+-----------+---------------+-------------+
|       0 |         1 |       1 |          0 |         1 |             1 |        NULL |
+---------+-----------+---------+------------+-----------+---------------+-------------+
```

### 安全等于运算符（<=>）

`<=>` 操作符和 `=` 操作符类似，不过 **`<=> 可以用来判断 NULL 值`**，具体语法规则为：

- **当两个操作数均为 `NULL`时**，其**返回值为 `1` 而不为 `NULL`**；
- 而当**`一个`**操作数为 **`NULL`** 时，其返回值为 **`0`** 而不为 **`NULL`**。

```sql
-- sample sql
SELECT (1 <=> 0) , ('2'<=>2) , (2<=>2) , ('0.02'<=>0) , ('b'<=>'b') , ((1+3) <=> (2+1)) , (NULL<=>NULL);
-- result
+-----------+-----------+---------+--------------+-------------+-------------------+---------------+
| (1 <=> 0) | ('2'<=>2) | (2<=>2) | ('0.02'<=>0) | ('b'<=>'b') | ((1+3) <=> (2+1)) | (NULL<=>NULL) |
+-----------+-----------+---------+--------------+-------------+-------------------+---------------+
|         0 |         1 |       1 |            0 |           1 |                 0 |             1 |
+-----------+-----------+---------+--------------+-------------+-------------------+---------------+
```

### 不等于运算符（<> 或者 !=）

与 `=` 的作用相反，`<>` 和` !=` 用于判断数字、字符串、表达式**`是否不相等`**。对于 `<> `和` !=`，如果**`两侧操作数不相等，返回值为 1，否则返回值为 0`**；如果**`两侧操作数有一个是 NULL，那么返回值也是 NULL`。**

```sql
-- sample sql
SELECT ('good'<>'god') , (1<>2) , (4!=4) , (5.5!=5) , ((1+3)!=(2+1)) , (NULL<>NULL);
-- result
+-----------------+--------+--------+----------+----------------+--------------+
| ('good'<>'god') | (1<>2) | (4!=4) | (5.5!=5) | ((1+3)!=(2+1)) | (NULL<>NULL) |
+-----------------+--------+--------+----------+----------------+--------------+
|               1 |      1 |      0 |        1 |              1 |         NULL |
+-----------------+--------+--------+----------+----------------+--------------+
```

### 小于等于运算符（<=）

`<=` 是小于等于运算符，用来判断左边的操作数是否小于或者等于右边的操作数；**`如果小于或者等于，返回值为 1，否则返回值为 0；如果两侧操作数有一个是 NULL，那么返回值也是 NULL。`**

```sql
-- sample sql
SELECT ('good'<='god') , (1<=2) , (4<=4) , (5.5<=5) , ((1+3)<=(2+1)) , (NULL<=NULL);
-- result
+-----------------+--------+--------+----------+----------------+--------------+
| ('good'<='god') | (1<=2) | (4<=4) | (5.5<=5) | ((1+3)<=(2+1)) | (NULL<=NULL) |
+-----------------+--------+--------+----------+----------------+--------------+
|               0 |      1 |      1 |        0 |              0 |         NULL |
+-----------------+--------+--------+----------+----------------+--------------+
```

### 小于运算符（<）

`<` 是小于运算符，用来判断左边的操作数是否小于右边的操作数；**`如果小于，返回值为 1，否则返回值为 0；如果两侧操作数有一个是 NULL，那么返回值也是 NULL`**。

```sql
-- sample sql
SELECT ('good'<'god') , (1<2) , (4<4) , (5.5<5) , ((1+3)<(2+1)) , (NULL<NULL);
-- result
+----------------+-------+-------+---------+---------------+-------------+
| ('good'<'god') | (1<2) | (4<4) | (5.5<5) | ((1+3)<(2+1)) | (NULL<NULL) |
+----------------+-------+-------+---------+---------------+-------------+
|              0 |     1 |     0 |       0 |             0 |        NULL |
+----------------+-------+-------+---------+---------------+-------------+
```

### 大于等于运算符（>=）

`>=` 是大于等于运算符，用来判断左边的操作数是否大于或者等于右边的操作数；**`如果大于或者等于，返回值为 1，否则返回值为 0；如果两侧操作数有一个是 NULL，那么返回值也是 NULL。`**

```sql
-- sample sql
SELECT ('good'>='god') , (1>=2) , (4>=4) , (5.5>=5) , ((1+3)>=(2+1)) , (NULL>=NULL);
-- result
+-----------------+--------+--------+----------+----------------+--------------+
| ('good'>='god') | (1>=2) | (4>=4) | (5.5>=5) | ((1+3)>=(2+1)) | (NULL>=NULL) |
+-----------------+--------+--------+----------+----------------+--------------+
|               1 |      0 |      1 |        1 |              1 |         NULL |
+-----------------+--------+--------+----------+----------------+--------------+
```



### 大于运算符（>）

`>`是大于运算符，用来判断左边的操作数是否大于右边的操作数；**`如果大于，返回值为 1，否则返回值为 0；如果两侧操作数有一个是 NULL，那么返回值也是 NULL。`**

```sql
-- sample sql
SELECT ('good'>'god') , (1>2) , (4>4) , (5.5>5) , ((1+3)>(2+1)) , (NULL>NULL);
-- result
+----------------+-------+-------+---------+---------------+-------------+
| ('good'>'god') | (1>2) | (4>4) | (5.5>5) | ((1+3)>(2+1)) | (NULL>NULL) |
+----------------+-------+-------+---------+---------------+-------------+
|              1 |     0 |     0 |       1 |             1 |        NULL |
+----------------+-------+-------+---------+---------------+-------------+
```



### IS NULL(ISNULL) 和 IS NOT NULL 运算符

`IS NULL` 或 `ISNULL` 运算符用来**`检测一个值是否为 NULL，如果为 NULL，返回值为 1，否则返回值为 0。`**ISNULL 可以认为是 IS NULL 的简写，去掉了一个空格而已，两者的作用和用法都是完全相同的。

**`IS NOT NULL 运算符用来检测一个值是否为非 NULL，如果是非 NULL，返回值为 1，否则返回值为 0。`**

```sql
-- sample sql
SELECT (NULL IS NULL) , (ISNULL(NULL)) , (ISNULL(10)) , (10 IS NOT NULL);
-- result
+----------------+----------------+--------------+------------------+
| (NULL IS NULL) | (ISNULL(NULL)) | (ISNULL(10)) | (10 IS NOT NULL) |
+----------------+----------------+--------------+------------------+
|              1 |              1 |            0 |                1 |
+----------------+----------------+--------------+------------------+
```

### BETWEEN AND 运算符

**`BETWEEN AND`** 运算符用来**`判断表达式的值是否位于两个数之间，或者说是否位于某个范围内`**，它的语法格式如下：

```sql
... where ... and <expr> BETWEEN minValue AND maxValue
-- expr 表示要判断的表达式，min 表示最小值，max 表示最大值。如果 expr 大于等于 min 并且小于等于 max，那么返回值为 1，否则返回值为 0。

-- sample sql
SELECT (4 BETWEEN 2 AND 5) , (4 BETWEEN 4 AND 6) , (12 BETWEEN 9 AND 10);
-- result
+---------------------+---------------------+-----------------------+
| (4 BETWEEN 2 AND 5) | (4 BETWEEN 4 AND 6) | (12 BETWEEN 9 AND 10) |
+---------------------+---------------------+-----------------------+
|                   1 |                   1 |                     0 |
+---------------------+---------------------+-----------------------+
```

## 逻辑运算符

| 运算符          | 作用     |
| --------------- | -------- |
| `NOT `或者 `!`  | 逻辑非   |
| `AND `或者 `&&` | 逻辑与   |
| `OR `和` ||`    | 逻辑或   |
| `XOR`           | 逻辑异或 |

### 逻辑非运算（NOT 或者 !）

`NOT`和`!`都是逻辑非运算符，返回和操作数相反的结果，具体语法规则为：

- 当操作数为 `0`（假）时，返回值为 1；
- 当操作数为`非零值`时，返回值为 0；
- 当操作数为 `NULL` 时，返回值为 NULL。

```sql
-- sample sql1
SELECT NOT 10,NOT(1-1),NOT-5,NOT NULL,NOT 1+1;
-- result
+--------+----------+-------+----------+---------+
| NOT 10 | NOT(1-1) | NOT-5 | NOT NULL | NOT 1+1 |
+--------+----------+-------+----------+---------+
|      0 |        1 |     0 |     NULL |       0 |
+--------+----------+-------+----------+---------+

-- sample sql2
SELECT !10,!(1-1),!-5,!NULL,!1+1;
-- result
+-----+--------+-----+-------+------+
| !10 | !(1-1) | !-5 | !NULL | !1+1 |
+-----+--------+-----+-------+------+
|   0 |      1 |   0 |  NULL |    1 |
+-----+--------+-----+-------+------+

-- sample sql3
SELECT !1+1;
-- result
+------+
| !1+1 |
+------+
|    1 |
+------+
```

由运行结果可以看出，`NOT 1+1`和`! 1+1`的返回值不同，这是因为 NOT 与 ! 的优先级不同：

- `NOT `的优先级低于 `+`，因此`NOT 1+1`相当于`NOT(1+1)`，先计算`1+1`，然后再进行 NOT 运算，由于操作数不为 0，因此`NOT 1+1`的结果是 0；
- 相反，`!` 的优先级别要高于` +`，因此`! 1+1`相当于`(!1)+1`，先计算`!1`结果为 0，再加 1，最后结果为 1。

在使用运算符运算时，一定要注意运算符的优先级，如果不能确定计算顺序，最好使用括号，以保证运算结果的正确。

### 逻辑与运算符（AND 或者 &&）

`AND `和 `&&` 都是逻辑与运算符，具体语法规则为：

- 当所有操作数都为`非零值并且不为 NULL` 时，返回值为 `1`；
- 当一个或多个操作数为 `0 `时，返回值为` 0`；
- 操作数中有任何一个为 `NULL `时，返回值为 `NULL`。

```sql
-- sample sql1
SELECT 1 AND -1,1 AND 0,1 AND NULL, 0 AND NULL;
-- result
+----------+---------+------------+------------+
| 1 AND -1 | 1 AND 0 | 1 AND NULL | 0 AND NULL |
+----------+---------+------------+------------+
|        1 |       0 |       NULL |          0 |
+----------+---------+------------+------------+

-- sample sql2
SELECT 1 && -1 , 1 && 0, 1 && NULL , 0 && NULL;
-- result
+---------+--------+-----------+-----------+
| 1 && -1 | 1 && 0 | 1 && NULL | 0 && NULL |
+---------+--------+-----------+-----------+
|       1 |      0 |      NULL |         0 |
+---------+--------+-----------+-----------+
```

> 由结果可以看到，AND 和 && 的作用相同。`1 AND-1`中没有 0 或者 NULL，所以返回值为 1；`1 AND 0`中有操作数 0，所以返回值为 0；`1 AND NULL`虽然有 NULL，所以返回值为 NULL。
>
> 注意：AND 运算符可以有多个操作数，但要注意多个操作数运算时，AND 两边一定要使用空格隔开，不然会影响结果的正确性。

### 逻辑或运算符（OR 或者 ||）

`OR`和 `||` 都是逻辑或运算符，具体语法规则为：

- **当两个操作数都为非 NULL 值时，如果有任意一个操作数为非零值，则返回值为 1，否则结果为 0；**
- **当有一个操作数为 `NULL` 时，如果另一个操作数为`非零值`，则返回值为 `1`，否则结果为`NULL`；**
- **假如两个操作数`均为 NULL` 时，则返回值为 `NULL`。**

```sql
-- sample sql1
SELECT 1 OR -1 OR 0,1 OR 2,1 OR NULL, 0 OR NULL, NULL OR NULL;
-- result
+--------------+--------+-----------+-----------+--------------+
| 1 OR -1 OR 0 | 1 OR 2 | 1 OR NULL | 0 OR NULL | NULL OR NULL |
+--------------+--------+-----------+-----------+--------------+
|            1 |      1 |         1 |      NULL |         NULL |
+--------------+--------+-----------+-----------+--------------+

-- sample sql2
SELECT (1 || -1 || 0) , (1 || 2) , (1 || NULL) , (0 || NULL) , (NULL || NULL);
-- result
+----------------+----------+-------------+-------------+----------------+
| (1 || -1 || 0) | (1 || 2) | (1 || NULL) | (0 || NULL) | (NULL || NULL) |
+----------------+----------+-------------+-------------+----------------+
|              1 |        1 |           1 |        NULL |           NULL |
+----------------+----------+-------------+-------------+----------------+
```

### 异或运算（XOR 运算符）

`XOR` 表示逻辑异或，具体语法规则为：

- **当任意一个操作数为 NULL 时，返回值为 NULL；**
- **对于`非 NULL` 的操作数，`如果两个操作数都是非 0 值或者都是 0 值，则返回值为 0`；**
- **如果一个为`0`值，另一个为`非 0` 值，返回值为`1`。**

```sql
-- sample sql
SELECT (1 XOR 1) , (0 XOR 0) , (1 XOR 0) , (1 XOR NULL) , (1 XOR 1 XOR 1);

-- result
+-----------+-----------+-----------+--------------+-----------------+
| (1 XOR 1) | (0 XOR 0) | (1 XOR 0) | (1 XOR NULL) | (1 XOR 1 XOR 1) |
+-----------+-----------+-----------+--------------+-----------------+
|         0 |         0 |         1 |         NULL |               1 |
+-----------+-----------+-----------+--------------+-----------------+
```

由结果可以看到：
`1 XOR 1`和`0 XOR 0`中运算符两边的操作数都为非零值，或者都是零值，因此返回 0；
`1 XOR 0`中两边的操作数，一个为 0 值，另一个为非 0 值，所以返回值为 1；
`1 XOR NULL`中有一个操作数为 NULL，所以返回值为 NULL；
`1 XOR 1 XOR 1`中有多个操作数，运算符相同，因此运算顺序从左到右依次计算，`1 XOR 1`的结果为 0，再与 1 进行异或运算，所以返回值为 1。

提示：`a XOR b` 的计算等同于`(a AND (NOT b))`或者`((NOT a) AND b)`。

## 位运算符

| 运算符 | 说明   | 使用形式 | 举例                             |
| ------ | ------ | -------- | -------------------------------- |
| `|`    | 位或   | a \| b   | 5 \| 8                           |
| `&`    | 位与   | a & b    | 5 & 8                            |
| `^`    | 位异或 | a ^ b    | 5 ^ 8                            |
| `~`    | 位取反 | ~a       | ~5                               |
| `<< `  | 位左移 | a << b   | 5 << 2，表示整数 5 按位左移 2 位 |
| `>> `  | 位右移 | a >> b   | 5 >> 2，表示整数 5 按位右移 2 位 |

**位运算中的`&`、`|`、`~`和逻辑运算中的`&&`、`||`、`!`非常相似。**

### 位或运算符 |

参与`|`运算的两个二进制位有一个为 1 时，结果就为 1，两个都为 0 时结果才为 0。例如`1|1`结果为 1，`0|0`结果为0，`1|0`结果为1，这和逻辑运算中的`||`非常类似。

```sql
-- sample sql1
SELECT 10|15,9|4|2;
-- result
-- 10 的补码为 1010，15 的补码为 1111，按位或运算之后，结果为 1111，即整数 15；9 的补码为 1001，4 的补码为 0100，2 的补码为 0010，按位或运算之后，结果为 111，即整数 15。

-- sample slq2
SELECT -7|-1;
-- result
-- -7 的补码为 60 个‘1’加 1001，-1 的补码为 64 个‘1’，按位或运算之后，结果为 64 个‘1’，即整数 18446744073709551615。
```

**可以发现，任何数和 -1 进行位或运算时，最终结果都是 -1 的十进制数。**

### 位与运算符 &

参与`&`运算的两个二进制位都为 1 时，结果就为 1，否则为 0。例如`1|1`结果为 1，`0|0`结果为 0，`1|0`结果为 0，这和逻辑运算中的`&&`非常类似。

```sql
-- sample sql1
SELECT 10 & 15,9 & 4 & 2;
-- result
+---------+-----------+
| 10 & 15 | 9 & 4 & 2 |
+---------+-----------+
|      10 |         0 |
+---------+-----------+
 -- 10 的补码为 1010，15 的补码为 1111，按位与运算之后，结果为 1010，即整数 10；9 的补码为 1001，4 的补码为 0100，2 的补码为 0010，按位与运算之后，结果为 0000，即整数 0。
 
 -- sample sql2
SELECT -7&-1;
 -- result
 +----------------------+
| -7&-1                |
+----------------------+
| 18446744073709551609 |
+----------------------+
 -- -7 的补码为 60 个‘1’加 1001，-1 的补码为 64 个‘1’，按位与运算之后，结果为 60 个‘1’加 1001，即整数 18446744073709551609。
```

**可以发现，任何数和 -1 进行位与运算时，最终结果都为任何数本身的十进制数。**

### 位异或运算符 ^

参与`^`运算的两个二进制位不同时，结果为 1，相同时，结果为 0。例如`1|1`结果为 0，`0|0`结果为 0，`1|0`结果为1。

```sql
-- sample sql1
SELECT 10^15,1^0,1^1;
-- result
+-------+-----+-----+
| 10^15 | 1^0 | 1^1 |
+-------+-----+-----+
|     5 |   1 |   0 |
+-------+-----+-----+
-- 10 的补码为 1010，15 的补码为 111，按位异或运算之后，结果为 0101，即整数 5；1 的补码为 0001，0 的补码为 0000，按位异或运算之后，结果为 0001；1 和 1 本身二进制位完全相同，因此结果为 0。

-- sample sql2
SELECT -7^-1;
-- result
+-------+
| -7^-1 |
+-------+
|     6 |
+-------+
-- -7 的补码为 60 个‘1’加 1001，-1 的补码为 64 个‘1’，按位异或运算之后，结果为 110，即整数 6。
```



### 位左移运算符 << 

位左移是按指定值的补码形式进行左移，左移指定位数之后，左边高位的数值被移出并丢弃，右边低位空出的位置用 0 补齐。

```sql
位左移的语法格式如下，其中，n 指定值 expr 要移位的位数，n 必须为非负数。
expr << n
-- sample sql1
SELECT 1<<2,4<<2;
-- result
+------+------+
| 1<<2 | 4<<2 |
+------+------+
|    4 |   16 |
+------+------+
-- 1 的补码为 0000 0001，左移两位之后变成 0000 0100，即整数 4；4 的补码为 0000 0100，左移两位之后变成 0001 0000，即整数 16。

-- sample sql2
SELECT -7<<2;
-- result
+----------------------+
| -7<<2                |
+----------------------+
| 18446744073709551588 |
+----------------------+
-- -7 的补码为 60 个‘1’加 1001，左移两位之后变成 56 个‘1’加 1110 0100，即整数 18446744073709551588。
```



### 位右移运算符 >>

位右移是按指定值的补码形式进行右移，右移指定位数之后，右边低位的数值被移出并丢弃，左边高位空出的位置用 0 补齐。

```sql
-- 位右移语法格式如下，其中，n 指定值 expr 要移位的位数，n 必须为非负数。 
expr >> n

-- sample sql1
SELECT 1>>1,16>>2;
-- result
+------+-------+
| 1>>1 | 16>>2 |
+------+-------+
|    0 |     4 |
+------+-------+
-- 1 的补码为 0000 0001，右移 1 位之后变成 0000 0000，即整数 0；16 的补码为 0001 0000，右移两位之后变成 0000 0100，即整数 4。

-- sample sql2
SELECT -7>>2;
-- result
+---------------------+
| -7>>2               |
+---------------------+
| 4611686018427387902 |
+---------------------+
-- -7 的补码为 60 个‘1’加 1001，右移两位之后变成 0011 加 56 个‘1’加 1110，即整数 4611686018427387902。

```



### 位取反运算符 ~ 

位取反是将参与运算的数据按对应的补码进行反转，也就是做 NOT 操作，即 1 取反后变 0，0 取反后变为 1。

```sql
-- sample sql1
SELECT ~1,~18446744073709551614;
-- result
+----------------------+-----------------------+
| ~1                   | ~18446744073709551614 |
+----------------------+-----------------------+
| 18446744073709551614 |                     1 |
+----------------------+-----------------------+
-- 常量 1 的补码为 63 个‘0‘加 1 个’1‘，位取反后就是 63 个’1‘加一个’0‘，转换为二进制后就是 18446744073709551614。

-- sample sql2
SELECT BIN(~1);
-- result
+------------------------------------------------------------------+
| BIN(~1)                                                          |
+------------------------------------------------------------------+
| 1111111111111111111111111111111111111111111111111111111111111110 |
+------------------------------------------------------------------+
-- 1 的补码表示为最右边位为 1，其他位均为 0，取反操作之后，除了最低位，其他位均变为 1。

-- sample sql3
SELECT 5 & ~1;
-- result
+--------+
| 5 & ~1 |
+--------+
|      4 |
+--------+
-- 逻辑运算5&~1 中，由于位取反运算符‘~’的级别高于位与运算符‘&’，因此先对 1 进行取反操作，结果为 63 个‘1’加一个‘0’，然后再与整数 5 进行与运算，结果为 0100，即整数 4。


```

## MySQL运算符的优先级

> 不同运算符的优先级是不同的。一般情况下，级别高的运算符优先进行计算，如果级别相同，MySQL 按表达式的顺序从**`左到右依次计算`**。
>
> 另外，在无法确定优先级的情况下，可以使用圆括号**`()`**来改变优先级，并且这样会使计算过程更加清晰。

| 优先级由低到高排列 | 运算符                                                       |
| ------------------ | ------------------------------------------------------------ |
| 1                  | =(赋值运算）、:=                                             |
| 2                  | II、OR                                                       |
| 3                  | XOR                                                          |
| 4                  | &&、AND                                                      |
| 5                  | NOT                                                          |
| 6                  | BETWEEN、CASE、WHEN、THEN、ELSE                              |
| 7                  | =(比较运算）、<=>、>=、>、<=、<、<>、!=、 IS、LIKE、REGEXP、IN |
| 8                  | \|                                                           |
| 9                  | &                                                            |
| 10                 | <<、>>                                                       |
| 11                 | -(减号）、+                                                  |
| 12                 | *、/、%                                                      |
| 13                 | ^                                                            |
| 14                 | -(负号）、〜（位反转）                                       |
| 15                 | !                                                            |

# MySQL IN和 NOT IN

MySQL 中的 IN 运算符用来判断表达式的值是否位于给出的列表中；如果是，返回值为 1，否则返回值为 0。
NOT IN 的作用和 IN 恰好相反，NOT IN 用来判断表达式的值是否不存在于给出的列表中；如果不是，返回值为 1，否则返回值为 0。
IN 和 NOT IN 的语法格式如下：

```sql
-- expr 表示要判断的表达式，value1, value2, value3 ... valueN 表示列表中的值。MySQL 会将 expr 的值和列表中的值逐一对比。
<expr> IN ( value1, value2, value3 ... valueN )
<expr> NOT IN ( value1, value2, value3 ... valueN )

-- sample sql1
SELECT 2 IN (1,3,5,'thks'),'thks' IN (1,3,5, 'thks');
-- result
+---------------------+---------------------------+
| 2 IN (1,3,5,'thks') | 'thks' IN (1,3,5, 'thks') |
+---------------------+---------------------------+
|                   0 |                         1 |
+---------------------+---------------------------+

-- sample sql2
SELECT 2 NOT IN (1,3,5,'thks'),'thks' NOT IN (1,3,5, 'thks');
-- result
+-------------------------+-------------------------------+
| 2 NOT IN (1,3,5,'thks') | 'thks' NOT IN (1,3,5, 'thks') |
+-------------------------+-------------------------------+
|                       1 |                             0 |
+-------------------------+-------------------------------+
```

## 对空值 NULL 的处理

当 IN 运算符的两侧有一个为空值 NULL 时，如果找不到匹配项，则返回值为 NULL；如果找到了匹配项，则返回值为 1。

```sql
-- sample sql1
SELECT NULL IN (1,3,5,'thks'),10 IN (1,3,NULL,'thks');
-- result
+------------------------+-------------------------+
| NULL IN (1,3,5,'thks') | 10 IN (1,3,NULL,'thks') |
+------------------------+-------------------------+
|                   NULL |                    NULL |
+------------------------+-------------------------+

-- sample sql2
SELECT NULL IN (1,3,5,'thks'),10 IN (1,10,NULL,'thks');
-- result
+------------------------+--------------------------+
| NULL IN (1,3,5,'thks') | 10 IN (1,10,NULL,'thks') |
+------------------------+--------------------------+
|                   NULL |                        1 |
+------------------------+--------------------------+
```

NOT IN 恰好相反，当 NOT IN 运算符的两侧有一个为空值 NULL 时，如果找不到匹配项，则返回值为 NULL；如果找到了匹配项，则返回值为 0。

```sql
-- sample sql1
SELECT NULL NOT IN (1,3,5,'thks'),10 NOT IN (1,0,NULL,'thks');
-- result
+----------------------------+-----------------------------+
| NULL NOT IN (1,3,5,'thks') | 10 NOT IN (1,0,NULL,'thks') |
+----------------------------+-----------------------------+
|                       NULL |                        NULL |
+----------------------------+-----------------------------+

-- sample sql2
SELECT NULL NOT IN (1,3,5,'thks'),10 NOT IN (1,10,NULL,'thks');
-- result
+----------------------------+------------------------------+
| NULL NOT IN (1,3,5,'thks') | 10 NOT IN (1,10,NULL,'thks') |
+----------------------------+------------------------------+
|                       NULL |                            0 |
+----------------------------+------------------------------+
```
