第15章 Kotlin 文件IO操作与多线程
===

我们在使用 Groovy 的文件 IO 操作的时候，感觉非常便利。同样的Kotlin也有好用的文件 IO 操作的 API。同样的在 Kotlin 中对 Java 的正则表达式功能做了一些实用的扩展。还有 Kotlin 中的多线程主要也是对 Java 的多线程 API 作了一些封装。因为这些 Java 已经有了很多的基础 API，Kotlin 并没有自己再去重复实现，而是在 Java 的基础上进行了实用的功能扩展。

本章我们就来介绍Kotlin 文件 IO 操作、正则表达式以及多线程相关的内容。


## 15.1 Kotlin IO 简介

Kotlin的IO操作都在kotlin.io包下。Kotlin的原则就是Java已经有的，好用的就直接使用，没有的或者不好用的，就在原有类的基础上进行封装扩展，例如Kotlin 就给 File 类写了扩展函数。这跟Groovy的扩展API 的思想是一样的。

## 15.2 终端 IO

Java 超长的输出语句 System.out.println() 居然延续到了现在！同样的工作在C++里面只需要简单的 cout<< 就可以完成。当然，如果需要的话，我们可以在工程中直接封装  System.out.println() 为简单的打印方法。

在Kotlin里面很简单，只需要使用println或者print这两个全局函数即可，我们不再需要冗长的前缀。当然如果我们很怀旧，就是想用  System.out.println() ，Kotlin 依然支持直接这么使用（与 Java 无缝互操作）。

```
>>> System.out.println("K")
K
>>> println("K")
K
```

这里的 println 函数Kotlin实现如下
```
@kotlin.internal.InlineOnly
public inline fun println(message: Any?) {
    System.out.println(message)
}
```

当然，Kotlin 也只是在 System.out.println() 的基础上进行了封装。

从终端读取数据也很简单，最基本的方法就是全局函数readLine，它直接从终端读取一行作为字符串。如果需要更进一步的处理，可以使用Kotlin提供的各种字符串处理函数来处理和转换字符串。

Kotlin 的封装终端IO 的类在 stdlib/src/kotlin/io/Console.kt 源文件中。

## 15.3 文件 IO 操作



Kotlin为java.io.File提供了大量好用的扩展函数，这些扩展函数主要在下面三个源文件中：

|kotlin/io/files/FileTreeWalk.kt|
|---|
|kotlin/io/files/Utils.kt|
|kotlin/io/FileReadWrite.kt|

同时，Kotlin 也针对InputStream、OutputStream和 Reader 等都做了简单的扩展。它们主要在下面的两个源文件中：

|kotlin/io/IOStreams.kt|
|---|
|kotlin/io/ReadWrite.kt|

Koltin 的序列化直接采用的 Java 的序列化类的类型别名：
```
internal typealias Serializable = java.io.Serializable
```



下面我们来简单介绍一下 Kotlin 文件读写操作。

### 15.3.1 读文件

#### 读取文件全部内容

我们如果简单读取一个文件，可以使用readText()方法，它直接返回整个文件内容。代码示例如下
```
    /**
     * 获取文件全部内容字符串
     * @param filename
     */
    fun getFileContent(filename: String): String {
        val f = File(filename)
        return f.readText(Charset.forName("UTF-8"))
    }
```

我们直接使用 File 对象来调用 readText 函数即可获得该文件的全部内容，它返回一个字符串。如果指定字符编码，可以通过传入参数Charset来指定，默认是UTF-8编码。

如果我们想要获得文件每行的内容，可以简单通过`split("\n")`来获得一个每行内容的数组。

#### 获取文件每行的内容

我们也可以直接调用 Kotlin 封装好的readLines函数，获得文件每行的内容。readLines函数返回一个持有每行内容的 List。

```
    /**
     * 获取文件每一行内容，存入一个 List 中
     * @param filename
     */
    fun getFileLines(filename: String): List<String> {
        return File(filename).readLines(Charset.forName("UTF-8"))
    }
```

#### 直接操作字节数组

我们如果希望直接操作文件的字节数组，可以使用readBytes()。如果想使用传统的Java方式，在Kotlin 中你也可以像 Groovy 一样自如使用。

```
    //读取为bytes数组
    val bytes: ByteArray = f.readBytes()
    println(bytes.joinToString(separator = " "))

    //直接像 Java 中的那样处理Reader或InputStream
    val reader: Reader = f.reader()
    val inputStream: InputStream = f.inputStream()
    val bufferedReader: BufferedReader = f.bufferedReader()
}
```

### 15.3.2 写文件

和读文件类似，写入文件也很简单。我们可以写入字符串，也可以写入字节流。还可以直接使用Java的 Writer 或者 OutputStream。


#### 覆盖写文件

```
    fun writeFile(text: String, destFile: String) {
        val f = File(destFile)
        if (!f.exists()) {
            f.createNewFile()
        }
        f.writeText(text, Charset.defaultCharset())
    }
```

#### 末尾追加写文件
```
    fun appendFile(text: String, destFile: String) {
        val f = File(destFile)
        if (!f.exists()) {
            f.createNewFile()
        }
        f.appendText(text, Charset.defaultCharset())
    }
```
 

## 15.4 遍历文件树


和Groovy一样，Kotlin也提供了方便的功能来遍历文件树。遍历文件树需要调用扩展方法walk()。它会返回一个FileTreeWalk对象，它有一些方法用于设置遍历方向和深度，详情参见FileTreeWalk API 文档说明。

提示：FileTreeWalk API 文档链接 https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.io/-file-tree-walk/

下面的例子遍历了指定文件夹下的所有文件。


```
    fun traverseFileTree(filename: String) {
        val f = File(filename)
        val fileTreeWalk = f.walk()
        fileTreeWalk.iterator().forEach { println(it.absolutePath) }
    }
```
测试代码：

```
    @Test fun testTraverseFileTree() {
        KFileUtil.traverseFileTree(".")
    }
```
运行上面的测试代码，它将输出当前目录下的所有子目录及其文件。

我们还可以遍历当前文件下面所有子目录文件，存入一个 Iterator<File> 中

```
    fun getFileIterator(filename: String): Iterator<File> {
        val f = File(filename)
        val fileTreeWalk = f.walk()
        return fileTreeWalk.iterator()
    }
```


我们遍历当前文件下面所有子目录文件，还可以根据条件过滤，并把结果存入一个 Sequence<File> 中

```
    fun getFileSequenceBy(filename: String, p: (File) -> Boolean): Sequence<File> {
        val f = File(filename)
        return f.walk().filter(p)
    }

```

测试代码：
```
    @Test fun testGetFileSequenceBy() {
        val fileSequence1 = KFileUtil.getFileSequenceBy(".", {
            it.isDirectory
        })
        fileSequence1.forEach { println("fileSequence1: ${it.absoluteFile} ") }

        val fileSequence2 = KFileUtil.getFileSequenceBy(".", {
            it.isFile
        })
        fileSequence2.forEach { println("fileSequence2: ${it.absoluteFile} ") }

        val fileSequence3 = KFileUtil.getFileSequenceBy(".", {
            it.extension == "kt"
        })
        fileSequence3.forEach { println("fileSequence3: ${it.absoluteFile} ") }
    }
```
在工程中运行上面的测试代码，它将会有类似下面的输出：
```
...
...

fileSequence3: /Users/jack/kotlin/chapter15_file_io/./src/main/kotlin/com/easy/kotlin/fileio/KFileUtil.kt 
fileSequence3: /Users/jack/kotlin/chapter15_file_io/./src/main/kotlin/com/easy/kotlin/fileio/KNetUtil.kt 
fileSequence3: /Users/jack/kotlin/chapter15_file_io/./src/main/kotlin/com/easy/kotlin/fileio/KShellUtil.kt 
fileSequence3: /Users/jack/kotlin/chapter15_file_io/./src/test/kotlin/com/easy/kotlin/fileio/KFileUtilTest.kt 


```


## 15.5 网络IO操作

Kotlin为java.net.URL增加了两个扩展方法，readBytes和readText。我们可以方便的使用这两个方法配合正则表达式实现网络爬虫的功能。

下面我们简单写几个函数实例。

根据 url 获取该 url 的响应 HTML函数

```

fun getUrlContent(url: String): String {
    return URL(url).readText(Charset.defaultCharset())
}
```


根据 url 获取该 url 响应比特数组函数

```
fun getUrlBytes(url: String): ByteArray {
    return URL(url).readBytes()
}
```

把 url 响应字节数组写入文件

```
fun writeUrlBytesTo(filename: String, url: String) {
    val bytes = URL(url).readBytes()
    File(filename).writeBytes(bytes)
}
```

下面这个例子简单的获取了百度首页的源代码。

```
getUrlContent("https://www.baidu.com")
```



下面这个例子根据 url 来获取一张图片的比特流，然后调用readBytes()方法读取到字节流并写入文件。

```
writeUrlBytesTo("图片.jpg", "http://n.sinaimg.cn/default/4_img/uplaod/3933d981/20170622/2fIE-fyhfxph6601959.jpg")

```

在项目相应文件夹下我们可以看到下载好的 “图片.jpg” 。


##  15.6 kotlin.io标准库

Kotlin 的 io 库主要是扩展 Java 的 io 库。下面我们简单举几个例子。

#### `appendBytes`

追加字节数组到该文件中

方法签名：
```
fun File.appendBytes(array: ByteArray)
```

#### `appendText`

追加文本到该文件中

方法签名：
```

fun File.appendText(
    text: String, 
    charset: Charset = Charsets.UTF_8)
```

#### `bufferedReader`

获取该文件的BufferedReader

方法签名：
```
fun File.bufferedReader(
    charset: Charset = Charsets.UTF_8, 
    bufferSize: Int = DEFAULT_BUFFER_SIZE
): BufferedReader
```
#### `bufferedWriter`

获取该文件的BufferedWriter

方法签名：
```
fun File.bufferedWriter(
    charset: Charset = Charsets.UTF_8, 
    bufferSize: Int = DEFAULT_BUFFER_SIZE
): BufferedWriter
```

#### `copyRecursively`

复制该文件或者递归复制该目录及其所有子文件到指定路径，如果指定路径下的文件不存在，会自动创建。

方法签名：
```
fun File.copyRecursively(
    target: File, 
    overwrite: Boolean = false, // 是否覆盖。true：覆盖之前先删除原来的文件
    onError: (File, IOException) -> OnErrorAction = { _, exception -> throw exception }
): Boolean
```





|提示： Kotlin 对 File 的扩展函数 API 文档https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.io/java.io.-file/index.html|
|---|
|关于 kotlin.io 下面的API文档在这里 https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.io/index.html|




## 15.7 执行Shell命令行


我们使用 Groovy 的文件 IO 操作感觉非常好用，例如

```
package com.easy.kotlin

import org.junit.Test
import org.junit.runner.RunWith
import org.junit.runners.JUnit4

@RunWith(JUnit4)
class ShellExecuteDemoTest {
    @Test
    def void testShellExecute() {
        def p = "ls -R".execute()
        def output = p.inputStream.text
        println(output)
        def fname = "我图.url"
        def f = new File(fname)
        def lines = f.readLines()
        lines.forEach({
            println(it)
        })
        println(f.text)
    }
}
```

Kotlin 中的文件 IO，网络 IO 操作跟 Groovy一样简单。 

另外，从上面的代码中我们看到使用 Groovy 执行终端命令非常简单：

```
def p = "ls -R".execute()
def output = p.inputStream.text
```

在 Kotlin 中，目前还没有对 String 类和 Process 扩展这样的函数。其实扩展这样的函数非常简单。我们完全可以自己扩展。

首先，我们来扩展 String 的 execute() 函数。

```
fun String.execute(): Process {
    val runtime = Runtime.getRuntime()
    return runtime.exec(this)
}

```

然后，我们来给 Process 类扩展一个 text函数。
```
fun Process.text(): String {
    var output = ""
    //	输出 Shell 执行的结果
    val inputStream = this.inputStream
    val isr = InputStreamReader(inputStream)
    val reader = BufferedReader(isr)
    var line: String? = ""
    while (line != null) {
        line = reader.readLine()
        output += line + "\n"
    }
    return output
}
```

完成了上面两个简单的扩展函数之后，我们就可以在下面的测试代码中，可以像 Groovy 一样执行终端命令了：

```
val p = "ls -al".execute()

val exitCode = p.waitFor()
val text = p.text()

println(exitCode)
println(text)
```

实际上，通过之前的很多实例的学习，我们可以看出 Kotlin 的扩展函数相当实用。Kotlin 语言本身API 也大量使用了扩展功能。




## 15.8 正则表达式

我们在 Kotlin 中除了仍然可以使用 Java中的 Pattern，Matcher 等类之外，Kotlin 还提供了一个正则表达式类 kotlin/text/regex/Regex.kt ，我们通过 Regex 的构造函数来创建一个正则表达式。

### 15.8.1 构造 Regex 表达式

#### 使用Regex构造函数
```
>>> val r1 = Regex("[a-z]+")
>>> val r2 = Regex("[a-z]+", RegexOption.IGNORE_CASE)
```

其中的匹配选项 RegexOption 是直接使用的 Java 类 Pattern中的正则匹配选项。

#### 使用 String 的 toRegex 扩展函数
```
>>> val r3 = "[A-Z]+".toRegex()
```

### 15.8.2  Regex 函数

 Regex 里面提供了丰富的简单而实用的函数，如下表所示

|函数名称| 功能说明 |
|---|---|
|matches(input: CharSequence): Boolean| 输入字符串全部匹配  |
|containsMatchIn(input: CharSequence): Boolean|输入字符串至少有一个匹配|
|matchEntire(input: CharSequence): MatchResult?|输入字符串全部匹配，返回一个匹配结果对象|
|replace(input: CharSequence, replacement: String): String|把输入字符串中匹配的部分替换成replacement的内容|
|replace(input: CharSequence, transform: (MatchResult) -> CharSequence): String|把输入字符串中匹配到的值，用函数 transform映射之后的新值替换|
|find(input: CharSequence, startIndex: Int = 0): MatchResult?|返回输入字符串中第一个匹配的值|
|findAll(input: CharSequence, startIndex: Int = 0): Sequence<MatchResult>|返回输入字符串中所有匹配的值MatchResult的序列|

下面我们分别就上面的函数给出简单实例。

#### `matches`

输入字符串全部匹配正则表达式返回 true , 否则返回 false。

```
>>> val r1 = Regex("[a-z]+")
>>> r1.matches("ABCzxc")
false
>>> 

>>> val r2 = Regex("[a-z]+", RegexOption.IGNORE_CASE)
>>> r2.matches("ABCzxc")
true

>>> val r3 = "[A-Z]+".toRegex()
>>> r3.matches("GGMM")
true

```


#### `containsMatchIn`
输入字符串中至少有一个匹配就返回true，没有一个匹配就返回false。

```
>>> val re = Regex("[0-9]+")
>>> re.containsMatchIn("012Abc")
true
>>> re.containsMatchIn("Abc")
false
```

#### `matchEntire`
输入字符串全部匹配正则表达式返回 一个MatcherMatchResult对象，否则返回 null。

```
>>> val re = Regex("[0-9]+")
>>> re.matchEntire("1234567890")
kotlin.text.MatcherMatchResult@34d713a2
>>> re.matchEntire("1234567890!")
null
```

我们可以访问MatcherMatchResult的value熟悉来获得匹配的值。

```
>>> re.matchEntire("1234567890")?.value
1234567890
```
由于 matchEntire 函数的返回是MatchResult? 可空对象，所以这里我们使用了安全调用符号 ` ?. ` 。

#### `replace(input: CharSequence, replacement: String): String`

把输入字符串中匹配的部分替换成replacement的内容。

```
>>> val re = Regex("[0-9]+")
>>> re.replace("12345XYZ","abcd")
abcdXYZ
```
我们可以看到，"12345XYZ"中`12345`是匹配正则表达式 `[0-9]+`的内容，它被替换成了 `abcd` 。

#### `replace(input: CharSequence, transform: (MatchResult) -> CharSequence): String`

把输入字符串中匹配到的值，用函数 transform映射之后的新值替换。

```
>>> val re = Regex("[0-9]+")
>>> re.replace("9XYZ8", { (it.value.toInt() * it.value.toInt()).toString() })
81XYZ64
```
我们可以看到，`9XYZ8`中数字9和8是匹配正则表达式`[0-9]+`的内容，它们分别被transform函数映射 `(it.value.toInt() * it.value.toInt()).toString() ` 的新值 81  和 64 替换。

#### `find`

返回输入字符串中第一个匹配的MatcherMatchResult对象。

```
>>> val re = Regex("[0-9]+")
>>> re.find("123XYZ987abcd7777")
kotlin.text.MatcherMatchResult@4d4436d0
>>> re.find("123XYZ987abcd7777")?.value
123
```


#### `findAll`

返回输入字符串中所有匹配的值的MatchResult的序列。

```
>>> val re = Regex("[0-9]+")
>>> re.findAll("123XYZ987abcd7777")
kotlin.sequences.GeneratorSequence@f245bdd

```

我们可以通过 forEach 循环遍历所以匹配的值

```
>>> re.findAll("123XYZ987abcd7777").forEach{println(it.value)}
123
987
7777

```

### 15.8.3 使用 Java 正则表达式类

除了上面 Kotlin 提供的函数之外，我们在 Kotlin 中仍然可以使用 Java 的正则表达式的 API。

```
val re = Regex("[0-9]+")
val p = re.toPattern()
val m = p.matcher("888ABC999")
while (m.find()) {
    val d = m.group()
    println(d)
}
```
上面的代码运行输出：

```
888
999
```






## 15.9 Kotlin 的多线程

Kotlin中没有synchronized关键字。
Kotlin中没有volatile关键字。
Kotlin的Any类似于Java的Object，但是没有wait()，notify()和notifyAll() 方法。

那么并发如何在Kotlin中工作呢？放心，Kotlin 既然是站在 Java 的肩膀上，当然少不了对多线程编程的支持——Kotlin通过封装 Java 中的线程类，简化了我们的编码。同时我们也可以使用一些特定的注解， 直接使用 Java 中的同步关键字等。下面我们简单介绍一下使用Kotlin 进行多线程编程的相关内容。


### 15.9.1 创建线程

我们在 Java中通常有两种方法在Java中创建线程：

- 扩展Thread类
- 或者实例化它并通过构造函数传递一个Runnable 

因为我们可以很容易地在Kotlin中使用Java类，这两个方式都可以使用。 

#### 使用对象表达式创建

```
    object : Thread() {
        override fun run() {
            Thread.sleep(3000)
            println("A 使用 Thread 对象表达式: ${Thread.currentThread()}")
        }
    }.start()
```

此代码使用Kotlin的对象表达式创建一个匿名类并覆盖run()方法。 


#### 使用 Lambda 表达式
下面是如何将一个Runnable传递给一个新创建的Thread实例：

```
    Thread({
        Thread.sleep(2000)
        println("B 使用 Lambda 表达式: ${Thread.currentThread()}")
    }).start()
```


我们在这里看不到Runnable，在Kotlin中可以很方便的直接使用上面的Lambda表达式来表达。 

还有更简单的方法吗？ 且看下文解说。

#### 使用 Kotlin 封装的 thread 函数

例如，我们写了下面一段线程的代码
```
    val t = Thread({
        Thread.sleep(2000)
        println("C 使用 Lambda 表达式:${Thread.currentThread()}")
    })
    t.isDaemon = false
    t.name = "CThread"
    t.priority = 3
    t.start()
```
后面的四行可以说是样板化的代码。在 Kotlin 中把这样的操作封装简化了。

```
    thread(start = true, isDaemon = false, name = "DThread", priority = 3) {
        Thread.sleep(1000)
        println("D 使用 Kotlin 封装的函数 thread(): ${Thread.currentThread()}")
    }
```

这样的代码显得更加精简整洁了。事实上，thread()函数就是对我们编程实践中经常用到的样板化的代码进行了抽象封装，它的实现如下：

```
public fun thread(start: Boolean = true, isDaemon: Boolean = false, contextClassLoader: ClassLoader? = null, name: String? = null, priority: Int = -1, block: () -> Unit): Thread {
    val thread = object : Thread() {
        public override fun run() {
            block()
        }
    }
    if (isDaemon)
        thread.isDaemon = true
    if (priority > 0)
        thread.priority = priority
    if (name != null)
        thread.name = name
    if (contextClassLoader != null)
        thread.contextClassLoader = contextClassLoader
    if (start)
        thread.start()
    return thread
}
```

这只是一个非常方便的包装函数，简单实用。从上面的例子我们可以看出，Kotlin 通过扩展 Java 的线程 API，简化了样板代码。


### 15.9.2 同步方法和块

synchronized不是Kotlin中的关键字，它替换为@Synchronized 注解。 Kotlin中的同步方法的声明将如下所示：

```
    @Synchronized fun appendFile(text: String, destFile: String) {
        val f = File(destFile)
        if (!f.exists()) {
            f.createNewFile()
        }
        f.appendText(text, Charset.defaultCharset())
    }
```

@Synchronized 注解与 Java中的 synchronized 具有相同的效果：它会将JVM方法标记为同步。 对于同步块，我们使用synchronized() 函数，它使用锁作为参数：

```
    fun appendFileSync(text: String, destFile: String) {
        val f = File(destFile)
        if (!f.exists()) {
            f.createNewFile()
        }

        synchronized(this){
            f.appendText(text, Charset.defaultCharset())
        }
    }
```
跟 Java 基本一样。

### 15.9.3 可变字段

同样的，Kotlin没有 volatile 关键字，但是有@Volatile注解。

```
@Volatile private var running = false
fun start() {
    running = true
    thread(start = true) {
        while (running) {
            println("Still running: ${Thread.currentThread()}")
        }
    }
}

fun stop() {
    running = false
    println("Stopped: ${Thread.currentThread()}")
}
```

@Volatile会将JVM备份字段标记为volatile。



当然，在 Kotlin 中我们有更好用的协程并发库。在代码工程实践中，我们可以根据实际情况自由选择。

## 本章小结

Kotlin 是一门工程实践性很强的语言，从本章介绍的文件IO、正则表达式以及多线程等内容中，我们可以领会到 Kotlin 的基本原则：充分使用已有的 Java 生态库，在此基础之上进行更加简单实用的扩展，大大提升程序员们的生产力。从中我们也体会到了Kotlin 编程中的极简理念——不断地抽象、封装、扩展，使之更加简单实用。

本章示例代码：https://github.com/EasyKotlin/chapter15_file_io


另外，笔者综合了本章的内容，使用 SpringBoot + Kotlin 写了一个简单的图片爬虫 Web 应用，感兴趣的读者可参考源码：https://github.com/EasyKotlin/chatper15_net_io_img_crawler



在下一章，也我们的最后一章中，让我们脱离 JVM，直接使用 Kotlin Native 来开发一个直接编译成机器码运行的 Kotlin 应用程序。




--------------------------------------

# 《Kotlin极简教程》正式上架：

> #### [点击这里 > *去京东商城购买阅读* ](https://item.jd.com/12181725.html)
> #### [点击这里 > *去天猫商城购买阅读* ](https://detail.tmall.com/item.htm?id=558540170670)

#### 非常感谢您亲爱的读者，大家请多支持！！！有任何问题，欢迎随时与我交流~
--------------------------------------
