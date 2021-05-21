
**需求：**

已经存在的MySQL数据表，希望增加一个自增主键的字段，并设置新数据的初始值。

## 测试表
通过以下命令查看表结构
```
show create table es_shop_goods_label_map;
```
如下：
```
CREATE TABLE `es_shop_goods_cate_map` (
  `category_id` int(11) NOT NULL DEFAULT 0,
  `goods_id` int(11) NOT NULL DEFAULT 0,
  `shop_id` int(11) NOT NULL DEFAULT 0,
  `_level` tinyint(2) NOT NULL DEFAULT 0 COMMENT '分类层级, 与es_shop_goods_category表level一致',
  `update_time` datetime NOT NULL DEFAULT current_timestamp() ON UPDATE current_timestamp() COMMENT '更新时间',
  PRIMARY KEY (`category_id`,`goods_id`,`shop_id`),
  UNIQUE KEY `idx_goods_id_2` (`goods_id`,`category_id`,`shop_id`),
  KEY `idx_shop_id` (`shop_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
```

添加自增字段并设置新数据的起始值：
```
alter table `es_shop_goods_cate_map` drop PRIMARY KEY;

alter table `es_shop_goods_cate_map` add column id int(11) unsigned auto_increment primary key comment '自增主键' FIRST;
```
