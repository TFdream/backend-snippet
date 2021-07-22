# Jackson快速入门

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

