## Spring AOP 自定义注解

在Spring中有两种方式设置AOP
* 使用@Aspect注解（基于AspectJ）；
* 使用DefaultPointcutAdvisor


以实现数据源路由切换为例，

### 方法1：使用Aspectj表达式

```
import org.aopalliance.intercept.MethodInterceptor;
import org.aopalliance.intercept.MethodInvocation;

import java.lang.reflect.Method;

class DynamicDataSourceInterceptor implements MethodInterceptor {

    @Override
    public Object invoke(MethodInvocation invocation) throws Throwable {
        Method method = invocation.getMethod();
        
        System.out.println("method "+method+" is called on "+
                invocation.getThis()+" with args "+invocation.getArguments());
        
        Object ret = invocation.proceed();
        
        System.out.println("method "+method+" returns "+ret);
        return ret;
    }
}
```

织入配置类：
```
@Configuration
public class DynamicDataSourceConfig {
 
    public static final String traceExecution = "execution(* com.github.datasource.aop..*.*(..))";
 
    @Bean
    public DefaultPointcutAdvisor defaultPointcutAdvisor() {
        DynamicDataSourceInterceptor interceptor = new DynamicDataSourceInterceptor();
        AspectJExpressionPointcut pointcut = new AspectJExpressionPointcut();
        pointcut.setExpression(traceExecution);
 
        // 配置增强类advisor
        DefaultPointcutAdvisor advisor = new DefaultPointcutAdvisor();
        advisor.setPointcut(pointcut);
        advisor.setAdvice(interceptor);
        return advisor;
    }
}
```

当执行到 ```com.github.datasource.aop``` 包下的方法，会自动被DynamicDataSourceInterceptor拦截。

### 方式2：自定义注解（切点）+@Aspect（切面）构成织入

```
import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.annotation.After;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;
import org.aspectj.lang.annotation.Pointcut;
import org.aspectj.lang.reflect.MethodSignature;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.core.annotation.Order;
import org.springframework.stereotype.Component;

import java.lang.reflect.Method;

@Component
@Aspect
@Order(-1)
public class DataSourceAspect {

    private static Logger logger = LoggerFactory.getLogger(DataSourceAspect.class);

    @Pointcut("@within(com.renzhenmall.oms.datasource.DataSourceRouting) || @annotation(com.renzhenmall.oms.datasource.DataSourceRouting)")
    public void dsRoutePoint() {
        // 数据源切换切点
    }

    @Before("dsRoutePoint()")
    public void before(JoinPoint joinPoint) {
        try {
            if (logger.isDebugEnabled()) {
                logger.debug("【数据源切换】开始设置数据源");
            }

            MethodSignature ms = (MethodSignature) joinPoint.getSignature();
            Method method = ms.getMethod();
            Class<?> refClass = method.getDeclaringClass();
            String pkgName = refClass.getPackage().getName();
            DataSourceRouting dsRouting = null;

            if (method.isAnnotationPresent(DataSourceRouting.class)) {
                dsRouting = method.getAnnotation(DataSourceRouting.class);
            }

            if (dsRouting == null && refClass.isAnnotationPresent(DataSourceRouting.class)) {
                dsRouting = refClass.getAnnotation(DataSourceRouting.class);
            }

            // 没有这个注解说明不正常，直接抛出异常
            if (dsRouting == null) {
                if (logger.isDebugEnabled()) {
                    logger.debug("【数据源切换】没有数据源切换标识，不做任何处理！方法{}.{}",
                            refClass.getCanonicalName(), method.getName());
                }
                throw new RuntimeException("数据源切换失败，请重试");
            }

            String dataSourceKey = dsRouting.value();
            //支持多个主数据源的逻辑
            if(StringUtils.isBlank(dataSourceKey)){
                dataSourceKey = DynamicDataSource.getPkgDefaultDsKey(pkgName);
            }
            DynamicDataSource.setDataSourceKey(dataSourceKey);

            if (logger.isDebugEnabled()) {
                logger.debug("【数据源切换】标签配置:{} (最终切换到：{})",
                        dsRouting.value(), DynamicDataSource.getDataSourceKey());
            }

        } catch (Exception ex) {
            logger.error("【数据源切换】异常：", ex);
            throw new RuntimeException(ex);
        }
    }

    @After("dsRoutePoint()")
    public void after(JoinPoint joinPoint) {
        try {

            if (logger.isDebugEnabled()) {
                logger.debug("【数据源清理】清理当前数据源 {}", DynamicDataSource.getDataSourceKey());
            }

            DynamicDataSource.clear();

            if (logger.isDebugEnabled()) {
                logger.debug("【数据源清理】清理数据源完成");
            }
        } catch (Exception e) {
            logger.error("【数据源清理】异常：", e);
        }
    }
}
```


### 方式3：自定义注解（切点）+interceptor（增强Advice）
首先自定义注解 DSRouting：
```
import java.lang.annotation.*;

@Target({ElementType.METHOD, ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Inherited
@Documented
public @interface DSRouting {

    String value() default "";
}
```

在拦截器中识别是否有 DSRouting注解，如下：
```
import org.aopalliance.intercept.MethodInterceptor;
import org.aopalliance.intercept.MethodInvocation;
import org.springframework.core.annotation.AnnotationUtils;

import java.lang.reflect.Method;

class DynamicDataSourceInterceptor implements MethodInterceptor {

    @Override
    public Object invoke(MethodInvocation invocation) throws Throwable {
        Method method = invocation.getMethod();
        //优先从方法上查找
        DSRouting dsRouting = AnnotationUtils.getAnnotation(method, DSRouting.class);
        if (dsRouting == null) {    //尝试从类上查找
            dsRouting = AnnotationUtils.findAnnotation(targetClass, DSRouting.class);
        }
        if (dsRouting == null) {
            return invocation.proceed();
        }
        
        //执行
        Object ret = invocation.proceed();
        
        return ret;
    }
}
```

织入配置：
```
@Configuration
public class DynamicDataSourceAnnotationConfig {
 
    @Bean
    public DefaultPointcutAdvisor defaultPointcutAdvisor() {
        DynamicDataSourceInterceptor interceptor = new DynamicDataSourceInterceptor();
 
        AnnotationMatchingPointcut pointcut = new AnnotationMatchingPointcut(DSRouting.class, DSRouting.class, true);
        //JdkRegexpMethodPointcut pointcut = new JdkRegexpMethodPointcut();
        //pointcut.setPatterns("com.github.*");
 
        // 配置增强类advisor
        DefaultPointcutAdvisor advisor = new DefaultPointcutAdvisor();
        advisor.setPointcut(pointcut);
        advisor.setAdvice(interceptor);
        return advisor;
    }
}
```

