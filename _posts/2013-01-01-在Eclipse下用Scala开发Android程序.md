title: 在Eclipse下用Scala开发Android程序
tags:
  - Android
  - Scala
date: 2013-01-01 11:31:08
---

(此文是唐古拉山写的)

## 一、 Why Scala？

最直接的想法就是：

1.  先把Scala当作更好的Java用
2.  保护好对Scala语言的已有投资
3.  看看别人怎么说：[Scala on Android](http://www.slideshare.net/jakub.kahovec/scala-on-android-12657430)

## 二、 Why Eclipse?

*   已经习惯了Eclipse
*   Scala-IDE for Eclipse 有了很大改进
*   ...

## 三、 配置环境

## 3.1 下载安装android-sdk

下载页面： [http://developer.android.com/sdk/index.html。](http://developer.android.com/sdk/index.html。)

通过SDK Manager按需下载相关的packages。（我这里主要有Tools, Android 4.1, Android 2.2, Extras/Android support library）

使用AVD Manager创建一个avd, Target为Android 4.1, 选中SNAPSHOT为enabled

将tools和platform-tools目录添加到PATH环境变量

## 3.2 安装Eclipse

使用Eclipse indigo JEE版, 下载页面是: [http://www.eclipse.org/downloads/packages/release/indigo/sr2](http://www.eclipse.org/downloads/packages/release/indigo/sr2)

## 3.3 安装配置ADT plugin for Eclipse

具体说明参考 [http://developer.android.com/sdk/installing/installing-adt.html](http://developer.android.com/sdk/installing/installing-adt.html)

Update Site是: [https://dl-ssl.google.com/android/eclipse/](https://dl-ssl.google.com/android/eclipse/)

## 3.4 安装Scala IDE for Eclipse

使用Scala IDE 2.1 Milestone 1版， 参考http://scala-ide.org/download/milestone.html#scala_ide_21_milestone_1

我使用的是For Scala2.9.x的版本, Update Site 是：[http://download.scala-ide.org/releases-29/milestone/site](http://download.scala-ide.org/releases-29/milestone/site)

## 3.5 安装AndroidProguardScala Nature"

AndroidProguardScala这个插件集成了Proguard, 主要用来去掉工程依赖包中用不到的类， 有了它，就算引入Scala运行时库和其他依赖包， 也能保证发布包apk足够的苗条(等下我们通过一个实际的工程来验证)

安装方式参考https://github.com/banshee/AndroidProguardScala

Update Site是：[https://androidproguardscala.s3.amazonaws.com/UpdateSiteForAndroidProguardScala](https://androidproguardscala.s3.amazonaws.com/UpdateSiteForAndroidProguardScala)

## 四、用Scala写个HelloWorld Android应用

## 4.1 使用向导创建Android工程并配置

使用Eclipse向导创建一个"Android Applicaton Project", 选择创建一个Blank Activity即可

*   给工程添加"Scala Nature": 工程, 右键 -> Configure -> Add Scala Nature
*   给工程添加"AndroidProguardScala Nature": 工程, 右键 -> Add AndroidProguardScala Nature

## 4.2 相关代码

修改res/layout/activity_main.xml, 给TextView设置一个id android:id="@+id/hello"

将MainActivity.java删除, 用Scala重写

代码如下:

<div class="mycode">

package com.example.helloworld

import android.os.Bundle     
import android.app.Activity      
import android.view.Menu      
import android.widget.TextView

class MainActivity extends Activity {

  override def onCreate(savedInstanceState: Bundle) {     
    super.onCreate(savedInstanceState)      
    setContentView(R.layout.activity_main)      
    val hello = findViewById(R.id.hello).asInstanceOf[TextView]      
    hello.setText(hello.getText + " 世界!")      
  }

  override def onCreateOptionsMenu(menu: Menu): Boolean = {     
    getMenuInflater().inflate(R.menu.activity_main, menu)      
    true      
  }      
}

</p></div>

## 4.3 运行

工程右键 -> Run As -> Android Application

默认会使用之前创建的Android Virtual Device 仿真器运行

## 4.4 导出apk

工程右键 -> Android Tools -> Export Singed Application Package ..., 按向导一步一步走即可.

导出的apk大概300k, 可以与Java版的做一个对比, Java版的大概150K. Scala版的HelloWorld 体积上个人是可以接受的.

有人将Android的samples用Scala重写, 然后专门做了一个对比, 详细信息请参考: [http://lampwww.epfl.ch/~michelou/android/library-code-shrinking.html](http://lampwww.epfl.ch/~michelou/android/library-code-shrinking.html)

## 五、 总结

用Scala开发Android应用算是走通了. 从上面看, 生成的apk体积方面完全不用担心, 至于像memory footprint等问题, 要等深入之后再来看.