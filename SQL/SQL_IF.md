# IF语句
MySQL的IF既可以作为表达式用，也可在存储过程中作为流程控制语句使用，如下是做为表达式使用：

## IF表达式
```
IF(expr1,expr2,expr3)
```
如果 expr1 是TRUE (expr1 <> 0 and expr1 <> NULL)，则 IF()的返回值为expr2; 否则返回值则为 expr3。IF() 的返回值为数字值或字符串值，具体情况视其所在语境而定。

示例如下：
```
SELECT IF(gender=1, "男","女") AS gender_hz FROM `sys_user` WHERE gender != '';
```

## 复杂条件
查询满足条件的记录：
```
select LEFT(tmp.birth_adn_code, 4)  as adnCode,
      count(tmp.id) as visitingNum,
      sum(if(risk_level = 1, 1, 0)) as lowRiskNum,
      sum(if(risk_level = 2, 1, 0)) as mediumRiskNum,
      sum(if(risk_level = 3, 1, 0)) as highRiskNum,
      sum(total_holding_amount) as totalHoldingAmount,
      avg(total_holding_amount) as averageHoldingAmount,
      sum(total_gap_amount) as totalGapAmount,
      avg(total_gap_amount) as averageGapAmount,
      sum(if( <![CDATA[ total_gap_amount < 0, 1, 0) ]]> ) as gapAmountL0,
      sum(if( <![CDATA[ total_gap_amount >= 0 AND total_gap_amount <10000, 1, 0) ]]> ) as gapAmountL1,
      sum(if( <![CDATA[ total_gap_amount >= 10000 AND total_gap_amount <50000, 1, 0) ]]> ) as gapAmountL5,
      sum(if( <![CDATA[ total_gap_amount >= 50000 AND total_gap_amount <500000, 1, 0) ]]> ) as gapAmountL50,
      sum(if( <![CDATA[ total_gap_amount >= 500000, 1, 0) ]]> ) as gapAmountG50
      from (
      select * from visiting_registration_record where id IN (
      select max(id) from visiting_registration_record
      where visit_time between '2020-03-01 00:00:00' and '2020-06-30 00:00:00'
      group by user_id )) tmp group by adnCode
```

visiting_registration_record表DDL:
```
CREATE TABLE `visiting_registration_record` (
  `id` bigint(20) unsigned NOT NULL AUTO_INCREMENT COMMENT '主键',
  `user_id` bigint(20) NOT NULL COMMENT '用户id',
  `name` varchar(40) NOT NULL DEFAULT '' COMMENT '用户姓名',
  `id_no` varchar(100) NOT NULL DEFAULT '' COMMENT '用户身份证号',
  `mobile` varchar(100) NOT NULL DEFAULT '' COMMENT '用户手机号',
  `birth_place` varchar(100) NOT NULL COMMENT '籍贯（省市区）',
  `birth_adn_code` int(8) NOT NULL COMMENT '籍贯（省市区）编码',
  `resident_place` varchar(100) NOT NULL COMMENT '常住地（省市区）',
  `resident_adn_code` int(8) NOT NULL COMMENT '常住地（省市区）编码',
  `detail_address` varchar(120) NOT NULL DEFAULT '' COMMENT '详细居住地址',
  `visit_time` datetime NOT NULL COMMENT '到访时间',
  `center_name` varchar(30) NOT NULL DEFAULT '' COMMENT '到访地点',
  `center_id` int(10) NOT NULL DEFAULT '1' COMMENT '到访地点id',
  `risk_level` int(10) NOT NULL DEFAULT '1' COMMENT '风险等级。1低，2中，3高',
  `risk_comments` varchar(100) NOT NULL DEFAULT '' COMMENT '风险等级备注',
  `total_recharge_amount` decimal(12,2) NOT NULL COMMENT '累计充值金额',
  `total_cash_amount` decimal(12,2) NOT NULL COMMENT '累计提现金额',
  `total_gap_amount` decimal(12,2) NOT NULL COMMENT '累计充提差',
  `total_holding_amount` decimal(12,2) NOT NULL COMMENT '当前在持资产',
  `ayb_amount` decimal(12,2) NOT NULL COMMENT '爱盈宝金额',
  `zcb_plus_amount` decimal(12,2) NOT NULL COMMENT '整存宝+金额',
  `yjb_amount` decimal(12,2) NOT NULL COMMENT '月进宝金额',
  `sb_amount` decimal(12,2) NOT NULL COMMENT '散表金额',
  `state` tinyint(2) NOT NULL DEFAULT '1' COMMENT '状态 0:无效 1:预约成功 ',
  `version` int(10) NOT NULL DEFAULT '1' COMMENT '版本号',
  `create_time` datetime NOT NULL COMMENT '创建时间',
  `update_time` datetime NOT NULL COMMENT '修改时间',
  `reason` varchar(100) NOT NULL DEFAULT '' COMMENT '来访事由',
  PRIMARY KEY (`id`),
  KEY `idx_user_id_visit_time` (`user_id`,`visit_time`),
  KEY `idx_visiting_visit_time_code` (`visit_time`,`birth_adn_code`),
  KEY `idx_visiting_mobile` (`mobile`)
) ENGINE=InnoDB AUTO_INCREMENT=103 DEFAULT CHARSET=utf8 COMMENT='用户到访登记预约记录表'

```
