---
title: Android开发三：布局（layout）
date: 2020-08-30 10:06:09
tags:
	- 移动端
categories:
	- Android
fileName: Android3-layout
---

## 控件与布局的继承



## Android常用控件

### TextView

> 在界面上显示一段文本信息

#### 常用属性

 ```
android:text="TextView"		显示的文本
android:gravity="center"	对齐方式
android:textSize="30sp"		文字大小
android:textColor="#03A9F4"	文字颜色
 ```



### Button

> 配置一个按钮，默认全为大写

#### 常用属性

常用属性与TextView类似

```
android:textAllCaps="false"		将默认文字全部大写关掉
```

#### 绑定逻辑代码（注册监听器）

**使用匿名类的写法，不需要对view进行判断**

```
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);
    Button button = (Button) findViewById(R.id.button);
    button.setOnClickListener(new View.OnClickListener() {
        @Override
        public void onClick(View view) {
            // 添加逻辑代码
        }
    });
}
```

**使用实现View.OnClickListener接口的方法**

```
public class MainActivity extends AppCompatActivity implements View.OnClickListener {

@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);
    Button button = (Button) findViewById(R.id.button);
    button.setOnClickListener(this);
}

@Override
public void onClick(View v) {
    switch (v.getId()) {	// 判断触发监听器的是哪个按钮
        case R.id.button:
            // 添加逻辑代码
            break;
        default:
            break;
    }
}
```



### EditText

> 允许用户在控件里输入和编辑内容，并可以在程序中对这些内容进行处理

#### 常用属性

```
android:hint="Type something here"	输入时的提示信息
android:maxLines="1"	指定最大行数（如密码输入框最大行数为1），大于最大行数向下滚动
```

#### 常用方法

```
getText()	// 获取输入的内容
```





### AlertDialog

> 在当前的界面弹出一个对话框，这个对话框是置顶于所有界面元素之上的，能够屏蔽掉其他控件的交互能力，因此 AlertDialog 一般都是用于提示一些非常重要的内容或者警告信息

#### 对话框使用流程

1. 通过` AlertDialog.Builder `创建一个 AlertDialog 的实例
2. 为这个对话框设置标题、内容（`setMessage`）、可否用 Back 键关闭对话框（`setCancelable`）等属性
3. 调用 setPositiveButton() 方法为对话框设置确定按钮的点击事件，调用 setNegativeButton() 方法设置取消按钮的点击事件
4. 调用 show()方法将对话框显示出来

#### 点击按钮将EditText在对话框中弹出

该示例需要使用到按钮点击事件，从EditText获取内容，及AlertDialog的相关操作。

```
public class MainActivity extends AppCompatActivity implements View.OnClickListener {

    private EditText editText;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        Button button = (Button) findViewById(R.id.button);
        editText = (EditText) findViewById(R.id.edit_text);
        button.setOnClickListener(this);
    }


    @Override
    public void onClick(View v) {
        switch (v.getId()) {
            case R.id.button:
                String inputText = editText.getText().toString();	// 获取输入内容
                AlertDialog.Builder dialog = new AlertDialog.Builder(MainActivity.this);
                dialog.setMessage(inputText);
                dialog.setCancelable(false);
                dialog.setPositiveButton("OK", new DialogInterface.OnClickListener() {
                    @Override
                    public void onClick(DialogInterface dialogInterface, int i) {

                    }
                });
                dialog.setNegativeButton("Cancel", new DialogInterface.OnClickListener() {
                    @Override
                    public void onClick(DialogInterface dialogInterface, int i) {

                    }
                });
                dialog.show();
                break;
            default:
                break;
        }
    }
}
```





### ImageView

> 在界面上展示图片的一个控件
>
> 图片通常放置在以 “drawable” 开头的目录下（drawable后会跟具体的分辨率）

#### 常用属性和方法

```
android:src="@drawable/img_1 "	指定显示图片的位置
```

在代码中可以使用`setImageResource(R.drawable.img_2)`来指定图片的位置



### ProgressBar

> 在界面上显示一个进度条，表示我们的程序正在加载一些数据

#### 常用属性

```
style="?android:attr/progressBarStyleHorizontal"	使用style属性改变进度条的样式（该行为指定为水平条状）
android:max="100"	max设置进度条最大值
```

#### 常用方法

```
getVisibility()
setVisibility(View.VISIBLE)	// 设置可见度
getProgress()	// 获取进度条进度
setProgress(progress)	// 更改进度条进度
```



### ProgressDialog

> 与`AlertDialog `类似，都可以在界面上弹出一个对话框，都能够屏蔽掉其他控件的交互能力
>
> 不同的是，ProgressDialog 会在对话框中显示一个进度条，一般用于表示当前操作比较耗时，让用户耐心地等待
>
> **用法和 AlertDialog 也相似**
>
> 可以使用dismiss() 方法来关闭对话框





## 布局

### 线性布局（LinearLayout）

```
<LinearLayout
    android:orientation="horizontal"    
    android:layout_width="match_parent"
    android:layout_height="match_parent">
        
	<!-- 添加布局内部的组件 -->
    
</LinearLayout>
```

#### 常用属性

```
android:orientation="horizontal"	指定是水平还是垂直（horizontal与vertical可选）
android:layout _gravity		指定布局内控件的对齐方式
android:layout _weight		使用比例的方式来指定控件的大小（即将所有控件按比例指定长宽大小）
```

#### layout_weight示例

```
<LinearLayout
    android:orientation="horizontal"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

<EditText
    android:id="@+id/edit_text"
    android:layout_width="0dp"
    android:layout_height="wrap_content"
    android:layout_weight="3"
    />

<Button
    android:id="@+id/button"
    android:layout_width="0dp"
    android:layout_height="wrap_content"
    android:text="send"
    android:layout_weight="1"
    />
    
</LinearLayout>
```





### 相对布局（RelativeLayout）

> 相对布局较为灵活，可以通过相对定位的方式让控件出现在布局的任何位置（通常相对于父布局）



### 帧布局（FrameLayout）

> 所有的控件都会默认摆放在布局的左上角，应用很少



### 百分比布局

> 百分比布局为 FrameLayout 和 RelativeLayout 进行了**功能扩展**，提供了 PercentFrameLayout 和 PercentRelativeLayout 这两个全新的布局





### 参考

第一行代码——Android（郭霖）