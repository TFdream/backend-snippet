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
到访记录按照user_id去重，并且保留最近一条记录，如下：
```
select * from `sys_v3_report_record` where id in (select max(id) from `sys_v3_report_record` group by user_id);
```

带筛选条件分页：
```
select * from (select * from sys_v3_report_record where id IN (
      select max(id) from sys_v3_report_record where report_time >='2020-06-01 00:00:00' group by user_id))  tmp order by id desc limit 10; 
```

