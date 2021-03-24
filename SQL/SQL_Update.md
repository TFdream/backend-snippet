

## MySQL 多表联合更新
举个栗子，有如下2张表
班级表：
```
CREATE TABLE `class` (
  `id` int(11) NOT NULL AUTO_INCREMENT COMMENT '主键ID',
  `name` varchar(50) NOT NULL DEFAULT '' COMMENT '班级名称',
  `status` tinyint(1) NOT NULL DEFAULT '1' COMMENT '状态',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COMMENT='班级表';
```

学生表：
```
CREATE TABLE `student` (
  `id` int(11) NOT NULL AUTO_INCREMENT COMMENT '主键ID',
  `name` varchar(50) NOT NULL DEFAULT '' COMMENT '分类名称',
  `class_id` int(11) NOT NULL DEFAULT '0' COMMENT '班级ID，关联class表id',
  `class_name` varchar(255) NOT NULL DEFAULT '' COMMENT '班级名称',
  PRIMARY KEY (`id`),
  KEY `idx_class_id` (`class_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COMMENT='学生表';
```

现在需要将student表class_name字段全部更新一边，怎么办呢？

方式1：
```
UPDATE `student` s , `class` c SET s.class_name = c.name WHERE s.class_id = c.id;
```

方式2：
```
UPDATE `student` s JOIN `class` c ON s.class_id = c.id SET s.class_name = c.name;
```

方式3：
```
UPDATE student s LEFT JOIN class c ON s.class_id = c.id SET s.class_name='test22',c.stu_name='test22'
```

