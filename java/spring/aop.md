##### Spring AOP与AspectJ的区别
* AspectJ依赖ajc编译器，Spring AOP不依赖
* Spring AOP通过动态代理技术实现(动态织入)，AspectJ通过静态代理实现(静态织入)

##### Spring AOP中常用注解
* Aspect
* Before
* After
* AfterReturning
* AfterThrowing
* Around
* PointCut

执行顺序  
Around(start) -> Before -> proceed()-> Around(end) -> After -> AfterReturning(AfterThrowing)

##### Spring AOP切入点指示符
* ***** 匹配任意访问修饰符、返回值类型、类完全限定名  
execution(\* \*(..)) 匹配任意访问修饰符、返回值类型、类完全限定名，任意数量参数的方法  
execution(public \*(..)) 匹配public，任意返回值类型、类完全限定名、任意数量的参数 
within(me.lee.service..\*) 匹配me.lee.service包及其子包下所有类中的所有方法  
execution(* set*(int)) 匹配任意访问修饰符、返回值类型，以set开头，参数为int的方法
* **+** 匹配给定类的任意子类  
within(me.lee.service.BaseService+) 匹配实现了BaseService接口的所有类的所有方法
* **..** 匹配任意数量的参数、类所在包下所有类  
execution(me.lee.service..*)

within execution的区别
* within在类型签名表达式中使用  
作用于类型(类、接口、枚举) within(< type name>)
* execution在方法签名表达式中使用  
作用于方法 execution(< scope> < return-type> < fully-qualified-class-name>.*(parameters))

其他指示符
* bean 匹配Spring中特定名称的bean  
bean(*Service) 匹配以Service结尾的bean
* target 用于匹配当前目标对象类型的执行方法  
target(me.lee.service.UserService) 匹配任意实现了UserService接口的目标对象的方法
* this 匹配当前AOP代理对象类型的执行方法  
this(me.lee.service.UserService) 匹配任意实现了UserService接口的代理对象的方法
* @within 匹配所有指定注解类型内的类  
@within(me.lee.annotation.Limit)匹配使用了Limit注解的**类**
* @annotation 匹配所有指定注解类型内的方法  
@annotation(me.lee.annotation.Limit)匹配使用了Limit注解的**方法**

##### Spring AOP实现原理
Spring AOP通过两种动态代理实现，JDK动态代理和CGLIB动态代理。JDK动态代理通过`Proxy`和`InvocationHandler`实现，
```java
Proxy.newProxyInstance(ClassLoader loader,
                       Class<?>[] interfaces,
                       InvocationHandler h);
```
要实现JDK动态代理，要求被代理的类必须实现接口。如果被代理的类没有实现接口，那么Spring AOP会使用CGLIB实现动态代理。CGLIB可以在运行时动态的生成类的子类，通过继承方式实现代理，因此，如果一个类被final修饰，那么这个类无法通过CGLIB动态代理。