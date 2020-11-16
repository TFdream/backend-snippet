# Java8常用代码片段

## 从列表中取出某个属性的列表
```
List<String> courseIds=  users.stream().map(User::getName).collect(Collectors.toList());
```

## 过滤Filter
从集合中过滤出来符合条件的元素：
```
//过滤出符合条件的数据
List<Apple> filterList = appleList.stream().filter(a -> a.getName().equals("香蕉")).collect(Collectors.toList());
 
System.err.println("filterList:"+filterList);
```


## 分组
List里面的对象元素，以某个属性来分组，例如，以id分组，将id相同的放在一起：
```
//List 以ID分组 Map<Integer,List<Apple>>
Map<Integer, List<Apple>> groupBy = appleList.stream().collect(Collectors.groupingBy(Apple::getId));
 
System.err.println("groupBy:"+groupBy);
{1=[Apple{id=1, name='苹果1', money=3.25, num=10}, Apple{id=1, name='苹果2', money=1.35, num=20}], 2=[Apple{id=2, name='香蕉', money=2.89, num=30}], 3=[Apple{id=3, name='荔枝', money=9.99, num=40}]}
```

## List转Map
id为key，apple对象为value，可以这么做：
```
Map<Integer, Apple> appleMap = appleList.stream().collect(Collectors.toMap(Apple::getId, a -> a,(k1,k2)->k1));
```
需要注意的是：
* toMap 如果集合对象有重复的key，会报错Duplicate key错误
* 如果apple1,apple2的id都为1 可以用 (k1,k2)->k1 来设置，如果有重复的key,则保留key1,舍弃key2
