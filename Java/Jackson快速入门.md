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
            .configure(SerializationFeature.INDENT_OUTPUT, true)
            .configure(DeserializationFeature.FAIL_ON_UNKNOWN_PROPERTIES, false)
            .setDateFormat(new SimpleDateFormat("yyyy-MM-dd HH:mm:ss"));
```

### 序列化对象
```
    CustomerOrderBill bill = new CustomerOrderBill();
    //序列化-字符串
    String json = mapper.writeValueAsString(bill);
    //序列化-字节数组
    byte[] buf = mapper.writeValueAsBytes(bill);
```

### 反序列化
```
        CustomerOrderBill bill = mapper.readValue(json, CustomerOrderBill.class);
        CustomerOrderBill bill = mapper.readValue(buf, CustomerOrderBill.class);
```

