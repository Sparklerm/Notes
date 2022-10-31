# DML（Data Manipulation Language） 数据操纵语言

> DML 用于完成对数据表中数据的添加、删除、修改操作。

## INSERT：插入数据（添加数据）

### 基本语法

1. #### INSERT...VALUES语句

    ```sql
    insert into <tableName> [<columnName_1>,...,<columnName_n>] values(value_1,...,value_n)
    ```

    - `<tableName>`：指定被操作的表名。
    - `<columnName_x>`：指定需要插入数据的列名。若向表中的所有列插入数据，则全部的列名均可以省略，直接采用 INSERT<表名>VALUES(…) 即可。
    - `VALUES` 或 `VALUE` 子句：该子句包含要插入的数据清单。数据清单中数据的顺序要和列的顺序相对应。

2. #### INSERT…SET语句

    ```sql
    insert into <tableName> set <columnName_1> = <value_1> , ... , <columnName_n> = <value_n>;
    -- 此语句用于直接给表中的某些列指定对应的列值，即要插入的数据的列名在 SET 子句中指定，col_name 为指定的列名，等号后面为指定的数据，而对于未指定的列，列值会指定为该列的默认值。
    ```

> - 使用 INSERT…VALUES 语句可以向表中插入一行数据，也可以插入多行数据；
> - 使用 INSERT…SET 语句可以指定插入行中每列的值，也可以指定部分列的值；
> - INSERT…SELECT 语句向表中插入其他表的数据。
> - 采用 INSERT…SET 语句可以向表中插入部分列的值，这种方式更为灵活；
> - INSERT…VALUES 语句可以一次插入多条数据。
> - 在 MySQL 中，用单条 INSERT 语句处理多个插入要比使用多条 INSERT 语句更快。当使用单条 INSERT 语句插入多行数据的时候，只需要将每行数据用`()`括起来即可。

### 向表中的全部字段添加值

```sql
insert into <tableName> values(value_1 , ... , value_n);
insert into <tableName>(<columnName_1> , ... , <columnName_n>) values(value_1 , ... , value_n);
```

```sql
-- sample sql
insert into job(job) values("Writer");
insert into job(job) values("Writer") , ("Test") , ("Engineer");
```

### 向表中指定字段添加值

> 为表的指定字段插入数据，是在 INSERT 语句中只向部分字段中插入值，而其他字段的值为表定义时的默认值。

```sql
insert into <tableName> (<columnName>) values(value_1);
```

```sql
-- sample sql
insert into worker(name,job) values("Holeo","Test");
```

### INSERT INTO…FROM 语句复制表数据

> NSERT INTO…SELECT…FROM 语句用于快速地从一个或多个表中取出数据，并将这些数据作为行数据插入另一个表中。
>
> SELECT 子句返回的是一个查询到的结果集，INSERT 语句将这个结果集插入指定表中，结果集中的每行数据的字段数、字段的数据类型都必须与被操作的表完全一致。

```sql
insert into <tableName_1>(<columnName_1> , ... , <columnName_n>) select columnName_1 , ... , columnName_n from <table_2>;
```

```sql
-- sample sql
create table job_2(
	id int primary key auto_increment,
    job varchar(32) not null
);
insert into job_2 select * from job;
```

## UPDATE：修改数据（更新数据）

**UPDATE 语句的基本语法 ：**

```sql
update <tableName> set <columnName_1> = <value_1> [, ... , <columnName_n> = <value_n>] [where ...] [order by ...][limit n,m];
```

- `<tableName>`：用于指定要更新的表名称。
- `set` 子句：用于指定表中要修改的列名及其列值。其中，每个指定的列值可以是表达式，也可以是该列对应的默认值。如果指定的是默认值，可用关键字 DEFAULT 表示列值。修改一行数据的多个列值时，SET 子句的每个值用逗号分开即可。
- `where` 子句：可选项。用于限定表中要修改的行。若不指定，则修改表中所有的行。
- `order by` 子句：可选项。用于限定表中的行被修改的次序。
- `limit` 子句：可选项。用于限定被修改的行数。

```sql
-- sample sql
update worker set salary = 20000 where job = 'Go' and salary < 20000 order by salary limit 2;
```

## DELETE：删除数据

**DELETE语句的基本语法 ：**

```sql
delete from <tableName> [where ...][order by][limit n,m];
```

- `<tableName>`：指定要删除数据的表名。
- `order by` 子句：可选项。表示删除时，表中各行将按照子句中指定的顺序进行删除。
- `where` 子句：可选项。表示为删除操作限定删除条件，若省略该子句，则代表删除该表中的所有行。
- `limit` 子句：可选项。用于告知服务器在控制命令被返回到客户端前被删除行的最大值。

**`在不使用 WHERE 条件的时候，将删除所有数据。`**

```sql
-- sample sql
delete from worker where age > 30 order by age DESC limit 1;
```



## TRUNCATE：清空表记录

> **TRUNCATE** 关键字用于完全清空一个表。

```sql
-- table 关键字可省略
truncate table <tableName>;
```

> #### TRUNCATE 和 DELETE 的区别：
>
> - DELETE 是 DML 类型的语句；TRUNCATE 是 DDL 类型的语句。它们都用来清空表中的数据。
> - DELETE 是逐行一条一条删除记录的；TRUNCATE 则是直接删除原来的表，再重新创建一个一模一样的新表，而不是逐行删除表中的数据，执行数据比 DELETE 快。因此需要删除表中全部的数据行时，尽量使用 TRUNCATE 语句， 可以缩短执行时间。
> - DELETE 删除数据后，配合事件回滚可以找回数据；TRUNCATE 不支持事务的回滚，数据删除后无法找回。
> - DELETE 删除数据后，系统不会重新设置自增字段的计数器；TRUNCATE 清空表记录后，系统会重新设置自增字段的计数器。
> - DELETE 的使用范围更广，因为它可以通过 WHERE 子句指定条件来删除部分数据；而 TRUNCATE 不支持 WHERE 子句，只能删除整体。
> - DELETE 会返回删除数据的行数，但是 TRUNCATE 只会返回 0，没有任何意义。

`当不需要该表时，用 DROP；当仍要保留该表，但要删除所有记录时，用 TRUNCATE；当要删除部分记录时，用 DELETE。`

​    ‘

   如图美女
