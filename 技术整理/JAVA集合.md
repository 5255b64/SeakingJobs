#### Java集合框架

##### Collection

- [ ] 

##### List

- [ ] List的元素是有序、可重复的，Set的元素是无序、不重复的，Map使用键值对（key-value）进行存储，key是无序不重复的，value是无序可重复的；
- [ ] 集合的线程安全性：
- [ ] 定制化排序：
  - [ ] 1）Collections.sort(Collection, Comparator) 使用自定义比较器Comparator来定制化排序；
  - [ ] 2）实现Comparable接口，重写compareTo(Object ,Object)方法；
- [ ] 无序性与不可重复性
  - [ ] 无序性：无序性不等于随机性 ，无序性是指存储的数据在底层数组中并非按照数组索引的顺序添加 ，而是根据数据的哈希值决定的；
  - [ ] 不可重复性：不可重复性是指添加的元素按照 equals()判断时 ，返回 false，需要同时重写 equals()方法和 HashCode()方法；
- [ ] Arrays.asList()方法

##### Map接口

- [ ] Map类使用了内部类Map.Entry对象来存储键值对；

AbstractMap抽象类

- [ ] containKey、get、remove方法实现：使用迭代器entrySet().iterator()，对Map的所有key进行遍历；AbstractMap对null key做了特殊处理。

###### HashMap与HashTable：

- [ ] Capacity：容量，bucket的数量，默认为16，即拉链法中的数组的长度；

- [ ] Load Factor：负载因子，默认为0.75，当map中的元素数量大于（Capacity*Load Factor）时，bucket的数量将调整为原先的2倍；

- [ ] threshold：临界值，即（Capacity*Load Factor）的值。

- [ ] 负载因子为什么默认是0.75：

  - [ ] 0.75介于0.5和1之间，是比较好的折中；
  - [ ] 如果负载因子较小，空间损耗会较高，比如0.5，那么每当bucket table被使用到一半时就会扩容，那么至少有一半的空间是未被占用的；
  - [ ] 如果负载因子较高，时间损耗会较高，get、put所需的时间开销会较大；比如负载因子为1时，当size与capacity相同时才会进行扩容，那么必然会存在较多的hash冲突情况。

- [ ] 扰动方法 static final int hash(Object key)

  - [ ] 为了防止一些实现比较差的hashCode()方法，HashMap使用扰动方法来减少hash碰撞；

  - [ ] jdk 1.8：

    ```java
        static final int hash(Object key) {
          int h;
          // key.hashCode()：返回散列值也就是hashcode
          // ^ ：按位异或
          // >>>:无符号右移，忽略符号位，空位都以0补齐
          return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
      }
    ```

  - [ ] jdk 1.7

    ```java
    static int hash(int h) {
        // This function ensures that hashCodes that differ only by
        // constant multiples at each bit position have a bounded
        // number of collisions (approximately 8 at default load factor).
    
        h ^= (h >>> 20) ^ (h >>> 12);
        return h ^ (h >>> 7) ^ (h >>> 4);
    }
    ```

- [ ] 都是基于Map实现的，但是HashMap继承自抽象类AbstractMap，而HashTable继承自抽象类Dictionary（该区别意义不大，应为Dictionaty已经被废弃了）；

- [ ] null key 和 null value 的支持：HashMap支持null为key值，以及null为value，而HashTable在遇到null时，会抛出NullPointerException异常，这是因为HashMap对null进行了特殊处理，将null的hash设置为0；

- [ ] 线程安全性：HashMap是非线程安全的；HashTable是线程安全的，因为HashTable内部的方法都经过synchronized修饰；HashTable只用了一把锁，效率低下；

- [ ] 效率：由于线程安全的问题，HashMap的效率要比HashTable高；

- [ ] 注意，HashTable基本已经被淘汰，如果要是用线程安全的Map，请使用ConcurrentHashMap；

- [ ] 扩容：HashTable默认初始值为11，扩充时容量变为2n+1，若给定了容量初始值，则容量变为给定初始值；HashMap默认的初始值为16，每次扩容将容量扩充为原来的2倍，若给定初始值，则将初始值扩充为2的幂次方大小，这是为bootstraping机制做保证；

- [ ] HashMap为什么容量为2的幂次，并且在扩充时选择乘2：

  - [ ] 使用位运算可以提高性能；
  - [ ] 判断元素在table中的位置，使用 **(n - 1) & hash** ，此处的n为2的幂次，等同于**hash%n**，不过效率更高，约能提升10倍；

- [ ] HashMap拉链法：

  - [ ] 当链表长度大于8时，先判断bucketTable的size是否大于64，若小于64则先对bucketTable进行数组扩容；
  - [ ] 当链表长度大于8，并且bucketTable的size是大于64时，将链表转换为红黑树（TreeNode）；

- [ ] 底层数据结构：jdk1.8以后，HashMap在解决冲突时，先将拉链法的链表的长度阈值设置为8，当超过该阈值时进行扩容；

###### TreeMap

- [ ] 不是线程安全的
- [ ] TreeMap并实现了NavigableMap接口，而NavigableMap接口实现了SortedMap接口；它的keyset是有序的，使用迭代器进行遍历时，可以根据key的排序顺序进行输出。
- [ ] 使用TreeMap时，key值必须是实现了Comparable接口，或者对TreeMap中的compareTo方法进行重写。
- [ ] TreeMap调用的是compare或者compareTo方法，而HashMap中的红黑树使用了hashCode()方法；
- [ ] 底层使用红黑树实现静态内部类TreeMap.Entry，通过根节点TreeMap.Entry root来访问所有的entry对象；
- [ ] get方法的实现：调用getEntry内部方法，通过对红黑树TreeMap.Entry root的访问，实现在log(n)时间内get到目标entry。
- [ ] containsKey方法实现：通过get方法，判断是否能够get到目标entry，复杂度O(log(n));
- [ ] containsValue方法实现：遍历红黑树，查询是否有目标Value，复杂度O(n)

###### ConcurrentHashMap

 - [ ] ConcurrentHashMap是一种线程安全的Map类型，底层使用了拉链法；
 - [ ] 保证线程安全的方式：
   - [ ] jdk1.7:对bucketTable使用了分段加锁的方法，保证并发的线程安全性；
   - [ ] jdk1.8:摒弃了 Segment 的概念，而是直接用 Node 数组+链表+红黑树的数据结构来实现，并发控制使用 synchronized 和 CAS 来操作；
   - [ ] synchronized 和 CAS的方式，只锁定当前链表或者红黑树的首节点，相当于对bucketTable数组中的每一个对象都加锁；
 - [ ] 与HashTable的对比：HashTable只使用了一把锁，效率非常低下；

##### Set

- [ ] HashSet：
  - [ ] Set 接口的主要实现类 ，HashSet 的底层是 HashMap，线程不安全的，可以存储 null 值；
  - [ ] 直接通过HashMap实现，通过内部的HashMap成员HashMap<E,Object> map 来访问HashMap的方法；
  - [ ] HashSet实现Map时，只有key，没有value，value是HashSet类的静态私有成员private static final Object PRESENT = new Object();
- [ ] LinkedHashSet：HashSet 的子类，能够按照添加的顺序遍历；
- [ ] TreeSet ：底层使用红黑树，能够按照添加元素的顺序进行遍历，排序的方式有自然排序和定制排序。

##### Iterator

- [ ] forEach循环：增强型for循环

```java
List<String> list = new ArrayList<String>();
for(String e : list){
	// TODO
}
```

本质是使用了迭代器Iterator对Collection中的对象进行遍历。

- [ ] fail-fast机制（快速失败）：一种集合的错误检测机制，当使用迭代器遍历集合时，若集合被修改，则会触发fail-fast机制，抛出ConcurrentModificationException异常；另外，在单线程下，如果在遍历过程中对集合对象的内容进行了修改的话也会触发 fail-fast 机制。
- [ ] fail-safe机制（安全失败）：采用安全失败机制的集合容器，在遍历时不是直接在集合内容上访问的，而是先复制原有集合内容，在拷贝的集合上进行遍历。所以，在遍历过程中对原集合所作的修改并不能被迭代器检测到，故不会抛 ConcurrentModificationException 异常。

#### 集合的线程安全性

##### Vector & CopyOnWriteArrayList

- [ ] Vector
  - [ ] 底层使用了synchronized 关键字 效率较低
- [ ] CopyOnWriteArrayList
  - [ ] 底层使用了 Lock 类（接口） 效率较高