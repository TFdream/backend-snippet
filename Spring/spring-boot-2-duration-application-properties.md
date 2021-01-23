## Spring Boot 2.x yml配置文件时间类型处理原理

其核心在于 ```org.springframework.boot.convert.ApplicationConversionService``` 类，

```
public class ApplicationConversionService extends FormattingConversionService {

	private static volatile ApplicationConversionService sharedInstance;

	public ApplicationConversionService() {
		this(null);
	}

	public ApplicationConversionService(StringValueResolver embeddedValueResolver) {
		if (embeddedValueResolver != null) {
			setEmbeddedValueResolver(embeddedValueResolver);
		}
		configure(this);
	}
  
  public static void configure(FormatterRegistry registry) {
		DefaultConversionService.addDefaultConverters(registry);
		DefaultFormattingConversionService.addDefaultFormatters(registry);
		addApplicationFormatters(registry);
		addApplicationConverters(registry);
	}
}
```

重点关注一下 configure方法，addApplicationFormatters 和 addApplicationConverters方法是注册Formatter和Converter，我们来看一下 addApplicationConverters方法：
```
	public static void addApplicationConverters(ConverterRegistry registry) {
		addDelimitedStringConverters(registry);
		registry.addConverter(new StringToDurationConverter());
		registry.addConverter(new DurationToStringConverter());
		registry.addConverter(new NumberToDurationConverter());
		registry.addConverter(new DurationToNumberConverter());
		registry.addConverter(new StringToPeriodConverter());
		registry.addConverter(new PeriodToStringConverter());
		registry.addConverter(new NumberToPeriodConverter());
		registry.addConverter(new StringToDataSizeConverter());
		registry.addConverter(new NumberToDataSizeConverter());
		registry.addConverter(new StringToFileConverter());
		registry.addConverter(new InputStreamSourceToByteArrayConverter());
		registry.addConverterFactory(new LenientStringToEnumConverterFactory());
		registry.addConverterFactory(new LenientBooleanToEnumConverterFactory());
	}
```
  

## 相关资料
* [Spring boot 2 Converting Duration java 8 application.properties](https://stackoverflow.com/questions/51818137/spring-boot-2-converting-duration-java-8-application-properties)
* [java.time.Duration properties can't be injected through @Value](https://github.com/spring-projects/spring-boot/issues/13237)
