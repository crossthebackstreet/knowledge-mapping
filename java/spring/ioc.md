IOC即Inversion of Control，控制反转，是一种思想，它参考了依赖倒置原则，高层模块不应该依赖于底层模块，而应该依赖于抽象。IOC通过DI(Dependency Injection，依赖注入)实现。依赖注入将原先上层建筑依赖下层建筑的方式转换为下层建筑注入到上层建筑中的方式，把底层类作为参数传递给上层类，实现上层对下层的控制。

以类A、B、C为例，A依赖于B，B依赖于C，
```java
public Class A {
    private final B b;
    public A() { b = new B(); }
}

public Class B {
    private final C c;
    public B() { this.c = new C(); }
}

public class C {
    public C() {}
}

A a = new A();
```
如果此时C需要通过构造函数初始化属性，那么依赖于C的类都将发生变化
```java
public Class A {
    private final B b;
    public A(Any any) { b = new B(any); }
}

public Class B {
    private final C c;
    public B(Any any) { this.c = new C(any); }
}

public class C {
    public C(any) { /*do something*/ }
}

A a = new A(any);
```
这样的程序是无法维护的，如果将被依赖的类作为参数注入，程序发生的变化
```java
public Class A {
    private final B b;
    public A(B b) { this.b = b; }
}

public class B {
    private final C c;
    public B(C c) { this.c = c; }
}

public class C {
    public C() {}
}

C c = new C();
B b = new B(c);
A a = new A(b);
```
如果此时C需要初始化属性，只需改动C即可
```java
public class C {
    public C(Any any) { this.any = any; }
}

C c = new C(any);
B b = new B(c);
A a = new A(b);
```
这样，其他依赖C的类都没有发生变化，程序也变得易维护。

依赖注入可以通过Setter、Interface、Constructor、Annotation等方式实现注入。
IOC容器的优点有1. 避免使用new创建类，可以实现对类的统一维护；2. 创建实例的时候不需要了解其中的细节。

Spring IOC支持的功能:
1. 依赖注入
2. 依赖检查
3. 自动装配
4. 支持集合
5. 指定初始化方法和销毁方法
6. 支持回调方法

Spring IOC容器的核心接口
- BeanFactory
  BeanFactory的作用
  - 提供IOC的配置机制
  - 包含Bean的定义，便于实例化Bean
  - 建立Bean之间的依赖关系
  - 管理Bean生命周期
- ApplicationContext
