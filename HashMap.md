### 数组

- 特点：顺序存储
- 优势：根据索引访问，速度快，查询时间复杂度为O(1)
- 劣势：更改，扩容时间复杂度为O(n)

### 链表

- 特点：链式存储
- 优势：插入，修改简单
- 劣势：查询慢，需要从头结点遍历

### 散列表

散列表结合了数组和链表，主体结构是一个数组，但是数组中的元素是链表结构，原始数据经过Hash算法映射为哈希值

### Node类

hash,key,value,next（碰撞再散列），数组中的节点

### map.put(K,V)

1.获取K的hash值

2.经过hash值扰动函数，获取Node.hash

3.构造出Node对象
4.经过路由寻址算法: (table.length - 1) & node.hash， 算出node应存放在的位置(index)

### hash碰撞

寻址算法得到的index中已经有数据，则后来的new_node接在原来的node节点pre_node后面，即pre_node.next = new_node，链表长度达到8时，升级为红黑树

### 几个重要常量

- `static final int DEFAULT_INITIAL_CAPACITY = 1 << 4; // aka 16`  缺省table大小

- `static final int MAXIMUM_CAPACITY = 1 << 30;` table最大长度

- `static final float DEFAULT_LOAD_FACTOR = 0.75f;` 缺省负载因子大小

- `static final int TREEIFY_THRESHOLD = 8;` 树化阈值(链表长度到达该阈值即转化为红黑树)

- `static final int UNTREEIFY_THRESHOLD = 6;` 树降级成为链表的阈值

- `static final int MIN_TREEIFY_CAPACITY = 64;` 树化的最小Node数阈值

  即链表树化需要满足两个条件

  1.链表长度达到TREEIFY_THRESHOLD

  2.整个table中Node个数达到MIN_TREEIFY_CAPACITY

### table

`transient Node<K,V>[] table;` 哈希表

### Set



### Size

当前哈希表中元素个数

### modeCount

当前哈希表中结构修改次数

### 几个重要变量

- `int theshold` 扩容阈值，当哈希表中元素个数超过阈值时，扩容  threshold = capacity * loadFactor
- `loadFactor` 负载因子 

### 构造方法

```java
    public HashMap(int initialCapacity, float loadFactor) {
        if (initialCapacity < 0)
            throw new IllegalArgumentException("Illegal initial capacity: " +
                                               initialCapacity);
        if (initialCapacity > MAXIMUM_CAPACITY)
            initialCapacity = MAXIMUM_CAPACITY;
        if (loadFactor <= 0 || Float.isNaN(loadFactor))
            throw new IllegalArgumentException("Illegal load factor: " +
                                               loadFactor);
        this.loadFactor = loadFactor;
        this.threshold = tableSizeFor(initialCapacity);
    }
```

三个if：限定capacity必须在[0,MAX_CAPACITY]内，loadFactor必须大于0

tableSizeFor：

```java
    static final int tableSizeFor(int cap) {
        int n = cap - 1;
        n |= n >>> 1;
        n |= n >>> 2;
        n |= n >>> 4;
        n |= n >>> 8;
        n |= n >>> 16;
        return (n < 0) ? 1 : (n >= MAXIMUM_CAPACITY) ? MAXIMUM_CAPACITY : n + 1;
    }
```

返回一个>=0的数，且这个数必须是2的次方数，从而符合表的要求

n >>> 1 : n的二进制数右移一位

n |= n >>> 1 即 n = n | (n >>> 1)，二进制与操作

举个例子，假设cap为10，则 n = 9 = 0b1001，0b开头表示这是一个二进制数，

n >>> 1 = 0b1001右移一位 = 0b0100

n |= n >>> 1 即为 n = 0b1001 | 0b0100 = 0b1101，按位与

n |= n >>> 2 即为 n = 0b1101 | 0b0011 = 0b1111

依次运行完后，n = 15，根据return条件返回n + 1，即返回16

- 为什么cap要-1

  避免原来就是2的次方的数经过计算后增大一次幂

  举例： cap = 8 = 0b1000

  n |= n >>> 1 ==> n =  0b1000 | 0b0100 = 0b1100

  ······

  最终算出n = 0b1111

  return n + 1 = 0b1111 + 0b0001 = 0b10000 = 16；

  



构造方法实际只有两句：

```
        this.loadFactor = loadFactor;
        this.threshold = tableSizeFor(initialCapacity);
```

给定了负载因子和扩容阈值

### put 

- hash方法（扰动函数）

  ```
     static final int hash(Object key) {
          int h;
          return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
      }
  ```

  如果key为null，则hash值为0，这个很容易理解，0与任何值进行hash运算都为0，所以index为0

  如果key不为null，返回h = key.hashCode()) ^ (h >>> 16)

  h = (key.hashCode()) ^ (h >>> 16)是个啥？ 这里的运算是key.hashCode() 与它自身右移16位后的数进行异或运算

  首先h是个int数值，int是32位的，再看看路由寻址算法：index = (table.length - 1) & hash，当table.length不大时，位数是很少的，它的高16位基本上都是0，进行&操作后得到的结果也是0，相当于这里的hashCode的高16位数据就损失掉了，所以这个通过一次异或运算让高16位也能够参与进运算

- putVal

  - tab：引用当前hashMap的散列表
  - p: 表示当前散列表的长度
  - n：表示散列表数组的长度
  - i：表示路由寻址结果

  

