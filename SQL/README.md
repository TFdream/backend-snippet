# SQL

## 创建数据库
```
DROP DATABASE IF EXISTS `product`;
CREATE DATABASE `product` DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci;
```

## 创建表
```
CREATE TABLE `crm_call_center_user` (
`id` bigint(20) unsigned NOT NULL AUTO_INCREMENT COMMENT '主键',
`user_id` bigint(20) NOT NULL COMMENT 'crm用户id',
`agent_id` varchar(64) NOT NULL DEFAULT '' COMMENT '坐席ID',
`agent_name` varchar(64) NOT NULL DEFAULT '' COMMENT '坐席真实名称',
`agent_nickname` varchar(64) NOT NULL DEFAULT '' COMMENT '坐席昵称',
`agent_email` varchar(64) NOT NULL DEFAULT '' COMMENT '坐席邮箱',
`call_way` smallint(4) NOT NULL DEFAULT '2' COMMENT 'Sip登录方式, 1:网页电话，2:sip话机，3:手机',
`voip_account` varchar(45) NOT NULL DEFAULT '' COMMENT '坐席voip账号',
`display_number` varchar(40) NOT NULL DEFAULT '' COMMENT '外显号码',
`group_id` varchar(64) NOT NULL DEFAULT '' COMMENT '坐席所属技能组名称',
`version` int(10) NOT NULL DEFAULT '1' COMMENT '版本号',
`login_time` datetime NOT NULL COMMENT '最近一次登录时间',
`create_time` datetime NOT NULL COMMENT '创建时间',
`update_time` datetime NOT NULL COMMENT '更新时间',
PRIMARY KEY (`id`),
UNIQUE KEY `uniq_user_id` (`user_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COMMENT='呼叫中心登录用户信息表';
```

## 增加/删除列
增加列：
```
ALTER TABLE `pig_v2_task` ADD COLUMN `agent_name` varchar(64) NOT NULL DEFAULT '' COMMENT '坐席真实名称';
```

修改列类型：
```
ALTER TABLE `pig_v2_task` MODIFY COLUMN `agent_name` varchar(128) NOT NULL DEFAULT '' COMMENT '坐席真实名称';
```

删除列：
```
ALTER TABLE `pig_v2_task` DROP COLUMN `agent_name`;
```
## 增加/删除索引
增加索引：
```
ALTER TABLE `pre_forum_thread` ADD KEY idx_authorid_displayorder (`authorid`, `displayorder`);
```
删除索引：
```
ALTER TABLE `pre_forum_thread` DROP KEY `idx_authorid`;
```
## SQL针对某一字段去重，并且保留最近一条记录
```
select * from `sys_v3_report_record` where id in (select max(id) from `sys_v3_report_record` group by user_id);
```

带筛选条件分页：
```
select * from (select * from sys_v3_report_record where id IN (
      select max(id) from sys_v3_report_record where report_time >='2020-06-01 00:00:00' group by user_id))  tmp order by id desc limit 10; 
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

### 复杂条件
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
