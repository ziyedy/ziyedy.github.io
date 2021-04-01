---
title: Android开发五：广播机制
date: 2020-09-02 10:50:44
tags:
	- Android
categories:
	- 移动端
	- Android
fileName: Android5-broadcast
---



## 接收系统广播

### 动态注册（app运行时能够进行接收）

1. 创建一个广播接收器

> 新建一个继承自 `BroadcastReceiver`的类，并重写父类的 `onReceive()` 方法

```
class NetworkChangeReceiver extends BroadcastReceiver {
    @Override
    public void onReceive(Context context, Intent intent) {
    	// 若检测到变化，则弹出相关信息
        Toast.makeText(context, "network changes", Toast.LENGTH_SHORT).show();
    }
}
```

2. 在`onCreate()`中创建一个`IntentFilter`实例，并使用`addAction()`添加系统发送的广播（`action1`）。之后创建一个步骤1类的实例。调用`registerReceiver()`方法将`NetworkChangeReceiver`的实例与`IntentFilter`的实例都传进去，即可**使得`NetworkChangeReceiver`接收到对应的`action1`**

```
private IntentFilter intentFilter;
private NetworkChangeReceive networkChangeReceive;

@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);
    intentFilter = new IntentFilter();
    intentFilter.addAction("android.net.conn.CONNECTIVITY_CHANGE");	// 添加需要监听的广播
    networkChangeReceive = new NetworkChangeReceive();
    registerReceiver(networkChangeReceive, intentFilter);	// 绑定
}
```

3. 动态注册的广播接收器一定都要取消注册才行，在`onDestroy()`方法中通过调用`unregisterReceiver()`方法来实现

```
@Override
protected void onDestroy() {
    super.onDestroy();
    unregisterReceiver(networkChangeReceive);
}
```

4. 在`AndroidManifest.xml`中声明权限

> 注：如果程序需要进行一些对用户来说比较敏感的操作，就必须在配置文件中声明权限才可以，否则程序将会直接崩溃

```
<uses-permission android:name="填入相关权限" />
```

### 静态注册（开机启动）

1. 右击 com.example.broadcasttest 包 →New→Other→ Broadcast Receiver，创建一个广播接收器
2. 重写`onReceive()`方法

3. 在`AndroidManifest.xml`文件中注册（AS通常情况会自动注册）

```
<!-- 声明权限 -->
<uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />

<receiver
    android:name=".BootCompleteReceiver"
    android:enabled="true"
    android:exported="true">
    <intent-filter>
    	<!-- 开机启动 -->
        <action android:name="android.intent.action.BOOT_COMPLETED" />
    </intent-filter>
</receiver>
```



## 自定义广播

### 发送标准广播

1. 定义接收器（与上一部分一样）

```
public class MyReceiver extends BroadcastReceiver {
    @Override
    public void onReceive(Context context, Intent intent) {
        Toast.makeText(context, "received in MyReceiver", Toast.LENGTH_SHORT).show();
    }
}
```

2. 在`AndroidManifest.xml`中对事件接收器进行注册（注册接收什么事件）

```
<receiver
    android:name=".MyReceiver"
    android:enabled="true"
    android:exported="true">
    <intent-filter>
    	<!-- 接收名为MY_BROADCAST的广播 -->
        <action android:name="com.example.broadcasttest.MY_BROADCAST" />
    </intent-filter>
</receiver>
```

3. 定义发送广播（名称与接收处一致，此处以点击事件作为例子）。首先构建一个`Intent`对象，并传入广播值，之后调用`sendBroadcast()`方法将广播发送出去（此时所有监听该广播的广播接收器就会收到消息）

```
@Override
public void onClick(View view) {
    Intent intent = new Intent("com.example.broadcasttest.MY_BROADCAST");
    sendBroadcast(intent);
}
```



### 发送有序广播

> 广播是一种可以跨进程的通信方式，即在我们应用程序内发出的广播，其他的应用程序应该也可以收到

发送有序广播只需要改变广播发送的代码为`sendOrderedBroadcast()`，传入Intent和与权限相关的字符串。

```
sendOrderedBroadcast(intent, null);
```

#### 设置广播接收器的先后顺序

如下，使用`android:priority`设置优先级为100

```
<intent-filter android:priority="100">
    <action android:name="com.example.broadcasttest.MY_BROADCAST" />
</intent-filter>
```

#### 中断广播

在`onReceive()`方法中调用了`abortBroadcast()`方法，就表示将这条广播截断，后面的广播接收器将无法再接收到这条广播。



## 本地广播

> **本地广播机制**：使用这个机制发出的广播只能够在应用程序的内部进行传递，并且广播接收器也只能接收来自本应用程序发出的广播

> **本地广播使用**：使用一个`LocalBroadcastManager`来对广播进行管理，并提供了发送广播和注册广播接收器的方法（*与动态注册广播流程基本一致*）

```
LocalBroadcastManager localBroadcastManager;
localBroadcastManager = LocalBroadcastManager.getInstance(this);	// 获取实例
localBroadcastManager.sendBroadcast(intent);	// 发送本地广播
localBroadcastManager.registerReceiver(localReceiver, intentFilter); 	// 注册本地广播监听器
```

















