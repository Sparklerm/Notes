# java.sql.SQLSyntaxErrorException: Table ‘XXXXX‘ doesn‘t exist]

`问题出现原因：在数据库没有找到 XXXXXX 表，当数据库存在此表时，在MySQL配置文件中添加如下配置：`

```
# 忽略表名大小写
lower_case_table_names = 1
```

