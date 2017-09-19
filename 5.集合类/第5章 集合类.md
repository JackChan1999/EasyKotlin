# 第5章 集合类

本章将介绍Kotlin标准库中的集合类，我们将了解到它是如何扩展的Java集合库，使得写代码更加简单容易。如果您熟悉Scala的集合库，您会发现Kotlin跟Scala集合类库的相似之处。

## 5.1 集合类是什么

### 5.1.2 集合类是一种数据结构

在讲 Kotlin 的集合类之前，为了更加深刻理解为什么要有集合类，以及集合类到底是怎么一回事，让我们先来简单回顾一下编程的本质：

> 数据结构 + 算法 （信息的逻辑结构及其基本操作）

我们使用计算机编程来解决一个具体问题时，大致需要经过下列几个步骤：

首先要从具体问题中抽象出一个适当的数学模型；
然后设计一个解此数学模型的算法（Algorithm）；
最后编出程序、进行测试、修改直至得到最终解答。

这里面的寻求数学模型的过程，实质就是分析问题，从中提取操作的对象，并找出这些操作对象之间含有的关系的过程。建立好的模型，我们使用数学语言来表达。

这里的模型对应的就是数据结构。我们用计算机编程来解决问题的关键就是，设计出合适的数据结构（例如，用线性表、树、图等）和性能良好的算法。

算法与数据的结构密切相关，算法无不依附于具体的数据结构，数据结构直接关系到算法的选择和效率。通常情况下，设计良好的数据结构可以大大简化算法的实现复杂度，同时可以提升存储效率。数据结构往往同高效的检索算法和索引技术相关。

我们可以把数据结构理解为是ADT的实现。数据结构就是现实问题模型的表达。

数据结构主要解决以下三个问题：

- 数据元素之间的逻辑关系。

这些逻辑关系有：集合、线性结构、树形结构、图形结构等。

- 数据的物理结构。

数据的逻辑结构在计算机存储空间的存放形式。数据的物理结构是数据结构在计算机中的映射。其具体实现的方法有： 顺序（Sequence）、链接（Link）、索引（Index）、散列（Hash）等形式。

其中，顺序存储结构和链式存储结构是我们常用的两种存储结构。

顺序存储是使用元素在存储器中的相对位置来表示数据元素之间的逻辑关系；

链式存储使用指示元素存储位置的指针（pointer）来表示数据元素之间的逻辑关系。

- 数据的处理运算。

### 5.1.2 集合类是SDK API

我们现在很少用抽象数据类型ADT（Abstract Data Type）这个概念，其实这个概念是OO范式的前身，也是类的前身。ADT加上继承、重载和多态性就是现代OOP编程范式中的类的概念了。我们简称类为广义ADT的概念。

如果我们更加广义的来理解这里的ADT的思想，其实各种编程语言的SDK API、所有的服务（IaaS，PaaS和SaaS等）都是一种更加广义的ADT。

使用ADT可以让我们更简单地描述现实世界。例如：用线性表描述学生成绩表，用树或图描述遗传关系等。

我们知道类的本质就是，对象及其关系的抽象（abstraction）。一个类通常有属性（数据结构）和行为（算法）。使用OO范式编程的大致过程为：

> 划分对象 → 抽象类 → 将类组织成为层次化结构(继承和合成) → 用类与实例进行设计和实现

等几个阶段。

数据抽象本质上讲就是我们解决现实问题的过程中，进行建立领域模型（Domain Model）的过程。

比如说，在前一章节中，我们介绍的程序设计语言的类型系统，本质上就是一种数据抽象。由于计算机的结构和存储的限制（无法像人类大脑神经系统一样去认知识别，并解决现实问题），人类大脑在解决实际问题过程中，经常要计算整数、小数, 要处理英文字符、中文字符， 要持有对象（被操作的数据），要对这些对象进行诸如：查找、排序、修改、传递等操作。把这些问题解决中最常用的数据结构以及其操作算法抽象成对应的类（例如：String、Array、List、Set、Map等），这样我们就可以极大的复用这些功能。而不需要我们自己来实现诸如：字符串、数组、列表、集合、映射等这些的数据结构。通常这些最通用的数据结构，都是现在编程语言中内置的了。

### 5.1.3 连续存储和离散存储

内存中的存储形式可以分为连续存储和离散存储两种。因此，数据的物理存储结构就有连续存储和离散存储两种，它们对应了我们通常所说的数组和链表。

由于数组是连续存储的，在操作数组中的数据时就可以根据离首地址的偏移量直接存取相应位置上的数据，但是如果要在数据组中任意位置上插入一个元素，就需要先把后面的元素集体向后移一位为其空出存储空间。与之相反，链表是离散存储的，所以在插入一个数据时只要申请一片新空间，然后将其中的连接关系做一个修改就可以，但是显然在链表上查找一个数据时就要逐个遍历了。

考虑以上的总结可见，数组和链表各有优缺点。在具体使用时要根据具体情况选择。当查找数据操作比较多时最好用数组；当对数据集中的数据进行添加或删除比较多时最好选择链表。

## 5.2 Kotlin 集合类简介

集合类存放的都是对象的引用，而非对象本身，我们通常说的集合中的对象指的是集合中对象的引用（reference)。

Kotlin的集合类分为：**可变集合类**（Mutable）与**不可变集合类**（Immutable）。

集合类型主要有3种：list(列表）、set(集）、和 map(映射)。

#### (1)列表

列表的主要特征是其对象以线性方式存储，没有特定顺序，只有一个开头和一个结尾，当然，它与根本没有顺序的集是不同的。

列表在数据结构中可表现为：数组和向量、链表、堆栈、队列等。

#### (2)集

集（set）是最简单的一种集合，它的对象不按特定方式排序，只是简单的把对象加入集合中，就像往口袋里放东西。

对集中成员的访问和操作是通过集中对象的引用进行的，所以集中不能有重复对象。

集也有多种变体，可以实现排序等功能，如TreeSet，它把对象添加到集中的操作将变为按照某种比较规则将其插入到有序的对象序列中。它实现的是SortedSet接口，也就是加入了对象比较的方法。通过对集中的对象迭代，我们可以得到一个升序的对象集合。

#### (3)映射

映射与集或列表有明显区别，映射中每个项都是成对的。映射中存储的每个对象都有一个相关的关键字（Key）对象，关键字决定了 对象在映射中的存储位置，检索对象时必须提供相应的关键字，就像在字典中查单词一样。关键字应该是唯一的。

关键字本身并不能决定对象的存储位置，它需要对过一种散列(hashing)技术来处理，产生一个被称作散列码(hash code)的整数值，

散列码通常用作一个偏置量，该偏置量是相对于分配给映射的内存区域起始位置的，由此确定关键字/对象对的存储位置。理想情况 下，散列处理应该产生给定范围内均匀分布的值，而且每个关键字应得到不同的散列码。

## 5.3 List

List接口继承于Collection接口，元素以线性方式存储，集合中可以存放重复对象。Kotlin的List分为：不可变集合类List（ReadOnly, Immutable）和可变集合类MutableList（Read&Write, Mutable）。

其类图结构如下：

![img](https://segmentfault.com/img/remote/1460000010313210)

其中，`Iterator`是所有容器类`Collection`的迭代器。迭代器（Iterator）模式，又叫做游标（Cursor）模式。GOF给出的定义为：提供一种方法访问一个容器对象中各个元素，而又不需暴露该对象的内部细节。 从定义可见，迭代器模式是为容器而生。

### 5.3.1 创建不可变List

我们可以使用`listOf`函数来构建一个不可变的List（read-only，只读的List）。它定义在`libraries/stdlib/src/kotlin/collections/Collections.kt` 里面。关于`listOf`这个构建函数有下面3个重载函数：

```
@kotlin.internal.InlineOnly
public inline fun <T> listOf(): List<T> = emptyList()

public fun <T> listOf(vararg elements: T): List<T> = if (elements.size > 0) elements.asList() else emptyList()

@JvmVersion
public fun <T> listOf(element: T): List<T> = java.util.Collections.singletonList(element)

```

这些函数创建的List都是是只读的（readonly，也就是不可变的immutable ）、可序列化的。

其中，

`listOf()`用于创建没有元素的空List
`listOf(vararg elements: T)`用于创建只有一个元素的List
`listOf(element: T)`用于创建拥有多个元素的List

我们使用代码示例分别来演示其用法：

首先，我们使用`listOf()`来构建一个没有元素的空的List：

```
>>> val list:List<Int> = listOf()
>>> list
[]
>>> list::class
class kotlin.collections.EmptyList
```

注意，这里的变量的类型不能省略，否则会报错：

```
>>> val list = listOf()
error: type inference failed: Not enough information to infer parameter T in inline fun <T> listOf(): List<T>
Please specify it explicitly.

val list = listOf()
           ^


```

因为这是一个泛型函数。关于泛型，我们将在下一章中介绍。

其中，`EmptyList` 是一个 `internal object EmptyList`, 这是Kotlin内部定义的一个默认空的object List类。

下面，我们再来创建一个只有1个元素的List：

```
>>> val list = listOf(1)
>>> list::class
class java.util.Collections$SingletonList
```

我们可以看出，它实际上是调用Java的`java.util.Collections` 里面的`singletonList`方法：

```
    public static <T> List<T> singletonList(T o) {
        return new SingletonList<>(o);
    }
```

我们再来创建一个有多个元素的List:

```
>>> val list = listOf(0,1, 2, 3, 4, 5, 6,7,8,9)
>>> list
[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
>>> list::class
class java.util.Arrays$ArrayList
>>> list::class.java
```

它调用的是

```
fun <T> listOf(vararg elements: T): List<T> = if (elements.size > 0) elements.asList() else emptyList()
```

这个函数。其中，`asList`函数是`Array`的扩展函数：

```
public fun <T> Array<out T>.asList(): List<T> {
    return ArraysUtilJVM.asList(this)
}
```

而这个`ArraysUtilJVM`是一个Java类，里面实际上调用的是`java.util.Arrays`和`java.util.List` :

```
package kotlin.collections;

import java.util.Arrays;
import java.util.List;

class ArraysUtilJVM {
    static <T> List<T> asList(T[] array) {
        return Arrays.asList(array);
    }
}
```

另外，我们还可以直接使用`arrayListOf`函数来创建一个Java中的ArrayList对象实例：

```
>>> val list = arrayListOf(0,1,2,3)
>>> list
[0, 1, 2, 3]
>>> list::class
class java.util.ArrayList
>>> val list = listOf(0,1, 2, 3, 4, 5, 6,7,8,9) 
>>> list::class
class java.util.Arrays$ArrayList
```

这个函数定义在`libraries/stdlib/src/kotlin/collections/Collections.kt`类中：

```
@SinceKotlin("1.1")
@kotlin.internal.InlineOnly
public inline fun <T> arrayListOf(): ArrayList<T> = ArrayList()
```

同样的处理方式，这里的`ArrayList()`是Java中的` java.util.ArrayList`的类型别名：

```
@SinceKotlin("1.1") public typealias ArrayList<E> = java.util.ArrayList<E>
```

### 5.3.2 创建可变集合MutableList

在MutableList中，除了继承List中的那些函数外，另外新增了add/addAll、remove/removeAll/removeAt、set、clear、retainAll等更新修改的操作函数。

```
override fun add(element: E): Boolean
override fun remove(element: E): Boolean
override fun addAll(elements: Collection<E>): Boolean
fun addAll(index: Int, elements: Collection<E>): Boolean
override fun removeAll(elements: Collection<E>): Boolean
override fun retainAll(elements: Collection<E>): Boolean
override fun clear(): Unit
operator fun set(index: Int, element: E): E
fun add(index: Int, element: E): Unit
fun removeAt(index: Int): E
override fun listIterator(): MutableListIterator<E>
override fun listIterator(index: Int): MutableListIterator<E>
override fun subList(fromIndex: Int, toIndex: Int): MutableList<E>
```

创建一个MutableList的对象实例跟List类似，前面加上前缀`mutable`，代码示例如下：

```
>>> val list = mutableListOf(1, 2, 3)
>>> list
[1, 2, 3]
>>> list::class
class java.util.ArrayList
>>> val list2 = mutableListOf<Int>()
>>> list2
[]
>>> list2::class
class java.util.ArrayList
>>> val list3 = mutableListOf(1)
>>> list3
[1]
>>> list3::class
class java.util.ArrayList
```

我们可以看出，使用`mutableListOf`函数创建的可变集合类，实际上背后调用的是`java.util.ArrayList`类的相关方法。

另外，我们可以直接使用Kotlin封装的`arrayListOf`函数来创建一个ArrayList：

```
>>> val list4 = arrayListOf(1, 2, 3)
>>> list4::class
class java.util.ArrayList
```

关于Kotlin中的`ArrayList`类型别名定义在
`kotlin/collections/TypeAliases.kt` 文件中：

```
@file:kotlin.jvm.JvmVersion

package kotlin.collections

@SinceKotlin("1.1") public typealias RandomAccess = java.util.RandomAccess


@SinceKotlin("1.1") public typealias ArrayList<E> = java.util.ArrayList<E>
@SinceKotlin("1.1") public typealias LinkedHashMap<K, V> = java.util.LinkedHashMap<K, V>
@SinceKotlin("1.1") public typealias HashMap<K, V> = java.util.HashMap<K, V>
@SinceKotlin("1.1") public typealias LinkedHashSet<E> = java.util.LinkedHashSet<E>
@SinceKotlin("1.1") public typealias HashSet<E> = java.util.HashSet<E>


// also @SinceKotlin("1.1")
internal typealias SortedSet<E> = java.util.SortedSet<E>
internal typealias TreeSet<E> = java.util.TreeSet<E>
```

如果我们已经有了一个不可变的List，而我们现在想把他转换成可变的List，我们可以直接调用转换函数`toMutableList`：

```
val list = mutableListOf(1, 2, 3)
val mlist = list.toMutableList()
mlist.add(5)
```

### 5.3.3 遍历List元素

#### 使用Iterator迭代器

我们以集合 `val list = listOf(0,1, 2, 3, 4, 5, 6,7,8,9) `为例，使用Iterator迭代器遍历列表所有元素的操作：

```
>>> val list = listOf(0,1, 2, 3, 4, 5, 6,7,8,9) 
>>> list
[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
>>> val iterator = list.iterator()
>>> iterator
java.util.AbstractList$Itr@438bad7c
>>> while(iterator.hasNext()){
... println(iterator.next())
... }
0
1
2
3
4
5
6
7
8
9
```

迭代器是一种设计模式，它是一个对象，它可以遍历并选择序列中的对象，而开发人员不需要了解该序列的底层结构。迭代器通常被称为“轻量级”对象，因为创建它的代价小。

　　Kotlin中的Iterator功能比较简单，并且只能单向移动：

（1） 调用iterator()函数，容器返回一个Iterator实例。iterator()函数是`kotlin.collections.Iterable`中的函数, 被Collection继承。
（2）调用hasNext()函数检查序列中是否还有元素。
（3）第一次调用Iterator的next()函数时，它返回序列的第一个元素。依次向后递推，使用next()获得序列中的下一个元素。

当我们调用到最后一个元素，再次调用`next()`函数，会抛这个异常`java.util.NoSuchElementException`。代码示例：

```
>>> val list = listOf(1,2,3)
>>> val iter = list.iterator()
>>> iter
java.util.AbstractList$Itr@3abfe845
>>> iter.hasNext()
true
>>> iter.next()
1
>>> iter.hasNext()
true
>>> iter.next()
2
>>> iter.hasNext()
true
>>> iter.next()
3
>>> iter.hasNext()
false
>>> iter.next()
java.util.NoSuchElementException
    at java.util.AbstractList$Itr.next(AbstractList.java:364)
```

我们可以看出，这里的Iterator的实现是在`AbstractList`中的内部类`IteratorImpl`：

```
private open inner class IteratorImpl : Iterator<E> {
        protected var index = 0
        override fun hasNext(): Boolean = index < size
        override fun next(): E {
            if (!hasNext()) throw NoSuchElementException()
            return get(index++)
        }
    }
```

通过这个实现源码，我们可以更加清楚地明白Iterator的工作原理。

其中，`NoSuchElementException()`这个类是`java.util.NoSuchElementException`的类型别名：

```
@kotlin.SinceKotlin public typealias NoSuchElementException = java.util.NoSuchElementException
```

#### 使用`forEach`遍历List元素

这个`forEach`函数定义如下：

```
@kotlin.internal.HidesMembers
public inline fun <T> Iterable<T>.forEach(action: (T) -> Unit): Unit {
    for (element in this) action(element)
}
```

它是`package kotlin.collections`包下面的Iterable的扩展内联函数。它的入参是一个函数类型：

```
action: (T) -> Unit
```

关于函数式编程，我们将在后面章节中学习。

这里的`forEach`是一个语法糖。实际上`forEach`在遍历List对象的时候，仍然使用的是iterator迭代器来进行循环遍历的。

```
>>> val list = listOf(1,2,3)
>>> list
[1, 2, 3]
>>> list.forEach{
... println(it)
... }
1
2
3

```

当参数只有一个函数的时候，括号可以省略不写。

也就是说，这里面的forEach函数调用的写法，实际上跟下面的写法等价：

```
list.forEach({
    println(it)
})
```

我们甚至还可以直接这样写：

```
>>> list.forEach(::println)
```

其中，`::` 是函数引用符。

### 5.3.4 List元素操作函数

#### `add` `remove` `set` `clear`

这两个添加、删除操作函数是MutableList里面的。跟Java中的集合类操作类似。

创建一个可变集合：

```
>>> val mutableList = mutableListOf(1,2,3)
>>> mutableList
[1, 2, 3]
```

向集合中添加一个元素：

```
>>> mutableList.add(4)
true
>>> mutableList
[1, 2, 3, 4]
```

在下标为0的位置添加元素0 ：

```
>>> mutableList.add(0,0)
>>> mutableList
[0, 1, 2, 3, 4]
```

删除元素1 ：

```
>>> mutableList.remove(1)
true
>>> mutableList
[0, 2, 3, 4]
>>> mutableList.remove(1)
false
```

删除下标为1的元素：

```
>>> mutableList.removeAt(1)
2
>>> mutableList
[0, 3, 4]
```

删除子集合：

```
>>> mutableList.removeAll(listOf(3,4))
true
>>> mutableList
[0]
```

添加子集合：

```
>>> mutableList.addAll(listOf(1,2,3))
true
>>> mutableList
[1, 2, 3]
```

更新设置下标0的元素值为100：

```
>>> mutableList.set(0,100)
0
>>> mutableList
[100]
```

清空集合：

```
>>> mutableList.clear()
>>> mutableList
[]

```

把可变集合转为不可变集合：

```
>>> mutableList.toList()
[1, 2, 3]
```

#### `retainAll`

取两个集合交集：

```
>>> val mlist1 = mutableListOf(1,2,3,4,5,6)
>>> val mlist2 = mutableListOf(3,4,5,6,7,8,9)
>>> mlist1.retainAll(mlist2)
true
>>> mlist1
[3, 4, 5, 6]

```

#### `contains(element: T): Boolean`

判断集合中是否有指定元素，有就返回true，否则返回false 。
代码示例：

```
>>> val list = listOf(1,2,3,4,5,6,7)
>>> list.contains(1)
true
```

#### `elementAt(index: Int): T`

查找下标对应的元素，如果下标越界会抛IndexOutOfBoundsException。
代码示例：

```
>>> val list = listOf(1,2,3,4,5,6,7)
>>> list.elementAt(6)
7
>>> list.elementAt(7)
java.lang.ArrayIndexOutOfBoundsException: 7
    at java.util.Arrays$ArrayList.get(Arrays.java:3841)


```

另外，针对越界的处理，还有下面两个函数：

`elementAtOrElse(index: Int, defaultValue: (Int) -> T): T` : 查找下标对应元素，如果越界会根据方法返回默认值。

```
>>> list.elementAtOrElse(7,{0})
0
>>> list.elementAtOrElse(7,{10})
10
```

`elementAtOrNull(index: Int): T?` : 查找下标对应元素，如果越界就返回null

```
>>> list.elementAtOrNull(7)
null
```

#### `first()`

返回集合第1个元素，如果是空集，抛出异常NoSuchElementException。

```
>>> val list = listOf(1,2,3)
>>> list.first()
1
>>> val emptyList = listOf<Int>()
>>> emptyList.first()
java.util.NoSuchElementException: List is empty.
    at kotlin.collections.CollectionsKt___CollectionsKt.first(_Collections.kt:178)


```

对应的有针对异常处理的函数`firstOrNull(): T?` :

```
>>> emptyList.firstOrNull()
null
```

#### `first(predicate: (T) -> Boolean): T`

返回符合条件的第一个元素，没有则抛异常NoSuchElementException 。

```
>>> val list = listOf(1,2,3)
>>> list.first({it%2==0})
2
>>> list.first({it>100})
java.util.NoSuchElementException: Collection contains no element matching the predicate.

```

对应的有针对异常处理的函数`firstOrNull(predicate: (T) -> Boolean): T?` ，返回符合条件的第一个元素，没有就返回null ：

```
>>> list.firstOrNull({it>100})
null
```

#### `indexOf(element: T): Int`

返回指定下标的元素，没有就返回-1

```
>>> val list = listOf("a","b","c")
>>> list.indexOf("c")
2
>>> list.indexOf("x")
-1
```

#### `indexOfFirst(predicate: (T) -> Boolean): Int`

返回第一个符合条件的元素下标，没有就返回-1 。

```
>>> val list = listOf("abc","xyz","xjk","pqk")
>>> list.indexOfFirst({it.contains("x")})
1
>>> list.indexOfFirst({it.contains("k")})
2
>>> list.indexOfFirst({it.contains("e")})
-1
```

#### `indexOfLast(predicate: (T) -> Boolean): Int`

返回最后一个符合条件的元素下标，没有就返回-1 。

```
>>> val list = listOf("abc","xyz","xjk","pqk")
>>> list.indexOfLast({it.contains("x")})
2
>>> list.indexOfLast({it.contains("k")})
3
>>> list.indexOfLast({it.contains("e")})
-1
```

#### `last()`

返回集合最后一个元素，空集则抛出异常NoSuchElementException。

```
>>> val list = listOf(1,2,3,4,7,5,6,7,8)
>>> list.last()
8
>>> val emptyList = listOf<Int>()
>>> emptyList.last()
java.util.NoSuchElementException: List is empty.
    at kotlin.collections.CollectionsKt___CollectionsKt.last(_Collections.kt:340)



```

#### `last(predicate: (T) -> Boolean): T`

返回符合条件的最后一个元素，没有就抛NoSuchElementException

```
>>> val list = listOf(1,2,3,4,7,5,6,7,8)
>>> list.last({it==7})
7
>>> list.last({it>10})
java.util.NoSuchElementException: List contains no element matching the predicate.

```

对应的针对越界处理的`lastOrNull `函数：返回符合条件的最后一个元素，没有则返回null :

```
>>> list.lastOrNull({it>10})
null
```

#### `lastIndexOf(element: T): Int`

返回符合条件的最后一个元素，没有就返回-1

```
>>> val list = listOf("abc","dfg","jkl","abc","bbc","wer")
>>> list.lastIndexOf("abc")
3
```

#### `single(): T`

该集合如果只有1个元素，则返回该元素。否则，抛异常。

```
>>> val list = listOf(1)
>>> list.single()
1

>>> val list = listOf(1,2)
>>> list.single()
java.lang.IllegalArgumentException: List has more than one element.
    at kotlin.collections.CollectionsKt___CollectionsKt.single(_Collections.kt:471)

>>> val list = listOf<Int>()

>>> list.single()
java.util.NoSuchElementException: List is empty.
    at kotlin.collections.CollectionsKt___CollectionsKt.single(_Collections.kt:469)



```

#### `single(predicate: (T) -> Boolean): T`

返回符合条件的单个元素，如有没有符合的抛异常NoSuchElementException，或超过一个的抛异常IllegalArgumentException。

```
>>> val list = listOf(1,2,3,4,7,5,6,7,8)
>>> list.single({it==1})
1
>>> list.single({it==7})
java.lang.IllegalArgumentException: Collection contains more than one matching element.

>>> list.single({it==10})
java.util.NoSuchElementException: Collection contains no element matching the predicate.

```

对应的针对异常处理的函数`singleOrNull `: 返回符合条件的单个元素，如有没有符合或超过一个，返回null

```
>>> list.singleOrNull({it==7})
null
>>> list.singleOrNull({it==10})
null
```

### 5.3.5 List集合类的`any` `all ``none ``count ``reduce ``fold ``max ``min` `sum` 函数算子(operator)

#### `any() `判断集合至少有一个元素

这个函数定义如下：

```
public fun <T> Iterable<T>.any(): Boolean {
    for (element in this) return true
    return false
}
```

如果该集合至少有一个元素，返回`true`，否则返回`false`。

代码示例：

```
>>> val emptyList = listOf<Int>()
>>> emptyList.any()
false
>>> val list1 = listOf(1)
>>> list1.any()
true
```

#### `any(predicate: (T) -> Boolean)` 判断集合中是否有满足条件的元素

这个函数定义如下：

```
public inline fun <T> Iterable<T>.any(predicate: (T) -> Boolean): Boolean {
    for (element in this) if (predicate(element)) return true
    return false
}
```

如果该集合中至少有一个元素匹配谓词函数参数`predicate: (T) -> Boolean`，返回true，否则返回false。

代码示例：

```
>>> val list = listOf(1, 2, 3)
>>> list.any() // 至少有1个元素
true
>>> list.any({it%2==0}) // 元素2满足{it%2==0}
true
>>> list.any({it>4}) // 没有元素满足{it>4}
false
```

#### `all(predicate: (T) -> Boolean)` 判断集合中的元素是否都满足条件

函数定义：

```
public inline fun <T> Iterable<T>.all(predicate: (T) -> Boolean): Boolean {
    for (element in this) if (!predicate(element)) return false
    return true
}
```

当且仅当该集合中所有元素都满足条件时，返回`true`；否则都返回`false`。

代码示例：

```
>>> val list = listOf(0,2,4,6,8)
>>> list.all({it%2==0})
true
>>> list.all({it>2})
false
```

#### `none() `判断集合无元素

函数定义：

```
public fun <T> Iterable<T>.none(): Boolean {
    for (element in this) return false
    return true
}
```

如果该集合没有任何元素，返回`true`，否则返回`false`。

代码示例：

```
>>> val list = listOf<Int>()
>>> list.none()
true
```

#### `none(predicate: (T) -> Boolean)`判断集合中所有元素都不满足条件

函数定义：

```
public inline fun <T> Iterable<T>.none(predicate: (T) -> Boolean): Boolean {
    for (element in this) if (predicate(element)) return false
    return true
}
```

当且仅当集合中所有元素都不满足条件时返回`true`，否则返回`false`。

代码示例：

```
>>> val list = listOf(0,2,4,6,8)
>>> list.none({it%2==1})
true
>>> list.none({it>0})
false
```

#### `count()` 计算集合中元素的个数

函数定义：

```
public fun <T> Iterable<T>.count(): Int {
    var count = 0
    for (element in this) count++
    return count
}
```

代码示例：

```
>>> val list = listOf(0,2,4,6,8,9)
>>> list.count()
6
```

#### `count(predicate: (T) -> Boolean)` 计算集合中满足条件的元素的个数

函数定义：

```
public inline fun <T> Iterable<T>.count(predicate: (T) -> Boolean): Int {
    var count = 0
    for (element in this) if (predicate(element)) count++
    return count
}
```

代码示例：

```
>>> val list = listOf(0,2,4,6,8,9)
>>> list.count()
6
>>> list.count({it%2==0})
5
```

#### `reduce`从 第一项到最后一项进行累计运算

函数定义：

```
public inline fun <S, T: S> Iterable<T>.reduce(operation: (acc: S, T) -> S): S {
    val iterator = this.iterator()
    if (!iterator.hasNext()) throw UnsupportedOperationException("Empty collection can't be reduced.")
    var accumulator: S = iterator.next()
    while (iterator.hasNext()) {
        accumulator = operation(accumulator, iterator.next())
    }
    return accumulator
}
```

首先把第一个元素赋值给累加子`accumulator`，然后逐次向后取元素累加，新值继续赋值给累加子`accumulator = operation(accumulator, iterator.next())`，以此类推。最后返回累加子的值。

代码示例：

```
>>> val list = listOf(1,2,3,4,5,6,7,8,9)
>>> list.reduce({sum, next->sum+next})
45
>>> list.reduce({sum, next->sum*next})
362880
>>> val list = listOf("a","b","c")
>>> list.reduce({total, s->total+s})
abc

```

#### `reduceRight`从最后一项到第一项进行累计运算

函数定义：

```
public inline fun <S, T: S> List<T>.reduceRight(operation: (T, acc: S) -> S): S {
    val iterator = listIterator(size)
    if (!iterator.hasPrevious())
        throw UnsupportedOperationException("Empty list can't be reduced.")
    var accumulator: S = iterator.previous()
    while (iterator.hasPrevious()) {
        accumulator = operation(iterator.previous(), accumulator)
    }
    return accumulator
}

```

从函数的定义`accumulator = operation(iterator.previous(), accumulator)`, 我们可以看出，从右边累计运算的累加子是放在后面的。

代码示例：

```
>>> val list = listOf("a","b","c")
>>> list.reduceRight({total, s -> s+total})
cba
```

如果我们位置放错了，会输出下面的结果：

```
>>> list.reduceRight({total, s -> total+s})
abc
```

#### `fold(initial: R, operation: (acc: R, T) -> R): R` 带初始值的reduce

函数定义：

```
public inline fun <T, R> Iterable<T>.fold(initial: R, operation: (acc: R, T) -> R): R {
    var accumulator = initial
    for (element in this) accumulator = operation(accumulator, element)
    return accumulator
}
```

从函数的定义，我们可以看出，fold函数给累加子赋了初始值`initial`。

代码示例：

```
>>> val list=listOf(1,2,3,4)
>>> list.fold(100,{total, next -> next + total})
110
```

`foldRight`和`reduceRight`类似，有初始值。

函数定义：

```
public inline fun <T, R> List<T>.foldRight(initial: R, operation: (T, acc: R) -> R): R {
    var accumulator = initial
    if (!isEmpty()) {
        val iterator = listIterator(size)
        while (iterator.hasPrevious()) {
            accumulator = operation(iterator.previous(), accumulator)
        }
    }
    return accumulator
}
```

代码示例：

```
>>> val list = listOf("a","b","c")
>>> list.foldRight("xyz",{s, pre -> pre + s})
xyzcba
```

#### `forEach(action: (T) -> Unit): Unit` 循环遍历元素，元素是it

我们在前文已经讲述，参看5.3.4。

再写个代码示例：

```
>>> val list = listOf(0, 1, 2, 3, 4, 5, 6, 7, 8, 9) 
>>> list.forEach { value -> if (value > 7) println(value) } 
8
9
```

#### `forEachIndexed` 带index(下标) 的元素遍历

函数定义：

```
public inline fun <T> Iterable<T>.forEachIndexed(action: (index: Int, T) -> Unit): Unit {
    var index = 0
    for (item in this) action(index++, item)
}
```

代码示例：

```
>>> val list = listOf(0, 1, 2, 3, 4, 5, 6, 7, 8, 9) 
>>> list.forEachIndexed { index, value -> if (value > 8) println("value of index $index is $value, greater than 8") } 

value of index 9 is 9, greater than 8
```

#### `max` 、`min`查询最大、最小的元素，空集则返回null

`max`函数定义：

```
public fun <T : Comparable<T>> Iterable<T>.max(): T? {
    val iterator = iterator()
    if (!iterator.hasNext()) return null
    var max = iterator.next()
    while (iterator.hasNext()) {
        val e = iterator.next()
        if (max < e) max = e
    }
    return max
}
```

返回集合中最大的元素。

代码示例：

```
>>> val list = listOf(1,2,3)
>>> list.max()
3
>>> val list = listOf("a","b","c")
>>> list.max()
c

```

`min`函数定义：

```
public fun <T : Comparable<T>> Iterable<T>.min(): T? {
    val iterator = iterator()
    if (!iterator.hasNext()) return null
    var min = iterator.next()
    while (iterator.hasNext()) {
        val e = iterator.next()
        if (min > e) min = e
    }
    return min
}
```

返回集合中的最小元素。

代码示例：

```
>>> val list = listOf(1,2,3)
>>> list.min()
1
>>> val list = listOf("a","b","c")
>>> list.min()
a

```

在Kotlin中，字符串的大小比较比较有意思的，我们直接通过代码示例来学习一下：

```
>>> "c" > "a"
true
>>> "abd" > "abc"
true
>>> "abd" > "abcd"
true
>>> "abd" > "abcdefg"
true
```

我们可以看出，字符串的大小比较是按照对应的下标的字符进行比较的。
另外，布尔值的比较是`true`大于`false`：

```
>>> true > false
true
```

#### `maxBy(selector: (T) -> R): T?` 、 `minBy(selector: (T) -> R): T?`获取函数映射结果的最大值、最小值对应的那个元素的值，如果没有则返回null

函数定义：

```
public inline fun <T, R : Comparable<R>> Iterable<T>.maxBy(selector: (T) -> R): T? {
    val iterator = iterator()
    if (!iterator.hasNext()) return null
    var maxElem = iterator.next()
    var maxValue = selector(maxElem)
    while (iterator.hasNext()) {
        val e = iterator.next()
        val v = selector(e)
        if (maxValue < v) {
            maxElem = e
            maxValue = v
        }
    }
    return maxElem
}
```

也就是说，不直接比较集合元素的大小，而是以集合元素为入参的函数`selector: (T) -> R` 返回值来比较大小，最后返回此元素的值（注意，不是对应的`selector`函数的返回值）。有点像数学里的求函数最值问题：

给定函数 `y = f(x)` ， 求` max f(x) `的`x`的值。

代码示例：

```
>>> val list = listOf(100,-500,300,200)
>>> list.maxBy({it})
300
>>> list.maxBy({it*(1-it)})
100
>>> list.maxBy({it*it})
-500
```

对应的 `minBy` 是获取函数映射后返回结果的最小值所对应那个元素的值，如果没有则返回null。

代码示例：

```
>>> val list = listOf(100,-500,300,200)
>>> list.minBy({it})
-500
>>> list.minBy({it*(1-it)})
-500
>>> list.minBy({it*it})
100
```

#### `sumBy(selector: (T) -> Int): Int` 获取函数映射值的总和

函数定义：

```
public inline fun <T> Iterable<T>.sumBy(selector: (T) -> Int): Int {
    var sum: Int = 0
    for (element in this) {
        sum += selector(element)
    }
    return sum
}

```

可以看出，这个`sumBy`函数算子，累加器`sum`初始值为0，返回值是`Int`。它的入参`selector`是一个函数类型`(T) -> Int`，也就是说这个selector也是返回Int类型的函数。

代码示例：

```
>>> val list = listOf(1,2,3,4)
>>> list.sumBy({it})
10
>>> list.sumBy({it*it})
30
```

类型错误反例：

```
>>> val list = listOf("a","b","c")
>>> list.sumBy({it})
error: type inference failed: inline fun <T> Iterable<T>.sumBy(selector: (T) -> Int): Int
cannot be applied to
receiver: List<String>  arguments: ((String) -> String)

list.sumBy({it})
     ^
error: type mismatch: inferred type is (String) -> String but (String) -> Int was expected
list.sumBy({it})
           ^


```

### 5.3.6 过滤操作函数算子

#### `take(n: Int): List<T>` 挑出该集合前n个元素的子集合

函数定义：

```
public fun <T> Iterable<T>.take(n: Int): List<T> {
    require(n >= 0) { "Requested element count $n is less than zero." }
    if (n == 0) return emptyList()
    if (this is Collection<T>) {
        if (n >= size) return toList()
        if (n == 1) return listOf(first())
    }
    var count = 0
    val list = ArrayList<T>(n)
    for (item in this) {
        if (count++ == n)
            break
        list.add(item)
    }
    return list.optimizeReadOnlyList()
}
```

如果n等于0，返回空集；如果n大于集合`size`，返回该集合。

代码示例：

```
>>> val list = listOf("a","b","c")
>>> list
[a, b, c]
>>> list.take(2)
[a, b]
>>> list.take(10)
[a, b, c]
>>> list.take(0)
[]

```

#### `takeWhile(predicate: (T) -> Boolean): List<T>` 挑出满足条件的元素的子集合

函数定义：

```
public inline fun <T> Iterable<T>.takeWhile(predicate: (T) -> Boolean): List<T> {
    val list = ArrayList<T>()
    for (item in this) {
        if (!predicate(item))
            break
        list.add(item)
    }
    return list
}
```

从第一个元素开始，判断是否满足`predicate`为true，如果满足条件的元素就丢到返回`ArrayList`中。只要遇到任何一个元素不满足条件，就结束循环，返回list 。

代码示例：

```
>>> val list = listOf(1,2,4,6,8,9)
>>> list.takeWhile({it%2==0})
[]
>>> list.takeWhile({it%2==1})
[1]

>>> val list = listOf(2,4,6,8,9,11,12,16)
>>> list.takeWhile({it%2==0})
[2, 4, 6, 8]

```

#### `takeLast` 挑出后n个元素的子集合

函数定义：

```
public fun <T> List<T>.takeLast(n: Int): List<T> {
    require(n >= 0) { "Requested element count $n is less than zero." }
    if (n == 0) return emptyList()
    val size = size
    if (n >= size) return toList()
    if (n == 1) return listOf(last())
    val list = ArrayList<T>(n)
    if (this is RandomAccess) {
        for (index in size - n .. size - 1)
            list.add(this[index])
    } else {
        for (item in listIterator(n))
            list.add(item)
    }
    return list
}
```

从集合倒数n个元素起，取出到最后一个元素的子集合。如果传入0，返回空集。如果传入n大于集合size，返回整个集合。如果传入负数，直接抛出IllegalArgumentException。

代码示例：

```
>>> val list = listOf(2,4,6,8,9,11,12,16)
>>> list.takeLast(0)
[]
>>> list.takeLast(3)
[11, 12, 16]
>>> list.takeLast(100)
[2, 4, 6, 8, 9, 11, 12, 16]
>>> list.takeLast(-1)
java.lang.IllegalArgumentException: Requested element count -1 is less than zero.
    at kotlin.collections.CollectionsKt___CollectionsKt.takeLast(_Collections.kt:734)

```

#### `takeLastWhile(predicate: (T) -> Boolean)` 从最后开始挑出满足条件元素的子集合

函数定义：

```
public inline fun <T> List<T>.takeLastWhile(predicate: (T) -> Boolean): List<T> {
    if (isEmpty())
        return emptyList()
    val iterator = listIterator(size)
    while (iterator.hasPrevious()) {
        if (!predicate(iterator.previous())) {
            iterator.next()
            val expectedSize = size - iterator.nextIndex()
            if (expectedSize == 0) return emptyList()
            return ArrayList<T>(expectedSize).apply {
                while (iterator.hasNext())
                    add(iterator.next())
            }
        }
    }
    return toList()
}
```

反方向取满足条件的元素，遇到不满足的元素，直接终止循环，并返回子集合。

代码示例：

```
>>> val list = listOf(2,4,6,8,9,11,12,16)
>>> list.takeLastWhile({it%2==0})
[12, 16]
```

#### `drop(n: Int)` 去除前n个元素返回剩下的元素的子集合

函数定义：

```
public fun <T> Iterable<T>.drop(n: Int): List<T> {
    require(n >= 0) { "Requested element count $n is less than zero." }
    if (n == 0) return toList()
    val list: ArrayList<T>
    if (this is Collection<*>) {
        val resultSize = size - n
        if (resultSize <= 0)
            return emptyList()
        if (resultSize == 1)
            return listOf(last())
        list = ArrayList<T>(resultSize)
        if (this is List<T>) {
            if (this is RandomAccess) {
                for (index in n..size - 1)
                    list.add(this[index])
            } else {
                for (item in listIterator(n))
                    list.add(item)
            }
            return list
        }
    }
    else {
        list = ArrayList<T>()
    }
    var count = 0
    for (item in this) {
        if (count++ >= n) list.add(item)
    }
    return list.optimizeReadOnlyList()
}
```

代码示例：

```
>>> val list = listOf(2,4,6,8,9,11,12,16)
>>> list.drop(5)
[11, 12, 16]
>>> list.drop(100)
[]
>>> list.drop(0)
[2, 4, 6, 8, 9, 11, 12, 16]
>>> list.drop(-1)
java.lang.IllegalArgumentException: Requested element count -1 is less than zero.
    at kotlin.collections.CollectionsKt___CollectionsKt.drop(_Collections.kt:538)
```

#### `dropWhile(predicate: (T) -> Boolean)` 去除满足条件的元素返回剩下的元素的子集合

函数定义：

```
public inline fun <T> Iterable<T>.dropWhile(predicate: (T) -> Boolean): List<T> {
    var yielding = false
    val list = ArrayList<T>()
    for (item in this)
        if (yielding)
            list.add(item)
        else if (!predicate(item)) {
            list.add(item)
            yielding = true
        }
    return list
}
```

去除满足条件的元素，当遇到一个不满足条件的元素时，中止操作，返回剩下的元素子集合。

代码示例：

```
>>> val list = listOf(2,4,6,8,9,11,12,16)
>>> list.dropWhile({it%2==0})
[9, 11, 12, 16]

```

#### `dropLast(n: Int)` 从最后去除n个元素

函数定义：

```
public fun <T> List<T>.dropLast(n: Int): List<T> {
    require(n >= 0) { "Requested element count $n is less than zero." }
    return take((size - n).coerceAtLeast(0))
}
```

代码示例：

```
>>> val list = listOf(2,4,6,8,9,11,12,16)
>>> list.dropLast(3)
[2, 4, 6, 8, 9]
>>> list.dropLast(100)
[]
>>> list.dropLast(0)
[2, 4, 6, 8, 9, 11, 12, 16]
>>> list.dropLast(-1)
java.lang.IllegalArgumentException: Requested element count -1 is less than zero.
    at kotlin.collections.CollectionsKt___CollectionsKt.dropLast(_Collections.kt:573)


```

#### `dropLastWhile(predicate: (T) -> Boolean)` 从最后满足条件的元素

函数定义：

```
public inline fun <T> List<T>.dropLastWhile(predicate: (T) -> Boolean): List<T> {
    if (!isEmpty()) {
        val iterator = listIterator(size)
        while (iterator.hasPrevious()) {
            if (!predicate(iterator.previous())) {
                return take(iterator.nextIndex() + 1)
            }
        }
    }
    return emptyList()
}
```

代码示例：

```
>>> val list = listOf(2,4,6,8,9,11,12,16)
>>> list.dropLastWhile({it%2==0})
[2, 4, 6, 8, 9, 11]
```

#### `slice(indices: IntRange)` 取开始下标至结束下标元素子集合

函数定义：

```
public fun <T> List<T>.slice(indices: IntRange): List<T> {
    if (indices.isEmpty()) return listOf()
    return this.subList(indices.start, indices.endInclusive + 1).toList()
}
```

代码示例：

```
val list = listOf(2,4,6,8,9,11,12,16)
>>> list
[2, 4, 6, 8, 9, 11, 12, 16]
>>> list.slice(1..3)
[4, 6, 8]
>>> list.slice(2..7)
[6, 8, 9, 11, 12, 16]
>>> list
[2, 4, 6, 8, 9, 11, 12, 16]
>>> list.slice(1..3)
[4, 6, 8]
>>> list.slice(2..7)
[6, 8, 9, 11, 12, 16]
```

#### `slice(indices: Iterable<Int>) `返回指定下标的元素子集合

函数定义：

```
public fun <T> List<T>.slice(indices: Iterable<Int>): List<T> {
    val size = indices.collectionSizeOrDefault(10)
    if (size == 0) return emptyList()
    val list = ArrayList<T>(size)
    for (index in indices) {
        list.add(get(index))
    }
    return list
}
```

这个函数从签名上看，不是那么简单直接。从函数的定义看，这里的indices是当做原来集合的下标来使用的。

代码示例：

```
>>> list
[2, 4, 6, 8, 9, 11, 12, 16]
>>> list.slice(listOf(2,4,6))
[6, 9, 12]
```

我们可以看出，这里是取出下标为2，4，6的元素。而不是直观理解上的，去掉元素2，4，6。

#### `filterTo(destination: C, predicate: (T) -> Boolean)` 过滤出满足条件的元素并赋值给destination

函数定义：

```
public inline fun <T, C : MutableCollection<in T>> Iterable<T>.filterTo(destination: C, predicate: (T) -> Boolean): C {
    for (element in this) if (predicate(element)) destination.add(element)
    return destination
}
```

把满足过滤条件的元素组成的子集合赋值给入参destination。

代码示例：

```
>>> val list = listOf(1,2,3,4,5,6,7)
>>> val dest = mutableListOf<Int>()
>>> list.filterTo(dest,{it>3})
[4, 5, 6, 7]
>>> dest
[4, 5, 6, 7]
```

#### `filter(predicate: (T) -> Boolean)`过滤出满足条件的元素组成的子集合

函数定义：

```
public inline fun <T> Iterable<T>.filter(predicate: (T) -> Boolean): List<T> {
    return filterTo(ArrayList<T>(), predicate)
}
```

相对于filterTo函数，filter函数更加简单易用。从源码我们可以看出，filter函数直接调用的`filterTo(ArrayList<T>(), predicate)`， 其中入参destination被直接默认赋值为`ArrayList<T>()`。

代码示例：

```
>>> val list = listOf(1,2,3,4,5,6,7)
>>> list.filter({it>3})
[4, 5, 6, 7]
```

另外，还有下面常用的过滤函数：

`filterNot(predicate: (T) -> Boolean)`, 用来过滤所有不满足条件的元素 ；
`filterNotNull()` 过滤掉`null`元素。

### 5.3.7 映射操作符

#### `map(transform: (T) -> R): List<R>`

将集合中的元素通过转换函数`transform`映射后的结果，存到一个集合中返回。

```
>>> val list = listOf(1,2,3,4,5,6,7)
>>> list.map({it})
[1, 2, 3, 4, 5, 6, 7]
>>> list.map({it*it})
[1, 4, 9, 16, 25, 36, 49]
>>> list.map({it+10})
[11, 12, 13, 14, 15, 16, 17]
```

这个函数内部调用的是

```
public inline fun <T, R> Iterable<T>.map(transform: (T) -> R): List<R> {
    return mapTo(ArrayList<R>(collectionSizeOrDefault(10)), transform)
}
```

这里的mapTo函数定义如下：

```
public inline fun <T, R, C : MutableCollection<in R>> Iterable<T>.mapTo(destination: C, transform: (T) -> R): C {
    for (item in this)
        destination.add(transform(item))
    return destination
}
```

我们可以看出，这个map实现的原理是循环遍历原集合中的元素，并把通过transform映射后的结果放到一个新的destination集合中，并返回destination。

#### `mapIndexed(transform: (kotlin.Int, T) -> R)`

转换函数`transform`中带有下标参数。也就是说我们可以同时使用下标和元素的值来进行转换。 其中，第一个参数是Int类型的下标。

代码示例：

```
>>> val list = listOf(1,2,3,4,5,6,7)
>>> list.mapIndexed({index,it -> index*it})
[0, 2, 6, 12, 20, 30, 42]
```

#### `mapNotNull(transform: (T) -> R?)`

遍历集合每个元素，得到通过函数算子transform映射之后的值，剔除掉这些值中的null，返回一个无null元素的集合。

代码示例：

```
>>> val list = listOf("a","b",null,"x",null,"z")
>>> list.mapNotNull({it})
[a, b, x, z]
```

这个函数内部实现是调用的`mapNotNullTo`函数：

```
public inline fun <T, R : Any, C : MutableCollection<in R>> Iterable<T>.mapNotNullTo(destination: C, transform: (T) -> R?): C {
    forEach { element -> transform(element)?.let { destination.add(it) } }
    return destination
}
```

#### `flatMap(transform: (T) -> Iterable<R>): List<R>`

在原始集合的每个元素上调用`transform`转换函数，得到的映射结果组成的单个列表。为了更简单的理解这个函数，我们跟`map(transform: (T) -> R): List<R>`对比下。

首先看函数的各自的实现：

map：

```
public inline fun <T, R> Iterable<T>.map(transform: (T) -> R): List<R> {
    return mapTo(ArrayList<R>(collectionSizeOrDefault(10)), transform)
}

public inline fun <T, R, C : MutableCollection<in R>> Iterable<T>.mapTo(destination: C, transform: (T) -> R): C {
    for (item in this)
        destination.add(transform(item))
    return destination
}
```

flatMap:

```
public inline fun <T, R> Iterable<T>.flatMap(transform: (T) -> Iterable<R>): List<R> {
    return flatMapTo(ArrayList<R>(), transform)
}

public inline fun <T, R, C : MutableCollection<in R>> Iterable<T>.flatMapTo(destination: C, transform: (T) -> Iterable<R>): C {
    for (element in this) {
        val list = transform(element)
        destination.addAll(list)
    }
    return destination
}
```

我们可以看出，这两个函数主要区别在transform函数返回上。

代码示例

```
>>> val list = listOf("a","b","c")
>>> list.map({it->listOf(it+1,it+2,it+3)})
[[a1, a2, a3], [b1, b2, b3], [c1, c2, c3]]
>>> list.flatMap({it->listOf(it+1,it+2,it+3)})
[a1, a2, a3, b1, b2, b3, c1, c2, c3]
```

从代码运行结果我们可以看出，使用 map 是把list中的每一个元素都映射成一个`List-n`，然后以这些`List-n`为元素，组成一个大的嵌套的`List`返回。而使用flatMap则是把list中的第一个元素映射成一个List1，然后把第二个元素映射成的List2跟List1合并：`List1.addAll(List2)`，以此类推。最终返回一个“扁平的”（flat）List。

其实，这个flatMap的过程是 `map + flatten `两个操作的组合。这个flatten函数定义如下：

```
public fun <T> Iterable<Iterable<T>>.flatten(): List<T> {
    val result = ArrayList<T>()
    for (element in this) {
        result.addAll(element)
    }
    return result
}
```

代码示例：

```
>>> val list = listOf("a","b","c")
>>> list.map({it->listOf(it+1,it+2,it+3)})
[[a1, a2, a3], [b1, b2, b3], [c1, c2, c3]]
>>> list.map({it->listOf(it+1,it+2,it+3)}).flatten()
[a1, a2, a3, b1, b2, b3, c1, c2, c3]

```

### 5.3.8 分组操作符

#### `groupBy(keySelector: (T) -> K): Map<K, List<T>>`

将集合中的元素按照条件选择器`keySelector`（是一个函数）分组，并返回Map。
代码示例：

```
>>> val words = listOf("a", "abc", "ab", "def", "abcd")
>>> val lengthGroup = words.groupBy { it.length }
>>> lengthGroup
{1=[a], 3=[abc, def], 2=[ab], 4=[abcd]}
```

#### `groupBy(keySelector: (T) -> K, valueTransform: (T) -> V)`

分组函数还有一个是`groupBy(keySelector: (T) -> K, valueTransform: (T) -> V)`，根据条件选择器keySelector和转换函数valueTransform分组。

代码示例

```
>>> val programmer = listOf("K&R" to "C", "Bjar" to "C++", "Linus" to "C", "James" to "Java")
>>> programmer
[(K&R, C), (Bjar, C++), (Linus, C), (James, Java)]
>>> programmer.groupBy({it.second}, {it.first})
{C=[K&R, Linus], C++=[Bjar], Java=[James]}


```

这里涉及到一个 二元组Pair 类，该类是Kotlin提供的用来处理二元数据组的。 可以理解成Map中的一个键值对，比如Pair(“key”,”value”) 等价于 “key” to “value”。

我们再通过下面的代码示例，来看一下这两个分组的区别：

```
>>> val words = listOf("a", "abc", "ab", "def", "abcd")
>>> words.groupBy( { it.length })
{1=[a], 3=[abc, def], 2=[ab], 4=[abcd]}
>>> words.groupBy( { it.length },{it.contains("b")})
{1=[false], 3=[true, false], 2=[true], 4=[true]}
```

我们可以看出，后者是在前者的基础上又映射了一次`{it.contains("b")}`，把第2次映射的结果放到返回的Map中了。

#### `groupingBy(crossinline keySelector: (T) -> K): Grouping<T, K>`

另外，我们还可以使用`groupingBy(crossinline keySelector: (T) -> K): Grouping<T, K>`函数来创建一个`Grouping`，然后调用计数函数`eachCount`统计分组：

代码示例

```
>>> val words = "one two three four five six seven eight nine ten".split(' ')
>>> words.groupingBy({it.first()}).eachCount()
{o=1, t=3, f=2, s=2, e=1, n=1}
```

上面的例子是统计words列表的元素单词中首字母出现的频数。

其中，`eachCount`函数定义如下：

```
@SinceKotlin("1.1")
@JvmVersion
public fun <T, K> Grouping<T, K>.eachCount(): Map<K, Int> =
        // fold(0) { acc, e -> acc + 1 } optimized for boxing
        foldTo( destination = mutableMapOf(),
                initialValueSelector = { _, _ -> kotlin.jvm.internal.Ref.IntRef() },
                operation = { _, acc, _ -> acc.apply { element += 1 } })
        .mapValuesInPlace { it.value.element }
```

### 5.3.9 排序操作符

#### `reversed(): List<T>`

倒序排列集合元素。
代码示例

```
>>> val list = listOf(1,2,3)
>>> list.reversed()
[3, 2, 1]
```

这个函数，Kotlin是直接调用的`java.util.Collections.reverse()`方法。其相关代码如下：

```
public fun <T> Iterable<T>.reversed(): List<T> {
    if (this is Collection && size <= 1) return toList()
    val list = toMutableList()
    list.reverse()
    return list
}

public fun <T> MutableList<T>.reverse(): Unit {
    java.util.Collections.reverse(this)
}


```

#### `sorted`和`sortedDescending`

升序排序和降序排序。

代码示例

```
>>> val list = listOf(1,3,2)
>>> list.sorted()
[1, 2, 3]
>>> list.sortedDescending()
[3, 2, 1]
```

#### `sortedBy`和`sortedByDescending`

可变集合MutableList的排序操作。根据函数映射的结果进行升序排序和降序排序。
这两个函数定义如下：

```
public inline fun <T, R : Comparable<R>> MutableList<T>.sortBy(crossinline selector: (T) -> R?): Unit {
    if (size > 1) sortWith(compareBy(selector))
}
public inline fun <T, R : Comparable<R>> MutableList<T>.sortByDescending(crossinline selector: (T) -> R?): Unit {
    if (size > 1) sortWith(compareByDescending(selector))
}
```

代码示例

```
>>> val mlist = mutableListOf("abc","c","bn","opqde","")
>>> mlist.sortBy({it.length})
>>> mlist
[, c, bn, abc, opqde]
>>> mlist.sortByDescending({it.length})
>>> mlist
[opqde, abc, bn, c, ]

```

### 5.3.10 生产操作符

#### `zip(other: Iterable<R>): List<Pair<T, R>>`

两个集合按照下标配对，组合成的每个Pair作为新的List集合中的元素，并返回。

如果两个集合长度不一样，取短的长度。

代码示例

```
>>> val list1 = listOf(1,2,3)
>>> val list2 = listOf(4,5,6,7)
>>> val list3 = listOf("x","y","z")
>>> list1.zip(list3)
[(1, x), (2, y), (3, z)]
>>> list3.zip(list1)
[(x, 1), (y, 2), (z, 3)]
>>> list2.zip(list3)
[(4, x), (5, y), (6, z)]  // 取短的长度
>>> list3.zip(list2)
[(x, 4), (y, 5), (z, 6)]
>>> list1.zip(listOf<Int>())
[]
```

这个zip函数的定义如下：

```
public infix fun <T, R> Iterable<T>.zip(other: Iterable<R>): List<Pair<T, R>> {
    return zip(other) { t1, t2 -> t1 to t2 }
}
```

我们可以看出，其内部是调用了`zip(other) { t1, t2 -> t1 to t2 }`。这个函数定义如下：

```
public inline fun <T, R, V> Iterable<T>.zip(other: Iterable<R>, transform: (a: T, b: R) -> V): List<V> {
    val first = iterator()
    val second = other.iterator()
    val list = ArrayList<V>(minOf(collectionSizeOrDefault(10), other.collectionSizeOrDefault(10)))
    while (first.hasNext() && second.hasNext()) {
        list.add(transform(first.next(), second.next()))
    }
    return list
}
```

依次取两个集合相同索引的元素，使用提供的转换函数transform得到映射之后的值，作为元素组成一个新的List，并返回该List。列表的长度取两个集合中最短的。

代码示例

```
>>> val list1 = listOf(1,2,3)
>>> val list2 = listOf(4,5,6,7)
>>> val list3 = listOf("x","y","z")
>>> list1.zip(list3, {t1,t2 -> t2+t1})
[x1, y2, z3]
>>> list1.zip(list2, {t1,t2 -> t1*t2})
[4, 10, 18]
```

#### `unzip(): Pair<List<T>, List<R>>`

首先这个函数作用在元素是Pair的集合类上。依次取各个Pair元素的first, second值，分别放到List<T>、List<R>中，然后返回一个first为List<T>，second为List<R>的大的Pair。

函数定义

```
public fun <T, R> Iterable<Pair<T, R>>.unzip(): Pair<List<T>, List<R>> {
    val expectedSize = collectionSizeOrDefault(10)
    val listT = ArrayList<T>(expectedSize)
    val listR = ArrayList<R>(expectedSize)
    for (pair in this) {
        listT.add(pair.first)
        listR.add(pair.second)
    }
    return listT to listR
}
```

看到这里，仍然有点抽象，我们直接看代码示例：

```
>>> val listPair = listOf(Pair(1,2),Pair(3,4),Pair(5,6))
>>> listPair
[(1, 2), (3, 4), (5, 6)]
>>> listPair.unzip()
([1, 3, 5], [2, 4, 6])
```

#### `partition(predicate: (T) -> Boolean): Pair<List<T>, List<T>>`

根据判断条件是否成立，将集合拆分成两个子集合组成的 Pair。我们可以直接看函数的定义来更加清晰的理解这个函数的功能：

```
public inline fun <T> Iterable<T>.partition(predicate: (T) -> Boolean): Pair<List<T>, List<T>> {
    val first = ArrayList<T>()
    val second = ArrayList<T>()
    for (element in this) {
        if (predicate(element)) {
            first.add(element)
        } else {
            second.add(element)
        }
    }
    return Pair(first, second)
}
```

我们可以看出，这是一个内联函数。

代码示例

```
>>> val list = listOf(1,2,3,4,5,6,7,8,9)
>>> list
[1, 2, 3, 4, 5, 6, 7, 8, 9]
>>> list.partition({it>5})
([6, 7, 8, 9], [1, 2, 3, 4, 5])
```

#### `plus(elements: Iterable<T>): List<T>`

合并两个List。

函数定义

```
public operator fun <T> Iterable<T>.plus(elements: Iterable<T>): List<T> {
    if (this is Collection) return this.plus(elements)
    val result = ArrayList<T>()
    result.addAll(this)
    result.addAll(elements)
    return result
}
```

我们可以看出，这是一个操作符函数。可以用”+”替代 。

代码示例

```
>>> val list1 = listOf(1,2,3)
>>> val list2 = listOf(4,5)
>>> list1.plus(list2)
[1, 2, 3, 4, 5]
>>> list1+list2
[1, 2, 3, 4, 5]
```

关于plus函数还有以下的重载函数：

```
plus(element: T): List<T>
plus(elements: Array<out T>): List<T>
plus(elements: Sequence<T>): List<T>
```

等。

#### `plusElement(element: T): List<T>`

在集合中添加一个元素。
函数定义

```
@kotlin.internal.InlineOnly
public inline fun <T> Iterable<T>.plusElement(element: T): List<T> {
    return plus(element)
}
```

我们可以看出，这个函数内部是直接调用的`plus(element: T): List<T>`。

代码示例

```
>>> list1 + 10
[1, 2, 3, 10]
>>> list1.plusElement(10)
[1, 2, 3, 10]
>>> list1.plus(10)
[1, 2, 3, 10]
```

## 5.4 Set

类似的，Kotlin中的Set也分为：不可变Set和支持增加和删除的可变MutableSet。

不可变Set同样是继承了Collection。MutableSet接口继承于Set, MutableCollection，同时对Set进行扩展，添加了对元素添加和删除等操作。

Set的类图结构如下：

![img](https://segmentfault.com/img/remote/1460000010313211)

### 5.4.1 空集

万物生于无。我们先来看下Kotlin中的空集：

```
internal object EmptySet : Set<Nothing>, Serializable {
    private const val serialVersionUID: Long = 3406603774387020532

    override fun equals(other: Any?): Boolean = other is Set<*> && other.isEmpty()
    override fun hashCode(): Int = 0
    override fun toString(): String = "[]"

    override val size: Int get() = 0
    override fun isEmpty(): Boolean = true
    override fun contains(element: Nothing): Boolean = false
    override fun containsAll(elements: Collection<Nothing>): Boolean = elements.isEmpty()

    override fun iterator(): Iterator<Nothing> = EmptyIterator

    private fun readResolve(): Any = EmptySet
}
```

空集继承了Serializable，表明是可被序列化的。它的size是0， isEmpty()返回true，hashCode()也是0。

下面是创建一个空集的代码示例：

```
>>> val emptySet = emptySet<Int>()
>>> emptySet
[]
>>> emptySet.size
0
>>> emptySet.isEmpty()
true
>>> emptySet.hashCode()
0
```

### 5.4.2 创建Set

#### `setOf`

首先，Set中的元素是不可重复的（任意两个元素 x, y 都不相等）。这里的元素 x, y 不相等的意思是：

```
x.hashCode() != y.hashCode() 
!x.equals(y) 

```

上面两个表达式值都为true 。

代码示例

```
>>> val list = listOf(1,1,2,3,3)
>>> list
[1, 1, 2, 3, 3]
>>> val set = setOf(1,1,2,3,3)
>>> set
[1, 2, 3]
```

Kotlin跟Java一样的，判断两个对象的是否重复标准是hashCode()和equals()两个参考值，也就是说只有两个对象的hashCode值一样与equals()为真时，才认为是相同的对象。所以自定义的类必须要要重写hashCode()和equals()两个函数。作为Java程序员，这里一般都会注意到。

创建多个元素的Set使用的函数是

```
setOf(vararg elements: T): Set<T> = if (elements.size > 0) elements.toSet() else emptySet()
```

这个toSet()函数是Array类的扩展函数，定义如下

```
public fun <T> Array<out T>.toSet(): Set<T> {
    return when (size) {
        0 -> emptySet()
        1 -> setOf(this[0])
        else -> toCollection(LinkedHashSet<T>(mapCapacity(size)))
    }
}
```

我们可以看出，setOf函数背后实际上用的是LinkedHashSet构造函数。关于创建Set的初始容量的算法是：

```
@PublishedApi
internal fun mapCapacity(expectedSize: Int): Int {
    if (expectedSize < 3) {
        return expectedSize + 1
    }
    if (expectedSize < INT_MAX_POWER_OF_TWO) {
        return expectedSize + expectedSize / 3
    }
    return Int.MAX_VALUE // 2147483647， any large value
}
```

也就是说，当元素个数n小于3，初始容量为n+1;
当元素个数n小于`2147483647 / 2 + 1` , 初始容量为 `n + n/3`;
否则，初始容量为`2147483647`。

如果我们想对一个List去重，可以直接使用下面的方式

```
>>> list.toSet()
[1, 2, 3]
```

上文我们使用`emptySet<Int>()`来创建空集，我们也可以使用setOf()来创建空集：

```
>>> val s = setOf<Int>()
>>> s
[]
```

创建1个元素的Set：

```
>>> val s = setOf<Int>(1)
>>> s
[1]
```

这个函数调用的是`setOf(element: T): Set<T> = java.util.Collections.singleton(element)`, 也是Java的Collections类里的方法。

#### `mutableSetOf(): MutableSet<T>`

创建一个可变Set。

函数定义

```
@SinceKotlin("1.1")
@kotlin.internal.InlineOnly
public inline fun <T> mutableSetOf(): MutableSet<T> = LinkedHashSet()
```

这个`LinkedHashSet()`构造函数背后实际上是`java.util.LinkedHashSet<E>`， 这就是Kotlin中的类型别名。

### 5.4.3 使用Java中的Set类

包kotlin.collections下面的TypeAliases.kt类中，有一些类型别名的定义如下：

```
@file:kotlin.jvm.JvmVersion

package kotlin.collections

@SinceKotlin("1.1") public typealias RandomAccess = java.util.RandomAccess


@SinceKotlin("1.1") public typealias ArrayList<E> = java.util.ArrayList<E>
@SinceKotlin("1.1") public typealias LinkedHashMap<K, V> = java.util.LinkedHashMap<K, V>
@SinceKotlin("1.1") public typealias HashMap<K, V> = java.util.HashMap<K, V>
@SinceKotlin("1.1") public typealias LinkedHashSet<E> = java.util.LinkedHashSet<E>
@SinceKotlin("1.1") public typealias HashSet<E> = java.util.HashSet<E>


// also @SinceKotlin("1.1")
internal typealias SortedSet<E> = java.util.SortedSet<E>
internal typealias TreeSet<E> = java.util.TreeSet<E>
```

从这里，我们可以看出，Kotlin中的`LinkedHashSet` , `HashSet`, `SortedSet`, `TreeSet` 就是直接使用的Java中的对应的集合类。

对应的创建的方法是

```
hashSetOf
linkedSetOf
mutableSetOf
sortedSetOf
```

代码示例如下：

```
>>> val hs = hashSetOf(1,3,2,7)
>>> hs
[1, 2, 3, 7]
>>> hs::class
class java.util.HashSet
>>> val ls = linkedSetOf(1,3,2,7)
>>> ls
[1, 3, 2, 7]
>>> ls::class
class java.util.LinkedHashSet
>>> val ms = mutableSetOf(1,3,2,7)
>>> ms
[1, 3, 2, 7]
>>> ms::class
class java.util.LinkedHashSet
>>> val ss = sortedSetOf(1,3,2,7)
>>> ss
[1, 2, 3, 7]
>>> ss::class
class java.util.TreeSet
```

我们知道在Java中，Set接口有两个主要的实现类HashSet和TreeSet：

HashSet : 该类按照哈希算法来存取集合中的对象，存取速度较快。
TreeSet : 该类实现了SortedSet接口，能够对集合中的对象进行排序。
LinkedHashSet：具有HashSet的查询速度，且内部使用链表维护元素的顺序，在对Set元素进行频繁插入、删除的场景中使用。

Kotlin并没有单独去实现一套HashSet、TreeSet和LinkedHashSet。如果我们在实际开发过程中，需要用到这些Set, 就可以直接用上面的方法。

### 5.4.4 Set元素的加减操作 `plus` `minus`

Kotlin中针对Set做了一些加减运算的扩展函数, 例如：

```
operator fun <T> Set<T>.plus(element: T)
plusElement(element: T)
plus(elements: Iterable<T>)
operator fun <T> Set<T>.minus(element: T)
minusElement(element: T)
minus(elements: Iterable<T>)
```

代码示例：

```
>>> val ms = mutableSetOf(1,3,2,7)
>>> ms+10
[1, 3, 2, 7, 10]
>>> ms-1
[3, 2, 7]
>>> 
>>> ms + listOf(8,9)
[1, 3, 2, 7, 8, 9]
>>> ms - listOf(8,9)
[1, 3, 2, 7]
>>> ms - listOf(1,3)
[2, 7]
```

## 5.5 Map

### 5.5.1 Map概述

Map是一种把键对象Key和值对象Value映射的集合，它的每一个元素都包含一对键对象和值对象（K-V Pair）。 Key可以看成是Value 的索引，作为key的对象在集合中不可重复（uniq）。

如果我们从数据结构的本质上来看，其实List就是Key是Int类型下标的特殊的Map。而Set也是Key为Int，但是Value值不能重复的特殊Map。

Kotlin中的Map与List、Set一样，Map也分为只读Map和可变的MutableMap。

Map没有继承于Collection接口。其类图结构如下：

![img](https://segmentfault.com/img/remote/1460000010313212)

在接口`interface Map<K, out V>`中，K是键值的类型，V是对应的映射值的类型。这里的`out V`表示类型为V或V的子类。这是泛型的相关知识，我们将在下一章节中介绍。

其中，`Entry<out K, out V>`中保存的是Map的键值对。

### 5.5.2 创建Map

跟Java相比不同的是，在Kotlin中的Map区分了只读的Map和可编辑的Map（MutableMap、HashMap、LinkedHashMap）。

Kotlin没有自己重新去实现一套集合类，而是在Java的集合类基础上做了一些扩展。

我们知道在Java中，根据内部数据结构的不同，Map 接口通常有多种实现类。

其中常用的有：

- HashMap

HashMap是基于哈希表（hash table）的 Map 接口的实现，以key-value的形式存在。在HashMap中，key-value是一个整体，系统会根据hash算法来来计算key-value的存储位置，我们可以通过key快速地存取value。它允许使用 null 值和 null 键。

另外，HashMap中元素的顺序，随着时间的推移会发生变化。

- TreeMap

使用红黑二叉树（red-black tree）的 Map 接口的实现。

- LinkedHashMap

还有继承了HashMap，并使用链表实现的LinkedHashMap。LinkedHashMap保存了记录的插入顺序，在用Iterator遍历LinkedHashMap时，先得到的记录是先插入的记录。简单说，LinkedHashMap是有序的，它使用链表维护内部次序。

我们在使用Kotlin创建Map的时候，实际上大部分都是调用Java的Map的方法。

下面我们就来介绍Map的创建以及基本操作函数。

#### `mapOf()`

创建一个只读空Map。

```
>>> val map1 = mapOf<String, Int>()
>>> map1.size
0
>>> map1.isEmpty()
true
```

我们还可以用另外一个函数创建空Map：

```
>>> val map2 = emptyMap<String, Int>()
>>> map2.size
0
>>> map2.isEmpty()
true
```

空Map都是相等的：

```
>>> map2==map1
true
```

这个空Map是只读的，其属性和函数返回都是预定义好的。其代码如下：

```
private object EmptyMap : Map<Any?, Nothing>, Serializable {
    private const val serialVersionUID: Long = 8246714829545688274

    override fun equals(other: Any?): Boolean = other is Map<*,*> && other.isEmpty()
    override fun hashCode(): Int = 0
    override fun toString(): String = "{}"

    override val size: Int get() = 0
    override fun isEmpty(): Boolean = true

    override fun containsKey(key: Any?): Boolean = false
    override fun containsValue(value: Nothing): Boolean = false
    override fun get(key: Any?): Nothing? = null
    override val entries: Set<Map.Entry<Any?, Nothing>> get() = EmptySet
    override val keys: Set<Any?> get() = EmptySet
    override val values: Collection<Nothing> get() = EmptyList

    private fun readResolve(): Any = EmptyMap
}
```

#### `mapOf(pair: Pair<K, V>): Map<K, V>`

使用二元组Pair创建一个只读Map。

```
>>> val map = mapOf(1 to "x", 2 to "y", 3 to "z")
>>> map
{1=x, 2=y, 3=z}
>>> map.get(1)
x
>>> map.get(3)
z
>>> map.size
3
>>> map.entries
[1=x, 2=y, 3=z]
```

这个创建函数内部是调用的LinkedHashMap构造函数，其相关代码如下：

```
pairs.toMap(LinkedHashMap(mapCapacity(pairs.size)))
```

如果我们想编辑这个Map， 编译器会直接报错

```
>>> map[1]="a"
error: unresolved reference. None of the following candidates is applicable because of receiver type mismatch: 
@InlineOnly public operator inline fun <K, V> MutableMap<Int, String>.set(key: Int, value: String): Unit defined in kotlin.collections
@InlineOnly public operator inline fun kotlin.text.StringBuilder /* = java.lang.StringBuilder */.set(index: Int, value: Char): Unit defined in kotlin.text
map[1]="a"
^
error: no set method providing array access
map[1]="a"
   ^
```

因为在不可变（Immutable）Map中，根本就没有提供set函数。

#### `mutableMapOf()`

创建一个空的可变的Map。

```
>>> val map = mutableMapOf<Int, Any?>()
>>> map.isEmpty()
true
>>> map[1] = "x"
>>> map[2] = 1
>>> map
{1=x, 2=1}
```

该函数直接是调用的LinkedHashMap()构造函数。

#### `mutableMapOf(vararg pairs: Pair<K, V>): MutableMap<K, V>`

创建一个可编辑的MutableMap对象。

```
>>> val map = mutableMapOf(1 to "x", 2 to "y", 3 to "z")
>>> map
{1=x, 2=y, 3=z}
>>> map[1]="a"
>>> map
{1=a, 2=y, 3=z}
```

另外，如果Map中有重复的key键，后面的会直接覆盖掉前面的：

```
>>> val map = mutableMapOf(1 to "x", 2 to "y", 1 to "z")
>>> map
{1=z, 2=y}
```

后面的`1 to "z"`直接把前面的`1 to "x"`覆盖掉了。

#### `hashMapOf(): HashMap<K, V>`

创建HashMap对象。Kotlin直接使用的是Java的HashMap。

```
>>> val map: HashMap<Int, String> = hashMapOf(1 to "x", 2 to "y", 3 to "z")
>>> map
{1=x, 2=y, 3=z}
```

#### `linkedMapOf(): LinkedHashMap<K, V>`

创建空对象LinkedHashMap。直接使用的是Java中的LinkedHashMap。

```
>>> val map: LinkedHashMap<Int, String> = linkedMapOf()
>>> map
{}
>>> map[1]="x"
>>> map
{1=x}
```

#### `linkedMapOf(vararg pairs: Pair<K, V>): LinkedHashMap<K, V>`

创建带二元组Pair元素的LinkedHashMap对象。直接使用的是Java中的LinkedHashMap。

```
>>> val map: LinkedHashMap<Int, String> = linkedMapOf(1 to "x", 2 to "y", 3 to "z")
>>> map
{1=x, 2=y, 3=z}
>>> map[1]="a"
>>> map
{1=a, 2=y, 3=z}
```

#### `sortedMapOf(vararg pairs: Pair<K, V>): SortedMap<K, V>`

创建一个根据Key升序排序好的TreeMap。对应的是使用Java中的SortedMap。

```
>>> val map = sortedMapOf(Pair("c", 3), Pair("b", 2), Pair("d", 1))
>>> map
{b=2, c=3, d=1}
```

### 5.5.3 访问Map的元素

#### entries属性

我们可以直接访问entries属性

```
val entries: Set<Entry<K, V>>
```

获取该Map中的所有键/值对的Set。这个Entry类型定义如下：

```
 public interface Entry<out K, out V> {
        public val key: K
        public val value: V
    }
```

代码示例

```
>>> val map = mapOf("x" to 1, "y" to 2, "z" to 3)
>>> map
{x=1, y=2, z=3}
>>> map.entries
[x=1, y=2, z=3]
```

这样，我们就可以遍历这个Entry的Set了：

```
>>> map.entries.forEach({println("key="+ it.key + " value=" + it.value)})
key=x value=1
key=y value=2
key=z value=3
```

#### keys属性

访问keys属性：

```
val keys: Set<K>
```

获取Map中的所有键的Set。

```
>>> map.keys
[x, y, z]
```

#### values属性

访问` val values: Collection<V>`获取Map中的所有值的Collection。这个值的集合可能包含重复值。

```
>>> map.values
[1, 2, 3]
```

#### size属性

访问`val size: Int`获取map键/值对的数目。

```
>>> map.size
3
```

#### get(key: K)

我们使用get函数来通过key来获取value的值。

```
operator fun get(key: K): V?
```

对应的操作符是`[]`：

```
>>> map["x"]
1
>>> map.get("x")
1

```

如果这个key不在Map中，就返回null。

```
>>> map["k"]
null
```

如果不想返回null，可以使用getOrDefault函数

```
getOrDefault(key: K, defaultValue: @UnsafeVariance V): V
```

当为null时，不返回null，而是返回设置的一个默认值：

```
>>> map.getOrDefault("k",0)
0
```

这个默认值的类型，要和V对应。类型不匹配会报错：

```
>>> map.getOrDefault("k","a")
error: type mismatch: inferred type is String but Int was expected
map.getOrDefault("k","a")
                     ^
```

### 5.5.4 Map操作符函数

#### `containsKey(key: K): Boolean`

是否包含该key。

```
>>> val map = mapOf("x" to 1, "y" to 2, "z" to 3)
>>> map.containsKey("x")
true
>>> map.containsKey("j")
false
```

#### `containsValue(value: V): Boolean`

是否包含该value。

```
>>> val map = mapOf("x" to 1, "y" to 2, "z" to 3)
>>> map.containsValue(2)
true
>>> map.containsValue(20)
false
```

#### `component1()` `component2()`

`Map.Entry<K, V>`的操作符函数，分别用来直接访问key和value。

```
>>> val map = mapOf("x" to 1, "y" to 2, "z" to 3)
>>> map.entries.forEach({println("key="+ it.component1() + " value=" + it.component2())})
key=x value=1
key=y value=2
key=z value=3
```

这两个函数的定义如下：

```
@kotlin.internal.InlineOnly
public inline operator fun <K, V> Map.Entry<K, V>.component1(): K = key

@kotlin.internal.InlineOnly
public inline operator fun <K, V> Map.Entry<K, V>.component2(): V = value
```

#### `Map.Entry<K, V>.toPair(): Pair<K, V>`

把Map的Entry转换为Pair。

```
>>> map.entries
[x=1, y=2, z=3]
>>> map.entries.forEach({println(it.toPair())})
(x, 1)
(y, 2)
(z, 3)
```

#### `getOrElse(key: K, defaultValue: () -> V): V`

通过key获取值，当没有值可以设置默认值。

```
>>> val map = mutableMapOf<String, Int?>()
>>> map.getOrElse("x", { 1 })
1
>>> map["x"] = 3
>>> map.getOrElse("x", { 1 })
3
```

#### `getValue(key: K): V`

当Map中不存在这个key，调用get函数，如果不想返回null，直接抛出异常，可调用此方法。

```
val map = mutableMapOf<String, Int?>()
>>> map.get("v")
null
>>> map.getValue("v")
java.util.NoSuchElementException: Key v is missing in the map.
    at kotlin.collections.MapsKt__MapWithDefaultKt.getOrImplicitDefaultNullable(MapWithDefault.kt:19)
    at kotlin.collections.MapsKt__MapsKt.getValue(Maps.kt:252)
```

#### `getOrPut(key: K, defaultValue: () -> V): V`

如果不存在这个key，就添加这个key到Map中，对应的value是defaultValue。

```
>>> val map = mutableMapOf<String, Int?>()
>>> map.getOrPut("x", { 2 })
2
>>> map
{x=2}
```

#### `iterator(): Iterator<Map.Entry<K, V>>`

这个函数返回的是 `entries.iterator()`。这样我们就可以像下面这样使用for循环来遍历Map：

```
>>> val map = mapOf("x" to 1, "y" to 2, "z" to 3 )
>>> for((k,v) in map){println("key=$k, value=$v")}
key=x, value=1
key=y, value=2
key=z, value=3
```

#### `mapKeys(transform: (Map.Entry<K, V>) -> R): Map<R, V>`

把Map的Key设置为通过转换函数transform映射之后的值。

```
>>> val map:Map<Int,String> = mapOf(1 to "a", 2 to "b", 3 to "c", -1 to "z")
>>> val mmap = map.mapKeys{it.key * 10}
>>> mmap
{10=a, 20=b, 30=c, -10=z}
```

注意，这里的it是Map的Entry。
如果不巧，有任意两个key通过映射之后相等了，那么后面的key将会覆盖掉前面的key。

```
>>> val mmap = map.mapKeys{it.key * it.key}
>>> mmap
{1=z, 4=b, 9=c}
```

我们可以看出，`1 to "a"`被`-1 to "z"`覆盖掉了。

#### `mapValues(transform: (Map.Entry<K, V>) -> R): Map<K, R>`

对应的这个函数是把Map的value设置为通过转换函数transform转换之后的新值。

```
>>> val map:Map<Int,String> = mapOf(1 to "a", 2 to "b", 3 to "c", -1 to "z")
>>> val mmap = map.mapValues({it.value + "$"})
>>> mmap
{1=a$, 2=b$, 3=c$, -1=z$}
```

#### `filterKeys(predicate: (K) -> Boolean): Map<K, V>`

返回过滤出满足key判断条件的元素组成的新Map。

```
>>> val map:Map<Int,String> = mapOf(1 to "a", 2 to "b", 3 to "c", -1 to "z")
>>> map.filterKeys({it>0})
{1=a, 2=b, 3=c}
```

注意，这里的it元素是Key。

#### `filterValues(predicate: (V) -> Boolean): Map<K, V>`

返回过滤出满足value判断条件的元素组成的新Map。

```
>>> val map:Map<Int,String> = mapOf(1 to "a", 2 to "b", 3 to "c", -1 to "z")
>>> map.filterValues({it>"b"})
{3=c, -1=z}
```

注意，这里的it元素是value。

#### `filter(predicate: (Map.Entry<K, V>) -> Boolean): Map<K, V>`

返回过滤出满足Entry判断条件的元素组成的新Map。

```
>>> val map:Map<Int,String> = mapOf(1 to "a", 2 to "b", 3 to "c", -1 to "z")
>>> map.filter({it.key>0 && it.value > "b"})
{3=c}
```

#### `Iterable<Pair<K, V>>.toMap(destination: M): M`

把持有Pair的Iterable集合转换为Map。

```
>>> val pairList = listOf(Pair(1,"a"),Pair(2,"b"),Pair(3,"c"))
>>> pairList
[(1, a), (2, b), (3, c)]
>>> pairList.toMap()
{1=a, 2=b, 3=c}
```

#### `Map<out K, V>.toMutableMap(): MutableMap<K, V>`

把一个只读的Map转换为可编辑的MutableMap。

```
>>> val map = mapOf(1 to "a", 2 to "b", 3 to "c", -1 to "z")
>>> map[1]="x"
error: unresolved reference. None of the following candidates is applicable ...
error: no set method providing array access
map[1]="x"
   ^

>>> val mutableMap = map.toMutableMap()
>>> mutableMap
{1=a, 2=b, 3=c, -1=z}
>>> mutableMap[1]="x"
>>> mutableMap
{1=x, 2=b, 3=c, -1=z}
```

#### `plus` `minus`

Map的加法运算符函数如下：

```
operator fun <K, V> Map<out K, V>.plus(pair: Pair<K, V>): Map<K, V>
operator fun <K, V> Map<out K, V>.plus(pairs: Iterable<Pair<K, V>>): Map<K, V>
operator fun <K, V> Map<out K, V>.plus(pairs: Array<out Pair<K, V>>): Map<K, V>
operator fun <K, V> Map<out K, V>.plus(pairs: Sequence<Pair<K, V>>): Map<K, V>
operator fun <K, V> Map<out K, V>.plus(map: Map<out K, V>): Map<K, V>
```

代码示例：

```
>>> val map = mapOf(1 to "a", 2 to "b", 3 to "c", -1 to "z")
>>> map+Pair(10,"g")
{1=a, 2=b, 3=c, -1=z, 10=g}
>>> map + listOf(Pair(9,"s"),Pair(10,"w"))
{1=a, 2=b, 3=c, -1=z, 9=s, 10=w}
>>> map + arrayOf(Pair(9,"s"),Pair(10,"w"))
{1=a, 2=b, 3=c, -1=z, 9=s, 10=w}
>>> map + sequenceOf(Pair(9,"s"),Pair(10,"w"))
{1=a, 2=b, 3=c, -1=z, 9=s, 10=w}
>>> map + mapOf(9 to "s", 10 to "w")
{1=a, 2=b, 3=c, -1=z, 9=s, 10=w}
```

加并赋值函数：

```
inline operator fun <K, V> MutableMap<in K, in V>.plusAssign(pair: Pair<K, V>)
inline operator fun <K, V> MutableMap<in K, in V>.plusAssign(pairs: Iterable<Pair<K, V>>)
inline operator fun <K, V> MutableMap<in K, in V>.plusAssign(pairs: Array<out Pair<K, V>>)
inline operator fun <K, V> MutableMap<in K, in V>.plusAssign(pairs: Sequence<Pair<K, V>>)
inline operator fun <K, V> MutableMap<in K, in V>.plusAssign(map: Map<K, V>)

```

代码示例：

```
>>> val map = mutableMapOf(1 to "a", 2 to "b", 3 to "c", -1 to "z")
>>> map+=Pair(10,"g")
>>> map
{1=a, 2=b, 3=c, -1=z, 10=g}
>>> map += listOf(Pair(9,"s"),Pair(11,"w"))
>>> map
{1=a, 2=b, 3=c, -1=z, 10=g, 9=s, 11=w}
>>> map += mapOf(20 to "qq", 30 to "tt")
>>> map
{1=a, 2=b, 3=c, -1=z, 10=g, 9=s, 11=w, 20=qq, 30=tt}
```

减法跟加法类似。

#### `put(key: K, value: V): V?`

根据key设置元素的value。如果该key存在就更新value；不存在就添加，但是put的返回值是null。

```
>>> val map = mutableMapOf(1 to "a", 2 to "b", 3 to "c", -1 to "z")
>>> map
{1=a, 2=b, 3=c, -1=z}
>>> map.put(10,"q")
null
>>> map
{1=a, 2=b, 3=c, -1=z, 10=q}
>>> map.put(1,"f")
a
>>> map
{1=f, 2=b, 3=c, -1=z, 10=q}
```

#### `putAll(from: Map<out K, V>): Unit`

把一个Map全部添加到一个MutableMap中。

```
>>> val map = mutableMapOf(1 to "a", 2 to "b", 3 to "c", -1 to "z")
>>> val map2 = mapOf(99 to "aa", 100 to "bb")
>>> map.putAll(map2)
>>> map
{1=a, 2=b, 3=c, -1=z, 99=aa, 100=bb}
```

如果有key重复的，后面的值会覆盖掉前面的值：

```
>>> map
{1=a, 2=b, 3=c, -1=z, 99=aa, 100=bb}
>>> map.putAll(mapOf(1 to "www",2 to "tttt"))
>>> map
{1=www, 2=tttt, 3=c, -1=z, 99=aa, 100=bb}
```

#### `MutableMap<out K, V>.remove(key: K): V?`

根据键值key来删除元素。

```
>>> val map = mutableMapOf(1 to "a", 2 to "b", 3 to "c", -1 to "z")
>>> map.remove(-1)
z
>>> map
{1=a, 2=b, 3=c}
>>> map.remove(100)
null
>>> map
{1=a, 2=b, 3=c}
```

#### `MutableMap<K, V>.clear(): Unit`

清空MutableMap。

```
>>> val map = mutableMapOf(1 to "a", 2 to "b", 3 to "c", -1 to "z")
>>> map
{1=a, 2=b, 3=c, -1=z}
>>> map.clear()
>>> map
{}
```

## 本章小结

本章我们介绍了Kotlin标准库中的集合类List、Set、Map，以及它们扩展的丰富的操作函数，这些函数使得我们使用这些集合类更加简单容易。

集合类持有的是对象，而怎样的放入正确的对象类型则是我们写代码过程中需要注意的。下一章节中我们将学习泛型。