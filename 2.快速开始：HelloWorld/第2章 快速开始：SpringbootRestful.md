# easy_kotlin_chapter2_hello_world_springboot_restful
easy_kotlin_chapter2_hello_world_springboot_restful


Kotlin极简教程
===

第2章 快速开始：HelloWorld
===

## 2.1 命令行的HelloWorld
安装配置完Kotlin命令行环境之后，我们直接命令行输入kotlinc, 即可进入 Kotlin REPL界面。

```kotlin

$ kotlinc
Welcome to Kotlin version 1.1.2-2 (JRE 1.8.0_40-b27)
Type :help for help, :quit for quit
>>> println("Hello,World!")
Hello,World!

>>> import java.util.Date
>>> Date()
Wed Jun 07 14:19:33 CST 2017

```

## 2.2 应用程序版HelloWorld

我们如果想拥有学习Kotlin的相对较好的体验，就不建议使用eclipse了。毕竟Kotlin是JetBrains家族的亲儿子，跟Intelli IDEA是血浓于水啊。

我们使用IDEA新建gradle项目，选择Java，Kotlin(Java)框架支持，如下图：

![](http://upload-images.jianshu.io/upload_images/1233356-0882a65869654dec.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

新建完项目，我们写一个HelloWorld.kt类

```
package com.easy.kotlin

/**
 * Created by jack on 2017/5/29.
 */

import java.util.Date
import java.text.SimpleDateFormat

fun main(args: Array<String>) {
    println("Hello, world!")
    println(SimpleDateFormat("yyyy-MM-dd HH:mm:ss").format(Date()))
}

```

整体的项目目录结构如下

```
.
├── README.md
├── build
│   ├── classes
│   │   └── main
│   │       ├── META-INF
│   │       │   └── easykotlin_main.kotlin_module
│   │       └── com
│   │           └── easy
│   │               └── kotlin
│   │                   └── HelloWorldKt.class
│   └── kotlin-build
│       └── caches
│           └── version.txt
├── build.gradle
├── settings.gradle
└── src
    ├── main
    │   ├── java
    │   ├── kotlin
    │   │   └── com
    │   │       └── easy
    │   │           └── kotlin
    │   │               └── HelloWorld.kt
    │   └── resources
    └── test
        ├── java
        ├── kotlin
        └── resources

21 directories, 7 files
```

直接运行HelloWorld.kt，输出结果如下

```
Hello, world!
2017-05-29 01:15:30
```

关于工程的编译、构建、运行，是由gradle协同kotlin-gradle-plugin，在kotlin-stdlib-jre8，kotlin-stdlib核心依赖下完成的。build.gradle配置文件如下：

```
group 'com.easy.kotlin'
version '1.0-SNAPSHOT'

buildscript {
    ext.kotlin_version = '1.1.1'

    repositories {
        mavenCentral()
    }
    dependencies {
        classpath "org.jetbrains.kotlin:kotlin-gradle-plugin:$kotlin_version"
    }
}

apply plugin: 'java'
apply plugin: 'kotlin'

sourceCompatibility = 1.8

repositories {
    mavenCentral()
}

dependencies {
    compile "org.jetbrains.kotlin:kotlin-stdlib-jre8:$kotlin_version"
    testCompile group: 'junit', name: 'junit', version: '4.12'
}

```

工程源码地址：https://github.com/EasyKotlin/easykotlin/tree/easykotlin_hello_world_20170529


## 2.3 Web RESTFul HelloWorld
本节介绍使用 `Kotlin` 结合 `SpringBoot` 开发一个RESTFul版本的 `Hello.World`。

1. 新建gradle，kotlin工程：

打开IDEA的`File > New > Project` , 如下图

![螢幕快照 2017-03-11 12.40.05.png](http://upload-images.jianshu.io/upload_images/1233356-8d1252f729630936.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

按照界面操作，输入相应的工程名等信息，即可新建一个使用Gradle构建的标准Kotlin工程。

2. build.gradle 基本配置

IDEA自动生成的Gradle配置文件如下：


```groovy
group 'com.easy.kotlin'
version '1.0-SNAPSHOT'

buildscript {
    ext.kotlin_version = '1.1.2-2'
    
    repositories {
        mavenCentral()
    }
    dependencies {
//        Kotlin Gradle插件
        classpath "org.jetbrains.kotlin:kotlin-gradle-plugin:$kotlin_version"

    }
}

apply plugin: 'java'
apply plugin: 'kotlin'

sourceCompatibility = 1.8
targetCompatibility = 1.8

repositories {
    mavenCentral()
}

dependencies {
    compile "org.jetbrains.kotlin:kotlin-stdlib-jre8:$kotlin_version"
    testCompile group: 'junit', name: 'junit', version: '4.12'

}


```
从上面的配置文件我们可以看出，IDEA已经自动把Gradle 构建Kotlin工程插件 kotlin-gradle-plugin，以及Kotlin标准库kotlin-stdlib添加到配置文件中了。

3. 配置SpringBoot相关内容

下面我们来配置SpringBoot相关内容。首先在构建脚本里面添加ext变量springBootVersion。

```
ext.kotlin_version = '1.1.2-2'
ext.springboot_version = '1.5.2.RELEASE'

```

然后在构建依赖里添加spring-boot-gradle-plugin

```
buildscript {
...
    dependencies {
//        Kotlin Gradle插件
        classpath "org.jetbrains.kotlin:kotlin-gradle-plugin:$kotlin_version"
//        SpringBoot Gradle插件
        classpath("org.springframework.boot:spring-boot-gradle-plugin:$springboot_version")

//        Kotlin整合SpringBoot的默认无参构造函数，默认把所有的类设置open类插件
        classpath("org.jetbrains.kotlin:kotlin-noarg:$kotlin_version")
        classpath("org.jetbrains.kotlin:kotlin-allopen:$kotlin_version")
    }
}
```

4. 配置无参（no-arg）、全开放（allopen）插件

其中,`org.jetbrains.kotlin:kotlin-noarg`是无参（no-arg）编译器插件，它为具有特定注解的类生成一个额外的零参数构造函数。 这个生成的构造函数是合成的，因此不能从 Java 或 Kotlin 中直接调用，但可以使用反射调用。 这样我们就可以使用 Java Persistence API（JPA）实例化 data 类。

其中，`org.jetbrains.kotlin:kotlin-allopen` 是全开放编译器插件。我们使用Kotlin 调用Java的Spring AOP框架和库，需要类为 open（可被继承实现），而Kotlin 类和函数都是默认 final 的，这样我们需要为每个类和函数前面加上open修饰符。

这样的代码写起来，可费事了。还好，我们有all-open 编译器插件。它会适配 Kotlin 以满足这些框架的需求，并使用指定的注解标注类而其成员无需显式使用 open 关键字打开。 例如，当我们使用 Spring 时，就不需要打开所有的类，跟我们在Java中写代码一样，只需要用相应的注解标注即可，如 @Configuration 或 @Service。 


完整的build.gradle配置文件如下

```
group 'com.easy.kotlin'
version '1.0-SNAPSHOT'

buildscript {
    ext.kotlin_version = '1.1.2-2'
    ext.springboot_version = '1.5.2.RELEASE'

    repositories {
        mavenCentral()
    }
    dependencies {
//        Kotlin Gradle插件
        classpath "org.jetbrains.kotlin:kotlin-gradle-plugin:$kotlin_version"
//        SpringBoot Gradle插件
        classpath("org.springframework.boot:spring-boot-gradle-plugin:$springboot_version")

//        Kotlin整合SpringBoot的默认无参构造函数，默认把所有的类设置open类插件
//        无参（no-arg）编译器插件为具有特定注解的类生成一个额外的零参数构造函数。 这个生成的构造函数是合成的，因此不能从 Java 或 Kotlin 中直接调用，但可以使用反射调用。 这允许 Java Persistence API（JPA）实例化 data 类，虽然它从 Kotlin 或 Java 的角度看没有无参构造函数
        classpath("org.jetbrains.kotlin:kotlin-noarg:$kotlin_version")
//        全开放插件（kotlin-allopen）
        classpath("org.jetbrains.kotlin:kotlin-allopen:$kotlin_version")
    }
}

apply plugin: 'java'
apply plugin: 'kotlin'

//Kotlin整合SpringBoot需要的spring，jpa，org.springframework.boot插件

//Kotlin-spring 编译器插件，它根据 Spring 的要求自动配置全开放插件。
apply plugin: 'kotlin-spring'
//该插件指定 @Entity 和 @Embeddable 注解作为应该为一个类生成无参构造函数的标记。
apply plugin: 'kotlin-jpa'
apply plugin: 'org.springframework.boot'

sourceCompatibility = 1.8
targetCompatibility = 1.8

repositories {
    mavenCentral()
}

dependencies {
    compile "org.jetbrains.kotlin:kotlin-stdlib-jre8:$kotlin_version"
    testCompile group: 'junit', name: 'junit', version: '4.12'

    compile("org.springframework.boot:spring-boot-starter-web")
    testCompile("org.springframework.boot:spring-boot-starter-test")
    compile("org.springframework.boot:spring-boot-starter-data-jpa")
    compile('mysql:mysql-connector-java:5.1.13')

}

```


5. 配置application.properties

```
spring.datasource.url = jdbc:mysql://localhost:3306/easykotlin
spring.datasource.username = root
spring.datasource.password = root
#spring.datasource.driverClassName = com.mysql.jdbc.Driver
# Specify the DBMS
spring.jpa.database = MYSQL
# Keep the connection alive if idle for a long time (needed in production)
spring.datasource.testWhileIdle = true
spring.datasource.validationQuery = SELECT 1
# Show or not log for each sql query
spring.jpa.show-sql = true
# Hibernate ddl auto (create, create-drop, update)
spring.jpa.hibernate.ddl-auto = update
# Naming strategy
spring.jpa.hibernate.naming-strategy = org.hibernate.cfg.ImprovedNamingStrategy
# The SQL dialect makes Hibernate generate better SQL for the chosen database
spring.jpa.properties.hibernate.dialect = org.hibernate.dialect.MySQL5Dialect

server.port=8000


```

6. 整体工程架构

> UNIX操作系统说，“一切都是文件”。所以,我们 的所有的源代码、字节码、工程资源文件等等，一切都是文件。文件里面存的是字符串（01也当做是字符）。各种框架、库、编译器，解释器，都是对这些字符串流进行过滤，最后映射成01机器码（或者CPU微指令码等），最终落地到硬件上的高低电平。

整体工程目录如下：

```
.
├── README.md
├── build
│   └── kotlin-build
│       └── caches
│           └── version.txt
├── build.gradle
├── easykotlin.sql
├── settings.gradle
└── src
    ├── main
    │   ├── java
    │   ├── kotlin
    │   │   └── com
    │   │       └── easy
    │   │           └── kotlin
    │   │               ├── Application.kt
    │   │               ├── controller
    │   │               │   ├── HelloWorldController.kt
    │   │               │   └── PeopleController.kt
    │   │               ├── entity
    │   │               │   └── People.kt
    │   │               ├── repository
    │   │               │   └── PeopleRepository.kt
    │   │               └── service
    │   │                   └── PeopleService.kt
    │   └── resources
    │       ├── application.properties
    │       └── banner.txt
    └── test
        ├── java
        ├── kotlin
        └── resources

19 directories, 13 files

```


一切尽在不言中，静静地看工程文件结构。

直接写个HelloWorldController

```
package com.easy.kotlin.controller

import org.springframework.web.bind.annotation.GetMapping
import org.springframework.web.bind.annotation.RestController

/**
 * Created by jack on 2017/6/7.
 */
@RestController
class HelloWorldController {
    @GetMapping(value = *arrayOf("/helloworld", "/"))
    fun helloworld(): Any {
        return "Hello,World!"
    }
}

```

我们再写个访问数据库的标准四层代码

写领域模型类People

```
package com.easy.kotlin.entity

import java.util.*
import javax.persistence.Entity
import javax.persistence.GeneratedValue
import javax.persistence.GenerationType
import javax.persistence.Id

/**
 * Created by jack on 2017/6/6.
 */
@Entity
class People(
        @Id @GeneratedValue(strategy = GenerationType.AUTO)
        val id: Long?,
        val firstName: String?,
        val lastName: String?,
        val gender: String?,
        val age: Int?,
        val gmtCreated: Date,
        val gmtModified: Date
) {
    override fun toString(): String {
        return "People(id=$id, firstName='$firstName', lastName='$lastName', gender='$gender', age=$age, gmtCreated=$gmtCreated, gmtModified=$gmtModified)"
    }
}

```

写PeopleRepository

```
package com.easy.kotlin.repository

import com.easy.kotlin.entity.People
import org.springframework.data.repository.CrudRepository

/**
 * Created by jack on 2017/6/7.
 */
interface PeopleRepository : CrudRepository<People, Long> {
    fun findByLastName(lastName: String): List<People>?
}

```

写PeopleService
```
package com.easy.kotlin.service

import com.easy.kotlin.entity.People
import com.easy.kotlin.repository.PeopleRepository
import org.springframework.beans.factory.annotation.Autowired
import org.springframework.stereotype.Service

/**
 * Created by jack on 2017/6/7.
 */
@Service
class PeopleService : PeopleRepository {

    @Autowired
    val peopleRepository: PeopleRepository? = null


    override fun findByLastName(lastName: String): List<People>? {
        return peopleRepository?.findByLastName(lastName)
    }

    override fun <S : People?> save(entity: S): S? {
        return peopleRepository?.save(entity)
    }

    override fun <S : People?> save(entities: MutableIterable<S>?): MutableIterable<S>? {
        return peopleRepository?.save(entities)
    }

    override fun delete(entities: MutableIterable<People>?) {
    }

    override fun delete(entity: People?) {
    }

    override fun delete(id: Long?) {
    }

    override fun findAll(ids: MutableIterable<Long>?): MutableIterable<People>? {
        return peopleRepository?.findAll(ids)
    }

    override fun findAll(): MutableIterable<People>? {
        return peopleRepository?.findAll()
    }

    override fun exists(id: Long?): Boolean {
        return peopleRepository?.exists(id)!!
    }

    override fun count(): Long {
        return peopleRepository?.count()!!
    }

    override fun findOne(id: Long?): People? {
        return peopleRepository?.findOne(id)
    }

    override fun deleteAll() {
    }


}

```

写PeopleController

```
package com.easy.kotlin.controller

import com.easy.kotlin.service.PeopleService
import org.springframework.beans.factory.annotation.Autowired
import org.springframework.stereotype.Controller
import org.springframework.web.bind.annotation.GetMapping
import org.springframework.web.bind.annotation.RequestParam
import org.springframework.web.bind.annotation.ResponseBody

/**
 * Created by jack on 2017/6/7.
 */
@Controller
class PeopleController {
    @Autowired
    val peopleService: PeopleService? = null

    @GetMapping(value = "/hello")
    @ResponseBody
    fun hello(@RequestParam(value = "lastName") lastName: String): Any {
        val peoples = peopleService?.findByLastName(lastName)
        val map = HashMap<Any, Any>()
        map.put("hello", peoples!!)
        return map
    }

}

```







7. 运行测试

点击Gradle的`bootRun` , 如下图


![螢幕快照 2017-06-07 11.47.42.png](http://upload-images.jianshu.io/upload_images/1233356-5496bf11120199e0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



如果没有异常，启动成功，我们将看到以下输出：

![螢幕快照 2017-06-07 11.50.07.png](http://upload-images.jianshu.io/upload_images/1233356-34dc6fdaa14ebda0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)




打开浏览器，访问请求：

http://127.0.0.1:8000/


输出响应：

```
Hello,World!

```
访问

http://127.0.0.1:8000/hello?lastName=chen


```
// 20170607115700
// http://127.0.0.1:8000/hello?lastName=chen

{
  "hello": [
    {
      "id": 1,
      "firstName": "Jason",
      "lastName": "Chen",
      "gender": "Male",
      "age": 28,
      "gmtCreated": 1496768497000,
      "gmtModified": 1496768497000
    },
    {
      "id": 3,
      "firstName": "Corey",
      "lastName": "Chen",
      "gender": "Female",
      "age": 20,
      "gmtCreated": 1496768497000,
      "gmtModified": 1496768497000
    }
    ...
  ]
}
```

本节示例工程源代码：

https://github.com/EasyKotlin/easy_kotlin_chapter2_hello_world_springboot_restful




## 2.4 Android版的HelloWorld


2017谷歌I/O大会：宣布 Kotlin 成 Android 开发一级语言。

![](http://upload-images.jianshu.io/upload_images/1233356-3f9d414107046319.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

2017谷歌I/O大会上，谷歌宣布，将Kotlin语言作为安卓开发的一级编程语言。Kotlin由JetBrains公司开发，与Java100%互通，并具备诸多Java尚不支持的新特性。谷歌称还将与JetBrains公司合作，为Kotlin设立一个非盈利基金会。

JetBrains在2010年首次推出Kotlin编程语言，并在次年将之开源。下一版的AndroidStudio（3.0）也将提供支持。


下面我们简要介绍如何在Android上开始一个Kotlin的HelloWorld程序。

对于我们程序员来说，我们正处于一个美好的时代。得益于互联网的发展、工具的进步，我们现在学习一门新技术的成本和难度都比过去低了很多。


假设你之前没有使用过Kotlin，那么从头开始写一个HelloWorld的app也只需要这么几步：

1. 首先，你要有一个Android Studio。
本节中，我们用的是2.2.3版本，其它版本应该也大同小异。

```
Android Studio 2.3.1
Build #AI-162.3871768, built on April 1, 2017
JRE: 1.8.0_112-release-b06 x86_64
JVM: OpenJDK 64-Bit Server VM by JetBrains s.r.o

```


2. 其次，安装一个Kotlin的插件。

依次打开：Android Studio > Preferences > Plugins，


![](http://upload-images.jianshu.io/upload_images/1233356-b3d7745cd9408848.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


然后选择『Browse repositories』，在搜索框中搜索Kotlin，结果列表中的『Kotlin』插件，如下图

![](http://upload-images.jianshu.io/upload_images/1233356-74f30d3242a2765c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


点击安装，安装完成之后，重启Android Studio。


3. 新建一个Android项目

重新打开Android Studio，新建一个Android项目吧，添加一个默认的MainActivity——像以前一样即可。

4. 转换Java to Kotlin

安装完插件的AndroidStudio现在已经拥有开发Kotlin的功能。我们先来尝试它的转换功能：Java -> Kotlin，可以把现有的java文件翻译成Kotlin文件。

打开MainActivity文件，在Code菜单下面可以看到一个新的功能：Convert Java File to Kotlin File。


![](http://upload-images.jianshu.io/upload_images/1233356-68101f8caa0a0fbe.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


点击转换，


![](http://upload-images.jianshu.io/upload_images/1233356-934a0c279af3c884.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


可以看到转换后的Kotlin文件：MainActivity.kt

```
package com.kotlin.easy.kotlinandroid

import android.support.v7.app.AppCompatActivity
import android.os.Bundle

class MainActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
    }
}

```

这个转换功能，对我们Java程序员在学习Kotlin是十分实用。我们可以基于我们之前的Java编码的经验来迅速学习Kotlin编程。

5. 配置gradle文件

MainActivity已经被转换成了Kotlin实现，但是项目目前gradle编译、构建、运行还不能执行，还需要进一步配置一下，让项目支持grade的编译、运行。当然，这一步也不需要我们做太多工作——IDEA都已经帮我们做好了。

在Java代码转换成Kotlin代码之后，打开MainActivity.kt文件，编译器会提示"Kotlin not configured"，点击一下Configure按钮，IDEA就会自动帮我们把配置文件写好了。

我们可以看出，主要的依赖项是：

```
kotlin-gradle-plugin
plugin: 'kotlin-android'
kotlin-stdlib-jre7
```
完整的配置文件如下：

Project build.gradle

```
// Top-level build file where you can add configuration options common to all sub-projects/modules.

buildscript {
    ext.kotlin_version = '1.1.2-4'
    repositories {
        jcenter()
    }
    dependencies {
        classpath 'com.android.tools.build:gradle:2.3.1'
        classpath "org.jetbrains.kotlin:kotlin-gradle-plugin:$kotlin_version"

        // NOTE: Do not place your application dependencies here; they belong
        // in the individual module build.gradle files
    }
}

allprojects {
    repositories {
        jcenter()
    }
}

task clean(type: Delete) {
    delete rootProject.buildDir
}

```

Module build.gradle
```
apply plugin: 'com.android.application'
apply plugin: 'kotlin-android'

android {
    compileSdkVersion 25
    buildToolsVersion "25.0.2"
    defaultConfig {
        applicationId "com.kotlin.easy.kotlinandroid"
        minSdkVersion 14
        targetSdkVersion 25
        versionCode 1
        versionName "1.0"
        testInstrumentationRunner "android.support.test.runner.AndroidJUnitRunner"
    }
    buildTypes {
        release {
            minifyEnabled false
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
        }
    }
}

dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    androidTestCompile('com.android.support.test.espresso:espresso-core:2.2.2', {
        exclude group: 'com.android.support', module: 'support-annotations'
    })
    compile 'com.android.support:appcompat-v7:25.3.1'
    compile 'com.android.support.constraint:constraint-layout:1.0.2'
    testCompile 'junit:junit:4.12'
    compile "org.jetbrains.kotlin:kotlin-stdlib-jre7:$kotlin_version"
}
repositories {
    mavenCentral()
}

``` 

所以说使用IDEA来写Kotlin代码，这工具的完美集成会让你用起来如丝般润滑。毕竟Kotlin的亲爸爸JetBrains是专门做工具的，而且Intelli IDEA又是那么敏捷、智能。

配置之后，等Gradle Sync完成，即可运行。

6.运行

运行结果如下


![](http://upload-images.jianshu.io/upload_images/1233356-a3afc675807f9881.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


工程源码：https://github.com/EasyKotlin/KotlinAndroid



## 2.5 JavaScript版HelloWorld

在Kotlin 1.1中，开始支持JavaScript和协程是引人注目的亮点。本节我们简单介绍Kotlin代码编译转化为JavaScript的方法。

为了极简直观地感受这个过程，我们先在命令行REPL环境体验一下Kotlin源码被编译生成对应的JavaScript代码的过程。

首先，使用编辑器新建一个HelloWord.kt

```
fun helloWorld(){
	println("Hello,World!")
}
```

命令行使用`kotlinc-js`编译

```
kotlinc-js -output HelloWorld.js HelloWorld.kt
```

运行完毕，我们会在当前目录下看到`HelloWorld.js`, 其内容如下

```
if (typeof kotlin === 'undefined') {
  throw new Error("Error loading module 'HelloWorld'. Its dependency 'kotlin' was not found. Please, check whether 'kotlin' is loaded prior to 'HelloWorld'.");
}
var HelloWorld = function (_, Kotlin) {
  'use strict';
  var println = Kotlin.kotlin.io.println_s8jyv4$;
  function helloWorld() {
    println('Hello,World!');
  }
  _.helloWorld = helloWorld;
  Kotlin.defineModule('HelloWorld', _);
  return _;
}(typeof HelloWorld === 'undefined' ? {} : HelloWorld, kotlin);

```

我们看到，使用`kotlinc-js` 转换成的js代码依赖'kotlin'模块。这个模块是Kotlin支持JavaScript脚本的内部封装模块。也就是说，如果我们想要使用`HelloWorld.js`，先要引用`kotlin.js`。这个`kotlin.js` 在kotlin-stdlib-js-1.1.2.jar里面。



下面我们使用IDEA新建一个Kotlin（JavaScript）工程。在这个过程中，我们将会看到使用Kotlin来开发js的过程。

首先按照以下步骤新建工程

![螢幕快照 2017-06-07 21.32.23.png](http://upload-images.jianshu.io/upload_images/1233356-00912c0684daf9c3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![螢幕快照 2017-06-07 21.33.40.png](http://upload-images.jianshu.io/upload_images/1233356-92c1c14e38ac53bf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![螢幕快照 2017-06-07 21.33.57.png](http://upload-images.jianshu.io/upload_images/1233356-2318499596ac2224.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


![螢幕快照 2017-06-07 21.34.08.png](http://upload-images.jianshu.io/upload_images/1233356-71eba5d0b05a2972.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)





等待Gradle初始化工程完毕，我们将得到一个Gradle KotlinJS 工程，其目录如下




```
.
├── build
│   └── kotlin-build
│       └── caches
│           └── version.txt
├── build.gradle
├── settings.gradle
└── src
    ├── main
    │   ├── java
    │   ├── kotlin
    │   └── resources
    └── test
        ├── java
        ├── kotlin
        └── resources

12 directories, 3 files

```

其中，build.gradle配置文件为

```
group 'com.easy.kotlin'
version '1.0-SNAPSHOT'

buildscript {
    ext.kotlin_version = '1.1.2'

    repositories {
        mavenCentral()
    }
    dependencies {
        classpath "org.jetbrains.kotlin:kotlin-gradle-plugin:$kotlin_version"
    }
}

apply plugin: 'kotlin2js'

repositories {
    mavenCentral()
}

dependencies {
    compile "org.jetbrains.kotlin:kotlin-stdlib-js:$kotlin_version"
}

```

其中，apply plugin: 'kotlin2js' 是Gradle的kotlin编译成js的插件。org.jetbrains.kotlin:kotlin-stdlib-js是KotlinJS的运行库。


另外，我们需要再配置一下Kotlin代码编译成JS的编译规则，以及文件放置目录等属性，如下所示

```
build.doLast {
    configurations.compile.each { File file ->
        copy {
            includeEmptyDirs = false

            from zipTree(file.absolutePath)
            into "${projectDir}/web"
            include { fileTreeElement ->
                def path = fileTreeElement.path
                path.endsWith(".js") && (path.startsWith("META-INF/resources/") || !path.startsWith("META-INF/"))
            }
        }
    }
}

compileKotlin2Js {
    kotlinOptions.outputFile = "${projectDir}/web/js/app.js"
    kotlinOptions.moduleKind = "plain" // plain (default),AMD,commonjs,umd
    kotlinOptions.sourceMap = true
    kotlinOptions.verbose = true
    kotlinOptions.suppressWarnings = true
    kotlinOptions.metaInfo = true
}
```

其中，`kotlinOptions.moduleKind`配置项是Kotlin代码编译成JavaScript代码的类型。 支持普通JS（plain），AMD(Asynchronous Module Definition，异步模块定义)、CommonJS和UMD(Universal Model Definition，通用模型定义)。

AMD通常在浏览器的客户端使用。AMD是异步加载模块，可用性和性能相对会好。

CommonJS是服务器端上使用的模块系统，通常用于nodejs。

UMD是想综合AMD、CommonJS这两种模型，同时支持它们在客户端或服务器端上使用。

我们这里为了极简化演示，直接采用了普通JS `plain` 类型。


除了输出的 JavaScript 文件，该插件默认会创建一个带二进制描述符的额外 JS 文件。 如果你是构建其他 Kotlin 模块可以依赖的可重用库，那么该文件是必需的，并且应该与转换结果一起分发。 其生成由 kotlinOptions.metaInfo 选项控制。


一切配置完毕，我们来写Kotlin代码App.kt

```
package com.easy.kotlin

/**
 * Created by jack on 2017/6/7.
 */

fun helloWorld() {
    println("Hello,World!")
}

```

然后，我们直接使用Gradle构建工程，如下图所示

![螢幕快照 2017-06-07 23.48.23.png](http://upload-images.jianshu.io/upload_images/1233356-41e5ec9326080542.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

控制台输出

```
23:47:05: Executing external task 'build'...
Using a single directory for all classes from a source set. This behaviour has been deprecated and is scheduled to be removed in Gradle 5.0
	at build_3e0ikl0qk0r006tvk0olcp2lu.run(/Users/jack/easykotlin/chapter2_hello_world_kotlin2js/build.gradle:15)
:compileJava NO-SOURCE
:compileKotlin2Js
:processResources NO-SOURCE
:classes
:jar
:assemble
:compileTestJava NO-SOURCE
:compileTestKotlin2Js NO-SOURCE
:processTestResources NO-SOURCE
:testClasses UP-TO-DATE
:test NO-SOURCE
:check UP-TO-DATE
:build

BUILD SUCCESSFUL in 2s
3 actionable tasks: 3 executed
23:47:08: External task execution finished 'build'.

```

此时，我们可以看到工程目录变为

```
.
├── build
│   └── kotlin-build
│       └── caches
│           └── version.txt
├── build.gradle
├── settings.gradle
└── src
    ├── main
    │   ├── java
    │   ├── kotlin
    │   └── resources
    └── test
        ├── java
        ├── kotlin
        └── resources

12 directories, 3 files
jack@jacks-MacBook-Air:~/easykotlin/chapter2_hello_world_kotlin2js$ tree ..
├── build
│   ├── kotlin
│   │   └── sessions
│   ├── kotlin-build
│   │   └── caches
│   │       └── version.txt
│   ├── libs
│   │   └── chapter2_hello_world_kotlin2js-1.0-SNAPSHOT.jar
│   └── tmp
│       └── jar
│           └── MANIFEST.MF
├── build.gradle
├── settings.gradle
├── src
│   ├── main
│   │   ├── java
│   │   ├── kotlin
│   │   │   └── com
│   │   │       └── easy
│   │   │           └── kotlin
│   │   │               └── App.kt
│   │   └── resources
│   └── test
│       ├── java
│       ├── kotlin
│       └── resources
└── web
    ├── js
    │   ├── app
    │   │   └── com
    │   │       └── easy
    │   │           └── kotlin
    │   │               └── kotlin.kjsm
    │   ├── app.js
    │   ├── app.js.map
    │   └── app.meta.js
    ├── kotlin.js
    └── kotlin.meta.js

26 directories, 14 files

```

这个web目录就是Kotlin代码通过kotlin-stdlib-js-1.1.2.jar编译的输出结果。

其中，app.js代码如下

```js
if (typeof kotlin === 'undefined') {
  throw new Error("Error loading module 'app'. Its dependency 'kotlin' was not found. Please, check whether 'kotlin' is loaded prior to 'app'.");
}
var app = function (_, Kotlin) {
  'use strict';
  var println = Kotlin.kotlin.io.println_s8jyv4$;
  function helloWorld() {
    println('Hello,World!');
  }
  var package$com = _.com || (_.com = {});
  var package$easy = package$com.easy || (package$com.easy = {});
  var package$kotlin = package$easy.kotlin || (package$easy.kotlin = {});
  package$kotlin.helloWorld = helloWorld;
  Kotlin.defineModule('app', _);
  return _;
}(typeof app === 'undefined' ? {} : app, kotlin);

//@ sourceMappingURL=app.js.map

```

里面这段自动生成的代码显得有点绕

```js
var package$com = _.com || (_.com = {});
  var package$easy = package$com.easy || (package$com.easy = {});
  var package$kotlin = package$easy.kotlin || (package$easy.kotlin = {});
  package$kotlin.helloWorld = helloWorld;
  
  Kotlin.defineModule('app', _);
  return _;
```

简化之后的意思表达如下

```js

_.com.easy.kotlin.helloWorld = helloWorld;

```
目的是建立Kotlin代码跟JavaScript代码的映射关系。这样我们在前端代码中调用

```js
  function helloWorld() {
    println('Hello,World!');
  }
```

这个函数时，只要这样调用即可

```
app.com.easy.kotlin.helloWorld()
```


下面我们来新建一个index.html页面，使用我们生成的app.js。代码如下

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>KotlinJS</title>

</head>
<body>


<!-- 优先加载kotlin.js，再加载应用程序代码app.js-->
<script type="text/javascript" src="kotlin.js"></script>
<script type="text/javascript" src="jquery.js"></script>
<script type="text/javascript" src="js/app.js"></script>

<script>
    var kotlinJS = app;
    console.log(kotlinJS.com.easy.kotlin.helloWorld())
</script>
</body>
</html>

```
我们需要优先加载kotlin.js，再加载应用程序代码app.js。
当然，我们仍然可以像以前一样使用诸如jquery.js这样的库。

在浏览器中打开index.html


![螢幕快照 2017-06-08 00.11.15.png](http://upload-images.jianshu.io/upload_images/1233356-1eac26bab3de5362.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

我们可以看到浏览器控制台输出


![螢幕快照 2017-06-08 00.14.57.png](http://upload-images.jianshu.io/upload_images/1233356-85de9abe557023f1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



这个helloWorld() JavaScript函数

```
  var println = Kotlin.kotlin.io.println_s8jyv4$;
  function helloWorld() {
    println('Hello,World!');
  }
```

对应kotlin.js代码中的3755行处的代码：


```js
BufferedOutputToConsoleLog.prototype.flush = function() {
    console.log(this.buffer);
    this.buffer = "";
  };
```


本节工程源代码：https://github.com/EasyKotlin/chapter2_hello_world_kotlin2js

参考资料
===
1.https://kotlinlang.org/docs/reference/compiler-plugins.html

2.http://kotlinlang.org/docs/tutorials/javascript/working-with-modules/working-with-modules.html

