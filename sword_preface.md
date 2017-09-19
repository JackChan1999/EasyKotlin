## 前言

Kotlin是JetBrains团队开发的一门现代的、注重工程实用性的静态类型编程语言，JetBrains团队以开发了世界上最好用的IDE而著称。Kotlin于2010年推出，并在2011年开源。Kotlin充分借鉴汲取了 Java、Scala、Groovy、C#、Gosu、JavaScript、Swift等多门杰出语言的优秀特性，语法简单优雅、表现力丰富、抽象扩展方便、代码可重用性好，同时也支持面向对象和函数式编程的多范式编程。Kotlin可以编译成Java字节码运行在JVM平台和Android平台，也可以编译成JavaScript运行在浏览器环境，而且还可以直接编译成机器码的系统级程序，直接运行在嵌入式、iOS、MacOS/Linux/Windows等没有JVM环境的平台。Kotlin源自产业界，它解决了工程实践中程序设计所面临的真实痛点，例如，类型系统可以避免空指针异常的问题。

我最早是被Kotlin的下面这段代码所吸引：

```kotlin
package com.easy.kotlin

fun <A, B, C> compose(f: (B) -> C, g: (A) -> B): (A) -> C {
    return { x -> f(g(x)) }
}

fun isOdd(x: Int) = x % 2 != 0
fun length(s: String) = s.length
fun main(args: Array<String>) {
    val oddLength = compose(::isOdd, ::length)
    val strings = listOf("a", "ab", "abc")
    println(strings.filter(oddLength))
}
```

13行。

这大约是在三年前，当时我在学习 Java 8 中的函数式编程以及 Lambda 表达式等新特性。那时，我也对Scala、Groovy、Clojure、Haskell等技术很感兴趣，简单学习了部分知识。在这样伴随着兴趣的学习过程中，我无意中看到了上面那段Kotlin代码，第一眼看到这么优雅的函数式编程风格，尤其是compose函数的定义实现，我被深深地吸引了。

Swift 使用func关键字声明函数多个 c 怪怪的，Groovy、Scala 等使用 def 跟函数本义联想不直接 ，JavaScript 中使用 function 关键字又显得死板了些。而 Kotlin 中的 fun 则简单优雅地恰到好处，关键还让人自然联想到“乐趣、开心的、使人愉快的”这样的意思。使用 Kotlin每写一个函数都是充满乐趣的。

我们不妨来看看同样的逻辑实现，如果我们使用 Java 8来写，代码如下所示
```kotlin
package com.easy.kotlin;

import java.util.ArrayList;
import java.util.List;

interface G<A, B> {
    B apply(A a);
}

interface F<B, C> {
    C apply(B b);
}

interface FG<A, B, C> {
    C apply(A a);
}

public class ComposeFunInJava<A, B, C> {
    public static void main(String[] args) {
        G<String, Integer> g = (s) -> s.length();
        F<Integer, Boolean> f = (x) -> x % 2 != 0;
        FG<String, Integer, Boolean> fg = (x) -> f.apply(g.apply(x));

        List<String> strings = new ArrayList();
        strings.add("a");
        strings.add("ab");
        strings.add("abc");
        List<String> result = new ArrayList();
        for (String s : strings) {
            if (fg.apply(s)) {
                result.add(s);
            }
        }
        System.out.println(result);
    }
}
```
36行，差不多是 Kotlin 的3倍。

我们知道，Java是一门非常优秀的面向对象语言。但是在函数式编程方面，跟其他函数语言相比，还是显得有些笨重与生涩，并且其内在体现出来的思想，依旧是面向对象的思想。

而功能强大的Scala 语言，复杂性却相对较高，学习成本也远高于 Kotlin。 另外，Scala 与 Java 的互操作性没有 Kotlin 好。所以，如果我们既想方便地流畅地使用 Java 的强大且完善的生态库，又想使用更加先进的编程语言的特性，无疑 Kotlin 是个非常不错的选择。

我立马进入了Kotlin的世界。

Kotlin之前一直是默默无闻的，直到今年（2017）5.18 Google IO 大会上，Google宣布正式支持Kotlin为Android的官方开发语言，而且从Android Studio 3.0开始，将直接内置集成Kotlin而无需安装任何的插件。另外，在Spring 5.0 M4 中也引入了对Kotlin专门的支持。

在学习和使用Kotlin中，我发现我越来越喜欢 Kotlin，它是一门非常优秀、优雅有趣、流畅实用的语言，绝对值得一试。感谢Kotlin团队！

本书可以说是我对Kotlin的使用和思考过程的粗浅总结。通过本书的写作，加深了我对 Kotlin语言及其编程的理解，我深刻体会到了学无止境的含义。写书的过程也是我系统学习与思考Kotlin的过程，如果本书能够对你有所帮助，将不胜欣慰。

## 如何阅读本书
本书共16章。我们希望通过简练的表述，全面介绍Kotlin语言特性以及如何使用Kotlin进行实际项目开发，主要包括如下内容：

- 快速开始 Hello World
- 基础语法
- 基本数据类型和类型系统
- 常用数据结构集合类
- 面向对象编程和函数式编程
- 协程
- Kotlin与 Java 互操作
- 集成 SpringBoot 进行服务端开发
- 使用 Kotlin DSL
- 文件IO操作与多线程
- 使用 Kotlin Native以及与 C 语言互操作

第1章是Kotlin 语言的简介，快速学习Kotlin的环境搭建以及常用工具的使用。该章最后还给出一个编程语言学习的小结。通过该章的学习，能够快速进入Kotlin的世界。

第2章是快速开始 Hello World，分别给出了使用命令行REPL、可执行应用程序、Web RESTFul、Android、Kotlin JavaScript等平台环境上的HelloWorld示例。通过该章的学习，可以快速体验在多平台上使用Kotlin语言进行开发的过程。

第3章介绍Kotlin语言的基础知识，包括Kotlin语言的关键字与标识符等、表达式与流程控制、运算操作符、函数及其扩展等基本内容。

第4章介绍Kotlin语言的基本类型和类型系统。我们会首先简单介绍类型的基本概念，然后具体介绍 Kotlin 的内置基本类型：数字、字符串、布尔、数组等。 接着介绍 Kotlin 中引入的特殊的可空类型。最后，简单介绍了Kotlin中的类型推断与类型转换的相关内容。

第5章介绍Kotlin标准库中的集合类：List、Set、Map。Kotlin 中提供了不可变集合类与可变集合类。通过该章的学习，我们将了解到Kotlin 是如何扩展的Java集合库，使得写代码更加简单容易。

第6章介绍Kotlin泛型的基本概念、型变以及类型边界等内容，同时简单介绍了泛型类与泛型函数。

第7章介绍Kotlin面向对象编程的特性： 类与构造函数、抽象类与接口、继承以及多重继承等基础知识，同时介绍了Kotlin中的注解类、枚举类、数据类、密封类、嵌套类、内部类、匿名内部类等特性类。最后我们学习了Kotlin中对单例模式、委托模式的语言层面上的内置支持：object对象、委托。

第8章介绍Kotlin函数式编程的相关内容，其中重点介绍了Kotlin中的高阶函数、Lambda表达式、闭包等核心语法，并给出相应的实例说明。还探讨了关于Lambda演算、Y组合子与递归等函数式编程思想等相关内容。

第9章介绍Kotlin中的协程。首先引入了协程的基本概念，然后通过一些基础使用实例来学习有关协程的创建、执行、取消等操作的方法。在该章的后半部分，我们主要探讨挂起函数的组合执行、协程上下文与调度器、通道与管道等相关内容。最后，我们对协程与线程进行了简单比较，简要介绍了Kotlin的协程API库。

第10章介绍Kotlin与Java的互操作。

第11章介绍如何使用Kotlin集成Spring Boot、SpringMVC等框架来开发Web服务端应用，给出了一个完整的开发实例。最后，简单介绍了Spring 5.0中对Kotlin的支持特性。

第12章介绍使用 Kotlin 集成Gradle 开发的相关内容。

第13章通过一个具体的Android开发实例，来一起学习使用 Kotlin 开发 Android 应用的具体方法。其中用到了Anko、ButterKnife、Realm 等相关框架。

第14章介绍Kotlin中DSL的相关内容。我们将会看到Kotlin 的扩展函数和高阶函数（Lambda 表达式）特性，为定义Kotlin DSL提供了极大的支持。使用DSL的代码风格，可以让我们的程序更加直观易懂、简洁优雅。如果使用Kotlin来开发项目的话，我们完全可以去尝试一下。

第15章介绍Kotlin文件IO 操作、正则表达式以及多线程相关的内容。

第16章简单介绍了Kotlin Native，该章给出了一个简单的 Hello World 的 Native 实例，同时给出了 Kotlin 与 C 语言互操作的完整实例。

## 谁适合阅读本书

本书适合于所有程序员，不管你是前端开发者、Android/iOS 开发者，还是Java 开发者、C 语言开发者，等等。
如果你目前还不是程序员，但想进入编程世界，那么可以尝试从Kotlin开始学习。虽然本书中的部分内容需要一定的编程基础，但是Kotlin本身的极简特性能激发你对编程的兴趣。

## 代码下载

基本上在每章末尾，我们都附上了该章示例工程源代码地址。这些源码都在 https://github.com/EasyKotlin 。可以根据需要，自由克隆下载学习。

## 致谢

在本书的写作出版过程中，得到了很多人的帮助和陪伴。

首先要感谢的是我的妻子和两个可爱的孩子。正是有了你们的陪伴，我的生活才更加有意义。写到这里，我脑海里不禁浮现了我的父亲母亲的音容笑貌，我要感谢我的父母。虽然你们可能不知道我写的东西是什么，但是正是有了你们的辛勤养育，我才能长成今天的我。

我要衷心地感谢我的编辑吴怡女士。当收到她的邀请让我写本关于Kotlin的书时，倍感荣幸之余又感到诚惶诚恐，生怕写出的东西太粗浅。在本书的写作修改过程中，她耐心细致地对稿件进行了详尽、细致的审阅和批注，还提出了很多宝贵的修改建议。同时，在写作过程中也给予了我极大的鼓励，才使我快速完成了这本书。感谢本书出版过程中的所有付出辛勤劳动的华章公司工作人员。

在此，我还要特别感谢我们公司的技术大牛雷卷（陈立兵）。非常感谢他能够抽出宝贵时间审阅本书，同时给出了本书内容的勘误，最后，还为本书写了序。真的非常感谢！

我还要感谢在我的工作学习中认识的所有朋友和同事们，能够认识你们并跟你们一起学习共事，是我的荣幸。

## 联系我们

虽然在本书写作与修改的过程中，我们竭尽全力追求简单正确、清晰流畅地表达内容，但是限于自身水平和有限的时间，也许仍有错误与疏漏之处，还望各位读者不吝指正。

关于本书的任何问题、意见或者建议都可以通过邮件 universsky@163.com 与我交流。

快乐生活，快乐学习，快乐分享，快乐实践出真知。

最后，祝大家阅读愉快！

陈光剑

2017年8月于杭州
