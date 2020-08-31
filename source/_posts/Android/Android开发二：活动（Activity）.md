---
title: Android开发二：活动（Activity）
date: 2020-08-30 09:21:58
tags:
	- 移动端
categories:
	- Android
fileName: Android2-activity
---

## 从0构建一个活动

### 构建活动及布局

1. 在创建项目时选择**“Add no Activity”**
2. 在项目架构中的app->src->main下的包中右击，选择新建一个Activity，命名为FirstActivity
3. 在布局文件夹layout中新建一个布局，命名为first_layout，并修改加上一个按钮，代码如下

```
<Button
    android:id="@+id/button_1"	<!-- 定义id用于代码中使用 -->
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:text="Button 1"
    />
```

4. 在**AndroidManifest.xml**文件中注册活动，使用`<activity>`标签注册活动（AS已经自动注册好了），并放在`<application>`标签内，之后

### 使用Toast完成消息提醒

**Android中的Toast类似于其他GUI中的messagebox，“弹出”一条消息**

```java
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.first_layout);
    Button button1 = (Button) findViewById(R.id.button_1);	// 获取该活动中的按钮
    button1.setOnClickListener(new View.OnClickListener() {	// 为按钮注册一个监听器
        @Override
        public void onClick(View view) {	// 点击按钮，即执行监听器下的onClick方法
            Toast.makeText(FirstActivity.this, "You clicked Button 1",
                    Toast.LENGTH_SHORT).show();
        }
    });
}
```

其中makeText()包含三个参数，第一个是要求的上下文，第二个是显示的文本内容，第三个是显示的时长

<img src="http://cdn.ziyedy.top/Android%E5%BC%80%E5%8F%91%E4%BA%8C%EF%BC%9A%E6%B4%BB%E5%8A%A8%EF%BC%88Activity%EF%BC%89/toast-demo.png" style="zoom: 50%;" />

### 使用menu

1. 在res目录下创建一个menu文件，之后创建一个menu资源文件。
2. 之后使用`<item>`标签创建一个菜单项，如下

```
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
    <item
        android:id="@+id/add_item"	<!-- 创建唯一标识符 -->
        android:title="Add" />	<!-- 创建菜单项的名称 -->
    <item
        android:id="@+id/remove_item"
        android:title="Remove" />
</menu>
```

3. 之后在相应活动中重写`onCreateOptionsMenu`函数，用以显示菜单，如下

```java
@Override
public boolean onCreateOptionsMenu(Menu menu) {
    getMenuInflater().inflate(R.menu.main, menu);	// 
    return true;	// 返回true表示允许菜单显示出来
}
```

4. 定义菜单响应事件，重写`onOptionsItemSelected()`方法

```
@Override
public boolean onOptionsItemSelected(@NonNull MenuItem item) {
    switch (item.getItemId()) {
        case R.id.add_item:
            Toast.makeText(this, "You clicked Add", Toast.LENGTH_SHORT).show();
            break;
        case R.id.remove_item:
            Toast.makeText(this, "You clicked remove", Toast.LENGTH_SHORT).show();
            break;
        default:
    }
    return true;
}
```

<img src="http://cdn.ziyedy.top/Android%E5%BC%80%E5%8F%91%E4%BA%8C%EF%BC%9A%E6%B4%BB%E5%8A%A8%EF%BC%88Activity%EF%BC%89/menu-demo.png" style="zoom:50%;" />



### 使用finish()销毁一个活动，类似于返回键

使用Activity类提供的**finish()**方法，取得的效果跟按下Back一致





## 使用Intent构建不同活动间的联系

intent是Android程序中**各组件之间进行交互的一种重要方式**，一般可用于启动活动、启动服务以及发送广播。根据指定响应对象是否直接可以分为显式Intent和隐式Intent。

以下，像之前一样构建一个`SecondActivity`，用于响应实验。

### 显式Intent

**直接指定响应对象**，如下（点击按钮即可跳转到第二个页面）

```
button1.setOnClickListener(new View.OnClickListener() {
    @Override
    public void onClick(View view) {
        Intent intent = new Intent(FirstActivity.this, SecondActivity.class);	// 由上下文响应目标活动
        startActivity(intent);	// 启动传入的Intent参数
    }
});
```

`Intent(Context packageContext, Class <?> cls)`，第一个参数 Context 要求提供一个启动活动的上下文，第二个参数 Class 则是指定想要启动的目标活动

Activity 类中提供了一个 startActivity() 方法，这个方法是专门用于启动活动的，它接收一个 Intent 参数，这里我们将构建好的 Intent 传入 startActivity() 方法就可以启动目标活动了。



### 隐式Intent

隐式Intent**并不明确指出我们想要启动哪一个活动，而是指定了一系列更为抽象的 action 和 category 等信息，然后交由系统去分析这个 Intent，并帮我们找出合适的活动**（即能够相应该隐式intent的活动）去启动

通过在`<activity>`标签下配置`< intent-filter >`的内容，可以指定当前活动能够响应的 action 和 category。同时，**只有 `<action>` 和 `<category>` 中的内容同时能够匹配上 Intent 中指定的 action 和 category 时，这个活动才能响应该 Intent**。

如下，在**AndroidManifest.xml**文件中配置SecondActivity的信息如下

```
<activity android:name=".SecondActivity">
    <intent-filter>
        <action android:name="com.example.activitytest.ACTION_START" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="com.example.activitytest.MY_CATEGORY" />
    </intent-filter>
</activity>
<activity
```

在FirstActivity中使用隐式Intent，完成点击按钮跳转到第二个界面

```
button1.setOnClickListener(new View.OnClickListener() {
    @Override
    public void onClick(View view) {
        Intent intent = new Intent("com.example.activitytest.ACTION_START");
        intent.addCategory("com.example.activitytest.MY_CATEGORY");
        startActivity(intent);
    }
});
```





用隐式 Intent，我们**不仅可以启动自己程序内的活动，还可以启动其他程序的活动（如调用浏览器）**，这使得 Android 多个应用程序之间的功能共享成为了可能

```
button1.setOnClickListener(new View.OnClickListener() {
    @Override
    public void onClick(View view) {
        Intent intent = new Intent(Intent.ACTION_VIEW);
        intent.setData(Uri.parse("http://www.baidu.com"));	// 将Uri对象传递进去，可以使用浏览器打开
        startActivity(intent);
    }
});
```

<img src="http://cdn.ziyedy.top/Android%E5%BC%80%E5%8F%91%E4%BA%8C%EF%BC%9A%E6%B4%BB%E5%8A%A8%EF%BC%88Activity%EF%BC%89/%E6%89%93%E5%BC%80%E6%B5%8F%E8%A7%88%E5%99%A8.png" style="zoom:50%;" />



### Intent向下一个活动传递数据

Intent 中提供了一系列 **putExtra()** 方法的重载，可以把我们想要传递的数据暂存在 Intent 中，启动了另一个活动后，只需要把这些数据再从 Intent 中取出就可以了

putExtra() 方法接收两个参数，第一个参数是键，用于后面从 Intent 中取值，第二个参数才是真正要传递的数据。

#### 示例

在FirstActivity中相应位置填入以下代码

```
String data = "Hello, Second Activity";
Intent intent = new Intent(FirstActivity.this, SecondActivity.class);
intent.putExtra("extra_data", data);	// 将data传入，同时键值为extra_dat
startActivity(intent);
```

在SecondActivity中相应位置填入以下代码

```
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.second_layout);
    Intent intent = getIntent();
    String data = intent.getStringExtra("extra_data");	// 通过键值获取信息
    Toast.makeText(this, data, Toast.LENGTH_SHORT).show();	// 显示信息
}
```

<img src="http://cdn.ziyedy.top/Android%E5%BC%80%E5%8F%91%E4%BA%8C%EF%BC%9A%E6%B4%BB%E5%8A%A8%EF%BC%88Activity%EF%BC%89/%E4%BC%A0%E9%80%92%E6%B6%88%E6%81%AF%E7%BB%99%E4%B8%8B%E4%B8%80%E4%B8%AA%E5%BA%94%E7%94%A8.png" style="zoom: 67%;" />

### Intent返回数据给上一个活动

**1、 使用startActivityForResult() 方法启动活动，在活动销毁后即可将数据传递回来**

> startActivityForResult() 方法接收两个参数，第一个参数还是 Intent，第二个参数是**请求码（requestCode）**，用于在之后的回调中判断数据的来源。

**2、在第二个活动中使用setResult() 方法将带有数据的Intent传递回去**

> setResult() 方法接收两个参数，第一个参数用于向上一个活动返回处理结果，一般只使用 RESULT_OK 或 RESULT_CANCELED 这两个值，第二个参数则把带有数据的 Intent 传递回去

**3、使用startActivityForResult() 方法启动活动在活动销毁后会回调onActivityResult() 方法**，因此在第一个活动中重写该方法。

>  onActivityResult() 方法带有三个参数，第一个参数 requestCode ，即我们在启动活动时传入的请求码。第二个参数 resultCode ，即我们在返回数据时传入的处理结果。第三个参数 data，即携带着返回数据的 Intent

#### 示例

在FirstActivity中相应位置填入以下代码

```
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.first_layout);
    Button button1 = (Button) findViewById(R.id.button_1);
    button1.setOnClickListener(new View.OnClickListener() {
        @Override
        public void onClick(View view) {
            Intent intent = new Intent(FirstActivity.this, SecondActivity.class);
            startActivityForResult(intent, 1);	// 请求码为1
        }
    });
}

@Override
protected void onActivityResult(int requestCode, int resultCode, @Nullable Intent data) {
    super.onActivityResult(requestCode, resultCode, data);
    if (requestCode == 1) {
        if (resultCode == RESULT_OK) {
            assert data != null;
            String returnData = data.getStringExtra("data_return");
            Toast.makeText(FirstActivity.this, returnData, Toast.LENGTH_SHORT).show();
        }
    }
}
```

在SecondActivity中相应位置填入以下代码

```
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.second_layout);
    Button button2 = (Button) findViewById(R.id.button_2);
    button2.setOnClickListener(new View.OnClickListener() {
        @Override
        public void onClick(View view) {
            Intent intent = new Intent();
            intent.putExtra("data_return", "Return message from SecondActivity");
            setResult(RESULT_OK, intent);
            finish();
        }
    });
}
```

<img src="http://cdn.ziyedy.top/Android%E5%BC%80%E5%8F%91%E4%BA%8C%EF%BC%9A%E6%B4%BB%E5%8A%A8%EF%BC%88Activity%EF%BC%89/%E4%BB%8E%E4%B8%8B%E4%B8%80%E4%B8%AA%E5%BA%94%E7%94%A8%E8%BF%94%E5%9B%9E%E6%B6%88%E6%81%AF.png" style="zoom:67%;" />



## 活动的生命周期

### 活动状态

每个活动在其生命周期最多可能有4种状态

#### 运行状态

一个活动位于返回栈的**栈顶**时，这时活动就处于运行状态

#### 暂停状态

当一个活动**不再处于栈顶位置，但仍然可见**时，这时活动就进入了暂停状态。

#### 停止状态

当一个活动不再处于栈顶位置，并且完全不可见的时候，就进入了停止状态。（当其他地方需要内存时，处于停止状态的活动有可能会被系统回收）

#### 销毁状态

当一个活动**从返回栈中移除后**就变成了销毁状态。系统会最倾向于回收处于这种状态的活动，从而保证手机的内存充足。

### 活动的生存期

Activity 类中定义了 7 个回调方法，覆盖了活动生命周期的每一个环节

#### onCreate()

> 在活动第一次被创建的时候调用
>
> 在这个方法中完成活动的初始化操作，比如说加载布局、绑定事件等

#### onStart()

> 活动由不可见变为可见的时候调用

#### onResume()

> 活动准备好和用户进行交互的时候调用。此时的活动一定位于返回栈的栈顶，并且处于运行状态

#### onPause()

> 这个方法在系统准备去启动或者恢复另一个活动的时候调用

#### onStop()

> 这个方法在活动完全不可见的时候调用

#### onDestroy()

> 这个方法在活动被销毁之前调用，之后活动的状态将变为销毁状态。

#### onRestart()

> 这个方法在活动由停止状态变为运行状态之前调用，也就是活动被重新启动了。





### 参考

第一行代码——Android（郭霖）