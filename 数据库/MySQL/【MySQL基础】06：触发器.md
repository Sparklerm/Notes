# 什么是触发器？

>   触发器是一种特殊的存储过程，触发器和存储过程一样是一个能够完成特定功能，存储在数据服务器上的SQL片段，但是触发器无需调用，当对数据表中的数据执行`DML（insert|delete|update）`操作时能够自动触发。

# 使用触发器

## 创建触发器

语法：

``` sql
create trigger <triggerName>
<before|after> -- 触发时间
<insert|update|delete>	-- 触发操作
on <tableName>	-- 触发对象
for each row	-- 声明为行级触发器（每操作一条记录触发一次触发器）
sql_statement	-- 触发器操作
```

## 查看触发器

```sql
show triggers;
```

## 删除触发器

```sql
drop trigger <trigger_name>;
```

# 触发器中的关键字：NEW，OLD

>   触发器用于监听对数据表中数据的DML操作，在触发器中通常处理一些关联操作；
>
>   可以使用NEW和OLD关键字在触发器中获取触发这个触发器的DML操作的数据：
>
>   -   `NEW`：在触发器中用于获取`insert`操作添加的数据、`update`操作修改`后`的数据
>   -   `OLD`：在触发器中用于获取`update`操作`前`的数据、`delete`操作`前`的数据

## NEW

-   `insert`操作中

    ```sql
    -- sample sql
    create trigger trigger_dome1
    after insert on worker
    for each row
    insert into worker_logs(log_text,create_time) values(concat('新增员工编号为',NEW.id),now());
    ```

-   `update`操作中

    ```sql
    -- sample sql
    create trigger trigger_dome1
    after update on worker
    for each row
    insert into worker_logs(log_text,create_time) values(concat('修改工作人员信息为：',concat_sw('-',NEW.id,NEW.name,NEW.job)),now());
    ```

## OLD

-   `update`操作中

    ```sql
    -- sample sql
    create trigger trigger_dome1
    after update on worker
    for each row
    insert into worker_logs(log_text,create_time) values(concat('工作人员',OLD.id,'信息修改为：',concat_sw('-',NEW.id,NEW.name,NEW.job)),now());
    ```

-   `delete`操作中

    ```sql
    -- sample sql
    create trigger trigger_dome1
    after delete on worker
    for each row
    insert into worker_logs(log_text,create_time) values(concat('工作人员',OLD.id,'信息被删除'),now());
    ```



# 触发器小结

优点：

-   触发器监听到响应的DML操作时自动触发
-   触发器可以实现表中数据的关联操作，有利于保证数据完整性
-   触发器可以对DML操作的数据进行更为复杂的合法性校验

缺点：

-   使用触发器实现的业务逻辑出现问题难以定位，后期维护困难
-   大量使用触发器，使程序与数据库程序耦合性更高
