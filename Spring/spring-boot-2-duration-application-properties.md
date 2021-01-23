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

看一下 ```org.springframework.boot.convert.StringToDurationConverter```类：
```
final class StringToDurationConverter implements GenericConverter {

	@Override
	public Object convert(Object source, TypeDescriptor sourceType, TypeDescriptor targetType) {
		if (ObjectUtils.isEmpty(source)) {
			return null;
		}
		return convert(source.toString(), getStyle(targetType), getDurationUnit(targetType));
	}

	private DurationStyle getStyle(TypeDescriptor targetType) {
		DurationFormat annotation = targetType.getAnnotation(DurationFormat.class);
		return (annotation != null) ? annotation.value() : null;
	}

	private Duration convert(String source, DurationStyle style, ChronoUnit unit) {
		style = (style != null) ? style : DurationStyle.detect(source);
		return style.parse(source, unit);
	}
}
```

DurationStyle是一个枚举类，有2个取值：
```
package org.springframework.boot.convert;

public enum DurationStyle {

	/**
	 * Simple formatting, for example '1s'.
	 */
	SIMPLE("^([+-]?\\d+)([a-zA-Z]{0,2})$") {

		@Override
		public Duration parse(String value, ChronoUnit unit) {
			try {
				Matcher matcher = matcher(value);
				Assert.state(matcher.matches(), "Does not match simple duration pattern");
				String suffix = matcher.group(2);
				return (StringUtils.hasLength(suffix) ? Unit.fromSuffix(suffix) : Unit.fromChronoUnit(unit))
						.parse(matcher.group(1));
			}
			catch (Exception ex) {
				throw new IllegalArgumentException("'" + value + "' is not a valid simple duration", ex);
			}
		}

		@Override
		public String print(Duration value, ChronoUnit unit) {
			return Unit.fromChronoUnit(unit).print(value);
		}

	},

	/**
	 * ISO-8601 formatting.
	 */
	ISO8601("^[+-]?P.*$") {

		@Override
		public Duration parse(String value, ChronoUnit unit) {
			try {
				return Duration.parse(value);
			}
			catch (Exception ex) {
				throw new IllegalArgumentException("'" + value + "' is not a valid ISO-8601 duration", ex);
			}
		}

		@Override
		public String print(Duration value, ChronoUnit unit) {
			return value.toString();
		}

	};
}
```

## 相关资料
* [Spring boot 2 Converting Duration java 8 application.properties](https://stackoverflow.com/questions/51818137/spring-boot-2-converting-duration-java-8-application-properties)
* [java.time.Duration properties can't be injected through @Value](https://github.com/spring-projects/spring-boot/issues/13237)
