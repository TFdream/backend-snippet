# MySQL解析JSON

很多时候，我们需要在sql里面直接解析json字符串。这里针对MySQL 5.7版本的分水岭进行区分。

## 1.对于MySQL 5.7以上版本
使用MySQL的内置函数JSON_EXTRACT(column, '$.key')，这个函数有两个参数，第一个参数column代表json列的列名；第二个参数key代表json字符串中的某一个key。

例如 es_shop_order_goods表activity_package字段内容如下：
```
{
    "full":{
        "rule":{
            "enough":"2",
            "minus":"2000"
        },
        "price":2000
    },
    "credit":{
        "rule":{
            "type":"credit",
            "use_credit":24398,
            "member_total":287904,
            "cut_price":24398,
            "rate":"1"
        },
        "price":24398,
        "use_credit":24398
    }
}
```

那如果我们想取 credit.use_credit 该如何做呢？
```
SELECT id, JSON_EXTRACT(activity_package, '$.credit.use_credit') AS use_credit from es_shop_order_goods where order_id = 866997;
```



