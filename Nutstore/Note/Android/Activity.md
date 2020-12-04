# Activity基本用法

勾选Generate Layout File表示会自动为Ativity创建一个对应的布局文件。

勾选Launcher Acivity表示会自动将该Activity设置为当前项目的主Activity。

勾选Backwards Compatibility表示会为项目启动向下兼容旧版系统模式。

Android讲究逻辑和视图分离，最好每一个Activity都能对应一个布局。布局是用来显示界面内容的。

在res文件夹中新建一个文件夹，然后在文件夹中新建一个布局文件（Layout resource file）

窗口左下方（Linux在右上）布局有两个（Linux有三个，还有一个split）窗口，Design和Text（Linux是code）。

Design是当前的可视化布局编辑器，在这里不仅可以预览当前的布局，还可以拖放编辑布局。

## 1.在Acitivity中创建一个按钮

首先在布局界面创建一个按钮

```xml
<Button
    android:id = "@+id/button1"					<!--定义一个id，如果是引入一个id则不需要+号-->
    android:layout_width= "match_parent"		<!--match_parent表示当前元素和父元素一样宽-->
    android:layout_height = "wrap_content"		<!--warp_content表示当前元素的高度只要能刚好包含里												 <!--面的内容就行-->
    android:text = "Button 1"					<!--元素中显示的文字内容-->
    />
```

在这里创建好按钮后，就可以在Design界面进行显示了，接下来需要在Acticity中进行加载。

```kotlin
setContentView(R.layout.first_layout)
```

调用setContentView()方法来给当前的Activity加载一个布局。项目中的任何资源都会在R文件中生成一个相应的资源id，所以现在first_layout布局的id已经添加到R文件中了。

所有的Activity都要在AndroidManifest.xml中进行注册才能生效。实际上FirstActivity已经在AndroidManifest.xml中注册过了。

```xml
<!--AndroidManifest.xml-->
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.example.activitytest">		<!--在这里定义了包名，所以后面的注册简写就可以-->

    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/Theme.ActivityTest">
        <activity android:name=".FIrstActivity"></activity>			<!--在这里-->
    </application>

</manifest>
```

然后在\<activity\>标签内部加入\<intent-filter\>标签，并声明

```xml
    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/Theme.ActivityTest">
        <activity android:name=".FIrstActivity"
            android:label="This is FirstActivity">
            <intent-filter>
                <action android:name = "android.intent.action.MAIN"/>
                <category android:name="android.intent.category.LAUNCHER"/>
            </intent-filter>
        </activity>
    </application>
```

这样FIrstActivity就成为这个主程序的主Activity了，打开程序时首先打开的就是这个Activity。如果没有声明任何一个主Activity，这个程序仍然可以正常安装，只不过无法在启动器中看到或者打开这个程序，一般作为第三方服务供其他应用在内部进行调用的。

## 2.在Activity中使用Toast

Toast是一种提醒功能，提醒的信息会在一段时间后自动消失，并且不会占用任何屏幕空间。

```kotlin
class FIrstActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.first_layout)
        var button1:Button = findViewById(R.id.button1)/*需要显式指明为Button类型，因为																   *findViewById返回的是一个继承自View的泛型													   *对象，无法进行自动推导。
        											   */
        button1.setOnClickListener{
            //第一个参数是上下文，即需要依赖显示的地方，第二个参数是显示内容，第三个参数是显示时长。
            Toast.makeText(this,"You clicked Button 1",Toast.LENGTH_SHORT).show()
        }
    }
}
```

kotlin在gradle文件的头部默认引入了扩展插件，这个插件会根据布局文件中定义的控件id自动生成一个具有相同名称的变量。可以直接使用这个变量（Linux中不会自动引入这个插件，需要手动添加 kotlin-android-extensions）

```kotlin
/*FIrstActivity.kt*/
package com.example.activitytest

import androidx.appcompat.app.AppCompatActivity
import android.os.Bundle
import android.widget.Button
import android.widget.Toast
import kotlinx.android.synthetic.main.first_layout.*

class FIrstActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.first_layout)
        button1
//        var button1:Button = findViewById(R.id.button1)
//        button1.setOnClickListener{
//            Toast.makeText(this,"You clicked Button 1",Toast.LENGTH_SHORT).show()
//        }
    }
}
```

## 3.在Activity中使用Menu

首先在res目录下新建一个menu文件夹，接着在文件夹下新建一个菜单文件（Menu resource file）

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
    <item
        android:id = "@+id/add_item"
        android:title="Add"
        />
    <item
        android:id = "@+id/remove_item"
        android:title="Remove"
        />
</menu>
```

创建了两个参单项，item标签用来创建具体的某一个菜单项。剩下的和按钮一样，定义一个唯一标识符和显示名称。

接下来在Activity中重写onCrateOptionMenu()方法，重写方法可以使用**Ctrl+O**快捷键

```kotlin
//add menu Ctrl+O
override fun onCreateOptionsMenu(menu: Menu?): Boolean {
    menuInflater.inflate(R.menu.main,menu)
    return true
}
```

这里使用了一个语法糖。

在Activity中重写onOptionsItemSelected()方法，定义菜单响应事件

```kotlin
override fun onOptionsItemSelected(item: MenuItem): Boolean {
    when(item.itemId){
        R.id.add_item -> Toast.makeText(this, "You clicked Add", Toast.LENGTH_SHORT).show()
        R.id.remove_item-> Toast.makeText(this, "You clicked Remove", Toast.LENGTH_SHORT).show()
    }
    return true
}
```

when小括号内调用的是item的getItemId()方法，这是一个语法糖。

## 4.销毁一个Activity

只需要按back就可以销毁Activity，如果从代码进行销毁的话，可以使用finish()方法。

修改按钮监听器中的代码：

```kotlin
button1.setOnClickListener{
    //Toast.makeText(this,"You clicked Button 1",Toast.LENGTH_SHORT).show()
    finish()
}
```

点击按钮即可进行销毁。

## 5.使用显式Intent

新建一个新的Activity，定义一个button2

Intent 是 各组件之间进行交互的一种重要方式，他不仅可以指明当前组件想要执行的动作，还可以在不同组件之间传递数据。Intent一般可用于启动Activity、启动Service以及发送广播等场景。

Intent大致可以分为两种：显式Intent和隐式Intent

Intent(Context packageContext,Class<?> cls),第一个参数要求提供一个启动Activity的上下文，第二个参数Class用于制定想要启动的目标Activity。然后使用startActivity()方法接收一个Intent参数，启动Activity。

修改Activity中按钮的点击事件

```kotlin
        button1.setOnClickListener{
//            Toast.makeText(this,"You clicked Button 1",Toast.LENGTH_SHORT).show()
//            finish()
            val intent = Intent(this,SecondActivity::class.java)
            startActivity(intent)
        }
```

## 6.使用隐式Intent

不直接指定响应的Activity，而是响应一个更合适的Activity

通过在\<activity\>标签下配置\<intent-filter\>的内容，可以指定当前Activity能够响应的action的category。

```xml
<activity android:name=".SecondActivity">
    <intent-filter>
        <action android:name="com.example.activitytest.ACTION_START"/>
        <category android:name="android.intent.category.DEFAULT"/>
    </intent-filter>>
</activity>
```

指明了当前Activity可以响应com.example.activitytest.ACTION_START，category中包含了一些附加信息，更加精确地指明了当前activity能够响应的Intent中还可能带有的category。

只有action和category同时满足条件才能进行响应。

修改Activity中的按钮点击事件

```kotlin
        button1.setOnClickListener{
//            Toast.makeText(this,"You clicked Button 1",Toast.LENGTH_SHORT).show()
//            finish()
//            val intent = Intent(this,SecondActivity::class.java)
//            startActivity(intent)
            val intent = Intent("com.example.activitytest.ACTION_START")
            intent.addCategory("ndroid.intent.category.DEFAULT")
            startActivity(intent)
        }
```

2020.12.04

------

