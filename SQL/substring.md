# substring用法

MySQL 字符串截取函数：left(), right(), substring(), substring_index()。还有 mid(), substr()。其中，mid(), substr() 等价于 substring() 函数，substring() 的功能非常强大和灵活。

## 语法
substring(参数1，参数2，参数3)，其中三个参数分别表示：参数1表示需要截取的字符串，参数2表示从字符串的那个位置开始截取（字符串下标从1开始），参数3表示要截取多少位。

注：如果参数3不填，表示截取从参数2指定的位置开始剩下的全部字符。

例如
```
select substring("jason",1,2);
```
结果为：ja
```
select substring("jason",1);
```
结果为：jason

复杂示例：
```
update es_shop_goods_option set goods_code = CONCAT('0059', substring(goods_code, 5)) where shop_id = 5 and goods_code IN 
('0023-002007-01', '0023-002008-01', '0023-002009-01')
```

