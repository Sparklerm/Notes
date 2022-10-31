# 视图的概念

>   **`由数据库中一张表或多张表根据特定的条件查询出的数据构成的虚拟表`**

# 视图的作用

-   简单：使用视图的用户完全不需要关心后面对应的表的结构、关联条件和筛选条件，对用户来说已经是过滤好的复合条件的结果集。
-   安全：使用视图的用户只能访问他们被允许查询的结果集，对表的权限管理并不能限制到某个行某个列，但是通过视图就可以简单的实现。
-   数据独立：一旦视图的结构确定了，可以屏蔽表结构变化对用户的影响，源表增加列对视图没有影响；源表修改列名，则可以通过修改视图来解决，不会造成对访问者的影响。

# 使用视图

## 创建视图

语法：

```sql
CREATE [OR REPLACE] [ALGORITHM = {UNDIFINED|MERGE|TEMPTABLE}] VIEW <view_name>[<column_list>]
AS select_statement
[WITH[CASCADED|LOCAL] CHECK OPTION]

create view <view_name>[<column_list>]
as <select_statement>
with check option;
```

-   `OR REPLACE`：表示在创建视图时候会替换已有视图
-   `ALGORITHM`：表示视图选择算法
-   `select_statement`：表示select语句
-   `[WITH [CASCADED | LOCAL] CHECK OPTION]`：表示视图在更新时保证在视图的权限范围之内。注意：推荐使用`WHIT [CASCADED|LOCAL] CHECK OPTION`选项，可以保证数据的安全性，所以建议加上它。

```sql
-- sample sql
drop table if exists book;
create table book(
	id int primary key auto_increment,
    book_name varchar(64),
    author varchar(32),
    price decimal(10,2),
    create_time date
);

insert into book(book_name,author,price,create_time) values('《且听风吟》','村上春树','36.66',sysdate()),('《白夜行》','东野圭吾','35.33',sysdate()),('《边城》','沈从文','38.88',sysdate()),('《活着》','余华','37.77',sysdate()),('《红玫瑰与白玫瑰》','张爱玲','35.55',sysdate()),('《三体》','刘慈欣','39.33',sysdate()),('《三体2：黑暗森林》','刘慈欣','39.33',sysdate()),('《三体3：死神永生》','刘慈欣','39.33',sysdate());

create view view_book
as select book_name,author,price from book
with check option;

select * from view_book;
```

## 视图操作

### 查看视图

```sql
-- 查看视图结构
desc <view_name>;
-- 查看视图创建详细
show create view <view_name>;
```

```sql
-- sample sql 1
desc view_book;
-- result
mysql> desc view_book;
+-----------+---------------+------+-----+---------+-------+
| Field     | Type          | Null | Key | Default | Extra |
+-----------+---------------+------+-----+---------+-------+
| book_name | varchar(64)   | YES  |     | NULL    |       |
| author    | varchar(32)   | YES  |     | NULL    |       |
| price     | decimal(10,2) | YES  |     | NULL    |       |
+-----------+---------------+------+-----+---------+-------+
3 rows in set (0.04 sec)

-- sample sql 2
show create view view_book;
-- result
mysql> show create view view_book;
+-----------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------+----------------------+
| View      | Create View                                                                                                                                                                                                                  | character_set_client | collation_connection |
+-----------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------+----------------------+
| view_book | CREATE ALGORITHM=UNDEFINED DEFINER=`root`@`%` SQL SECURITY DEFINER VIEW `view_book` AS select `book`.`book_name` AS `book_name`,`book`.`author` AS `author`,`book`.`price` AS `price` from `book` WITH CASCADED CHECK OPTION | utf8mb4              | utf8mb4_0900_ai_ci   |
+-----------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------+----------------------+
1 row in set (0.04 sec)
```

### 修改视图

```sql
-- 1
create or replace view <view_name>
as <select_statement>;
-- 2
alter view <view_name>
as <select_statement>;
```

```sql
-- sample sql 1
create or replace view view_book
as select id,book_name,author,price from book;

desc view_book;

-- sample sql 2
alter view view_book
as select book_name,author,price,create_time from book;

desc view_book;
```



### 删除视图

```sql
drop view <view_name>;
```

```sql
drop view view_book;
```



## 视图数据的特性

-   视图是虚拟表，查询视图的数据是来源于数据表中，当对原表中的数据修改时，视图的数据也会随之改变。
-   当视图中的数据做`DML`操作时，视图为`单表视图`则会影响到原表数据，当视图为`连表视图`时则不允许对视图进行DML操作。

>   对复杂查询简化操作，并且不会对数据进行修改的情况下可以使用视图。

```sql
-- sample sql
drop table if exists class;
create table class(
	id int primary key auto_increment,
    class_no varchar(32),
    grade tinyint
);
insert into class(class_no,grade) values('A01',1),('B01',1),('C01',1);
drop table if exists student;
create table student(
	id int primary key auto_increment,
    name varchar(32),
    age int,
    gender tinyint,
    class_id int
);
insert into student(name,age,gender,class_id) values('Jonh',19,1,1),('Yrusp',25,1,3),('Orisk',22,0,2),('Pande',21,1,1),('Loice',19,0,1),('Alan',22,1,2);

-- sample view single table
drop view if exists view_class;
create view view_class
as select class_no,grade from class;

select * from view_class;

insert into view_class values('A02',2);

select * from view_class;
select * from class;

-- sample view double table
drop view if exists view_class_stu;
create view view_class_stu
as select c.class_no,s.name,s.age,s.gender from class c left join student s on c.id = s.class_id;

select * from view_class_stu;

insert into view_class_stu values(99,'Boli',23,1);
insert into view_class_stu(name,age,gender) values('Boli',23,1);
-- result
1471 - The target table view_class_stu of the INSERT is not insertable-into
```

