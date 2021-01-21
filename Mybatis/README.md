# Mybatis常见问题

## 1、Mybatis 查询返回List<String>集合
有时候，我们不需要整个表的所有字段，而是只需要某一个字段的内容，比如：我希望从资产表中查出所有资产的名称，并且不存在重复。
```
<select id="groupNameList" resultType="java.lang.String">
	SELECT `asset_name` FROM `asset` group by `asset_name`
</select>
```

返回List<String>集合时，需要将resultType的值定义为集合中元素类型，而不是返回集合本身。
