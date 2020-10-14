# group语句

## 1、统计次数
学生表如下：
```
CREATE TABLE `student` (
  `id` int(10) NOT NULL AUTO_INCREMENT COMMENT '主键ID',
  `sno` varchar(40) NOT NULL DEFAULT '' COMMENT '学籍编号',
  `name` varchar(255) NOT NULL DEFAULT '' COMMENT '姓名',
  `country` varchar(255) NOT NULL DEFAULT '' COMMENT '国籍',
  `age` smallint(4) NOT NULL DEFAULT 0 COMMENT '年龄',
  `create_time` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
  `update_time` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT '修改时间',
  PRIMARY KEY (`id`),
  KEY `idx_sno` (`sno`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COMMENT='学生信息表';
```

需要查询学生中各个国家出现的次数：
```
SELECT country as 国家,COUNT(*) as 次数 FROM student GROUP BY country;

```

## 2、SQL针对某一字段去重，并且保留最近一条记录
到访记录表DDL：
```
CREATE TABLE `sys_v3_report_record` (
  `id` bigint(20) unsigned NOT NULL AUTO_INCREMENT COMMENT '主键',
  `user_id` bigint(20) NOT NULL COMMENT '用户id',
  `name` varchar(40) NOT NULL DEFAULT '' COMMENT '用户姓名',
  `id_no` varchar(100) NOT NULL DEFAULT '' COMMENT '用户身份证号',
  `gender` smallint(2) NOT NULL DEFAULT '0' COMMENT '性别 0:男性 1:女性',
  `mobile` varchar(100) NOT NULL DEFAULT '' COMMENT '注册手机号',
  `birth_place` varchar(100) NOT NULL COMMENT '籍贯（省市区）',
  `birth_adn_code` int(8) NOT NULL COMMENT '籍贯（省市区）编码',
  `resident_place` varchar(100) NOT NULL COMMENT '常住地（省市区）',
  `resident_adn_code` int(8) NOT NULL COMMENT '常住地（省市区）编码',
  `detail_address` varchar(120) NOT NULL DEFAULT '' COMMENT '详细居住地址',
  `platform_id` int(10) NOT NULL COMMENT '平台ID',
  `report_place` varchar(100) NOT NULL COMMENT '报案地点（省市区）',
  `report_adn_code` int(8) NOT NULL COMMENT '报案地点（省市区）编码',
  `report_time` datetime NOT NULL COMMENT '报案时间',
  `contact_mobile` varchar(100) NOT NULL COMMENT '联系电话（本人）',
  `backup_contact_name` varchar(50) NOT NULL COMMENT '其他联系人姓名',
  `backup_contact_mobile` varchar(100) NOT NULL COMMENT '其他联系人电话',
  `bank_id` smallint(5) NOT NULL COMMENT '归属银行id',
  `bank_name` varchar(20) NOT NULL COMMENT '归属银行名称',
  `bank_card_name` varchar(50) NOT NULL COMMENT '银行账户名',
  `bank_card_no` varchar(50) NOT NULL COMMENT '银行账号',
  `total_recharge_amount` decimal(12,2) NOT NULL COMMENT '累计充值金额',
  `total_cash_amount` decimal(12,2) NOT NULL COMMENT '累计提现金额',
  `total_gap_amount` decimal(12,2) NOT NULL COMMENT '累计充提差',
  `total_holding_amount` decimal(12,2) NOT NULL COMMENT '当前在持资产',
  `state` tinyint(2) NOT NULL DEFAULT '1' COMMENT '状态 0:无效 1:预约成功 ',
  `version` int(10) NOT NULL DEFAULT '1' COMMENT '版本号',
  `create_time` datetime NOT NULL COMMENT '创建时间',
  `update_time` datetime NOT NULL COMMENT '修改时间',
  PRIMARY KEY (`id`),
  KEY `idx_report_time` (`report_time`),
  KEY `idx_user_id_report_time` (`user_id`,`report_time`),
  KEY `idx_create_time` (`create_time`),
  KEY `idx_update_time` (`update_time`)
) ENGINE=InnoDB AUTO_INCREMENT=80 DEFAULT CHARSET=utf8 COMMENT='用户到访登记报案记录表'
```

到访记录按照user_id去重，并且保留最近一条记录，如下：
```
select * from `sys_v3_report_record` where id in (select max(id) from `sys_v3_report_record` group by user_id);
```

带筛选条件分页：
```
select * from (select * from sys_v3_report_record where id IN (
      select max(id) from sys_v3_report_record where report_time >='2020-06-01 00:00:00' group by user_id))  tmp order by id desc limit 10; 
```

