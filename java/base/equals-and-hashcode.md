#### == 与 equals()的区别
== 比较两个对象的地址，equals()是Object类中的方法，用于判定两个对象是否相等，如果一个类的对象实例需要比较，那这个类应该重写equals()，默认equals()比较的是两个对象的地址，Object类中equals()的实现：
```java
public boolean equals(Object obj) { return this == obj; }
```

#### equals()重写原则
* 自反性：对于任何非null的引用x，x.equals(x)应该返回true
* 对称性：对于任何非null的引用x和y，当且仅当x.equals(y)返回true时，y.equals(x)才能返回true
* 传递性：对于任何非null的引用x，y，z，如果y.equals(x)返回true，y.equals(z)返回true，那么x.equals(z)也应返回true
* 一致性：对于任何非null的引用x，y，如果equals()中比较的属性没有发生改变，那么多次调用x.equals(y)应该始终返回true或false
* 对于任何非null引用x，x.equals(null)应返回false

#### hashcode()
hashcode()是Object类中定义的用来返回对象int类型的hash码的方法，用来确定对象在hash表(如HashMap,HashSet, Hashtable等)中的位置。

#### equals()与hashcode()
equals()文档中提到，如果重写了equals()，有必要重写hashcode()，这样做是为了保持hashcode()的general contract(通用契约)。equals()和hashcode()都是Object类中的方法，都可以被重写，所以两个方法本身并没有很大关系。但是如果要用到hashcode()，即与hash表关联，那么如果equals()返回true，那么两个对象的hashcode一定相等，反之则不一定。在hash表中，对元素的操作都需要通过key的hashcode来定位元素，如果两个对象相等，即equals()返回true，而hashcode不相等，那么hash表中就会存在两个key相等的元素。


*<i> equals()文档原文</i>
> Indicates whether some other object is "equal to" this one.   
> The equals method implements an equivalence relation on non-null object references:
> * It is <b>reflexive</b>: for any non-null reference value x, x.equals(x) should return true.
> * It is <b>symmetric</b>: for any non-null reference values x and y, x.equals(y)
should return true if and only if y.equals(x) returns true.
> * It is <b>transitive</b>: for any non-null reference values x, y, and z, if x.equals(y) returns true and y.equals(z) returns true, then x.equals(z) should return true.
> * It is <b>consistent</b>: for any non-null reference values
x and y, multiple invocations of x.equals(y) consistently return true or consistently return false, provided no information used in equals comparisons on the objects is modified.
> * For any non-null reference value x, x.equals(null) should return false.
> 
> The equals method for class Object implements the most discriminating possible equivalence relation on objects; that is, for any non-null reference values x and y, this method returns true if and only if x and y refer to the same object x == y has the value true.
>
> <b>Note that it is generally necessary to override the hashCode method whenever this method is overridden, so as to maintain the general contract for the hashCode method, which states that <u>equal objects must have equal hash codes.</u></b>

*<i>hashcode()通用契约</i>
> The general contract of hashCode is:
> * Whenever it is invoked on the same object more than once during an execution of a Java application, the hashCode method must consistently return the same integer, provided no information used in equals comparisons on the object is modified. This integer need not remain consistent from one execution of an application to another execution of the same application. 
> 每当Java应用程序执行过程中在同一对象上多次调用时，hashcode方法必须始终返回相同的整数，如果没有修改对象中的相等信息。从应用程序的一次执行到同一应用程序的另一次执行，这个整数不需要保持一致。
<br>
> * If two objects are equal according to the equals(Object) method, then calling the hashCode method on each of the two objects must produce the same integer result.
> 如果两个对象根据equals(object)方法相等，则对两个对象中的每个对象调用hashcode方法都必须产生相同的整数结果。
<br>
> * It is <em>not</em> required that if two objects are unequal according to the java.lang.Object#equals(java.lang.Object) method, then calling the hashCode method on each of the two objects must produce distinct integer results.  However, the programmer should be aware that producing distinct integer results for unequal objects may improve the performance of hash tables.
> 不需要根据java.lang.Object#equals(java.lang.Object）方法，如果两个对象不相等，那么调用两个对象中的每一个对象的hashcode方法必须产生不同的整数结果。但是，程序员应该知道，为不同的对象生成不同的整数结果可能会提高哈希表的性能。