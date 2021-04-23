# Update子查询示例
MySQL的update的一些特点：
1. 更新的表不能在set和where中子查询；
2. 可以对多个表进行更新（sqlserver不行）；如：update ta a,tb b set a.Bid=b.id ,b.Aid=a.id;  
3. update 后面可以做任意的查询，这个作用等同于from；


## 修改订单商品维权状态

订单商品表如下：
```
CREATE TABLE `es_shop_order_goods` (
  `id` int(11) NOT NULL AUTO_INCREMENT COMMENT '主键ID',
  `shop_id` int(11) NOT NULL DEFAULT '0' COMMENT '店铺ID',
  `order_id` int(11) NOT NULL DEFAULT '0' COMMENT '订单ID',
  `goods_id` int(11) NOT NULL DEFAULT '0' COMMENT '商品ID',
  `status` int(11) NOT NULL DEFAULT '0' COMMENT '订单商品状态 0:未支付 1:已支付',
  `member_id` int(11) NOT NULL DEFAULT '0' COMMENT '会员ID',
  `price` decimal(10,2) NOT NULL DEFAULT '0.00' COMMENT '实际付现金金额',
  `price_unit` decimal(10,2) NOT NULL DEFAULT '0.00' COMMENT '商品售价（单价）',
  `price_original` decimal(10,2) NOT NULL DEFAULT '0.00' COMMENT '商品划线价',
  `price_discount` decimal(10,2) NOT NULL DEFAULT '0.00' COMMENT '商品折扣（包括：积分+优惠券）',
  `price_change` decimal(10,2) NOT NULL DEFAULT '0.00',
  `cost_price` decimal(10,2) NOT NULL DEFAULT '0.00' COMMENT '商品成本价',
  `credit` int(11) NOT NULL,
  `credit_unit` int(11) NOT NULL,
  `credit_original` int(11) NOT NULL,
  `coupon_price` decimal(10,2) NOT NULL DEFAULT '0.00' COMMENT '抵扣优惠券金额',
  `member_discount_price` decimal(10,2) NOT NULL DEFAULT '0.00',
  `total` int(11) NOT NULL DEFAULT '1' COMMENT '数量',
  `title` varchar(255) NOT NULL DEFAULT '' COMMENT '商品名称',
  `option_id` int(11) NOT NULL DEFAULT '0' COMMENT '商品规格ID',
  `option_title` varchar(255) NOT NULL DEFAULT '' COMMENT '商品规格名称',
  `thumb` varchar(255) NOT NULL DEFAULT '' COMMENT '商品图片',
  `goods_code` varchar(255) NOT NULL DEFAULT '' COMMENT '商品编码',
  `unit` varchar(10) NOT NULL DEFAULT '',
  `weight` decimal(10,2) NOT NULL DEFAULT '0.00',
  `volume` decimal(10,2) NOT NULL DEFAULT '0.00',
  `deduct_credit` int(10) NOT NULL DEFAULT '0',
  `add_credit` int(11) NOT NULL DEFAULT '0',
  `form_data` text,
  `refund_status` tinyint(1) NOT NULL DEFAULT '0' COMMENT '商品维权状态 0:未维权 1:维权中 2:维权完成',
  `refund_type` tinyint(1) NOT NULL DEFAULT '0' COMMENT '商品维权类型 0:未维权 1:仅退款 2:退货退款 3:换货',
  `refund_id` int(11) NOT NULL DEFAULT '0' COMMENT '商品维权记录ID，关联es_shop_order_refund表id',
  `package_id` int(11) NOT NULL DEFAULT '0' COMMENT '商品物流记录ID，关联es_shop_order_package表id ',
  `package_cancel_reason` varchar(255) NOT NULL DEFAULT '',
  `comment_status` tinyint(1) NOT NULL DEFAULT '0',
  `create_time` timestamp NOT NULL COMMENT '创建时间',
  `update_time` timestamp NOT NULL COMMENT '更新时间',
  PRIMARY KEY (`id`),
  KEY `idx_order_id` (`order_id`)
) ENGINE=InnoDB  DEFAULT CHARSET=utf8;
```

根据 订单号和商品编码 修改维权状态，如下：
```
update es_shop_order_goods a, (SELECT id from es_shop_order_goods where order_id = 
(SELECT id from es_shop_order where order_no = 'ES20210409113948645971') and goods_code = '0010-000077-01') b
 set a.refund_id = 0, a.refund_type = 0,  a.refund_status = 0 
where a.id = b.id;
```

## 示例2

```
UPDATE article a,(select id from article where content like '%/public/%') b set a.content = REPLACE(a.content,'/public/','/') where a.id in b.id;
```
