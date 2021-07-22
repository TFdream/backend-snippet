# Jackson快速入门

## 一、简单使用
### 添加依赖
```
  <dependency>
      <groupId>com.fasterxml.jackson.core</groupId>
      <artifactId>jackson-databind</artifactId>
      <version>2.11.4</version>
  </dependency>
```

### 初始化
```
    ObjectMapper mapper = new ObjectMapper()
            .setSerializationInclusion(JsonInclude.Include.NON_NULL)
            .configure(SerializationFeature.INDENT_OUTPUT, true) //控制pretty printer
            .configure(DeserializationFeature.FAIL_ON_UNKNOWN_PROPERTIES, false)
            .setDateFormat(new SimpleDateFormat("yyyy-MM-dd HH:mm:ss"));
```

### 序列化对象
```
    @Test
    public void testJSON() throws IOException {
        ObjectMapper mapper = new ObjectMapper()
                .setSerializationInclusion(JsonInclude.Include.NON_NULL)
                .configure(SerializationFeature.INDENT_OUTPUT, true)
                .configure(DeserializationFeature.FAIL_ON_UNKNOWN_PROPERTIES, false)
                .setDateFormat(new SimpleDateFormat("yyyy-MM-dd HH:mm:ss"));

        CustomerOrderBill bill = new CustomerOrderBill();
        bill.setId(1L);
        bill.setUpdateTime(new Date());
        bill.setCreateTime(new Date());
        bill.setName("测试账单");
        //序列化
        String json = mapper.writeValueAsString(bill);
        byte[] buf = mapper.writeValueAsBytes(bill);
        System.out.println(json);
    }
```
输出：
```
{
  "id" : 1,
  "name" : "测试账单",
  "createTime" : "2021-07-22 10:46:56",
  "updateTime" : "2021-07-22 10:46:56"
}
```

### 反序列化
```
        CustomerOrderBill bill = mapper.readValue(json, CustomerOrderBill.class);
        CustomerOrderBill bill = mapper.readValue(buf, CustomerOrderBill.class);
```

## 二、支持jdk8的日期类型

首先需要添加依赖：
```
        <jackson.version>2.11.4</jackson.version>

        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-databind</artifactId>
            <version>${jackson.version}</version>
        </dependency>
        
        <dependency>
            <groupId>com.fasterxml.jackson.module</groupId>
            <artifactId>jackson-module-parameter-names</artifactId>
            <version>${jackson.version}</version>
        </dependency>
        <dependency>
            <groupId>com.fasterxml.jackson.datatype</groupId>
            <artifactId>jackson-datatype-jdk8</artifactId>
            <version>${jackson.version}</version>
        </dependency>
        <dependency>
            <groupId>com.fasterxml.jackson.datatype</groupId>
            <artifactId>jackson-datatype-jsr310</artifactId>
            <version>${jackson.version}</version>
        </dependency>
```

UserDTO如下：
```
public class UserDTO {
    private Long id;
    private String name;

    /**
     * 生日
     */
    private LocalDate birthday;

    /**
     * 注册时间
     */
    private LocalDateTime regTime;

    private Date createTime;
    private Date updateTime;

    //getter/setter
    
}
```

代码示例：
```

    @Test
    public void testJSON() throws IOException {
        ObjectMapper mapper = new ObjectMapper()
                .setSerializationInclusion(JsonInclude.Include.NON_NULL)
                .configure(SerializationFeature.INDENT_OUTPUT, true)
                .configure(DeserializationFeature.FAIL_ON_UNKNOWN_PROPERTIES, false)
                .registerModule(new ParameterNamesModule())
                .registerModule(new Jdk8Module())
                .registerModule(new JavaTimeModule())
                .setDateFormat(new SimpleDateFormat("yyyy-MM-dd HH:mm:ss"));

        UserDTO userDTO = new UserDTO();
        userDTO.setId(1L);
        userDTO.setName("张三丰");
        userDTO.setBirthday(LocalDate.of(2002, 7, 1));
        userDTO.setRegTime(LocalDateTime.of(2021, 7, 1, 0, 0, 0));
        userDTO.setUpdateTime(new Date());
        userDTO.setCreateTime(new Date());

        String json = mapper.writeValueAsString(userDTO);
        byte[] buf = mapper.writeValueAsBytes(userDTO);
        System.out.println(json);
    }
```

输出：
```
{
  "id" : 1,
  "name" : "张三丰",
  "birthday" : "2002-07-01",
  "regTime" : "2021-07-01T00:00:00",
  "createTime" : "2021-07-22 10:58:56",
  "updateTime" : "2021-07-22 10:58:56"
}

```

