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

## MySQL在update语句中使用limit
mysql的update语句只支持更新前多少行，不支持从某行到另一行，比如 
```
UPDATE tb_name SET column_name='test' ORDER BY id ASC LIMIT 30; 
```
更新前30行的某个字段内容，没什么问题。

UPDATE tb_name SET column_name='test' ORDER BY id ASC LIMIT 20,10; 
更新从20行到30行的某个字段的内容，这样会报错。

解决办法就是采用子查询的方式:
```
UPDATE tb_name SET column_name='test' WHERE id in (SELECT id FROM (SELECT * FROM tb_name ORDER BY id ASC LIMIT 20,10) AS tt); 
```

这样就能实现更新表中根据id升序排序的第20条到第30条数据的某个字段的内容。
