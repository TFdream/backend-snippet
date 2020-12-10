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
Map<Long, OrderAggregationDTO> orderMap = orderList.stream().collect(Collectors.toMap(OrderAggregationDTO::getOrderGoodsId, a -> a));
```
需要注意的是：
* toMap 如果集合对象有重复的key，会报错Duplicate key错误
* 如果apple1,apple2的id都为1 可以用 (k1,k2)->k1 来设置，如果有重复的key,则保留key1,舍弃key2

示例如下：
```
Map<Long, OrderAggregationDTO> orderMap = orderList.stream().collect(Collectors.toMap(OrderAggregationDTO::getOrderGoodsId, a -> a,(k1,k2) -> k1));
```

## List排序
### 方式1
```
List<Developer> listDevs = ComparatorTest.getDevelopers();

System.out.println("排序前:");
//JAVA8的写法，循环
listDevs.forEach((developer)->System.out.println(developer));

//第一个写法
Collections.sort(listDevs, new Comparator<Developer>() {
    @Override
    public int compare(Developer o1, Developer o2) {
        return o1.getAge().compareTo(o2.getAge());
    }
});
```

### 方式2
按年龄升序(Integer类型)如下：
```
List<StudentInfo> studentsSortName = studentList.stream().sorted(Comparator.comparing(StudentInfo::getAge)).collect(Collectors.toList());
```

按年龄排序(Integer类型)如下：
```
List<StudentInfo> studentsSortName = studentList.stream().sorted(Comparator.comparing(StudentInfo::getAge).reversed()).collect(Collectors.toList());
```

使用年龄进行降序排序，年龄相同再使用身高升序排序：
```
        //按年龄排序(Integer类型)
        List<StudentInfo> studentsSortName = studentList.stream()
                .sorted(Comparator.comparing(StudentInfo::getAge).reversed().thenComparing(StudentInfo::getHeight))
                .collect(Collectors.toList());
```

### 方式3

基础类型List排序:
```
//对数字进行排序
List<Integer> nums = Arrays.asList(3,1,5,2,9,8,4,10,6,7);
nums.sort(Comparator.reverseOrder()); //reverseOrder倒序
System.err.println("倒序:"+nums);

nums.sort(Comparator.naturalOrder()); //naturalOrder自然排序即：正序
System.err.println("正序:"+nums);
```

对象List单属性排序:
```
List<Developer> listDevs = ComparatorTest.getDevelopers();
listDevs.sort((Developer o1, Developer o2)->o1.getAge().compareTo(o2.getAge()));
```

也可以如下：
```
//第四个写法,Lambda写法，JAVA8的写法
//listDevs.sort((o1, o2)->o1.getAge().compareTo(o2.getAge()));

//第五写法,个Lambda写法，JAVA8的写法
//listDevs.sort(Comparator.comparing(Developer::getAge));
```

```
//第六写法,个Lambda写法，JAVA8的写法
Comparator<Developer> ageComparator = (o1, o2)->o1.getAge().compareTo(o2.getAge());
listDevs.sort(ageComparator);       //按上面配置的顺序取值
listDevs.sort(ageComparator.reversed());    //按上面配置的顺序反向取值
```

## 去重
### 1.去除List中重复的String
```
List<String> unique = list.stream().distinct().collect(Collectors.toList());
```

或者：
```
Set<Integer> userIds = list.stream().map(OrderAggregationDTO::getMemberId).collect(Collectors.toSet());
```

### 2.去除List中重复的对象
根据name去重:
```
List<Person> unique = persons.stream().collect(
            Collectors.collectingAndThen(
                    Collectors.toCollection(() -> new TreeSet<>(Comparator.comparing(Person::getName))), ArrayList::new)
);
```

根据name,sex两个属性去重:
```
List<Person> unique = persons.stream().collect(
           Collectors. collectingAndThen(
                    Collectors.toCollection(() -> new TreeSet<>(Comparator.comparing(o -> o.getName() + ";" + o.getSex()))), ArrayList::new)
);
```

