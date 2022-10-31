# Error Code: 1055. Expression #3

Error Code: 1055. Expression #3 of SELECT list is not in GROUP BY clause and contains nonaggregated column '×××' which is not functionally dependent on columns in GROUP BY clause; `this is incompatible with sql_mode=only_full_group_by`

问题原因：`MySQL5.7+`将`sql_mode`的`ONLY_FULL_GROUP_BY`模式默认设置为`打开`状态，DQL语句在使用group by时只能只用被group by的字段与聚合函数；

解决方案：

1. 通过 `select @@global.sql_mode;`来查看当前数据库的sql_mode属性值；
    通过`set sql_mode =  'STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION';`但是这种方式设置的只是当前会话中的sql_model，服务器重启后，设置可能会失效。

2. **配置文件中设置`sql_mode`，将`ONLY_FULL_GROUP `移除，并重启MySQL服务。**

