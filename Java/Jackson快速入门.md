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

### 1、基础用法
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
/**
 * @author Ricky Fung
 */
public class UserDTO {
    private Long id;
    private String name;

    /**
     * 生日
     */
    private LocalDate birthday;

    /**
     * 工作时间
     */
    private LocalTime workTime;

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

### 2、进阶版

自定义模块：
```
import com.fasterxml.jackson.core.json.PackageVersion;
import com.fasterxml.jackson.databind.module.SimpleModule;
import com.fasterxml.jackson.datatype.jsr310.deser.LocalDateDeserializer;
import com.fasterxml.jackson.datatype.jsr310.deser.LocalDateTimeDeserializer;
import com.fasterxml.jackson.datatype.jsr310.deser.LocalTimeDeserializer;
import com.fasterxml.jackson.datatype.jsr310.ser.LocalDateSerializer;
import com.fasterxml.jackson.datatype.jsr310.ser.LocalDateTimeSerializer;
import com.fasterxml.jackson.datatype.jsr310.ser.LocalTimeSerializer;

import java.time.LocalDate;
import java.time.LocalDateTime;
import java.time.LocalTime;
import java.time.format.DateTimeFormatter;

public class Java8TimeCustomModule extends SimpleModule {

	private static final String DATE_TIME_FORMAT = "yyyy-MM-dd HH:mm:ss";
	private static final String DATE_FORMAT = "yyyy-MM-dd";
	private static final String TIME_FORMAT = "HH:mm:ss";

	public Java8TimeCustomModule() {
		super(PackageVersion.VERSION);
		this.addSerializer(LocalDateTime.class, new LocalDateTimeSerializer(DateTimeFormatter.ofPattern(DATE_TIME_FORMAT)));
		this.addSerializer(LocalDate.class, new LocalDateSerializer(DateTimeFormatter.ofPattern(DATE_FORMAT)));
		this.addSerializer(LocalTime.class, new LocalTimeSerializer(DateTimeFormatter.ofPattern(TIME_FORMAT)));

		this.addDeserializer(LocalDateTime.class, new LocalDateTimeDeserializer(DateTimeFormatter.ofPattern(DATE_TIME_FORMAT)));
		this.addDeserializer(LocalDate.class, new LocalDateDeserializer(DateTimeFormatter.ofPattern(DATE_FORMAT)));
		this.addDeserializer(LocalTime.class, new LocalTimeDeserializer(DateTimeFormatter.ofPattern(TIME_FORMAT)));
	}
}
```

测试用例：
```
    @Test
    public void testJSON() throws IOException {
        ObjectMapper mapper = new ObjectMapper()
                .setSerializationInclusion(JsonInclude.Include.NON_NULL)
                .configure(SerializationFeature.INDENT_OUTPUT, true)
                .configure(DeserializationFeature.FAIL_ON_UNKNOWN_PROPERTIES, false)
                .registerModule(new Java8TimeCustomModule())
                .setDateFormat(new SimpleDateFormat("yyyy-MM-dd HH:mm:ss"));

        UserDTO userDTO = new UserDTO();
        userDTO.setId(1L);
        userDTO.setName("张三丰");
        userDTO.setBirthday(LocalDate.of(2002, 7, 1));
        userDTO.setWorkTime(LocalTime.of(9, 0, 0));
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
  "workTime" : "09:00:00",
  "regTime" : "2021-07-01 00:00:00",
  "createTime" : "2021-07-22 11:14:23",
  "updateTime" : "2021-07-22 11:14:23"
}
```

细节可参考：```org.springframework.http.converter.json.Jackson2ObjectMapperBuilder``` 类的 configure方法：
```
	/**
	 * Configure an existing {@link ObjectMapper} instance with this builder's
	 * settings. This can be applied to any number of {@code ObjectMappers}.
	 * @param objectMapper the ObjectMapper to configure
	 */
	public void configure(ObjectMapper objectMapper) {
		Assert.notNull(objectMapper, "ObjectMapper must not be null");

		MultiValueMap<Object, Module> modulesToRegister = new LinkedMultiValueMap<>();
		if (this.findModulesViaServiceLoader) {
			ObjectMapper.findModules(this.moduleClassLoader).forEach(module -> registerModule(module, modulesToRegister));
		}
		else if (this.findWellKnownModules) {
			registerWellKnownModulesIfAvailable(modulesToRegister);
		}

		if (this.modules != null) {
			this.modules.forEach(module -> registerModule(module, modulesToRegister));
		}
		if (this.moduleClasses != null) {
			for (Class<? extends Module> moduleClass : this.moduleClasses) {
				registerModule(BeanUtils.instantiateClass(moduleClass), modulesToRegister);
			}
		}
		List<Module> modules = new ArrayList<>();
		for (List<Module> nestedModules : modulesToRegister.values()) {
			modules.addAll(nestedModules);
		}
		objectMapper.registerModules(modules);

		if (this.dateFormat != null) {
			objectMapper.setDateFormat(this.dateFormat);
		}
		if (this.locale != null) {
			objectMapper.setLocale(this.locale);
		}
		if (this.timeZone != null) {
			objectMapper.setTimeZone(this.timeZone);
		}

		if (this.annotationIntrospector != null) {
			objectMapper.setAnnotationIntrospector(this.annotationIntrospector);
		}
		if (this.propertyNamingStrategy != null) {
			objectMapper.setPropertyNamingStrategy(this.propertyNamingStrategy);
		}
		if (this.defaultTyping != null) {
			objectMapper.setDefaultTyping(this.defaultTyping);
		}
		if (this.serializationInclusion != null) {
			objectMapper.setSerializationInclusion(this.serializationInclusion);
		}

		if (this.filters != null) {
			objectMapper.setFilterProvider(this.filters);
		}

		this.mixIns.forEach(objectMapper::addMixIn);

		if (!this.serializers.isEmpty() || !this.deserializers.isEmpty()) {
			SimpleModule module = new SimpleModule();
			addSerializers(module);
			addDeserializers(module);
			objectMapper.registerModule(module);
		}

		this.visibilities.forEach(objectMapper::setVisibility);

		customizeDefaultFeatures(objectMapper);
		this.features.forEach((feature, enabled) -> configureFeature(objectMapper, feature, enabled));

		if (this.handlerInstantiator != null) {
			objectMapper.setHandlerInstantiator(this.handlerInstantiator);
		}
		else if (this.applicationContext != null) {
			objectMapper.setHandlerInstantiator(
					new SpringHandlerInstantiator(this.applicationContext.getAutowireCapableBeanFactory()));
		}
	}

```
