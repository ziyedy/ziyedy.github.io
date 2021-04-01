---
title: Android开发四：碎片（Fragment）
date: 2020-09-01 14:48:59
tags:
	- Android
categories:
	- 移动端
	- Android
fileName: Android4-fragment
---

## 碎片的基本使用

> 碎片是一种可嵌入在活动当中的UI片段，能让程序更加合理充分的利用大屏幕空间（常应用在平板上）

### 基本使用

1. 新建一个碎片布局（xml文件）
2. 新建一个`LeftFragment`类，并让它继承自 `Fragment`，并重写`onCreateView()`方法（将刚刚新建的布局文件加载进来）

```
public class RightFragment extends Fragment {

    @Override
    public View onCreateView(LayoutInflater inflater, ViewGroup container,
                             Bundle savedInstanceState) {
        View view = inflater.inflate(R.layout.right_fragment, container, false);
        return view;
    }
}
```

3. 使用`<fragment>`标签在*主布局*中添加碎片，通过 `android:name`属性来显式指明要添加的碎片类名

```
<fragment
    android:id="@+id/right_fragment"
    android:name="com.example.fragmenttest.RightFragment"
    android:layout_width="0dp"
    android:layout_height="match_parent"
    android:layout_weight="1" />
```



### 动态添加碎片

1. 创建待添加的碎片实例
2. 获取 `FragmentManager` ，在活动中可以直接通过调用 `getSupportFragmentManager()` 方法得到
3. 通过调用`beginTransaction()`方法开启一个事务
4. 向容器内添加或替换碎片，一般使用`replace()`方法实现，需要传入*容器*的 id 和待添加的碎片实例
5. 调用`commit()`方法来提交事务

*实现动态添加碎片如下：*

```
private void replaceFragment(Fragment fragment) {
    FragmentManager fragmentManager = getSupportFragmentManager();
    FragmentTransaction transaction = fragmentManager.beginTransaction();
    transaction.replace(R.id.right_layout, fragment);
    // transaction.addToBackStack(null);	// 将该事务添加到返回栈
    transaction.commit();
}
```



### 碎片与活动之间的通信

#### 在碎片中获取活动

使用`getActivity()`方法来得到与当前碎片相关联的活动实例

```
MainActivity activity = (MainActivity) getActivity();
```

#### 在活动中获取碎片

```
RightFragment rightFragment = (RightFragment) getSupportFragmentManager().findFragmentById(R.id.right_fragment);
```



## 动态加载布局

> 使程序根据设备的分辨率或屏幕大小在运行时来决定加载哪个布局

### 限定符（Qualifiers）

可以简单理解为在资源文件夹名字之后添加的关键字

#### 常用限定符

| 屏幕特征 | 限定符 | 描述             |
| -------- | ------ | ---------------- |
| 大小     | small  | 提供给小屏幕设备 |
|          | large  | 提供给大屏幕设备 |
| 分辨率   | ldpi   | 120dpi以下的设备 |
|          | mdpi   | 120-160dpi的设备 |
|          | hdpi   | 160-240dpi的设别 |
| 方向     | land   | 横屏设备         |
|          | port   | 竖屏设备         |

#### 最小宽度限定符（Smallest-width Qualifier）

**以 dp 为单位**，如新建`layout-sw 600dp`文件夹，当程序运行在屏幕宽度大于 600dp 的设备上时，则会加载 `layout-sw 600dp/activity_main`布局

#### 示例

在 res 目录下新建`layout-large`文件夹，并新建布局`activity_main.xml`。之后在两个`activity_main.xml`中分别写入布局信息

* 当使用平板运行时，会使用`layout-large`中的布局信息
* 当使用手机运行时，会使用`layout`中的布局信息







