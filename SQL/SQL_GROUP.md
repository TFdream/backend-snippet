# group语句
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
