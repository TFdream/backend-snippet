# JOIN查询

## LEFT JOIN
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
