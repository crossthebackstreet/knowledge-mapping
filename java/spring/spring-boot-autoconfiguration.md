##### Spring Boot自动配置原理(基于Spring Boot 2.1.5.RELEASE)
Spring Boot应用启动类一般会加上@SpringBootApplication注解，@SpringBootApplication有三个Spring注解，@SpringBootConfiguration @EnableAutoConfiguration @ComponentScan，通过名字可以看出@EnableAutoConfiguration开启自动配置功能。@EnableAutoConfiguration中又有其他的注解，
```java
...
@AutoConfigurationPackage
@Import(AutoConfigurationImportSelector.class)
public @interface EnableAutoConfiguration {
    ...
}
```
@Import导入AutoConfigurationImportSelector类并注册为bean。AutoConfigurationImportSelector中重写了selectImports方法，selectImports方法是AutoConfigurationImportSelector实现的接口ImportSelector中的方法，这个方法返回了应该被导入的类的名字。  

通过调用关系和注释可以看出getCandidateConfigurations()方法返回自动配置的类
```java
/**
  * Return the auto-configuration class names that should be considered. By default
  * this method will load candidates using {@link SpringFactoriesLoader} with
  * {@link #getSpringFactoriesLoaderFactoryClass()}.
  * @param metadata the source metadata
  * @param attributes the {@link #getAttributes(AnnotationMetadata) annotation
  * attributes}
  * @return a list of candidate configurations
*/
protected List<String> getCandidateConfigurations(AnnotationMetadata metadata,
			AnnotationAttributes attributes) {
    ...
}
```
getCandidateConfigurations()调用SpringFactoriesLoader.loadFactoryNames() SpringFactoriesLoader.loadSpringFactories()获取META-INF/spring.factories中定义的自动配置类。