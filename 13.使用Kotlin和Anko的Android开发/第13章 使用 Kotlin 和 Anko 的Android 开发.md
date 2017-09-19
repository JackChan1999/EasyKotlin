第13章 使用 Kotlin 和 Anko 的Android 开发
===

## 13.1 什么是 Anko？

Anko (https://github.com/Kotlin/anko)  是一个用 Kotlin 写的Android DSL (Domain-Specific Language)。长久以来，Android视图都是用 XML 来完成布局的。这些 XML可重用性比较差。同时在运行的时候，XML 要转换成 Java 表述，这在一定程度上占用了 CPU 和耗费了电量。

Anko是一个 Kotlin 库, 它使 android 应用程序的开发变得更快、更容易。它使您的代码干净, 易于阅读, 并让您忘记了粗糙的边缘 android sdk 为 java。

Anko由几个部分组成:

|模块|         功能说明|
|---|---|
|Anko Commons|  使得对 intents, dialogs, logging等操作更加简单的轻量级库|
|Anko Layouts| 快速和类型安全的动态的 android 布局库|
|Anko SQLite| 用于 android sqlite 的查询 dsl 和分析库|
|Anko Coroutines|  基于 kotlinx 协程库|

有了Anko 我们就能直接用 Kotlin 在任何的 Activity 、 Fragment 或者 AnkoComponent里来编写视图。

## 13.2 一个简单Anko视图

这里是一个转换成 Anko 的简单 XML 文件。

```xml
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_height="match_parent"
    android:layout_width="match_parent">
    <EditText
        android:id="@+id/todo_title"
        android:layout_width="match_parent"
        android:layout_heigh="wrap_content"
        android:hint="@string/title_hint" />
    <Button
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="@string/add_todo" />
</LinearLayout>
```

用 Anko 描述的同样的视图

```
verticalLayout {
    var title = editText {
        id = R.id.todo_title
        hintResource = R.string.title_hint
    }
    button {
        textResource = R.string.add_todo
        onClick { view -> {
                // 可以在这里添加一些处理逻辑
                title.text = "Foo"
            }
        }
    }
}
```

可以看到在button布局中的onClick监听函数中，因为我们是使用 Kotlin代码来设计视图，所以可以直接使用title变量（editText视图对象）。

## 13.3 快速入门实例

下面我们通过一个“我的日程”待办事项应用，来详细介绍使用 Kotlin 混合 Java，使用 Anko 开发的Android 应用的方法。移动端数据库引擎我们使用 Realm，视图绑定使用Butter Knife。

这个应用程序界面如下所示：

![Kotlin极简教程](http://upload-images.jianshu.io/upload_images/1233356-cba3711fbfd95c9d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![Kotlin极简教程](http://upload-images.jianshu.io/upload_images/1233356-d7afc8397b6be9c3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![Kotlin极简教程](http://upload-images.jianshu.io/upload_images/1233356-d912560d42ac69c5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


## 13.4 使用 Android Studio 新建工程

我们首先在 Android Studio 中新建工程，步骤如下：

第一步，新建项目

![Kotlin极简教程](http://upload-images.jianshu.io/upload_images/1233356-4e3643c7cd6cfee9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

第二步，配置项目基本信息

![Kotlin极简教程](http://upload-images.jianshu.io/upload_images/1233356-0afbd5f256c422e6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

第三步，设置支持设备以及 SDK 版本

![Kotlin极简教程](http://upload-images.jianshu.io/upload_images/1233356-886c980af1df31f4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

第四步，选择 Basic Activity

![Kotlin极简教程](http://upload-images.jianshu.io/upload_images/1233356-c7de31f6b8b44556.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

第五步，使用默认的Activity命名

![Kotlin极简教程](http://upload-images.jianshu.io/upload_images/1233356-39592f366c8e4a5e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

我们将得到一个标准的 Gradle Android 工程：

![Kotlin极简教程](http://upload-images.jianshu.io/upload_images/1233356-a2ed24522aecd3b2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

其中，app 工程 src 目录如下：

```
.
├── androidTest
│   └── java
│       └── com
│           └── easy
│               └── kotlin
│                   └── mytodoapplication
│                       └── ExampleInstrumentedTest.java
├── main
│   ├── AndroidManifest.xml
│   ├── java
│   │   └── com
│   │       └── easy
│   │           └── kotlin
│   │               └── mytodoapplication
│   │                   └── MainActivity.java
│   └── res
│       ├── drawable
│       ├── layout
│       │   ├── activity_main.xml
│       │   └── content_main.xml
│       ├── menu
│       │   └── menu_main.xml
│       ├── mipmap-hdpi
│       │   ├── ic_launcher.png
│       │   └── ic_launcher_round.png
│       ├── mipmap-mdpi
│       │   ├── ic_launcher.png
│       │   └── ic_launcher_round.png
│       ├── mipmap-xhdpi
│       │   ├── ic_launcher.png
│       │   └── ic_launcher_round.png
│       ├── mipmap-xxhdpi
│       │   ├── ic_launcher.png
│       │   └── ic_launcher_round.png
│       ├── mipmap-xxxhdpi
│       │   ├── ic_launcher.png
│       │   └── ic_launcher_round.png
│       └── values
│           ├── colors.xml
│           ├── dimens.xml
│           ├── strings.xml
│           └── styles.xml
└── test
    └── java
        └── com
            └── easy
                └── kotlin
                    └── mytodoapplication
                        └── ExampleUnitTest.java

28 directories, 21 files

```

我们直接在Android 模拟器中（也可以选择用真机）运行它，可以看到如下效果：

![Kotlin极简教程](http://upload-images.jianshu.io/upload_images/1233356-fd623bdd8a8402dc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 13.5 设计UI 界面主题颜色

我们首先把应用名称改成“我的日程”。在文件MyTodoApplication/app/src/main/res/values/strings.xml中：

```
<resources>
    <string name="app_name">MyTodoApplication</string>
    <string name="action_settings">Settings</string>
</resources>

```

改写成

```
<resources>
    <string name="app_name">我的日程</string>
    <string name="action_settings">设置</string>
</resources>

```

再去colors.xml中，设计主题颜色为：
```
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <color name="colorPrimary">#f2fced</color>
    <color name="colorPrimaryDark">#456a7c</color>
    <color name="colorAccent">#8fb3c4</color>
</resources>

```

然后到文件MyTodoApplication/app/src/main/res/layout/activity_main.xml中，设置android.support.v7.widget.Toolbar的背景色为
```
android:background="?attr/colorPrimaryDark"
```

配置android.support.design.widget.FloatingActionButton的图标为：
```
app:srcCompat="drawable/ic_content_add"
```

其中，ic_content_add.png图片是我们添加按钮中间的加号 icon。

## 13.6 配置 Kotlin 与 Anko 依赖

我们默认生成的 app 项目的 Gradle 配置文件build.gradle如下：
```
apply plugin: 'com.android.application'

android {
    compileSdkVersion 25
    buildToolsVersion "25.0.3"
    defaultConfig {
        applicationId "com.easy.kotlin.mytodoapplication"
        minSdkVersion 21
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
    compile 'com.android.support:design:25.3.1'
    testCompile 'junit:junit:4.12'
}

```

下面我们在 app 项目的build.gradle里面加上Kotlin 、Anko 、Realm、Butter Knife 等依赖。

### 13.6.1 Kotlin依赖

首先，启用插件`kotlin-android` :
```
apply plugin: 'kotlin-android'
```
然后，添加构建脚本
```
buildscript {

}
```
我们使用 Kotlin 1.1.3版本。在构建脚本中添加kotlin-gradle-plugin依赖，使用 Kotlin 对应的版本号。
```
buildscript {
    ext.kotlin_version = '1.1.3'
    repositories {
        mavenCentral()
    }
    dependencies {
        classpath "org.jetbrains.kotlin:kotlin-gradle-plugin:$kotlin_version"
    }
}
```
在项目依赖里添加 Kotlin 标准库：
```
// Kotlin
compile "org.jetbrains.kotlin:kotlin-stdlib:$kotlin_version"
```

### 13.6.2 添加 Kotlin 源代码目录

首先，我们在 src/main/下面新建一个 kotlin 目录，来存放 Kotlin源码。然后在 build.gradle 文件里的 `android {}` 配置里面添加Java的编译路径：

```
android {
    ...
    sourceSets {
        // += , 在main中创建kotlin文件夹, 用于存放kotlin代码
        main.java.srcDirs += 'src/main/kotlin'
    }
}
```

刚添加完毕，src/main/kotlin 还没有变成源码目录的蓝色，这个时候点击下图右上角的 Sync Now :

![Kotlin极简教程](http://upload-images.jianshu.io/upload_images/1233356-32539498ceffa7cd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

Gradle 同步完毕，即可看到kotlin 目录已经变成蓝色的源码目录了：

![Kotlin极简教程](http://upload-images.jianshu.io/upload_images/1233356-b128b7270abbf0fd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



### 13.6.3 Anko依赖

在项目依赖里添加
```
    // Anko
    compile 'org.jetbrains.anko:anko-sdk15:0.8.2' // sdk19, sdk21, sdk23 are also available
    compile 'org.jetbrains.anko:anko-support-v4:0.8.2' // In case you need support-v4 bindings
    compile 'org.jetbrains.anko:anko-appcompat-v7:0.8.2' // For appcompat-v7 bindings

```

### 13.6.4 Realm依赖

```
    compile 'io.realm:realm-android:0.87.1'
    compile 'com.github.thorbenprimke:realm-recyclerview:0.9.12' // 在jitpack.io上
```

其中，Realm是一个轻量级的跨平台移动数据库引。Realm 简单易用，model 设计在代码中，更加易于维护，同时其性能也不错。在Android开发中，它可以替代 SQLite 和 ORM 框架。相比SQLite，Realm更快并且具有很多现代数据库的特性，比如支持JSON，流式api，数据变更通知，以及加密支持。

RecyclerView用于在有限的窗口展现大量的数据，相比ListView、GridView，RecyclerView标准化了ViewHolder，而且更加灵活，可以轻松实现ListView实现不了的样式和功能。我们使用的`com.github.thorbenprimke:realm-recyclerview` 依赖包在在jitpack.io上， 所以我们还需要配置一下仓库地址：

```
repositories {
    mavenCentral()
    maven { url "https://jitpack.io" }
}
```

提示：realm-recyclerview的 Github 地址是 https://github.com/thorbenprimke/realm-recyclerview

另外， Kotlin使用 Realm 还要加上注解处理的依赖库：
```
    // kotlin使用realm的注解处理依赖库
    kapt "io.realm:realm-annotations:0.87.1"
    kapt "io.realm:realm-annotations-processor:0.87.1"
```



### 13.6.5 Butter Knife依赖

Butter Knife是基于注解处理方式工作：通过对代码注解自动生成模板代码。我们添加其依赖如下：

```
    // Butter Knife，专门为Android View设计的绑定注解，专业解决各种findViewById
    compile 'com.jakewharton:butterknife:8.7.0'
    annotationProcessor 'com.jakewharton:butterknife-compiler:8.7.0'
```

Butter Knife主要是用来做Android视图的成员变量和属性的数据绑定。在开发过程中，我们通常要写大量的findViewById和点击事件，像初始view、设置view监听这样简单而重复的操作会显得比较繁琐。而我们有了 Butter Knife，就可以通过使用注解直接生成样板代码。例如，在 Java 中我们可以通过在字段上使用 @BindView 来替代 findViewById 的调用。上面的配置中的`annotationProcessor 'com.jakewharton:butterknife-compiler:8.7.0'`就是来处理这些注解从而生成样板代码的。

```
@Bind(R.id.todo_item_todo_title)
public TextView todoTitle;

@Bind(R.id.todo_item_todo_content)
public TextView todoContent;
```

而在 Kotlin 中使用Butter Knife情况有些不同，需要作额外的配置。

如果在Kotlin中直接使用ButterKnife的注解方式的话，会出现空指针的异常，导致绑定失败。例如
```
@Bind(R.id.todos_recycler_view)
var realmRecyclerView: RealmRecyclerView? = null
```
运行会报错：
```
Caused by: kotlin.KotlinNullPointerException 
at com.easy.kotlin.mytodoapplication.TodoListFragment.onResume(TodoListFragment.kt:43)
```

一般情况下，我们使用Kotlin集成 Java 生态的一些框架的时候，像 Spring Boot，JPA，Butter Knife，Realm等，都需要一些额外的插件或者依赖来“填充缝隙”（例如：all-open, kotterknife，realm-annotations等）， 所谓Kotlin 与 Java 的无缝集成，很多时候并非Java 中怎么用，Kotlin就直接拿过来就怎么用，往往是要再添加一些插件或者额外的配置等。

那么要如何才能在Kotlin的环境中使用ButterKnife呢？

在早些时候，ButterKnife的作者已经帮我们想好解决方案了，那就是——KotterKnife，见名知意。KotterKnife的GitHub地址是：https://github.com/JakeWharton/kotterknife 。这个插件是建立在ButterKnife 7的基础上的。

下面我们配置一下在 Kotlin 中使用 Butter Knife 的依赖库 KotterKnife。

首先在repositories中添加KotterKnife的仓库地址（KotterKnife不在 Maven Center 仓库中，而是在oss.sonatype.org仓库中。这么多仓库，要是哪天能统一用一个就方便多了）。
```
repositories {
    ...
    maven { url 'https://oss.sonatype.org/content/repositories/snapshots/' }
}
```
然后在dependencies里面添加依赖
```
dependencies {
    ...
    compile 'com.jakewharton:butterknife:7.0.1'
    compile 'com.jakewharton:kotterknife:0.1.0-SNAPSHOT'
}
```

采用这种方式的配置，我们的视图注入代码如下
```
val todoTitle: TextView by bindView(R.id.todo_item_todo_title)
val todoContent: TextView by bindView(R.id.todo_item_todo_content)
```

这样的代码看起来不是那么的优雅，还没有在 Java 中直接使用注解来的简单好看。同时要注意的是，如果使用 kotterknife 0.1.0 + butterknife:7.0.1 ，同时使用 Java 跟 Kotlin 混合编程的场景中使用 Butter Knife，发现配了KotterKnife 之后的 Java 的注解式写法就失效了。也就是说，如果我们上面添加了KotterKnife的依赖，那么 Java 代码中同时使用 Butter Knife 注解的地方会绑定失败。不过这个问题，在后面的新版本中已经解决。例如在butterknife 8.7.0中，我们可以直接添加下面的依赖项：

```
    compile 'com.jakewharton:butterknife:8.7.0'
    annotationProcessor 'com.jakewharton:butterknife-compiler:8.7.0'
    kapt 'com.jakewharton:butterknife-compiler:8.7.0'
```
其中，`annotationProcessor 'com.jakewharton:butterknife-compiler:8.7.0'` 是 Java 的butterknife注解处理器。` kapt 'com.jakewharton:butterknife-compiler:8.7.0'` 是 Kotlin 的butterknife注解处理器（Kotlin Annotation processing tool，kapt）。

这样我们的代码就继续优雅简洁下去了：

```
@BindView(R.id.todo_item_todo_title)
lateinit var todoTitle: TextView
@BindView(R.id.todo_item_todo_content)
lateinit var todoContent: TextView
```

其中，lateinit 修饰符允许声明非空类型，并在对象创建后(构造函数调用后)初始化。 不使用 lateinit 则需要声明可空类型并且有额外的空安全检测操作。

当然，我们使用 Butter Knife 的同时，仍然可以使用原生的 findViewById :

```
class MainActivity : AppCompatActivity() {
    var fab: FloatingActionButton? = null

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        val toolbar = findViewById(R.id.toolbar) as Toolbar
        setSupportActionBar(toolbar)

        fab = findViewById(R.id.fab) as FloatingActionButton

        // 添加日程事件
        fab?.setOnClickListener { _ ->
            ...
            hideFab()
        }
    }

    fun hideFab() {
        fab?.visibility = View.GONE
    }

    fun showFab() {
        fab?.visibility = View.VISIBLE
    }

}
```


## 13.7 将MainActivity.java 转成 Kotlin 代码

选中默认生成的MainActivity.java， 我们使用 IDEA 的 Code > Convert Java File to Kotlin File :

![Kotlin极简教程](http://upload-images.jianshu.io/upload_images/1233356-0ca9bcd9e85ac9f5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

点击转换，即可看到转换成 Kotlin 的代码：

```
package com.easy.kotlin.mytodoapplication

import android.os.Bundle
import android.support.design.widget.FloatingActionButton
import android.support.design.widget.Snackbar
import android.support.v7.app.AppCompatActivity
import android.support.v7.widget.Toolbar
import android.view.Menu
import android.view.MenuItem

class MainActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        val toolbar = findViewById(R.id.toolbar) as Toolbar
        setSupportActionBar(toolbar)

        val fab = findViewById(R.id.fab) as FloatingActionButton
        fab.setOnClickListener { view ->
            Snackbar.make(view, "Replace with your own action", Snackbar.LENGTH_LONG)
                    .setAction("Action", null).show()
        }
    }

    override fun onCreateOptionsMenu(menu: Menu): Boolean {
        // Inflate the menu; this adds items to the action bar if it is present.
        menuInflater.inflate(R.menu.menu_main, menu)
        return true
    }

    override fun onOptionsItemSelected(item: MenuItem): Boolean {
        // Handle action bar item clicks here. The action bar will
        // automatically handle clicks on the Home/Up button, so long
        // as you specify a parent activity in AndroidManifest.xml.
        val id = item.itemId

        if (id == R.id.action_settings) {
            return true
        }

        return super.onOptionsItemSelected(item)
    }
}

```

看，这就是Android 开发者，从 Java无缝转到 Kotlin 的过程。

我们把这个MainActivity.kt放到对应的 src/main/kotlin 目录下。首先新建`package com.easy.kotlin.mytodoapplication` , 直接在 IDEA 中把这个MainActivity.kt 拖到这个package 下面即可。现在我们的工程目录是下面这个样子

![Kotlin极简教程](http://upload-images.jianshu.io/upload_images/1233356-1b7628842782b84e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


## 13.8 在 Kotlin 中使用 Realm

我们需要添加针对 Kotlin 的realm注解处理的库：

```
    kapt "io.realm:realm-annotations:0.87.1"
    kapt "io.realm:realm-annotations-processor:0.87.1"
```

## 13.9 添加日程实体类

我们先从领域模型的建立开始。首先我们需要设计一个极简的待办事项的实体类 Todo, 它有主键 id、标题、内容三个字段。

```
@RealmClass
open class Todo : RealmObject() {
    @PrimaryKey
    open var id: String = "-1"
    open var title: String = "日程"
    open var content: String = "事项"
}
```

然后，我们写一个应用程序入口类`MyTodoApplication`继承`android.app.Application`, 在 onCreate() 里面初始化 Realm 数据库的配置。代码如下：

```
class MyTodoApplication : Application() {
    override fun onCreate() {
        super.onCreate()

        val config = RealmConfiguration.Builder(this)
                .name("realm.my_todos")// 库文件名
                .encryptionKey(getKey())  // 加密
                .schemaVersion(1)  // 版本号
                .deleteRealmIfMigrationNeeded()
                .build()

        Realm.setDefaultConfiguration(config)// 设置默认 RealmConfiguration
        
    }

    /**
     * 64 bits
     * @return
     */
    private fun getKey(): ByteArray {
        return byteArrayOf(0, 1, 2, 3, 4, 3, 2, 1, 0, 1, 2, 3, 4, 3, 2, 1, 0, 1, 2, 3, 4, 3, 2, 1, 0, 1, 2, 3, 4, 3, 2, 1, 0, 1, 2, 3, 4, 3, 2, 1, 0, 1, 2, 3, 4, 3, 2, 1, 0, 1, 2, 3, 4, 3, 2, 1, 0, 1, 2, 3, 4, 3, 2, 1)
    }
}
```

RealmConfiguration.Builder里面如果没有deleteRealmIfMigrationNeeded()的话，会如下报错误：

```
Caused by: io.realm.exceptions.RealmMigrationNeededException: 
RealmMigration must be provided ...
at com.easy.kotlin.mytodoapplication.TodoListFragment.onActivityCreated(TodoListFragment.kt:36)
```

提示： 更多关于 realm 数据库的相关内容可参考 https://realm.io/docs/


## 13.10 添加日程事件

现在我们点击添加日程的浮层按钮中，添加切换到 “日程添加编辑” `TodoEditFragment`的逻辑。

```
// 添加日程事件
fab?.setOnClickListener { _ ->
    // Snackbar.make(view, "Replace with your own action", Snackbar.LENGTH_LONG).setAction("Action", null).show()
    val todoEditFragment = TodoEditFragment()
    getSupportFragmentManager()
            .beginTransaction()
            .replace(R.id.content_main, todoEditFragment, todoEditFragment.javaClass.getSimpleName())
            .addToBackStack(todoEditFragment.javaClass.getSimpleName())
            .commit()

    hideFab()
}
```

## 13.11 添加日程界面

下面我们来完成这个添加日程的界面。

![Kotlin极简教程](http://upload-images.jianshu.io/upload_images/1233356-967218566488993f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

我们采用Fragment来实现。首先新建一个TodoEditFragment继承Fragment() ：
```
class TodoEditFragment : Fragment() {
    val realm: Realm = Realm.getDefaultInstance()
    var todo: Todo? = null

    companion object {
        val TODO_ID_KEY: String = "todo_id_key"

        fun newInstance(id: String): TodoEditFragment {
            var args: Bundle = Bundle()
            args.putString(TODO_ID_KEY, id)
            var todoEditFragment: TodoEditFragment = newInstance()
            todoEditFragment.arguments = args
            return todoEditFragment
        }

        fun newInstance(): TodoEditFragment {
            return TodoEditFragment()
        }
    }

    override fun onCreateView(inflater: LayoutInflater?, container: ViewGroup?, savedInstanceState: Bundle?): View? {
        return UI {
            // AnkoContext

            verticalLayout {
                padding = dip(30)
                var title = editText {
                    // editText 视图
                    id = R.id.todo_title
                    hintResource = R.string.title_hint
                }

                var content = editText {
                    id = R.id.todo_content
                    height = 400
                    hintResource = R.string.content_hint
                }
                button {
                    // button 视图
                    id = R.id.todo_add
                    textResource = R.string.add_todo
                    textColor = Color.WHITE
                    setBackgroundColor(Color.DKGRAY)
                    onClick { _ -> createTodoFrom(title, content) }
                }
            }
        }.view
    }

    override fun onActivityCreated(savedInstanceState: Bundle?) {
        super.onActivityCreated(savedInstanceState)

        if (arguments != null && arguments.containsKey(TODO_ID_KEY)) {

            val todoId = arguments.getString(TODO_ID_KEY)
            todo = realm.where(Todo::class.java).equalTo("id", todoId).findFirst()

            val todoTitle = find<EditText>(R.id.todo_title)
            todoTitle.setText(todo?.title)

            val todoContent = find<EditText>(R.id.todo_content)
            todoContent.setText(todo?.content)

            val add = find<Button>(R.id.todo_add)
            add.setText(R.string.save)
        }
    }

    override fun onDestroy() {
        super.onDestroy()
        realm.close()
    }

    /**
     *  新增待办事项，存入Realm数据库
     *
     *  @param title the title edit text.
     *  @param todoContent the content edit text.
     */
    private fun createTodoFrom(title: EditText, todoContent: EditText) {

        realm.beginTransaction()
        // Either update the edited object or create a new one.
        var t = todo ?: realm.createObject(Todo::class.java)
        t.id = todo?.id ?: UUID.randomUUID().toString()
        t.title = title.text.toString()
        t.content = todoContent.text.toString()

        realm.commitTransaction()
        activity.supportFragmentManager.popBackStack()
    }

}
```

其中，我们重点讲下 Anko 的 UI 布局部分的代码。

```
return UI {
    // AnkoContext

    verticalLayout {
        padding = dip(30)
        var title = editText {
            // editText 视图
            id = R.id.todo_title
            hintResource = R.string.title_hint
        }

        var content = editText {
            id = R.id.todo_content
            height = 400
            hintResource = R.string.content_hint
        }
        button {
            // button 视图
            id = R.id.todo_add
            textResource = R.string.add_todo
            textColor = Color.WHITE
            setBackgroundColor(Color.DKGRAY)
            onClick { _ -> createTodoFrom(title, content) }
        }
    }
}.view
```

我们使用 Kotlin 的代码 Anko DSL 创建了一个垂直方向的线性布局(用代码写配置写布局要比 XML 灵活方便多了)。 其中 UI 函数
```
fun Fragment.UI(init: AnkoContext<Fragment>.() -> Unit) = createAnkoContext(activity, init)

```
是Fragment的扩展函数，它接收一个函数

```
init: AnkoContext<Fragment>.() -> Unit
```
init 的入参是AnkoContext类型。

而verticalLayout函数则是ViewManager的内联扩展函数。

```
inline fun ViewManager.verticalLayout(init: _LinearLayout.() -> Unit): LinearLayout {
    return ankoView(`$$Anko$Factories$CustomViews`.VERTICAL_LAYOUT_FACTORY, init)
}
```

从这些例子我们可以看出 Kotlin 的函数扩展功能相当实用，尤其在 DSL 中用的非常广泛。

在 verticalLayout 代码段内部，创建了三个Android的控件 - 两个 editText 视图和一个 button 视图。这里视图的属性都在一行里面设置好了。

```
padding = dip(30)
var title = editText {
    // editText 视图
    id = R.id.todo_title
    hintResource = R.string.title_hint
}

var content = editText {
    id = R.id.todo_content
    height = 400
    hintResource = R.string.content_hint
}
button {
    // button 视图
    id = R.id.todo_add
    textResource = R.string.add_todo
    textColor = Color.WHITE
    setBackgroundColor(Color.DKGRAY)
    onClick { _ -> createTodoFrom(title, content) }
}
```
这样的视图文件要比 XML 优雅许多了，XML 的配置有时候让人看了就心生烦恼。

我们可以看下按钮控件定义的地方。按钮有一个点击监听函数是定义在视图定义文件里面的。在定义按钮之前，有两个参数 title 和 content 的方法 createTodoFrom 已经被调用了。最后，通过在 AnkoContext （UI 类）上调用 view 属性`UI {...}.view`来返回视图。


这里的 ids 被设置为 R.id.<id_name>。这些 ids 需要手工在一个加做 ids.xml 的文件里创建，这个文件放在 app/src/main/res/values/ids.xml。如果这个文件不存在就创建它。文件内容如下：

```
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <item name="todo_title" type="id" />
    <item name="todo_content" type="id" />
    <item name="todo_add" type="id" />
</resources>
```
这个 ids.xml 文件定义了所有能够被代码引用到的各种视图的 ids。

## 13.12 保存到 Realm 中

新增待办事项，存入Realm数据库：

```
    private fun createTodoFrom(title: EditText, todoContent: EditText) {

        realm.beginTransaction()
        // Either update the edited object or create a new one.
        var t = todo ?: realm.createObject(Todo::class.java)
        t.id = todo?.id ?: UUID.randomUUID().toString()
        t.title = title.text.toString()
        t.content = todoContent.text.toString()

        realm.commitTransaction()

        activity.supportFragmentManager.popBackStack()
    }
```


## 13.13 用RecyclerView 来展示待办事项

下面我们来实现这个页面。

![Kotlin极简教程](http://upload-images.jianshu.io/upload_images/1233356-965e8a49b3f96510.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

首先，这个是主页面，对应 activity_main.xml 视图, 文件内容如下：

```

<?xml version="1.0" encoding="utf-8"?>
<android.support.design.widget.CoordinatorLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:fitsSystemWindows="true"
    tools:context="com.easy.kotlin.mytodoapplication.MainActivity">

    <android.support.design.widget.AppBarLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:theme="@style/AppTheme.AppBarOverlay">

        <android.support.v7.widget.Toolbar
            android:id="@+id/toolbar"
            android:layout_width="match_parent"
            android:layout_height="?attr/actionBarSize"
            android:background="?attr/colorPrimaryDark"
            app:popupTheme="@style/AppTheme.PopupOverlay" />

    </android.support.design.widget.AppBarLayout>

    <include layout="@layout/content_main" />

    <android.support.design.widget.FloatingActionButton
        android:id="@+id/fab"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="bottom|end"
        android:layout_margin="@dimen/fab_margin"
        app:srcCompat="@drawable/ic_content_add" />

</android.support.design.widget.CoordinatorLayout>

```

我们的待办事项列表视图是fragment_todos.xml， 文件内容如下：
```
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:paddingBottom="@dimen/activity_vertical_margin"
    android:paddingLeft="@dimen/activity_horizontal_margin"
    android:paddingRight="@dimen/activity_horizontal_margin"
    android:paddingTop="@dimen/activity_vertical_margin"
    tools:context=".TodosFragment"
    tools:showIn="@layout/activity_main">

    <co.moonmonkeylabs.realmrecyclerview.RealmRecyclerView
        android:id="@+id/todos_recycler_view"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:rrvEmptyLayoutId="@layout/empty_view"
        app:rrvIsRefreshable="false"
        app:rrvLayoutType="LinearLayout" />

</RelativeLayout>

```

我们看下`RealmRecyclerView`的配置：

|配置项 | 功能说明|
|-------|--------------|
|app:rrvEmptyLayoutId|当列表为空的时候的显示页面|
|app:rrvIsRefreshable|是否支持下拉刷新，通过setOnRefreshListener 或 setRefreshing来进行事件处理|
|app:rrvLayoutType| 配置LayoutManager，可选项是：LinearLayout，Grid，LinearLayoutWithHeaders等|




下面我们来实现这个TodosFragment 。

首先新建TodosFragment类，继承如下面代码所示：
```
class TodosFragment : Fragment(), TodoAdapter.TodoItemClickListener {
    @BindView(R.id.todos_recycler_view)
    lateinit var realmRecyclerView: RealmRecyclerView

    private var realm: Realm? = null
    ...
}
```

其中，TodoAdapter是继承了RealmBasedRecyclerViewAdapter的适配器类。我们在 TodoAdapter 里面定义了一个视图持有类：
```
    inner class ViewHolder(view: View, private val clickListener: TodoItemClickListener?) :
            RealmViewHolder(view), View.OnClickListener {

        // Bind a field to the view for the specified ID. The view will automatically be cast to the field type
        @BindView(R.id.todo_item_todo_title)
        lateinit var todoTitle: TextView
        // val todoTitle: TextView by bindView(R.id.todo_item_todo_title)
        @BindView(R.id.todo_item_todo_content)
        lateinit var todoContent: TextView
        // val todoContent: TextView by bindView(R.id.todo_item_todo_content)

        init {
            // Bind annotated fields and methods
            ButterKnife.bind(this, view)
            view.setOnClickListener(this)
        }

        override fun onClick(v: View) {
            clickListener?.onClick(v, realmResults[adapterPosition])
        }
    }
```
在ViewHolder初始化 View 的时候，我们使用ButterKnife进行了绑定

```
init {
            // Bind annotated fields and methods
            ButterKnife.bind(this, view)
            view.setOnClickListener(this)
        }
```


待办事项监听器类：

```
    interface TodoItemClickListener {
        fun onClick(caller: View, todo: Todo)
    }
```

我们在TodosFragment中实现这个方法：

```
    override fun onClick(caller: View, todo: Todo) {
        (activity as MainActivity).hideFab()

        val todoEditFragment = TodoEditFragment.newInstance(todo.id)
        activity.supportFragmentManager
                .beginTransaction()
                .replace(R.id.content_main, todoEditFragment, todoEditFragment.javaClass.getSimpleName())
                .addToBackStack(todoEditFragment.javaClass.getSimpleName())
                .commit()
    }
```

点击待办事项，把当前的content_main切换成编辑事项 EditFragment的视图。



然后我们在TodoAdapter中重写RealmBasedRecyclerViewAdapter的onCreateRealmViewHolder和onBindRealmViewHolder方法。

```
    override fun onCreateRealmViewHolder(viewGroup: ViewGroup, viewType: Int): ViewHolder {
        val v = inflater.inflate(R.layout.todo_item, viewGroup, false)
        return ViewHolder(v, clickListener)
    }

    override fun onBindRealmViewHolder(viewHolder: ViewHolder, position: Int) {
        val todo = realmResults[position]

        viewHolder.todoTitle.setText(todo.title)
        viewHolder.todoTitle.fontFeatureSettings = "font-size:12px"
        viewHolder.todoTitle.setTextColor(Color.argb(255, 69, 106, 124))

        viewHolder.todoContent.setText(todo.content)
    }

```

我们在添加（保存）完事项的时候，回到之前的列表页面：

```
private fun createTodoFrom(title: EditText, todoContent: EditText) {
        realm.beginTransaction()
        // Either update the edited object or create a new one.
        var t = todo ?: realm.createObject(Todo::class.java)
        t.id = todo?.id ?: UUID.randomUUID().toString()
        t.title = title.text.toString()
        t.content = todoContent.text.toString()

        realm.commitTransaction()

        activity.supportFragmentManager.popBackStack()
    }
```

当回退到待办事项列表的时候，我们在TodosFragment中的 `onResume()` 函数中来实现数据的更新展示：

```
override fun onResume() {
        super.onResume()
        val todos = realm!!.where(Todo::class.java).findAll()
        Log.i(MY_TAG, "onResume: ${todos}")
        Log.i(MY_TAG, "onResume: realmRecyclerView = ${realmRecyclerView} ")
        val adapter = TodoAdapter(activity, todos, true, true, this)
        realmRecyclerView.setAdapter(adapter)
    }
```

其中，`val todos = realm!!.where(Todo::class.java).findAll()` 是去 Realm 数据库中查询出所有Todo对应的实体记录。

然后，通过适配器`val adapter = TodoAdapter(activity, todos, true, true, this)`把数据装配到RecyclerView中 `realmRecyclerView.setAdapter(adapter)` 。

## 13.14 运行测试

编译安装应用，我们就可以看到如下的界面了，我们可以在里面添加编辑我们的待办事项。

![Kotlin极简教程](http://upload-images.jianshu.io/upload_images/1233356-53ff659c13e6e703.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![Kotlin极简教程](http://upload-images.jianshu.io/upload_images/1233356-75d1e23143f7beed.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![Kotlin极简教程](http://upload-images.jianshu.io/upload_images/1233356-ba713c9d892b6582.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



## 本章小结

Android 中经常出现的空引用、API的冗余样板式代码等都是是驱动我们转向 Kotlin 语言的动力。另外，Kotlin 的 Android 视图 DSL  Anko帮我们从繁杂的 XML 视图配置文件中解放出来。我们可以像在 Java 中一样方便的使用 Android 开发的流行的库诸如 Butter Knife、Realm、RecyclerView等。当然，我们使用 Kotlin 集成这些库来进行 Andorid 开发，既能够直接使用我们之前的开发库，又能够从 Java 语言、Android API 的限制中出来。这不得不说是一件好事。

下一章我们介绍使用 Kotlin 创建 DSL。

本章工程源码：

https://github.com/EasyKotlin/chapter13_kotlin_android


--------------------------------------

# 《Kotlin极简教程》正式上架：

> #### [点击这里 > *去京东商城购买阅读* ](https://item.jd.com/12181725.html)
> #### [点击这里 > *去天猫商城购买阅读* ](https://detail.tmall.com/item.htm?id=558540170670)

#### 非常感谢您亲爱的读者，大家请多支持！！！有任何问题，欢迎随时与我交流~
--------------------------------------
