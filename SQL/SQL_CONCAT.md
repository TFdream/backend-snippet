# CONCAT函数用法
CONCAT()函数用于将多个字符串连接成一个字符串。

返回结果为连接参数产生的字符串。如有任何一个参数为NULL ，则返回值为 NULL。或许有一个或多个参数。 
如果所有参数均为非二进制字符串，则结果为非二进制字符串。 如果自变量中含有任一二进制字符串，则结果为一个二进制字符串。
一个数字参数被转化为与之相等的二进制字符串格式；若要避免这种情况，可使用显式类型 cast, 例如： SELECT CONCAT(CAST(int_col AS CHAR), char_col)

## 简单示例
将'0023'打头的替换成0059
```
update es_shop_goods_option set goods_code = CONCAT('0059', substring(goods_code, 5)) where shop_id = 5 and goods_code IN 
('0023-002007-01', '0023-002008-01')
```
