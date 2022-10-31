# 什么是数据库事务

>   数据库的**事务（Transaction）**是一种机制、一个操作序列，包含了一组数据库操作命令。事务把所有的命令作为一个整体一起向系统提交或撤销操作请求，即这一组数据库命令要么都执行，要么都不执行，因此事务是一个不可分割的工作逻辑单元。

在 **`MySQL `**中只有使用了 **`Innodb `**数据库引擎的数据库或表才支持事务。

# 数据库事务特性

事务是必须满足4个条件（`ACID`）：：原子性（**`A`**tomicity，或称不可分割性）、一致性（**`C`**onsistency）、隔离性（**`I`**solation，又称独立性）、持久性（**`D`**urability）。

-   **`原子性`：**一个事务（transaction）中的所有操作，要么全部完成，要么全部不完成，不会结束在中间某个环节。事务在执行过程中发生错误，会被回滚（Rollback）到事务开始前的状态，就像这个事务从来没有执行过一样。
-   **`一致性`：**在事务开始之前和事务结束以后，数据库的完整性没有被破坏。这表示写入的资料必须完全符合所有的预设规则，这包含资料的精确度、串联性以及后续数据库可以自发性地完成预定的工作。
-   **`隔离性`：**数据库允许多个并发事务同时对其数据进行读写和修改的能力，隔离性可以防止多个事务并发执行时由于交叉执行而导致数据的不一致。事务隔离分为不同级别，包括读未提交（Read uncommitted）、读提交（read committed）、可重复读（repeatable  read）和串行化（Serializable）。
-   **`持久性`：**事务处理结束后，对数据的修改就是永久的，即便系统故障也不会丢失。

# MySQL 事务管理

## 自动提交与手动提交

-   MySQL中，默认DML指令的执行是自动提交的，当执行DML指令后，自动提交并同步到数据库中。
-   当使用`start transaction`手动开启事务时，自动提交关闭，事务需要通过`commit`指令提交。

## 事务控制语句

-   `BEGIN `或 `START TRANSACTION` 显式地开启一个事务；
-   `COMMIT `也可以使用 `COMMIT WORK`，不过二者是等价的。COMMIT 会提交事务，并使已对数据库进行的所有修改成为永久性的；
-   `ROLLBACK `也可以使用 `ROLLBACK WORK`，不过二者是等价的。回滚会结束用户的事务，并撤销正在进行的所有未提交的修改；
-   `SAVEPOINT identifier`，`SAVEPOINT `允许在事务中创建一个保存点，一个事务中可以有多个 SAVEPOINT；
-   `RELEASE SAVEPOINT identifier` 删除一个事务的保存点，当没有指定的保存点时，执行该语句会抛出一个异常；
-   `ROLLBACK TO identifier` 把事务回滚到标记点；
-   `SET TRANSACTION` 用来设置事务的隔离级别。`InnoDB `存储引擎提供事务的隔离级别有`RU`(READ UNCOMMITTED)、`RC`(READ COMMITTED)、`RR`(REPEATABLE READ) 和 SERIALIZABLE。

## 事务操作

```sql
start transaction;
-- SQL
-- IF ERROR
rollback;
__ IF SUCCESS
commit;
```



# 事务隔离级别

## 读未提交 `RU`（Read uncommitted）

>   读未提交：一个事务可以读取到了另一个事务未提交的数据。
>
>   脏读：事务一提交了修改被事务二所读取到，事务一出现事务回滚，修改的数据被回滚但是事务二已经拿到了数据。



## 读已提交 `RC`（Read committed）

>   读已提交：事务之间只能读取提交的数据，避免了脏读但可能会导致不可重复度。
>
>   不可重复读（虚读）：同一个事务中两次查询操作读取到的数据不一致。



## 可重复读 `RR`（Repeatable  read）

>   可重复读：在一个表的多个事务中，一个事务执行了查询操作之后，其他的事务不能修改对应的数据。避免了不可重复度，但是可能会导致幻读。
>
>   幻读：事务一对数据表中的数据进行修改然后查询，在执行查询操作之前，事务二提交了新增一条记录，导致事务一认为已修改了全部的数据，但是却查询出了与修改值不一致的数据。



## 串行化（Serializable）

>   串行化：同时只允许一个事务对数据表进行操作，避免了脏读，虚读和幻读。

| 隔离级别         | 脏读 | 不可重复读（虚读） | 幻读 |
| :--------------- | :--: | :----------------: | :--: |
| Read uncommitted |  ⭕   |         ⭕          |  ⭕   |
| Read committed   |  ❌   |         ⭕          |  ⭕   |
| Repeatable  read |  ❌   |         ❌          |  ⭕   |
| Serializable     |  ❌   |         ❌          |  ❌   |

## 设置MySQL数据库默认隔离级别

>   MySQL数据库的默认隔离级别为**`可重复读`**

-   查看MySQL数据库当前的事务隔离级别

    ```sql
    -- Befor MySQL8.0.3
    select @@tx_isolation;
    
    -- After MySQL8.0.3
    select @@transaction_isolation;
    ```

-   设置事务隔离级别

    ```sql
    set session transaction isolation level <read uncommitted|read committed|repeatable read|serializable>;
    ```

    
