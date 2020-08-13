# JOIN查询
SQL JOIN 子句用于把来自两个或多个表的行结合起来，基于这些表之间的共同字段。

最常见的 JOIN 类型：SQL INNER JOIN（简单的 JOIN）、SQL LEFT JOIN、SQL  RIGHT JOIN、SQL FULL JOIN，其中前一种是内连接，后三种是外链接。

## 一、INNER JOIN
内连接是最常见的一种连接，只连接匹配的行。

inner join语法:
```
select column_name(s)
from table 1
INNER JOIN table 2
ON
table 1.column_name=table 2.column_name
```
注：INNER JOIN与JOIN是相同



INNER JOIN产生的结果集中，是1和2的交集。

## 二、LEFT JOIN
LEFT JOIN返回左表的全部行和右表满足ON条件的行，如果左表的行在右表中没有匹配，那么这一行右表中对应数据用NULL代替。

LEFT JOIN 语法:
```
select column_name(s)
from table 1
LEFT JOIN table 2
ON table 1.column_name=table 2.column_name
```
注：在某些数据库中，LEFT JOIN 称为LEFT OUTER JOIN

LEFT JOIN产生表1的完全集，而2表中匹配的则有值，没有匹配的则以null值取代。

```
SELECT * FROM (
( select * from es_shop_order where shop_id = 1 and status = 2 and create_time between '2020-08-01 00:00:00' and '2020-08-12 15:00:00' ) sr
 LEFT JOIN  es_shop_order_goods srg 
	ON sr.id = srg.order_id );
``` 

把两个select语句的结果JOIN 到一起并返回：
```
SELECT sr.*, srg.title FROM (
( select * from es_shop_order where shop_id = 1 and status = 2 and create_time between '2020-08-01 00:00:00' and '2020-08-12 15:00:00' ) sr
 LEFT JOIN  (select * from es_shop_order_goods where status = 1 and refund_status=0) srg 
	ON sr.id = srg.order_id );
```

## 三、RIGHT JOIN
RIGHT JOIN返回右表的全部行和左表满足ON条件的行，如果右表的行在左表中没有匹配，那么这一行左表中对应数据用NULL代替。

RIGHT JOIN语法:
```
select column_name(s)
from table 1
RIGHT JOIN table 2
ON table 1.column_name=table 2.column_name
```

注：在某些数据库中，RIGHT JOIN 称为RIGHT OUTER JOIN

RIGHT JOIN产生表2的完全集，而1表中匹配的则有值，没有匹配的则以null值取代。

## 四、FULL OUTER JOIN
FULL JOIN 会从左表 和右表 那里返回所有的行。如果其中一个表的数据行在另一个表中没有匹配的行，那么对面的数据用NULL代替

FULL OUTER JOIN语法:
```
select column_name(s)
from table 1
FULL OUTER JOIN table 2
ON table 1.column_name=table 2.column_name
```

FULL OUTER JOIN产生1和2的并集。但是需要注意的是，对于没有匹配的记录，则会以null做为值。

