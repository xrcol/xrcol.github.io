---
title: Activity
date: 2023-08-13 23:22:10
tags: Android
categories: Android
---

## Activity

### Activity生命周期

![activity](https://developer.android.com/guide/components/images/activity_lifecycle.png?hl=zh-cn)

#### Activity状态

##### 运行状态

当一个Activity位于返回栈的栈顶时，Activity就处于运行状态

##### 暂停状态

当一个Activity不再处于栈顶位置，但仍然可见时，Activity就进入了暂停状态，比如对话框形式的Activity。只有在内存极低时，系统才会去考虑回收这种Activity

##### 停止状态

当一个Activity不再处于栈顶状态，并且完全不可见时，就进入了停止状态。当其它地方需要时，处理停止状态的Activity有可能会被系统回收

##### 销毁状态

一个Activity从返回栈中移除后就变成了销毁状态。系统倾向于回收处于这种状态的Activity，来保证手机内存充足

#### Activity被回收

当重新回到已经被回收的Activity时，此时会执行Activity的onCreate()方法。为了保存之前的临时数据和状态，需要重写onSaveInstanceState()方法(该方法保证在回收之前一定会调用)，之后在onCreate中进行恢复

```kotlin
override fun onSaveInstanceState(outState: Bundle) {
    super.onSaveInstanceState(outState)
    val tmp = "someting"
    outState.putString("data_key", tmp)
}

override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
    if (savedInstanceState != null) {
        val tmp = savedInstanceState.getString("data_key")

    }
}
```

### Activity消息传递

#### 显示Intent

```kotlin
button.setOnClickListener {
    val intent = Intent(this, SecondActivity::class.java)
    startActivity(intent)
}
```

#### 隐式Intent

需要在AndroidManifest.xml中配置intent-filter

```xml
<activity android:name=".SecondActivity">
    <intent-filter>
        <action android:name="com.example.activitytest.ACTION_START" />
        <category android:name="android.intent.category.DEFAULT" />
    </intent-filter>
</activity>
```

```kotlin
button.setOnClickListener {
    val intent = Intent("com.example.activitytest.ACTION_START")
    intent.addCategory("xxx")
    startActivity(intent)
}
```

##### 打开系统浏览器

```kotlin
button.setOnClickListener {
    val intent = Intent(Intent.ACTION_VIEW)
    intent.data = Uri.parse("https://www.baidu.com")
    startActivity(intent)
}
```

##### 向下一个Activity传递数据

```kotlin
button.setOnClickListener {
    val data = "test"
    val intent = Intent(this, SecondActivity::class.java)
    intent.putExtra("extra_data", data)
    startActivity(intent)
}
```

```kotlin
class SecondActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        val data = intent.getStringExtra("extra_data")
    }
}
```

返回数据给上一个Activity

```kotlin
class MainActivity : AppCompatActivity() {

    private val startForResult = registerForActivityResult(ActivityResultContracts.StartActivityForResult()) { result: ActivityResult ->
        if (result.resultCode == Activity.RESULT_OK) {
            val intent = result.data
            val returnData = intent?.getStringExtra("data_return")
            Log.d("FirstActivity", "return data is $returnData")
        }
    }
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.first_layout)
        val button: Button = findViewById(R.id.button)
        button.setOnClickListener{
            val data = "Hello SecondActivity"
            val intent = Intent(this, SecondActivity::class.java)
            intent.putExtra("extra_data", data)
            startForResult.launch(intent)
        }
    }
}
```

点击返回，回到上一个Activity时，也传递数据，需要在onBackPressedDispatcher中添加callback

```kotlin
class SecondActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        onBackPressedDispatcher.addCallback(object: OnBackPressedCallback(true) {
            override fun handleOnBackPressed() {
                val intent = Intent()
                intent.putExtra("data_return", "Hello FirstActivity")
                setResult(RESULT_OK, intent)
                finish()
            }
        }
    }
}
```

### Activity启动模式

#### standard

在standard模式下，每当启动一个新的Activity，就会在返回栈中入栈，并处于栈顶的位置。对于使用standard模式的Activity，系统不会在乎这个Activity是否已经在返回栈中存在，每次启动都会创建一个该Activity的新实例

#### singleTop

当Activity的启动模式设置为singleTop，在启动Activity时如果发现返回栈的栈顶已经是该Activity，则认为可以直接使用它，不会再创建新的Activity实例

```xml
<activity
      android:name=".MainActivity"
      android:launchMode="singleTop">
</activity>
```

#### singleTask

当Activity的启动模式设置为singleTask，在启动该Activity时，系统首先会在返回栈中检查是否存在该Activity的实例，如果发现已经存在则直接使用该实例，并把在这个Activity之上的所有其它Activity统统出栈；如果没有找到，则创建一个新的实例

#### singleInstance

当Activity的启动模式设置为singleInstance，在这种模式下，会有一个单独的栈来管理这个Activity，不论是哪个应用程序来访问这个Activity，都公用一个栈，来实现共享Activity实例
