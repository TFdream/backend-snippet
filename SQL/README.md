# SQL

## SQL针对某一字段去重，并且保留最近一条记录
```
select * from `user_visiting` where id in (select max(id) from `user_visiting` group by user_id);
```

## SQL的IF语句
语法如下：
```
IF(expr1,expr2,expr3)
```
如果 expr1 是TRUE (expr1 <> 0 and expr1 <> NULL)，则 IF()的返回值为expr2; 否则返回值则为 expr3。IF() 的返回值为数字值或字符串值，具体情况视其所在语境而定。

示例如下：
```
SELECT IF(gender=1, "男","女") AS gender_hz FROM `sys_user` WHERE gender != '';
```
