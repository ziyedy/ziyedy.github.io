---
title: Java08：集合与容器
date: 2020-07-10 14:34:11
tags:
	- Java
categories:
	- Java
	- JavaSE
fileName: java-collection
---

# 集合框架体系



Set是无序的，相比Collection没有增加任何方法，List相比Collection增加和索引相关的方法



## 迭代器

### 迭代器的使用

Iterator接口可以以统一的方式对各种集合元素进行遍历，调用各种集合元素的`iterator()`方法得到迭代器。

```java
Iterator it = set.iterator();
while (it.hasNext()) {	// 检测集合中是否还有下一个元素
	System.out.println(it.next());	// 返回集合中的下一个元素
}
```

### Iterator与Iterable

* Iterator为Java中的迭代器对象，实现了对List这样的集合进行迭代遍历的底层依赖（使用迭代器设计模式实现）

* Iterable接口里定义了返回iterator的方法，相当于**对Iterator的封装**，如下所示。所以实现`Iterable`接口的集合类都可以使用迭代器遍历

```java
public interface Iterable<T> {
    Iterator<T> iterator();	// 获取Iterator的方法
}
```

### for-each与Iterator

* 联系：for-each循环的底层使用的也是迭代器`Iterator`，因此凡是能够使用for-each进行循环的也一定能够使用迭代器进行遍历

* 区别：但for-each能够遍历数组，而`Iterator`不行；同时使用for-each遍历集合时不能够删除元素



# List

> List是元素有序并且可以重复的集合，称为序列，其中**元素有序、不唯一**

List集合的主要实现类有 `ArrayList` 和 `LinkedList` ，分别是数据结构中顺序表和链表的实现。

另外还包括栈和队列的实现类或接口：`Stack`、`Deque`和`Queue`。

针对Collection增加了一些关于索引位置操作的方法`get(i) add(i,elem),remove(i),set(i,elem)`

## 主要使用方法

### 主要方法

```
ArrayList list = new ArrayList();
list.add(1);	// 在最后添加1

list.size();	// 获取元素数量
list.get(1);	// 取出索引值为1的元素
list.remove(2);	// 移除索引为2的元素
```

### 遍历

```java
// 方法1：for循环
for (int i = 0; i < list.size() ; i++) {
    int elem = list.get(i);
}

// 方法2：for-each循环
for(int elem:list){
    
}

// 方法3：Iterator
Iterator<Integer> it = list.iterator();
while(it.hasNext()) {	// 判断是否还有元素
    int elem = it.next();	// 取出元素
}

// 方法4：Lambda表达式+流式编程（JDK1.8）
list.forEach((elem)->System.out.println(elem2));
list.forEach(System.out::println);
```



## ArrayList

> ArrayList底层由数组（`Object[]`）实现，在内存中分配连续的空间，实现了长度可变的数组

* 优点：遍历元素和随机访问元素的效率比较高

* 缺点：添加和删除需大量移动元素，效率低



## LinkedList

> 采用**双向链表**方式存储

相对于`ArrayList`，添加了添加、删除、获取首尾元素的方法`addFirst()、addLast()、removeFirst()、removeLast()、getFirst()、getLast()`

* 优点：插入、删除元素效率比较高

* 缺点：遍历和随机访问元素效率低下



## 栈与队列

### 队列

队列使用`Queue`接口实现，`Queue`直接继承`Collection`接口

### 栈

栈可以使用`Stack`也可以使用`Deque`实现，其中`Deque`是双向队列（即可以作为栈使用）

`Stack`继承了`Vector`

```
class Stack<E> extends Vector<E> { }
```

`Deque`继承了`Queue`，同时有`ArrayDeque `和`LinkedList`两个实现类

```java
// 创建
Stack<Integer> stack = new Stack<>();	// 栈实现类
Deque<Integer> deque = new LinkedList<>();	// 双向队列

// 入栈
deque.push(1);
// 获取栈顶元素
int top = deque.peek();
// 出栈并返回栈顶元素
int top = deque.pop();
```



# Set

> 集合：元素无序，且不可以重复

set针对Collection没有增加任何方法

## HashSet

> HashSet是Set的一个重要元素，称为哈希集，其中元素无序且不可以重复，只允许一个null元素。底层采用**哈希表**存储集合元素

* 优点：添加速度快，查询速度快，删除速度快

* 缺点：无序

针对无序的缺点，有`LinkedHashSet`作为HashSet的子类，该结构采用哈希表存储结构，同时使用链表维护次序（此处的次序指元素添加的次序）

### 重复元素的检查

1、首先计算`hashcode`来判断对象加入的位置，并与其他加入的对象进行比较，如果没有重复的`hashcode`，则假设对象没有重复出现

2、若有重复的`hashcode`，则调用`equals()`方法进行重复检查

因此：

要使用自定义类作为元素存储在`HashSet`与`LinkedHashSet`中时，**自定义类必须实现`hashCode()`和`equals()`**，否则会存储“相同的元素”。

### 底层实现

**HashSet的底层使用的是HashMap**，所以底层结构也是哈希表 ，如下所示

HashSet的元素到HashMap中做key，value统一是同一个Object()

```java
public class HashSet<E> implements Set<E> {
    private transient HashMap<E, Object> map;
    private static final Object PRESENT = new Object();
    public HashSet() {
        map = new HashMap<>();
    }
    public boolean add(E e) {
		return map.put(e, new Object()) == null;
        return map.put(e, PRESENT) == null;
    }
    public int size() {
        return map.size();
    }
    public Iterator<E> iterator() {
        return map.keySet().iterator();
    }
}
```





## TreeSet

> 采用二叉树（红黑树）的存储结构

* 优点：有序（此处指的是自然顺序，也可以自己定义），查询速度比List快（按照内容查询）

* 缺点：查询速度没有HashSet快

**使用自定义类作为元素存储在`TreeSet`中时，自定义类需要实现`Comparable`接口并指定比较的规则**，否则会抛出异常。

### 底层实现

> **TreeSet的底层使用的是TreeMap**，所以底层结构也是红黑树 ，如下所示
>
> TreeSet的元素e是作为TreeMap的key存在的，value统一为同一个 Object()

```java
public class TreeSet<E> extends AbstractSet<E> implements NavigableSet<E> {
    private transient NavigableMap<E, Object> m; 
    private static final Object PRESENT = new Object();
    public TreeSet() {
        this(new TreeMap<E, Object>()); 
    }
	TreeSet(NavigableMap<E, Object> m) {
        this.m = m;
    }
    public boolean add(E e) {
        return m.put(e, PRESENT) == null;
    }
	public int size() {
        return m.size();
    }
}

```





# Map

> 存储的键值对映射关系，根据key可以找到value

## HashMap

> 采用哈希表存储的键值对

* 优点：添加速度快、查询速度快、删除速度快
* 缺点：key值无序

与`HashSet`对应，其也有`LinkedHashMap`保证键值按添加顺序使用链表维护



## TreeMap

> 底层由红黑树实现

* 优点：key有序，查询速度比List快
* 缺点：查询速度没有HashMap快

### 底层实现

每个节点的结构如下

```java
static final class Entry<K,V> implements Map.Entry<K,V> {
    K key;
    V value;
    Entry<K,V> left;
    Entry<K,V> right;
    Entry<K,V> parent;
    boolean color = BLACK;
}
```









### 迭代器



排序

```
Collections.sort(list);
```

使用Comparable或Comparator接口对自定义类进行排序



Comparator接口

强行对某个对象进行整体排序的比较函数

可以将Comparator传递给sort方法（Collections.sort()或Arrays.sort）

int compare()



Comparable接口

此接口强行对实现它的每个类的对象进行整体排序



参考：

> https://snailclimb.gitee.io/javaguide/#/docs/java/collection/Java%E9%9B%86%E5%90%88%E6%A1%86%E6%9E%B6%E5%B8%B8%E8%A7%81%E9%9D%A2%E8%AF%95%E9%A2%98