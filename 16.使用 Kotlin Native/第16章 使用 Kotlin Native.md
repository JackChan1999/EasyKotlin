第16章 使用 Kotlin Native
===

不得不说 JetBrains 是一家务实的公司，各种IDE让人赞不绝口，用起来也是相当溜。同样的，诞生自 JetBrains 的 Kotlin 也是一门务实的编程语言，Kotlin以工程实用性为导向，充分借鉴了Java, Scala, Groovy, C#, Gosu, JavaScript, Swift等等语言的精华，让我们写起代码来可谓是相当优雅却又不失工程质量与效率。Kotlin Native能把 Kotlin代码直接编译成机器码，也就是站在了跟 C/C++、Go和Rust的同一个层次，于是这个领域又添一位竞争对手。

在前面的所有章节中，我们使用的 Kotlin 都是基于 JVM 的运行环境。本章我们将从  JVM 的运行环境中离开，走向直接编译生成原生机器码的系统编程的生态系统：Kotlin Native  。


## 16.1 Kotlin Native 简介 

Kotlin Native利用LLVM来编译到机器码。Kotlin Native 主要是基于 LLVM后端编译器（Backend Compiler）来生成本地机器码。

Kotlin Native 的设计初衷是为了支持在非JVM虚拟机平台环境的编程，如 ios、嵌入式平台等。同时支持与 C 互操作。


### 16.1.1 LLVM

LLVM最初是Low Level Virtual Machine的缩写，定位是一个虚拟机，但是是比较底层的虚拟机。LLVM是构架编译器(compiler)的框架系统，以C++编写而成，用于优化以任意程序语言编写的程序的编译时间(compile-time)、链接时间(link-time)、运行时间(run-time)以及空闲时间(idle-time)，对开发者保持开放，并兼容已有脚本。

LLVM的出现正是为了解决编译器代码重用的问题，LLVM一上来就站在比较高的角度，制定了LLVM IR这一中间代码表示语言。LLVM IR充分考虑了各种应用场景，例如在IDE中调用LLVM进行实时的代码语法检查，对静态语言、动态语言的编译、优化等。


### 16.1.2 支持平台

Kotlin Native现在已支持以下平台：

|  平台名称   |    target 配置   |  
|----|----|
|Linux    |     linux      |
| Mac OS | macbook|
|Windows | mingw   |
|Android arm32 |android_arm32|
|Android arm64|android_arm64|
|iOS| iphone|
|Raspberry Pi| raspberrypi|

这意味着我们可以在这些平台上愉快地开始体验了！目前Kotlin Native 已经发布的最新预发布版本是 v0.3 。



### 16.1.3 解释型语言与编译型语言

编译型语言，是在程序执行之前有一个单独的编译过程，将程序翻译成机器语言，以后执行这个程序的时候，就不用再进行翻译了。例如，C/C++ 等都是编译型语言。

解释型语言，是在运行的时候将程序翻译成机器语言，所以运行速度相对于编译型语言要慢。例如，Java，C#等都是解释型语言。

虽然Java程序在运行之前也有一个编译过程，但是并不是将程序编译成机器语言，而是将它编译成字节码（可以理解为一个中间语言）。在运行的时候，由JVM将字节码再翻译成机器语言。



## 16.2 快速开始 Hello World

###  16.2.1 运行环境准备

我们直接去 Github上面去下载 kotlin-native 编译器的软件包。下载地址是 ：https://github.com/JetBrains/kotlin-native/releases  。


![螢幕快照 2017-07-29 13.23.30.png](http://upload-images.jianshu.io/upload_images/1233356-18482ccb52840231.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


下载解压之后，我们可以看到 Kotlin Native 编译器 konan 的目录如下：
```
-rw-r--r--@  1 jack  staff   6828  6 20 22:47 GRADLE_PLUGIN.md
-rw-r--r--@  1 jack  staff  16286  6 20 22:47 INTEROP.md
-rw-r--r--@  1 jack  staff   1957  6 21 01:03 README.md
-rw-r--r--@  1 jack  staff   4606  6 20 22:47 RELEASE_NOTES.md
drwxr-xr-x@  8 jack  staff    272  6 20 23:04 bin
drwxr-xr-x   6 jack  staff    204  7 28 17:08 dependencies
drwxr-xr-x@  3 jack  staff    102  6 20 23:01 klib
drwxr-xr-x@  5 jack  staff    170  5 12 00:02 konan
drwxr-xr-x@  4 jack  staff    136  5 12 00:02 lib
drwxr-xr-x@ 22 jack  staff    748  6 22 19:04 samples
```

关于这个目录里面的内容我们在后面小节中介绍。

另外，我们也可以自己下载源码编译，这里就不多说了。

###  16.2.2新建 Gradle 工程

在本小节中，我们先来使用IDEA 来创建一个普通的 Gradle 工程。

第1步，打开 File -> New -> Project  ，如下图所示

![螢幕快照 2017-07-29 13.35.12.png](http://upload-images.jianshu.io/upload_images/1233356-da70984a266be5b7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


第2步，新建Gradle项目。我们直接在左侧栏中选择 Gradle，点击 Next 

![螢幕快照 2017-07-29 13.36.01.png](http://upload-images.jianshu.io/upload_images/1233356-c8f0c139fd743783.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

第3步，设置项目的 GroupId、ArtifactId、Version 信息

![螢幕快照 2017-07-29 13.36.47.png](http://upload-images.jianshu.io/upload_images/1233356-98e087ca8e642b3e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

第4步，配置 Gradle 项目的基本设置。我们直接选择本地的 Gradle 环境目录，省去下载的时间（有时候网络不好，要下载半天），具体配置如下图所示

![螢幕快照 2017-07-29 13.37.11.png](http://upload-images.jianshu.io/upload_images/1233356-2fc210c945af42db.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

第5步，配置项目名称和项目存放目录，点击 Finish

![螢幕快照 2017-07-29 13.37.23.png](http://upload-images.jianshu.io/upload_images/1233356-e08b78637c00d4b5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

 第6步，等待 IDEA 创建完毕，我们将得到一个如下的Gradle 工程

![螢幕快照 2017-07-29 13.38.50.png](http://upload-images.jianshu.io/upload_images/1233356-33b84cd89067c38e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

现在这个工程里面什么都没有。下面我们就来开始原始的手工新建文件编码。

###  16.2.3 源代码目录

首先我们在工程根目录下面新建 src 目录，用来存放源代码。在 src 下面新建 c 目录存放 C 代码，新建 kotlin 目录存放 Kotlin 代码。我们的源代码组织结构设计如下

```
src
├── c
│   ├── cn_kotlinor.c
│   ├── cn_kotlinor.h
└── kotlin
    └── main.kt

```


###  16.2.4 C 代码文件

#### cn_kotlinor.h

C头文件中声明如下

```
#ifndef CN_KOTLINOR_H
#define CN_KOTLINOR_H
void printHello();
int factorial(int n);
int fib(int n);
#endif

```
我们简单声明了3个函数。

#### cn_kotlinor.c

C 源代码文件内容如下
```
#include "cn_kotlinor.h"
#include <stdio.h>

void printHello(){
    printf("[C]HelloWorld\n");
}

int factorial(int n){
    printf("[C]calc factorial: %d\n", n);
    if(n == 0) return 1;
    return n * factorial(n - 1);
}

int fib(int n){
    printf("[C]calc fibonacci: %d\n", n);
    if(n==1||n==2) return 1;
    return fib(n-1) + fib(n-2);
}

```
这就是我们熟悉的 C 语言代码。

###  16.2.5 Kotlin 代码文件

main.kt 文件内容如下

```
import ckotlinor.*

fun main(args: Array<String>) {
    printHello()
    (1..7).map(::factorial).forEach(::println)
    (1..7).map(::fib).forEach(::println)
}


```

其中，`import kotlinor.*` 是 C 语言代码经过 clang 编译之后的C 的接口包路径，我们将在下面的 build.gradle 配置文件中的konanInterop中配置这个路径。

###  16.2.6  konan插件配置

首先，我们在 build.gradle 里面添加构建脚本 buildscript 闭包

```
buildscript {
    repositories {
        mavenCentral()
        maven {
            url "https://dl.bintray.com/jetbrains/kotlin-native-dependencies"
        }
    }
    dependencies {
        classpath "org.jetbrains.kotlin:kotlin-native-gradle-plugin:0.3"
    }
}
```

这里我们添加了Gradle 构建 Kotlin Native 工程的 DSL 插件  kotlin-native-gradle-plugin:0.3 。这里的版本号，对应我们下载的 konan 编译器的版本号，我们使用的是 v0.3，所以这里我们也使用0.3版本的插件。这个插件发布在https://dl.bintray.com/jetbrains/kotlin-native-dependencies仓库里，所以我们在repositories里面添加了这个仓库。


然后，我们应用插件 konan 

```
apply plugin: 'konan' 
```
konan 就是用来编译 Kotlin 为 native 代码的插件。

###  16.2.7  konanInterop 互操作配置

```
konanInterop {
    ckotlinor {
        defFile 'kotlinor.def' // interop 的配置文件
        includeDirs "src/c" // C 头文件目录，可以传入多个
    }
}
```
konanInterop 主要用来配置 Kotlin 调用 C 的接口。konanInterop 的配置是由konan 插件API中的 KonanInteropTask.kt来处理的（这个类的源码在： https://github.com/JetBrains/kotlin-native/blob/master/tools/kotlin-native-gradle-plugin/src/main/kotlin/org/jetbrains/kotlin/gradle/plugin/KonanInteropTask.kt）。

这里我们声明的 ckotlinor 是插件中的KonanInteropConfig 对象。我们在下面的konanArtifacts里面会引用这个 ckotlinor 。

关于konanInterop的配置选项有

```
  konanInterop {
       pkgName {
           defFile <def-file>  
           pkg <package with stubs>
           target <target: linux/macbook/iphone/iphone_sim>
           compilerOpts <Options for native stubs compilation>
           linkerOpts <Options for native stubs >
           headers <headers to process> 
           includeDirs <directories where headers are located> 
           linkFiles <files which will be linked with native stubs>
           dumpParameters <Option to print parameters of task before execution>
       }
 
       // TODO: add configuration for konan compiler
 }
```

我们简要说明如下表所示

|配置项     |       功能说明     |
|---|----|
|defFile|       互操作映射关系配置文件         |
| pkg |          C 头文件编译后映射为 Kotlin 的包名      |
|target|      编译目标平台：linux/macbook/iphone/iphone_sim等          |
|compilerOpts|       编译选项        |
|linkerOpts|       链接选项         |
|headers|     要处理的头文件           |
|includeDirs|        包括的头文件目录        |
|linkFiles|       与native stubs 链接的文件       |
| dumpParameters  |  打印 Gradle 任务参数选项配置|







其中，kotlinor.def 是Kotlin Native 与 C 语言互操作的配置文件，我们在kotlinor.def 里面配置 C 源码到 kotlin 的映射关系。这个文件内容如下

kotlinor.def

```
headers=cn_kotlinor.h
compilerOpts=-Isrc/c
```

同样的配置，如果我们写在 build.gradle 文件中的konanInterop配置里如下

```
konanInterop {
    ckotlinor {
        // defFile 'kotlinor.def' // interop 的配置文件
        compilerOpts '-Isrc/c'
        headers 'src/c/cn_kotlinor.h' // interop 的配置文件
        includeDirs "src/c" // C 头文件存放目录，可以传入多个
    }
}
```



关于这个配置文件的解析原理可以参考 KonanPlugin.kt 文件的源码（https://github.com/JetBrains/kotlin-native/blob/master/tools/kotlin-native-gradle-plugin/src/main/kotlin/org/jetbrains/kotlin/gradle/plugin/KonanPlugin.kt）。







### 16.2.8  konanArtifacts 配置

在 konan 插件中，我们使用konanArtifacts来配置编译任务执行。

```
konanArtifacts { 
    KotlinorApp { // (1)
        inputFiles fileTree("src/kotlin") // (2)
        useInterop 'ckotlinor' // (3)
        nativeLibrary fileTree('src/c/cn_kotlinor.bc') // (4)
        target 'macbook' // (5)
    }
}
```
其中，(1)处的KotlinorApp名称，在 build 之后会生成以这个名称命名的 KotlinorApp.kexe 可执行程序。
(2)处的inputFiles配置的是 kotlin 代码目录，程序执行的入口 main 定义在这里。

(3)处的useInterop 配置的是使用哪个互操作配置。我们使用的是前面的 konanInterop 里面的配置  ckotlinor 。

(4) 处的nativeLibrary配置的是本地库文件。关于'src/c/cn_kotlinor.bc'文件的编译生成我们在下面讲。

(5) 处的target 配置的是编译的目标平台，这里我们配置为 'macbook' 。


关于konanArtifacts可选的配置如下所示

```
 konanArtifacts {
 
       artifactName1 {
 
           inputFiles "files" "to" "be" "compiled"
 
           outputDir "path/to/output/dir"
 
           library "path/to/library"
           library File("Library")
 
           nativeLibrary "path/to/library"
           nativeLibrary File("Library")
 
           noStdLib
           produce "library"|"program"|"bitcode"
           enableOptimization
 
           linkerOpts "linker" "args"
           target "target"
 
           languageVersion "version"
           apiVersion "version"
 
     }
      artifactName2 {
 
           extends artifactName1

           inputDir "someDir"
           outputDir "someDir"
      }
 
   }
```



konan 编译任务配置处理类是KonanCompileTask.kt （https://github.com/JetBrains/kotlin-native/blob/master/tools/kotlin-native-gradle-plugin/src/main/kotlin/org/jetbrains/kotlin/gradle/plugin/KonanCompileTask.kt）。



### 16.2.9 完整的 build.gradle 配置

完整的 build.gradle 配置文件内容如下

```
group 'com.easy.kotlin'
version '1.0-SNAPSHOT'


buildscript {
    repositories {
        mavenCentral()
        maven {
            url "https://dl.bintray.com/jetbrains/kotlin-native-dependencies"
        }
    }
    dependencies {
        classpath "org.jetbrains.kotlin:kotlin-native-gradle-plugin:0.3"
    }
}

apply plugin: 'konan' // konan 就是用来编译 Kotlin 为 native 代码的插件


konanInterop { // konanInterop 主要用来配置 Kotlin 调用 C 的接口
    ckotlinor {
        defFile 'kotlinor.def' // interop 的配置文件
        includeDirs "src/c" // C 头文件目录，可以传入多个
    }
}

konanArtifacts { //konanArtifacts 配置我们的项目
    KotlinorApp { // build 之后会生成 KotlinorApp.kexe 可执行程序
        inputFiles fileTree("src/kotlin") //kotlin 代码配置，项目入口 main 需要定义在这里
        useInterop 'ckotlinor' //使用前面的 konanInterop 里面的配置  kotlinor{ ... }
        nativeLibrary fileTree('src/c/cn_kotlinor.bc') //自己编译的 llvm 字节格式的依赖
        target 'macbook' // 编译的目标平台
    }
}

```





提示：关于konan 插件详细配置文档：Gradle DSL https://github.com/JetBrains/kotlin-native/blob/master/GRADLE_PLUGIN.md


### 16.2.10 使用 clang 编译 C 代码

为了实用性，我们新建一个 shell 脚本 kclang.sh 来简化 clang 编译的命令行输入参数
```
#!/usr/bin/env bash
clang -std=c99 -c $1 -o $2 -emit-llvm
```
这样，我们把 kclang.sh 放到 C 代码目录下，然后直接使用脚本来编译：
```
 kclang.sh cn_kotlinor.c cn_kotlinor.bc
```
我们将得到一个 cn_kotlinor.bc 库文件。



提示：clang是一个C++编写、基于LLVM、发布于LLVM BSD许可证下的C/C++/Objective-C/Objective-C++编译器。它与GNU C语言规范几乎完全兼容。更多关于 clang 的内容可参考 ： http://clang.llvm.org/docs/index.html 。





### 16.2.11 配置 konan 编译器主目录
最后，在执行 Gradle 构建之前，我们还需要指定konan 编译器主目录。我们在工程根目录下面新建 gradle.properties 这个属性配置文件，内容如下

```
konan.home=/Users/jack/soft/kotlin-native-macos-0.3
```

### 16.2.12 执行构建操作

我们直接在 IDEA 右侧的 Gradle 工具栏点击Tasks ->build -> build 命令执行构建操作


![螢幕快照 2017-07-30 03.42.19.png](http://upload-images.jianshu.io/upload_images/1233356-3f59984a4276ed67.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

我们会看到终端输出

```
15:12:02: Executing external task 'build'...
:assemble UP-TO-DATE
:check UP-TO-DATE
:downloadKonanCompiler
:genKotlinerInteropStubs
:compileKotlinerInteropStubs
KtFile: kotliner.kt
:compileKonanKotliner
KtFile: main.kt
ld: warning: object file (/var/folders/q5/kvt7_nsd6ngdw5qry4d99xv00000gn/T/combined697750051437954502.o) was built for newer OSX version (10.12) than being linked (10.11)
:compileKonan
:build

BUILD SUCCESSFUL in 29s
4 actionable tasks: 4 executed
15:12:31: External task execution finished 'build'.

```
构建完成之后，会在build/konan/bin/目录下面生成一个KotlinorApp.kexe可执行程序，它直接在 Mac OS 上运行，不再依赖JVM 环境了。我们得到的完整的构建输出目录树如下

```
build
└── konan
    ├── bin
    │   ├── KotlinorApp.kexe
    │   └── KotlinorApp.kt.bc
    ├── interopCompiledStubs
    │   └── ckotlinorInteropStubs
    │       ├── ckotlinorInteropStubs
    │       │   ├── linkdata
    │       │   │   ├── module
    │       │   │   ├── package_ckotlinor
    │       │   │   └── root_package
    │       │   ├── manifest
    │       │   ├── resources
    │       │   └── targets
    │       │       └── macbook
    │       │           ├── kotlin
    │       │           │   └── program.kt.bc
    │       │           └── native
    │       └── ckotlinorInteropStubs.klib
    ├── interopStubs
    │   └── genCkotlinorInteropStubs
    │       └── ckotlinor
    │           └── ckotlinor.kt
    └── nativelibs
        └── genCkotlinorInteropStubs
            └── ckotlinorstubs.bc

16 directories, 10 files

```

其中在 ckotlinor.kt中，我们可以看出 konan 编译器还为我们生成了 C 代码对应的 Kotlin 的接口


```
@file:Suppress("UNUSED_EXPRESSION", "UNUSED_VARIABLE")
package ckotlinor

import konan.SymbolName
import kotlinx.cinterop.*

fun printHello(): Unit {
    val res = kni_printHello()
    return res
}

@SymbolName("ckotlinor_kni_printHello")
private external fun kni_printHello(): Unit

fun factorial(n: Int): Int {
    val _n = n
    val res = kni_factorial(_n)
    return res
}

@SymbolName("ckotlinor_kni_factorial")
private external fun kni_factorial(n: Int): Int

fun fib(n: Int): Int {
    val _n = n
    val res = kni_fib(_n)
    return res
}

@SymbolName("ckotlinor_kni_fib")
private external fun kni_fib(n: Int): Int


```

我们在Kotlin 代码中，调用的就是这些映射到 C 中的函数接口。


### 16.2.12 执行 kexe 应用程序

我们直接在命令行中执行 KotlinorApp.kexe 如下

```
chatper16_kotlin_native_helloworld$ build/konan/bin/KotlinorApp.kexe  
```

我们可以看到如下输出：

```
[C]HelloWorld
[C]calc factorial: 1
[C]calc factorial: 0
[C]calc factorial: 2
...
[C]calc factorial: 2
[C]calc factorial: 1
[C]calc factorial: 0
1
2
6
24
120
720
5040
[C]calc fibonacci: 1
[C]calc fibonacci: 2
[C]calc fibonacci: 3
...
[C]calc fibonacci: 3
[C]calc fibonacci: 2
[C]calc fibonacci: 1
1
1
2
3
5
8
13

```


至此，我们完成了一次简单的Kotlin Native 与 C 语言互操作在系统级编程的体验之旅。

我们看到，Kotlin Native仍然看重互操作性(Interoperability)。它能高效地调用C函数，甚至还能从C头文件自动生成了对应的Kotlin接口，发扬了JetBrains为开发者服务的良好传统！

但是，在体验的过程中我们也发现整个过程比较手工化，显得比较繁琐（例如手工新建各种配置文件、手工使用 clang 编译C 代码等）。

不过，Kotlin Native 的 Gradle 插件用起来还是相当不错的。相信未来 IDEA 会对 Kotlin Native 开发进行智能的集成，以方便系统编程的开发者更好更快的完成项目的配置以及开发编码工作。







## 16.3 Kotlin Native 编译器 konan 简介

本小节我们简单介绍一下Kotlin Native 编译器的相关内容（主要以 Mac OS 平台示例）。

#### bin目录

bin目录下面是执行命令行

```
cinterop       klib           konanc         kotlinc        kotlinc-native  run_konan
```

`run_konan` 是真正的入口 shell，它的执行逻辑是

```
TOOL_NAME="$1"
shift

if [ -z "$JAVACMD" -a -n "$JAVA_HOME" -a -x "$JAVA_HOME/bin/java" ]; then
    JAVACMD="$JAVA_HOME/bin/java"
else
    JAVACMD=java
fi
[ -n "$JAVACMD" ] || JAVACMD=java
...
java_opts=(-ea \
            -Xmx3G \
            "-Djava.library.path=${NATIVE_LIB}" \
            "-Dkonan.home=${KONAN_HOME}" \
           -Dfile.encoding=UTF-8)

KONAN_JAR="${KONAN_HOME}/konan/lib/backend.native.jar"
KOTLIN_JAR="${KONAN_HOME}/konan/lib/kotlin-compiler.jar"
STUB_GENERATOR_JAR="${KONAN_HOME}/konan/lib/StubGenerator.jar"
INTEROP_INDEXER_JAR="${KONAN_HOME}/konan/lib/Indexer.jar"
INTEROP_JAR="${KONAN_HOME}/konan/lib/Runtime.jar"
HELPERS_JAR="${KONAN_HOME}/konan/lib/helpers.jar"
KLIB_JAR="${KONAN_HOME}/konan/lib/klib.jar"
UTILITIES_JAR="${KONAN_HOME}/konan/lib/utilities.jar"
KONAN_CLASSPATH="$KOTLIN_JAR:$INTEROP_JAR:$STUB_GENERATOR_JAR:$INTEROP_INDEXER_JAR:$KONAN_JAR:$HELPERS_JAR:$KLIB_JAR:$UTILITIES_JAR"
TOOL_CLASS=org.jetbrains.kotlin.cli.utilities.MainKt

LIBCLANG_DISABLE_CRASH_RECOVERY=1 \
$TIMECMD "$JAVACMD" "${java_opts[@]}" "${java_args[@]}" -cp "$KONAN_CLASSPATH" "$TOOL_CLASS" "$TOOL_NAME" "${konan_args[@]}"
```

我们可以看出，Kotlin Native 编译器 konan 的运行环境还是在 JVM 上，但是它生成的机器码的可执行程序是直接运行在对应的平台系统上（直接编译成机器语言）。




#### konan目录

konan目录是 Kotlin Native 编译器的核心实现部分。目录结构如下：

```
kotlin-native-macos-0.3$ tree konan
konan/
├── konan.properties
├── lib
│   ├── Indexer.jar
│   ├── Runtime.jar
│   ├── StubGenerator.jar
│   ├── backend.native.jar
│   ├── callbacks
│   │   └── shared
│   │       └── libcallbacks.dylib
│   ├── clangstubs
│   │   └── shared
│   │       └── libclangstubs.dylib
│   ├── helpers.jar
│   ├── klib.jar
│   ├── kotlin-compiler.jar
│   ├── protobuf-java-2.6.1.jar
│   └── utilities.jar
└── nativelib
    ├── libcallbacks.dylib
    ├── libclangstubs.dylib
    ├── libllvmstubs.dylib
    └── liborgjetbrainskotlinbackendkonanhashstubs.dylib

6 directories, 16 files
```

我们可以看到在 `run_konan` 命令行 shell 中依赖了上面的这些 jar 包。上面的目录文件是 Mac OS 平台上的。


对应的 Linux 平台上的konan目录文件如下

```
kotlin-native-linux-0.3$ tree konan
konan
├── konan.properties
├── lib
│   ├── Indexer.jar
│   ├── Runtime.jar
│   ├── StubGenerator.jar
│   ├── backend.native.jar
│   ├── callbacks
│   │   └── shared
│   │       └── libcallbacks.so
│   ├── clangstubs
│   │   └── shared
│   │       └── libclangstubs.so
│   ├── helpers.jar
│   ├── klib.jar
│   ├── kotlin-compiler.jar
│   ├── protobuf-java-2.6.1.jar
│   └── utilities.jar
└── nativelib
    ├── libcallbacks.so
    ├── libclangstubs.so
    ├── libllvmstubs.so
    └── liborgjetbrainskotlinbackendkonanhashstubs.so

6 directories, 16 files
```


Windows 平台上的 konan 目录文件如下
```
kotlin-native-windows-0.3$ tree konan
konan
├── konan.properties
├── lib
│   ├── Indexer.jar
│   ├── Runtime.jar
│   ├── StubGenerator.jar
│   ├── backend.native.jar
│   ├── callbacks
│   │   └── shared
│   │       └── callbacks.dll
│   ├── clangstubs
│   │   └── shared
│   │       └── clangstubs.dll
│   ├── helpers.jar
│   ├── klib.jar
│   ├── kotlin-compiler.jar
│   ├── protobuf-java-2.6.1.jar
│   └── utilities.jar
└── nativelib
    ├── callbacks.dll
    ├── clangstubs.dll
    ├── llvmstubs.dll
    └── orgjetbrainskotlinbackendkonanhashstubs.dll

6 directories, 16 files

```



#### klib 目录

klib 目录下是 Kotlin 的标准库的关联元数据文件以及 Kotlin Native 针对各个目标平台的 bc 文件

```
kotlin-native-macos-0.3$ tree klib
klib/
└── stdlib
    ├── linkdata
    │   ├── module
    │   ├── package_konan
    │   ├── package_konan.internal
    │   ├── package_kotlin
    │   ├── package_kotlin.annotation
    │   ├── package_kotlin.collections
    │   ├── package_kotlin.comparisons
    │   ├── package_kotlin.coroutines
    │   ├── package_kotlin.coroutines.experimental
    │   ├── package_kotlin.coroutines.experimental.intrinsics
    │   ├── package_kotlin.experimental
    │   ├── package_kotlin.internal
    │   ├── package_kotlin.io
    │   ├── package_kotlin.properties
    │   ├── package_kotlin.ranges
    │   ├── package_kotlin.reflect
    │   ├── package_kotlin.sequences
    │   ├── package_kotlin.text
    │   ├── package_kotlin.text.regex
    │   ├── package_kotlin.util
    │   ├── package_kotlinx
    │   ├── package_kotlinx.cinterop
    │   └── root_package
    ├── manifest
    ├── resources
    └── targets
        ├── android_arm32
        │   ├── kotlin
        │   │   └── program.kt.bc
        │   └── native
        │       ├── launcher.bc
        │       ├── runtime.bc
        │       └── start.bc
        ├── android_arm64
        │   ├── kotlin
        │   │   └── program.kt.bc
        │   └── native
        │       ├── launcher.bc
        │       ├── runtime.bc
        │       └── start.bc
        ├── iphone
        │   ├── kotlin
        │   │   └── program.kt.bc
        │   └── native
        │       ├── launcher.bc
        │       ├── runtime.bc
        │       ├── start.bc
        │       ├── start.kt.bc
        │       └── stdlib.kt.bc
        └── macbook
            ├── kotlin
            │   └── program.kt.bc
            └── native
                ├── launcher.bc
                ├── runtime.bc
                └── start.bc

16 directories, 42 files
```

上面的目录是 kotlin-native-macos-0.3 平台的版本。我们可以看出，在Mac OS上，我们可以使用 Kotlin Native 编译android_arm32、android_arm64、iphone、macbook等目标平台的机器码可执行的程序。



另外，对应的 Linux 平台的目录文件如下


```
kotlin-native-linux-0.3$ tree klib
klib/
└── stdlib
    ├── linkdata
    │   ├── module
    │   ├── package_konan
    │   ├── package_konan.internal
    │   ├── package_kotlin
    │   ├── package_kotlin.annotation
    │   ├── package_kotlin.collections
    │   ├── package_kotlin.comparisons
    │   ├── package_kotlin.coroutines
    │   ├── package_kotlin.coroutines.experimental
    │   ├── package_kotlin.coroutines.experimental.intrinsics
    │   ├── package_kotlin.experimental
    │   ├── package_kotlin.internal
    │   ├── package_kotlin.io
    │   ├── package_kotlin.properties
    │   ├── package_kotlin.ranges
    │   ├── package_kotlin.reflect
    │   ├── package_kotlin.sequences
    │   ├── package_kotlin.text
    │   ├── package_kotlin.text.regex
    │   ├── package_kotlin.util
    │   ├── package_kotlinx
    │   ├── package_kotlinx.cinterop
    │   └── root_package
    ├── manifest
    ├── resources
    └── targets
        ├── android_arm32
        │   ├── kotlin
        │   │   └── program.kt.bc
        │   └── native
        │       ├── launcher.bc
        │       ├── runtime.bc
        │       └── start.bc
        ├── android_arm64
        │   ├── kotlin
        │   │   └── program.kt.bc
        │   └── native
        │       ├── launcher.bc
        │       ├── runtime.bc
        │       └── start.bc
        ├── linux
        │   ├── kotlin
        │   │   └── program.kt.bc
        │   └── native
        │       ├── launcher.bc
        │       ├── runtime.bc
        │       └── start.bc
        └── raspberrypi
            ├── kotlin
            │   └── program.kt.bc
            └── native
                ├── launcher.bc
                ├── runtime.bc
                ├── start.bc
                ├── start.kt.bc
                └── stdlib.kt.bc

16 directories, 42 files
```
也就是说我们可以在 Linux 平台上编译android_arm32、android_arm64、linux、raspberrypi等平台上的目标程序。

对应Windows 平台的如下

```
kotlin-native-windows-0.3$ tree klib
klib/
└── stdlib
    ├── linkdata
    │   ├── module
    │   ├── package_konan
    │   ├── package_konan.internal
    │   ├── package_kotlin
    │   ├── package_kotlin.annotation
    │   ├── package_kotlin.collections
    │   ├── package_kotlin.comparisons
    │   ├── package_kotlin.coroutines
    │   ├── package_kotlin.coroutines.experimental
    │   ├── package_kotlin.coroutines.experimental.intrinsics
    │   ├── package_kotlin.experimental
    │   ├── package_kotlin.internal
    │   ├── package_kotlin.io
    │   ├── package_kotlin.properties
    │   ├── package_kotlin.ranges
    │   ├── package_kotlin.reflect
    │   ├── package_kotlin.sequences
    │   ├── package_kotlin.text
    │   ├── package_kotlin.text.regex
    │   ├── package_kotlin.util
    │   ├── package_kotlinx
    │   ├── package_kotlinx.cinterop
    │   └── root_package
    ├── manifest
    ├── mingw
    │   ├── kotlin
    │   │   └── program.kt.bc
    │   └── native
    │       ├── launcher.bc
    │       ├── runtime.bc
    │       └── start.bc
    ├── resources
    └── targets
        └── mingw
            ├── kotlin
            │   └── program.kt.bc
            └── native
                ├── launcher.bc
                ├── runtime.bc
                └── start.bc

10 directories, 32 files
```
在 Windows 平台中，Kotlin Native 使用的是 mingw 库来实现的。目前，在 V0.3预发布版本，我们在 Windows 平台上可以体验的东西比较少，像 Android，iOS，Raspberrypi都还不支持。

提示：MinGW，是Minimalist GNUfor Windows的缩写。它是一个可自由使用和自由发布的Windows特定头文件和使用GNU工具集导入库的集合，允许你在GNU/Linux和Windows平台生成本地的Windows程序而不需要第三方C运行时（C Runtime）库。MinGW 是一组包含文件和端口库，其功能是允许控制台模式的程序使用微软的标准C运行时（C Runtime）库（MSVCRT.DLL）,该库在所有的 NT OS 上有效，在所有的 Windows 95发行版以上的 Windows OS 有效，使用基本运行时，你可以使用 GCC 写控制台模式的符合美国标准化组织（ANSI）程序，可以使用微软提供的 C 运行时（C Runtime）扩展，与基本运行时相结合，就可以有充分的权利既使用 CRT（C Runtime）又使用 WindowsAPI功能。





#### samples目录
 
samples目录下面是官方给出的一些实例。关于这些实例的文档介绍以及源码工程是： https://github.com/JetBrains/kotlin-native/tree/master/samples 。想更加深入了解学习的同学可以参考。



## 本章小结

本章工程源码： https://github.com/EasyKotlin/chatper16_kotlin_native_helloworld

现在我们可以把 Kotlin 像C 一样地直接编译成的机器码来运行，这样在 C 语言出现的地方（例如应用于嵌入式等对性能要求比较高的场景），Kotlin 也来了。Kotlin 将会在嵌入式系统和物联网、数据分析和科学计算、游戏开发、服务端开发和微服务等领域持续发力。

Kotlin 整个语言的架构不可谓不宏大：上的了云端（服务端程序），下的了手机端（ Kotlin / Native ），写的了前端（JS，HTML DSL 等），嵌的了冰箱（Kotlin Native）。Kotlin 俨然已成为一门擅长多个领域的语言了。


在互联网领域，目前在Web服务端应用开发、Android移动端开发是Kotlin 最活跃的领域。 Kotlin 将会越来越多地进入 Java 程序员们的视野， Java 程序员们会逐渐爱上 Kotlin 。

未来的可能性有很多。但是真正的未来还是要我们去创造。

