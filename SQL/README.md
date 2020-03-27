# SQL

## SQL针对某一字段去重，并且保留最近一条记录
```
select * from `user_visiting` where id in (select max(id) from `user_visiting` group by user_id);
```
