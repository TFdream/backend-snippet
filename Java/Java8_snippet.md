# Java8常用代码片段

## 从列表中取出某个属性的列表
```
List<String> courseIds=  users.stream().map(User::getName).collect(Collectors.toList());
```

