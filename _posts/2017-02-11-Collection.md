---
layout: post
title: Java源码系列之JCF概览
categories: [Java, JCF]
description: Java Collections Framework
keywords: JDK, JCF, Collection, Map
---

最近开始重看JDK源码，首先从常用的Java collections framework开始熟悉。要想对Java集合框架建立清晰而深入的理解，必须理解JCF里面是利用什么数据结构组织**存储**每一种容器，并为我们提供了哪些**操作**方法。

> The **Java collections framework (JCF)** is a set of classes and interfaces that implement commonly reusable collection data structures.

# 介绍

## 什么是Collection ？

容器，就是可以容纳其他Java对象的对象。

## 什么是Framework?

- 提供现成的结构。
- 代表一组类和接口。
- 是可选的。

## JCF有什么特点？

从JDK 1.2开始，**Java Collections Framework（JCF）**为Java开发者提供了通用的容器，表示用于**存储**和**操纵**对象组的统一结构。

- 其优点是：
  - 降低编程难度
  - 提高程序性能
  - 提高API间的互操作性
  - 降低学习难度
  - 降低设计和实现相关API的难度
  - 增加程序的重用性
- 限制：
  - Java容器里只能放对象，对于基本类型（int, long, float, double等），需要将其包装成对象类型后（Integer, Long, Float, Double等）才能放到容器里。
- Java容器能够容纳任何类型的对象，表面上是通过泛型机制完成，实质上编译器会在编译阶段将其转换为Object对象，所有容器的内部存放的都是Object对象。
- 由于Java里对象都在堆上，且对象只能通过引用访问，所以容器里放的其实是对象的引用而不是对象本身。



# 接口（Interfaces）

为了规范容器的行为，统一设计，JCF定义了14种容器接口（collection interfaces），它们的关系如下图所示： 

![JCF接口](/images/posts/JCF/JCF_Collection_Interfaces.png)

### Collection 

> The root interface in the collection hierarchy. A collection represents a group of objects, known as its elements. Some collections allow duplicate elements and others do not. Some are ordered and others unordered. The JDK does not provide any direct implementations of this interface: it provides implementations of more specific subinterfaces like Set and List. This interface is typically used to pass collections around and manipulate them where maximum generality is desired.

Collection接口是集合层次中的顶层接口。

- 一个集合表示一组单个元素的对象。有些有序，有些无序。有些重复，有些重复。
- Collection没有直接的实现，只提供继承与该接口的子接口。

#### Collection有哪些实现？



![](/images/posts/JCF/collection-hierarchy.png)



#### Collection有哪些方法？

| No.  | Method                                   | Description                              |
| ---- | ---------------------------------------- | ---------------------------------------- |
| 1    | public boolean add(Object element)       | is used to insert an element in this collection. |
| 2    | public boolean addAll(Collection c)      | is used to insert the specified collection elements in the invoking collection. |
| 3    | public boolean remove(Object element)    | is used to delete an element from this collection. |
| 4    | public boolean removeAll(Collection c)   | is used to delete all the elements of specified collection from the invoking collection. |
| 5    | public boolean retainAll(Collection c)   | is used to delete all the elements of invoking collection except the specified collection. |
| 6    | public int size()                        | return the total number of elements in the collection. |
| 7    | public void clear()                      | removes the total no of element from the collection. |
| 8    | public boolean contains(Object element)  | is used to search an element.            |
| 9    | public boolean containsAll(Collection c) | is used to search the specified collection in this collection. |
| 10   | public Iterator iterator()               | returns an iterator.                     |
| 11   | public Object[] toArray()                | converts collection into array.          |
| 12   | public boolean isEmpty()                 | checks if collection is empty.           |
| 13   | public boolean equals(Object element)    | matches two collection.                  |
| 14   | public int hashCode()                    | returns the hashcode number for collection. |



#### 如何遍历Collection？

- Collection接口实现了Iterable接口，其方法iterator()返回一个代表当前集合对象的泛型迭代器，可用于之后的遍历操作。

#### 什么是迭代器（Iterator）？

- Iterator不是容器，只是一个操作遍历集合的方法接口。

  | Method                   | Description                              |
  | ------------------------ | ---------------------------------------- |
  | public boolean hasNext() | returns true if iterator has more elements. |
  | public object next()     | returns the element and moves the cursor pointer to the next element. |
  | public void remove()     | removes the last elements returned by the iterator. It is rarely used. |

- 每个容器都会通过内部类的形式实现自己的迭代器。

  ```java
  //使用迭代器
  Iterator<String> it = list.iterator();//得到迭代器
  while(it.hasNext()){
      String s = it.next();//访问元素
  }
  //使用增强for循环，只是迭代器的“语法糖”
  for(String s : list){
    
  }
  ```

### Map

一个保存键值映射的对象组。 映射Map中不能包含重复的key，每一个key最多对应一个value。

- *Map*接口没有继承自*Collection*接口，因为*Map*表示的是关联式容器而不是集合。
- Map接口也没有实现Iterable接口。

#### Map中有哪些方法?

| Method                               | Description                              |
| ------------------------------------ | ---------------------------------------- |
| Object put(Object key, Object value) | It is used to insert an entry in this map. |
| void putAll(Map map)                 | It is used to insert the specified map in this map. |
| Object remove(Object key)            | It is used to delete an entry for the specified key. |
| Object get(Object key)               | It is used to return the value for the specified key. |
| boolean containsKey(Object key)      | It is used to search the specified key from this map. |
| Set keySet()                         | It is used to return the Set view containing all the keys. |
| Set entrySet()                       | It is used to return the Set view containing all the keys and values. |

#### Map中是怎么封装键值对的？

Entry是Map中的一个内部接口，Entry< K,V >里包含key，value。

| Method            | Description                 |
| ----------------- | --------------------------- |
| Object getKey()   | It is used to obtain key.   |
| Object getValue() | It is used to obtain value. |

- 不同的Map根据其自身特点，会有不同的Entry实现，以对应Map的内部类形式出现。
- 可以将Map看作是Entry的一个Collection。

#### 如何遍历Map？

- 获得所有key的集合然后通过key访问value：
  - `keySet()`返回所有key组成的Set< K >。
- 获得value的集合：
  - `values()`方法返回所有value组成的Collection< V > 。
- 获得key-value键值对的集合，再通过Entry中的getKey和getValue获取key和value：
  - `entrySet()`方法返回所有Entry组成的Set< Map.Entry< K, V>> 。

# 实现（Implementations）

上述接口的通用实现见下表：

|           | **Hash Table** | **Resizable Array** | **Balanced Tree** | **Linked List** | **Hash Table + Linked List** |
| --------- | -------------- | ------------------- | ----------------- | --------------- | ---------------------------- |
| **Set**   | `HashSet`      |                     | `TreeSet`         |                 | `LinkedHashSet`              |
| **List**  |                | `ArrayList`         |                   | `LinkedList`    |                              |
| **Deque** |                | `ArrayDeque`        |                   | `LinkedList`    |                              |
| **Map**   | `HashMap`      |                     | `TreeMap`         |                 | `LinkedHashMap`              |

# 参考资料

[Collections in Java](http://www.javatpoint.com/collections-in-java)

[Java Map Interface](http://www.javatpoint.com/java-map)