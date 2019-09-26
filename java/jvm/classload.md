#### Java类加载机制

虚拟机把描述类的数据从Class文件加载到内存，并对数据进行连接、初始化，最终形成可以被虚拟机直接使用的Java类型，这个过程称为类加载机制。

类在内存中的的生命周期包括加载(Loading)、连接(Linking)、初始化(Initialization)、使用(Using)、卸载(Unloading)几个阶段，其中连接阶段包括验证(Verification)、准备(Preparation)、解析(Resolution)三个部分。

类的加载过程包括加载、验证、准备、解析、初始化五个阶段，其中加载、验证、准备、初始化四个阶段是相对有序的，这些阶段通常是相互交叉进行的。解析阶段可能发生在初始化之前也可能发生在初始化之后，这是为了支持Java运行时绑定，也可以称为动态绑定或者晚期绑定。

> 动态绑定、静态绑定\
> 绑定是把方法与方法所在的类进行关联。静态绑定，也称为前期绑定，在程序编译期完成绑定，Java中的方法只有final、static、private修饰的方法和构造方法是前期绑定的。动态绑定，也称为后期绑定，运行时根据具体的实例对象进行绑定。

1. 加载\
  加载阶段的主要工作是将字节码加载到内存中。加载阶段虚拟机要完成三件事情。
  - 通过一个类的全限定名获取这个类的字节流
  - 将字节流所代表的静态存储结构转化为方法区的运行时数据结构
  - 在内存中生成一个代表这个二进制字节流的java.lang.Class对象，通过这个Class对象来访问方法区中这个类中的数据

2. 验证\
  验证阶段的目的是为了保证class文件中字节流包含的信息符合虚拟机的要求，不会危害虚拟机的安全。验证阶段包括四部分：
 - 文件格式验证
 - 元数据验证
 - 字节码验证
 - 符号引用验证

3. 准备\
  准备阶段的主要工作是在方法区中为类变量分配内存，并为类变量设置默认的初始化值。

  准备阶段只为类变量分配内存，不包括被final修饰类变量，实例变量会在对象实例化时随着对象一起在堆中分配内存。类变量的初始化值为默认零值，为类变量赋值的putstatic指令发生在初始化阶段，类构造器`<clinit>`方法执行时会对类变量进行初始化。

4. 解析

5. 初始化

  

类初始化触发的条件：虚拟机规范规定了**有且只有**五种情况必须立即对类进行初始化
1. 遇到`new`、`getstatic`、`putstatic`、`invokestatic`四条字节码指令，分别对应使用new关键字实例化对象、获取或设置类静态字段（不包括被final字段修饰的静态字段）、调用类静态方法。
2. 使用`java.lang.reflect`包中的方法对类进行反射调用。
3. 初始化一个类时，如果它的父类还没有初始化，则先初始化父类。
4. 主类会先被初始化。
5. 使用JDK 1.7的动态语言支持时，如果一个`java.lang.invoke.MethodHandle`实例最后的解析结果REF_getstatic、REF_putstatic、REF_invokestatic的方法句柄，并且这个方法句柄所对应的类还没有初始化过，会先触发初始化。

这五种方式称作对类的主动引用，除此之外称为被动引用。

接口初始化过程与类初始化过程一致，有区别的地方在于，接口在初始化时并不要求它的父接口要先初始化，只有在真正使用父接口的时候才会初始化。


#### 类加载器

#### 双亲委派模型
![双亲委派模型]\
类加载之间的这种层级关系称为类加载器的双亲委派模型。

双亲委派模型的工作过程：如果一个类加载器接收到类加载请求时，自己不会先加载这个类，而是将这个加载请求委派给父类加载器，直到最终将加载请求委派给启动类加载器，如果父类加载器无法加载类（它的搜索范围中没有找到所需的类），子加载器会尝试加载。

双亲委派模型的优点：使Java类随着它的类加载器一起具备一种带有优先级的层级关系，这样可以保证Java程序的稳定运作。

双亲委派模型实现：检查类是否已经被加载，如果没有，则调用父加载器的loadClass()方法，如果父加载器为空，则默认使用启动类加载器作为父加载器。如果父类加载失败，抛出`ClassNotFoundException`异常，再调用自己的findClass()加载。
源码（基于JDK 11）
```java
protected Class<?> loadClass(String name, boolean resolve) throws ClassNotFoundException {
    synchronized (getClassLoadingLock(name)) {
        // First, check if the class has already been loaded
        Class<?> c = findLoadedClass(name);
        if (c == null) {
            long t0 = System.nanoTime();
            try {
                if (parent != null) {
                    c = parent.loadClass(name, false);
                } else {
                    c = findBootstrapClassOrNull(name);
                }
            } catch (ClassNotFoundException e) {
                // ClassNotFoundException thrown if class not found
                // from the non-null parent class loader
            }

            if (c == null) {
                // If still not found, then invoke findClass in order
                // to find the class.
                long t1 = System.nanoTime();
                c = findClass(name);

                // this is the defining class loader; record the stats
                PerfCounter.getParentDelegationTime().addTime(t1 - t0);
                PerfCounter.getFindClassTime().addElapsedTimeFrom(t1);
                PerfCounter.getFindClasses().increment();
            }
        }
        if (resolve) {
            resolveClass(c);
        }
        return c;
    }
}
```


[双亲委派模型]:<../../_assets/parents-delegation-model.png>