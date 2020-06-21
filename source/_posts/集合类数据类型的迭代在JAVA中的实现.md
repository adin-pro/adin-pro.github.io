---
title: 集合类数据类型的迭代在JAVA中的实现
mathjax: true
date: 2020-06-14 23:05:17
tags:
- Java
- 基础知识
categories: 数据结构与算法
---

集合类数据类型包括背包Bag、栈Stack、队列Queue。它们的共同基本操作包括判断空集合isEmpty，添加Add，删除Delete，以及遍历集合元素foreach。这里对集合类数据类型的迭代方法如何实现做一个记录。

<!-- more -->

## 实现迭代的基本要求
一个用例：

```java

Stack <String> Collection = new Stack<String>();
...
for (String s: Collection)
{
    StdOut.println(s);
}
...

另一种等价写法为：

Iterator<String> i = collection.iterator();
while(i.hasNext()){
    String s = i.next();
    StdOut.println(s);
}

```

由此可知，在任意可迭代的集合数据类型中需要实现的东西：
- 集合数据类型必须实现一个iterator（）方法并且返回一个**Iterator**对象；
- Iterator类必须包含两个方法hasNext（），返回Boolean和next（），返回一个泛型元素

## 使用接口实现迭代方法iterator（）

在Java中，使用接口的方式为类添加迭代所需方法iterator（）。

```java

import java.lang.Iterable;

public interface Iterable<item>{
    Iterator iterator();
}

```

假如我们要实现一个集合数据类型Member，可以定义一个**Iterator接口**的类别DefinedIterator：

```java

import java.util.Iterator;

public interface Iterator<item>{
    boolean hasNext();
    item next();
    void remove();
}
```

Iterator的三个方法通常只要前两个实现即可，remove方法保持实现为空不影响遍历。

```java

public class Member<item> implements Iterable<item>{
    public Iterator<item> iterator(){ return DefinedIterator}

    private class DefinedIterator implements Iterator<item>{
        public boolean hasNext() {return ...}
        public Item next(){return ...}
        public void remove() {}

    }
    ...
}


```

这样就完成了迭代所需要的基本的实现。对于每一个具体不同的集合数据类型，在自定义的DefinedIterator里面可以实现不同的迭代细节，比如数组的和链表的实现方法对应的迭代方法就完全不同，需要自行去调整。

## 一些值得额外记录的信息

在Java里面，泛型数组的直接初始化是不可以的，需要先new一些Object类别的数组，再强制转化为泛型的Item类别。

```java

Error:

Member<item>[] members = new Members<item>[N];

Ok:

Member<item>[] members = (Members<item>[]) new Objects[N] 


```

这种方法会得到Warning，但可以执行。


------------------

对于集合数据类型，需要常常做的操作是扩容和缩容（数组类），改变容量的原则是
- 当填满集合时，容量变为目前的两倍
- 当数量不足容量的**四分之一**时，容量缩小为目前的**二分之一**

这种方法可以避免频繁的改变容量。扩容放在Push中，缩容放在Pop中，都是作为if的条件进行数量/容量判断，之后再执行进出的操作。


