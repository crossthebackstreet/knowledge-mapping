#### HashMap
![HashMap]

##### `put(K key, V value)`实现过程
```java
public V put(K key, V value) {
    return putVal(hash(key), key, value, false, true);
}
```
put()调用putVal(), putVal(hash, key, value, onlyIfAbsent, evict)有五个参数， onlyIfAbsent是个boolean值，如果值为false，则不会改变已存在的值，默认值为false，evict也是boolean值，如果值为true，哈希表将进入创建模式(creation mode)，evict只在调用afterNodeInsertion(evict)用到了，afterNodeInsertion(evict)是一个空实现，在LinkedHashMap中会用到这个方法，默认值是true。

putVal()实现过程大致如下：
1. 判断数组是否初始化，如果没有初始化，则先初始化数组，在源码中即调用resize()
```
if table == null || table.length == 0
  resize()
```
2. 根据计算出的索引判断桶(bucket)中是否有值，如果没有，则直接放入桶中
```
index = hash(key) & (table.length - 1)
if table[index] == null
    table[index] = newNode(key, value)
```
3. 如果桶中有值，即发生冲突，则先判断桶中的key与put的key是否相等，如果相等，则赋值给其他变量，等待之后替换
4. 如果节点是树节点，将节点添加到红黑树中
5. 如果节点是链表节点，将节点添加到链表中
6. 如果链表的长度大于等于树化阈值，则将链表转换成红黑树
7. 如果节点已经存在，替换旧值
8. 如果键值对数量大于阈值(capacity * load factor)，进行扩容

##### `get(K key)`实现过程
1. 如果数组为空或者对应索引处桶中元素为空，则返回null
2. 如果桶中节点命中，返回桶中节点
3. 如果节点是树节点，则在树中查找
4. 如果节点是链表节点，则在链表查找

##### hash(Object key)
hash()用来返回int类型key的hash值，在hash()中并不是直接返回key的hashcode，而是对Key的hashcode做了处理，对于非空key，返回key的hashcode与hashcode无符号右移16位的值做异或操作，hash()实现
```java
static final int hash(Object key) {
   int h;
   return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
}
```
这样做是为了使元素分布更加均匀，减少碰撞几率，提高HashMap性能，hash()也称为*扰动函数*

在选择key时应当选择String, Integer等类型的值，这些类型的值确定后就不会再发生改变，hashCode()返回的值也是一样的。String hashCode()中是通过char数组中的每个元素计算得出的，char数组是不可变的，保证同一个String返回相同的hashcode。

##### resize()实现
resize()用于初始化数组或者将数组扩容为两倍，并设置参数，将旧数据迁移到新数组。
数据迁移时如果key的hash值参与运算的最高位是0，那么元素扩容后的位置=原位置，
如果key的hash值参与运算的最高位是1，那么元素扩容后的位置=原位置+扩容前数组的大小

##### 数组的大小为什么是2的整数次幂
1. 在计算key索引时，可以通过table[hash(key) & (table.length - 1)]按位与操作替换table[hash(key) % table.length]取模操作，提高效率
2. 如resize()扩容所讲，数组的大小是2的整数次幂，可以很巧妙地进行数据迁移，提高效率。由于hash值新增位可能是0也可能是1，可以使分布更加均匀。

#### LinkedHashMap
![LinkedHashMap]

#### Hashtable
![Hashtable]
##### Hashtable与HashMap的区别
1. Hashtable继承Dictionary，HashMap继承AbstractMap
2. HashMap允许null键和null值，Hashtable不允许null键和null值
3. HashMap非线程安全，Hashtable线程安全
4. 初始容量不同，HashMap初始容量是16，Hashtable初始容量是11，因此计算索引的方式也不一样，HashMap通过key的hash值按位与数组长度-1，Hashtable通过hash值按位与Integer最大值取余数组长度
5. 数据结构不一样，HashMap是数组+链表+红黑树，Hashtable是数组+链表
6. 扩容不一样，HashMap扩容为原来2倍，Hashtable扩容为2n+1
7. hash计算方法不一样，HashMap通过hash方法返回key的hash值，Hashtable直接使用key的hashCode()返回hash值

#### ConcurrentHashMap
![ConcurrentHashMap]  
在JDK1.8以前，ConcurrentHashMap使通过segment分段锁实现的，在JDK1.8开始通过CAS + synchronized实现

#### SynchronizedMap
SynchronizedMap是Collections类中的私有静态类，实现Map接口，通过`Map<K, V> Collections.synchronizedMap(Map<K, V> map)`获取，SynchronizedMap中的所有方法都使用synchronized代码块对操作加锁。

[HashMap]: <../../_assets/HashMap.png>
[LinkedHashMap]: <../../_assets/LinkedHashMap.png>
[Hashtable]: <../../_assets/Hashtable.png>
[ConcurrentHashMap]: <../../_assets/ConcurrentHashMap.png>