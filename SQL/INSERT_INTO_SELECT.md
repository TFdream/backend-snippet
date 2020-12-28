# INSERT INTO SELECT 语句
通过 SQL，您可以从一个表复制信息到另一个表。
INSERT INTO SELECT 语句从一个表复制数据，然后把数据插入到一个已存在的表中。

## SQL INSERT INTO SELECT 语法
我们可以从一个表中复制所有的列插入到另一个已存在的表中：
```
INSERT INTO table2
SELECT * FROM table1;
```

或者我们可以只复制希望的列插入到另一个已存在的表中：
```
INSERT INTO table2
(column_name(s))
SELECT column_name(s)
FROM table1;
```

## 示例
源表：
```
CREATE TABLE `es_shop_order_remark_tmp` (
  `create_time` timestamp NOT NULL DEFAULT '0000-00-00 00:00:00',
  `id` int(10) NOT NULL AUTO_INCREMENT,
  `order_no` varchar(40) NOT NULL DEFAULT '',
  `remark` varchar(255) NOT NULL DEFAULT '',
  `shop_id` int(10) NOT NULL DEFAULT '0',
  `uid` int(10) NOT NULL DEFAULT '0',
  `supplier_shop_id` int(11) NOT NULL DEFAULT '0',
  PRIMARY KEY (`id`),
  KEY `idx_createtime` (`create_time`),
  KEY `idx_order_no` (`order_no`),
  KEY `idx_shopid` (`shop_id`),
  KEY `idx_uid` (`uid`)
) ENGINE=InnoDB CHARSET=utf8
```

目标表：
```
CREATE TABLE `es_shop_order_remark` (
  `create_time` timestamp NOT NULL DEFAULT '0000-00-00 00:00:00',
  `id` int(10) NOT NULL AUTO_INCREMENT,
  `order_id` int(10) NOT NULL DEFAULT 0,
  `remark` varchar(255) NOT NULL DEFAULT '',
  `shop_id` int(10) NOT NULL DEFAULT 0,
  `uid` int(10) NOT NULL DEFAULT 0,
  `supplier_shop_id` int(11) NOT NULL DEFAULT 0,
  PRIMARY KEY (`id`),
  KEY `idx_createtime` (`create_time`),
  KEY `idx_orderid` (`order_id`),
  KEY `idx_shopid` (`shop_id`),
  KEY `idx_uid` (`uid`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
```

导入SQL如下：
```

INSERT INTO `es_shop_order_remark`(`order_id`, `remark`, `shop_id`, `create_time`) 
select so.`id` AS order_id, tmp.`remark`, tmp.`shop_id`, tmp.`create_time` from `es_shop_order_remark_tmp` tmp join  es_shop_order so on tmp.order_no = so.order_no;
```

### 复杂点
筛选es_shop_order_remark_tmp表符合条件的记录插入到es_shop_order_remark表，如下：
```
INSERT INTO `es_shop_order_remark`(`order_id`, `remark`, `shop_id`, `create_time`) 
select so.`id` AS order_id, tmp.`remark`, tmp.`shop_id`, tmp.`create_time` from (select * from `es_shop_order_remark_tmp` where id > 3143) as tmp  join  es_shop_order so on tmp.order_no = so.order_no;
```

### 示例

将店铺8中 商品编码前4位是0010的商品加上 特殊标签，SQL如下：
```
insert into es_shop_goods_label_map(goods_id, label_id, shop_id) 
SELECT distinct(og.id) AS goods_id, 16, og.shop_id AS shop_id from es_shop_goods_option op INNER JOIN es_shop_goods og on op.goods_id = og.id and op.shop_id = 8 and op.goods_code LIKE '0010-%';
```

也可以这样做：
```
insert into es_shop_goods_label_map(goods_id, label_id, shop_id) 
select id AS goods_id, 16, shop_id from es_shop_goods where id in (SELECT distinct(goods_id) from es_shop_goods_option where goods_code LIKE '0010-%');
```
