# 什么是存储过程

>   存储过程是为了完成特定功能的SQL语句集，经编译创建并保存在数据库中，用户可通过指定存储过程的名字并给定参数(需要时)来调用执行并获取返回结果。

## 存储过程的优点

1.   SQL指令无需客户端编写后通过网络传输给数据库，可以节省网络开销，同时避免了SQL指令在网络传输过程中被恶意篡改保证安全性。
2.   存储过程经过编译存储在数据库中，调用过程时无需再次编译，提升了SQL执行的效率。
3.   存储过程支持流程控制语句（分支，循环），可以实现更复杂的业务。

## 存储过程的缺点

1.   存储过程的性能调校与撰写，受限于各种数据库系统。
2.   存储过程，往往定制化于特定的数据库上，因为支持的编程语言不同。当切换到其他厂商的数据库系统时，需要重写原有的存储过程。
3.   在互联网项目中，如果需要数据库的高（连接）并发访问，使用存储过程会增加数据库的连接执行时间，造成连接开销大。

# 创建存储过程

**创建存储过程的语法**

```sql
-- IN 输入参数 OUT 输出参数 INOUT 输入输出参数
create procedure <procedureName>(IN <value_1> <dataType>,OUT <value_2> <dataType>,INOUT <value_3> <dataType>)
begin
	-- 声明变量 需要声明在存储过程的最前
    DECLARE <valueName> <dataType> default <defaultValue>;
	-- 赋值
	SET <value_3> = <value_1>;
	select <columnName> INTO <value_2> from <tableName>;
	-- 
end;
```

```sql
-- sample sql
create procedure computed(IN a int,IN b int,OUT c int)
begin
	declare temp int default 0;
	SET temp = a + b;
	SET c = temp * temp;
end;
```

# 调用存储过程

```sql
SET @value = <defalutValue>;
-- OUT @value
call procedureName(inValue,@value);
-- 显示变量值
select @value from dual;
```

```sql
set @a = 1;
set @b = 2;
set @c = 0;
call computed(@a,@b,@c);
select @c from dual;
```

# 存储过程的参数

```sql
CREATE PROCEDURE procedureName([[IN|OUT|INOUT] <valueName> <dataType>]) begin ... end;
```

>   -   `IN `输入参数：表示调用者向过程传入值（传入值可以是字面量或变量）
>   -   `OUT `输出参数：表示过程向调用者传出值(可以返回多个值)（传出值只能是变量）
>   -   `INOUT `输入输出参数：既表示调用者向过程传入值，又表示过程向调用者传出值（值只能是变量）
>
>   
>
>   -   **如果过程没有参数，也必须在过程名后面写上小括号**
>   -   **确保`参数`的名字不等于`列`的名字，否则在过程体中，参数名被当做列名来处理**

```sql
-- sample sql for IN
create procedure dome1(IN a int)
begin
	select a;
end;
call dome1(1);

-- sample sql for OUT
create procedure dome2(OUT b int)
begin
	declare temp int default 10;
	SET b = temp;
end;
SET @b = 0;
call dome2(@b);
select @b from dual;

-- sample sql for INOUT
create procedure dome3(INOUT c int)
begin
	declare temp int default 10;
	select c;
	SET c = temp;
end;
SET @c = 0;
call dome3(@c);
select @c from dual;
```



# 变量

## 变量定义

局部变量声明一定要放在存储过程体的开始：

```sql
-- datatype 为 MySQL 的数据类型，如: int, float, date,varchar(length)
create procedure <procedureName>([[IN|OUT|INOUT] <paramName> <dataType>])
begin
	DECLARE l_int int unsigned default 4000000;  
    DECLARE l_numeric number(8,2) DEFAULT 9.95;  
    DECLARE l_date date DEFAULT '1999-12-31';  
    DECLARE l_datetime datetime DEFAULT '1999-12-31 23:59:59';  
    DECLARE l_varchar varchar(255) DEFAULT 'This will not be padded';
end;
```

## 变量赋值

```sql
create procedure <procedureName>([[IN|OUT|INOUT] <paramName> <dataType>])
begin
	SET <paramName> = <expr>;
end;
```

## 用户变量

### 在MySQL客户端使用用户变量

```sql
select <defaultValue> into @a;
set @a = <defaultValue>;
select @a;
```

### **在存储过程中使用用户变量**

```sql
create procedure <procedureName>()
begin
	select @a;
end;
set @a = <defaultValue>;
call <procedureName>;
```

### 在存储过程间传递全局范围的用户变量

```sql
create procedure <procedureName_1>()
begin
	select @a;
	SET @a = 'Hello';
end;

create procedure <procedureName_2>()
begin
	declare temp varchar(32) default '';
	select @a;
	select concat(@a,' World');
end;
```

```sql
-- sample sql
create procedure p1()
begin
	SET @a = 'Hello';
	select @a;
end;

create procedure p2()
begin
	declare temp varchar(32) default '';
	select @a;
	select concat(@a,' World');
end;
call p1();
call p2();
```

# MySQL存储过程的控制语句

## 条件语句

>   `IF ... THEN ... ELSE`

```sql
create procedure <procedureName>()
begin
	if ... then
		-- SQL
	elseif ... then
		-- SQL
	else
		-- SQL
	end if;
end;
```

```sql
-- sample sql
drop procedure if exists dome1;

create procedure dome1(IN a int,IN b int ,OUT c varchar(100))
begin
	declare result int default 0;
	select a - b into result;
	if result > 0 then
		set c = 'a > b';
	elseif result < 0 then
		set c = 'a < b';
	else
		set c = 'a = b';
	end if;
end;

SET @a = 1;
SET @b = 2;
SET @r = '';
CALL dome1 ( @a, @b, @r );
SELECT @r;
```



>   `CASE`语句

```sql
create procedure <procedureName>()
begin
	declare <value_1> <dataType> default <defaultValue>;
	case <param>
	when <value_1> then
		-- SQL
	when <value_2> then
		-- SQL
	else
		-- SQL
	end case;
end;
```

```sql
-- sample sql
drop procedure if exists dome2;

create procedure dome2(IN a int)
begin
	case a
	when 1 then
		select 'case 1' as result;
	when 2 then
		select 'case 2' as result;
	else
		select 'case else' as result;
	end case;
end;

SET @a = 1;
CALL dome2(@a);
```



## 循环语句

>   `while ... end while`

```sql
create procedure <procedureName>()
begin
	declare <value_1> <dataType> default <defaultValue>;
	while <expr> do
		-- SQL
	end while;
end;
```

```sql
-- sample sql
drop PROCEDURE if exists dome1;
create procedure dome1()
begin
	declare num int default 10;
	declare result varchar(200) default '';
	while num>0 do
		select CONCAT_WS('-->',result,num) INTO result;
		set num = num-1;
	end while;
	select result;
end;

call dome1();
```



>   `repeat ... end repeat`

```sql
create procedure <procedureName>()
begin
	repeat
		-- SQL
	until <expr> -- until <expr> 为终止条件
	end repeat;
end;
```

```sql
-- sample sql
drop procedure if exists dome2;
create procedure dome2()
begin
	declare num int default 10;
	declare result varchar(200) default '';
	repeat
		select CONCAT_WS('-->',result,num) INTO result;
		set num = num-1;
	until num < 0
	end repeat;
	select result;
end;

call dome2();
```



>   `loop`

```sql
create procedure <procedureName>()
begin
	<loopName>:loop
		-- SQL
		if <expr> then
			leave <loopName>;
		end if;
	end loop;
end;
```

```sql
-- sample sql
drop procedure if exists dome3;

create procedure dome3()
begin
	declare num int default 10;
	declare result varchar(200) default '';	
	domeloop:loop
		select CONCAT_WS('-->',result,num) INTO result;
		set num = num-1;
		if num < 0 then
			select result;
			leave domeloop;
		end if;
	end loop;
end;

call dome3();
```



# 存储过程：游标

## 游标的概念

>   游标可以依次取出查询结果集中的每一条数据，也就是逐条读取查询结果集中的数据。

## 游标的使用步骤

>   声明游标 —— 打开游标 —— 提取游标数据 —— 关闭游标

```sql
-- 声明游标
declare <cursorName> cursor for select ... from ...;
-- 打开游标
open <cursorName>;
-- 提取游标数据，提取后游标自动下移
fetch <cursorName> into <param_1>,...,<param_n>;
-- 关闭游标
close <cursorName>;
```

```sql
-- sample sql
drop table if exists `classes`;
create table `classes`(
	id int primary key auto_increment,
    classes varchar(32)
);
insert into `classes`(classes) values('Java'),('C++'),('C'),('C#'),('PHP'),('Python'),('Go'),('Vue'),('Node');

drop procedure if exists dome4;
create procedure dome4()
begin
	declare temp varchar(32) default '';
	declare classList varchar(200) default 'Classes : ';
	-- 定义游标
	declare cursor_a cursor for select classes from classes;
	-- 游标中的内容执行完后将done设置为1
	declare done int default 0;
	declare continue handler for not found set done =1;
	-- 打开游标
	open cursor_a;
	-- 循环
	cursor_loop:loop
		-- 取游标的值
		fetch cursor_a into temp;
		select concat_ws(',',classList,temp) into classList;
		if done = 1 then
			leave cursor_loop;
		end if;
	end loop;
	close cursor_a;
	select classList;
end;

call dome4();
```



# 存储过程管理

## 创建存储过程

```sql
-- IN 输入参数 OUT 输出参数 INOUT 输入输出参数
create procedure <procedureName>(IN <value_1> <dataType>,OUT <value_2> <dataType>,INOUT <value_3> <dataType>)
begin
	-- 声明变量 需要声明在存储过程的最前
    DECLARE <valueName> <dataType> default <defaultValue>;
	-- 赋值
	SET <value_3> = <value_1>;
	select <columnName> INTO <value_2> from <tableName>;
	-- 
end;
```

## 查看存储过程

```sql
-- 更具数据名查看数据库中所有的存储过程
show procedure status where db=<databaseName>;
-- 查询存储过程创建细节
show create procedure <databaseName>.<procedureName>;
```

## 更新存储过程

>   更新存储过程指的是修改存储过程的特征

```sql
alter procedure <procedureName> <feature_1>,...,<feature_n>;
```

存储过程的特征参数：

-   `NO SQL`：表示子程序中不包含SQL语句。

-   `CONTAINS SQL`：表示子程序包含SQL语句，但不包含读或写数据的语句。
-   `READS SQL DATA`：表示子程序中包含读取数据的SQL。
-   `MODIFIES SQL DATA`：表示子程序中包含修改数据的SQL。
-   `SQL SECURITY `[DEFIER|INVOKER]：指明谁有权限来执行：
    -   `DEFINER `表示只有定义者自己可以执行
    -   `INVOKER `表示调用者可以执行
-   `COMMENT '<str>'`：表示注释信息

## 删除存储过程

```sql
drop procedure <procedureName>;
```

