# SQL子查询

## where型子查询
指把内层查询的结果作为外层查询的比较条件，典型题：查询id最大,最贵商品

如果where 列 =（内层sql），则内层sql返回的必须是单行单列，单个值；

如果where 列 in（内层sql），则内层sql只返回单列，可以多行。

查出本网站最新的（goods_id最大）的一条商品, 按goods_id desc排序，再取第一行:
```
select goods_id,goods_name from goods
order by goods_id desc limit 0,1;
```

查出本网站最新的（goods_id最大）一条商品，要求：不用排序
其实很简单，实质就是下面的语句（得到最大的goods_id）:
1.select goods_id,goods_name from goods where goods_id = 32;
2.查出最大的goods_id -> select max(goods_id) from goods;
```
select goods_id,goods_name from goods 
where 
goods_id = (select max(goods_id) from goods);
```

用where型子查询，查询"每个栏目下goods_id最大的商品"
先查出所有栏目下最大的商品id，然后根据上步的id在商品表中取出相应id的商品
```
select goods_id,goods_name,cat_id from goods
where goods_id in
(select max(goods_id) from goods group by cat_id) 
```

## from型子查询
把内层的查询结果当成临时表，供外层sql再次查询，典型题：查询id最大,最贵商品

查出本网站最新的（goods_id最大）的一条商品
```
select goods_id,cat_id,goods_name from goods
order by goods_id DESC,cat_id ASC;
```
如果存在上述查询结果的这张表，表名为tmp，则只需select * from tmp group by cat_id;就可以得到结果
因为group by取每个分组下第一次出现的行
```
select * from (select goods_id,cat_id,goods_name
from goods 
order by goods_id DESC,cat_id ASC) as tmp
group by cat_id order by goods_id;
```

示例2:
```
INSERT INTO `es_shop_order_remark`(`order_id`, `remark`, `shop_id`, `create_time`) 
select so.`id` AS order_id, tmp.`remark`, tmp.`shop_id`, tmp.`create_time` from (select * from `es_shop_order_remark_tmp` where id > 3143) as tmp  join  es_shop_order so on tmp.order_no = so.order_no;
```

## exists型子查询
把外层sql的结果，拿到内层sql去测试，如果内层sql成立，则该行取出

要求：查出有商品的栏目->取栏目表，且只取有商品的栏目
假设栏目cat_id为N，则select * from goods where cat_Id = N ==>能取出数据，则说明该栏目有商品
```
select cat_id,cat_name from category
where exists
(select * from goods where goods.cat_id=category.cat_id); 
```

