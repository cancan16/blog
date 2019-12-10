---
title: BeanPostProcessor分析及组件使用
date: 2019-12-08 12:39:36
update: 2019-12-09 12:39:36
categories: Spring
tags: [spring]
---

### bean初始化前增强的方法`applyBeanPostProcessorsBeforeInitialization`

在`org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory#initializeBean(java.lang.String, java.lang.Object, org.springframework.beans.factory.support.RootBeanDefinition)`中调用bean初始化前置处理器`applyBeanPostProcessorsBeforeInitialization`。

* 1,ApplicationContextAwareProcessor实现分析
* 2,BeanValidationPostProcess实现分析
* 3,InitDestroyAnnotationBeanPostProcessor实现分析


#### bean实现`ApplicationContextAware`时bean执行前置处理器

```java
import org.springframework.beans.BeansException;
import org.springframework.context.ApplicationContext;
import org.springframework.context.ApplicationContextAware;
import org.springframework.stereotype.Component;

import javax.annotation.PostConstruct;
import javax.annotation.PreDestroy;

@Component
public class Plane implements ApplicationContextAware {
    private ApplicationContext applicationContext;

    public Plane() {
        System.out.println("Plane.....constructor........");
    }

    @PostConstruct
    public void init() {
        System.out.println("Plane.....@PostConstruct........");
    }

    @PreDestroy
    public void destory() {
        System.out.println("Plane.....@PreDestroy......");
    }

    @Override
    public void setApplicationContext(ApplicationContext applicationContext) throws BeansException {
        //将applicationContext传进来,可以拿到
        this.applicationContext = applicationContext;
    }
}
```

*源码*

```java
private void invokeAwareInterfaces(Object bean) {
    if (bean instanceof Aware) {
        if (bean instanceof EnvironmentAware) {
            ((EnvironmentAware) bean).setEnvironment(this.applicationContext.getEnvironment());
        }
        if (bean instanceof EmbeddedValueResolverAware) {
            ((EmbeddedValueResolverAware) bean).setEmbeddedValueResolver(this.embeddedValueResolver);
        }
        if (bean instanceof ResourceLoaderAware) {
            ((ResourceLoaderAware) bean).setResourceLoader(this.applicationContext);
        }
        if (bean instanceof ApplicationEventPublisherAware) {
            ((ApplicationEventPublisherAware) bean).setApplicationEventPublisher(this.applicationContext);
        }
        if (bean instanceof MessageSourceAware) {
            ((MessageSourceAware) bean).setMessageSource(this.applicationContext);
        }
        if (bean instanceof ApplicationContextAware) {
            ((ApplicationContextAware) bean).setApplicationContext(this.applicationContext);
        }
    }
}
```

总结:
Spring底层对BeanPostProcessor的使用, 包括bean的赋值, 注入其它组件, 生命周期注解功能等


### @Value赋值

```java
import org.springframework.beans.factory.annotation.Value;

public class Bird {
    //使用@Value进行赋值:1,基本字符  2,springEL表达式, 3,可以读取 我们的配置文件
    @Value("James")
    private String name;

    @Value("#{20-2}")
    private Integer age;

    @Value("${bird.color}")
    private String color;

    public Bird() {
        super();
    }

    public Bird(String name, Integer age, String color) {
        super();
        this.name = name;
        this.age = age;
        this.color = color;
    }

    public String getColor() {
        return color;
    }

    public void setColor(String color) {
        this.color = color;
    }

    @Override
    public String toString() {
        return "Bird [name=" + name + ", age=" + age + ", color=" + color + "]";
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public Integer getAge() {
        return age;
    }

    public void setAge(Integer age) {
        this.age = age;
    }
}
```

### bean的自动装配方式

使用`@Autowired @Qualifier @Primary`和`@Resource`进行对象的自动装配

使用@Autowired的对象会不会注入到IOC容器中，只会给bean装配某个对象。

@Autowired是根据对象类型装配。如果IOC有多个类型相同的bean，可以使用@Primary注解标注优先使用该依赖装配到bean中，就不会产生`No qualifying bean of type 'com.enjoy.cap9.dao.TestDao' available: expected single matching bean but found 2: testDao,testDao2`的错误。

@Autowired和@Qualifier连用是根据IOC容器中的ID名称装配。

@Resource是根据IOC容器中的ID名称装配。

@Primary修饰的类或方法，在其他bean装配时，都会优先装配使用@Primary修饰的bean。如果@Autowired和@Qualifier连用则@Primary失效，即优先使用装配@Qualifier指定的IDbean。

#### 自动装配源码

*源码`org.springframework.beans.factory.support.DefaultListableBeanFactory#doResolveDependency`*

```java
protected Map<String, Object> findAutowireCandidates(
			@Nullable String beanName, Class<?> requiredType, DependencyDescriptor descriptor) {

		String[] candidateNames = BeanFactoryUtils.beanNamesForTypeIncludingAncestors(
				this, requiredType, true, descriptor.isEager());
		Map<String, Object> result = new LinkedHashMap<>(candidateNames.length);
		for (Class<?> autowiringType : this.resolvableDependencies.keySet()) {
			if (autowiringType.isAssignableFrom(requiredType)) {
				Object autowiringValue = this.resolvableDependencies.get(autowiringType);
				autowiringValue = AutowireUtils.resolveAutowiringValue(autowiringValue, requiredType);
				if (requiredType.isInstance(autowiringValue)) {
					result.put(ObjectUtils.identityToString(autowiringValue), autowiringValue);
					break;
				}
			}
		}
		for (String candidate : candidateNames) {
			if (!isSelfReference(beanName, candidate) && isAutowireCandidate(candidate, descriptor)) {
				addCandidateEntry(result, candidate, descriptor, requiredType);
			}
		}
		if (result.isEmpty() && !indicatesMultipleBeans(requiredType)) {
			// Consider fallback matches if the first pass failed to find anything...
			DependencyDescriptor fallbackDescriptor = descriptor.forFallbackMatch();
			for (String candidate : candidateNames) {
				if (!isSelfReference(beanName, candidate) && isAutowireCandidate(candidate, fallbackDescriptor)) {
					addCandidateEntry(result, candidate, descriptor, requiredType);
				}
			}
			if (result.isEmpty()) {
				// Consider self references as a final pass...
				// but in the case of a dependency collection, not the very same bean itself.
				for (String candidate : candidateNames) {
					if (isSelfReference(beanName, candidate) &&
							(!(descriptor instanceof MultiElementDescriptor) || !beanName.equals(candidate)) &&
							isAutowireCandidate(candidate, fallbackDescriptor)) {
						addCandidateEntry(result, candidate, descriptor, requiredType);
					}
				}
			}
		}
		return result;
	}
```

```java
@Autowired
private OrderService orderService1
```
```java
@Primary // 该注解避免了找不到idbean的错误
@Bean("orderService2")
```

当装配OrderService时，需要相同的OrderService类型的bean对象，但是他们的id值没有orderService1就会报错。这时可以在bean上使用@Primary注解，那么在装配OrderService类型时，不需要匹配到id为orderService1的bean，从而避免了报错。


#### @Resource和@Autowired的区别

* @Resource不支持Primary功能
* @Resource不支持@Autowired(required = false)的功能，使用Resource注解如果没有IOC容器中没有对应的ID则会报错。

#### @Inject自动装配bean

和`@Autowired`注解功能类似，而且还支持`@Primary`,但是不支持`@Autowired(required = false)`的功能

需要引入第三方jar包

```xml
<dependency>
    <groupId>javax.inject</groupId>
    <artifactId>javax.inject</artifactId>
    <version>1</version>
</dependency>
```











