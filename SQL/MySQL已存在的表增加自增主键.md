
**需求：**

已经存在的MySQL数据表，希望增加一个自增主键的字段，并设置新数据的初始值。

## 测试表
通过以下命令查看表结构
```
show create table es_shop_goods_label_map;
```
如下：
```
CREATE TABLE `es_shop_goods_label_map` (
  `goods_id` int(11) NOT NULL DEFAULT '0' COMMENT '商品ID',
  `label_id` int(11) NOT NULL DEFAULT '0' COMMENT '商品标签(支持)ID',
  `shop_id` int(11) NOT NULL DEFAULT '0' COMMENT '店铺ID',
  `update_time` datetime NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT '更新时间',
  KEY `idx_goods_id` (`goods_id`),
  KEY `idx_laber_id` (`label_id`),
  KEY `idx_shop_id` (`shop_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COMMENT='商品所属商品标签(支持)信息'
```


添加自增字段并设置新数据的起始值：
```
alter table `es_shop_goods_group_map` drop PRIMARY KEY;

alter table `es_shop_goods_group_map` add column id int(11) unsigned auto_increment primary key comment '自增主键' FIRST;
```
