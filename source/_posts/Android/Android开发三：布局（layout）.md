---
title: Android开发三：布局（layout）
date: 2020-08-30 10:06:09
tags:
	- 移动端
categories:
	- Android
fileName: Android3-layout
---

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

![](http://cdn.ziyedy.top/Android%E5%BC%80%E5%8F%91%E4%B8%89%EF%BC%9A%E5%B8%83%E5%B1%80%EF%BC%88layout%EF%BC%89/TextView.png)

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

<img src="http://cdn.ziyedy.top/Android%E5%BC%80%E5%8F%91%E4%B8%89%EF%BC%9A%E5%B8%83%E5%B1%80%EF%BC%88layout%EF%BC%89/%E8%BE%93%E5%85%A5%E6%A1%86%E5%BC%B9%E5%87%BA%E6%95%88%E6%9E%9C.png" style="zoom:67%;" />



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
> 可以使用`dismiss()` 方法来关闭对话框





## 滚动控件（ListView与RecyclerView）

### ListView

> ListView 允许用户通过手指上下滑动的方式将屏幕外的数据滚动到屏幕内，同时屏幕上原有的数据则会滚动出屏幕（如聊天记录等等）

#### 基础用法

1. 编写相关的布局代码

```
<ListView
	android:id="@+id/list_view"
	android:layout_width="match_parent"
	android:layout_height="match_parent" />
```

2. 定义数据，并使用`ArrayAdapter`适配器将要适配的数据传入

> `ArrayAdapter`可以指定数据泛型。
>
> 参数分别为`(当前上下文, ListView 子项布局的 id, 要适配的数据)`

3. 调用 ListView 的` setAdapter() `方法，将构建好的适配器对象传递进去，建立ListView与数据的关联

```
private String[] data = { "Apple", "Banana", "Orange", "Watermelon",
            "Pear", "Grape", "Pineapple", "Strawberry", "Cherry", "Mango",
            "Apple", "Banana", "Orange", "Watermelon", "Pear", "Grape",
            "Pineapple", "Strawberry", "Cherry", "Mango" };

@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);
	// 使用适配器
    ArrayAdapter<String> adapter = new ArrayAdapter<>(MainActivity.this,
                android.R.layout.simple_list_item_1, data);
    ListView listView = (ListView) findViewById(R.id.list_view);
    listView.setAdapter(adapter);	// 传入适配器
}
```

**显示效果如下**

<img src="http://cdn.ziyedy.top/Android%E5%BC%80%E5%8F%91%E4%B8%89%EF%BC%9A%E5%B8%83%E5%B1%80%EF%BC%88layout%EF%BC%89/ListView%E7%AE%80%E5%8D%95%E4%BD%BF%E7%94%A8.png" style="zoom: 50%;" />



#### 自定义ListView界面

> 1、自定义 ListView 子项布局的样式（即写一个layout的xml）
>
> 2、写一个实例类，用于作为列表项的泛型
>
> 3、创建一个自定义的适配器，这个适配器继承自 ArrayAdapter。重写构造方法与getView()等方法
>
> 4、按ListView的基础用法在主活动中引入即可

#### ListView的点击事件

**标准写法如下：`setOnItemClickListener`**

```
listView.setOnItemClickListener(new AdapterView.OnItemClickListener() {
    @Override
    public void onItemClick(AdapterView<?> parent, View view, int position, long id) {
    	// position指列表中点击的索引
        // 添加逻辑代码
    }
});
```



### RecyclerView

> 加强版的ListView，更推荐使用
>
> ListView 的布局排列是由自身去管理的，而 RecyclerView 则将这个工作交给了`LayoutManager`等接口，因此在布局上非常方便

#### 标准使用

> 与ListView相比不同之处在于`setLayoutManager`设置了“滚动方式”

```
RecyclerView recyclerView = (RecyclerView) findViewById(R.id.recycler_view);

LinearLayoutManager layoutManager = new LinearLayoutManager(this);
layoutManager.setOrientation(LinearLayoutManager.HORIZONTAL);	// 设置滚动方式（此处设置为水平）
recyclerView.setLayoutManager(layoutManager);	// 设置LinearLayoutManager对象
FruitAdapter adapter = new FruitAdapter(fruitList);
recyclerView.setAdapter(adapter);
```

除此之外，还可以通过`GridLayoutManager`与`StaggeredGridLayoutManager`这两种方式实现网格布局和瀑布流布局





## 布局

### 常用布局属性

```
android:layout_width="match_parent"		表示width与父布局的width相同
android:layout_height="wrap_content"	表示height刚好能够容纳内容
```



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

![layout-weight：3比1的效果](http://cdn.ziyedy.top/Android%E5%BC%80%E5%8F%91%E4%B8%89%EF%BC%9A%E5%B8%83%E5%B1%80%EF%BC%88layout%EF%BC%89/layout-weight%E6%95%88%E6%9E%9C.png)



### 相对布局（RelativeLayout）

> 相对布局较为灵活，可以通过相对定位的方式让控件出现在布局的任何位置

#### 相对于父布局定位



<img src="http://cdn.ziyedy.top/Android%E5%BC%80%E5%8F%91%E4%B8%89%EF%BC%9A%E5%B8%83%E5%B1%80%EF%BC%88layout%EF%BC%89/%E7%9B%B8%E5%AF%B9%E7%88%B6%E5%B8%83%E5%B1%80.png" style="zoom: 50%;" />



#### 相对于控件定位



<img src="http://cdn.ziyedy.top/Android%E5%BC%80%E5%8F%91%E4%B8%89%EF%BC%9A%E5%B8%83%E5%B1%80%EF%BC%88layout%EF%BC%89/%E7%9B%B8%E5%AF%B9%E6%8E%A7%E4%BB%B6.png" style="zoom:50%;" />



### 帧布局（FrameLayout）

> 所有的控件都会默认摆放在布局的左上角，应用很少



### 百分比布局

> 百分比布局为 FrameLayout 和 RelativeLayout 进行了**功能扩展**，提供了 PercentFrameLayout 和 PercentRelativeLayout 这两个全新的布局



## 自定义控件

### 引入布局

> 引入布局即新建一个布局文件，并在“主布局文件中”进行引用，达到**重复应用某一布局的效果**

引用布局使用include语句即可，示例：`<include layout="@layout/title" />`。即引入了一个`title.xml`的布局文件

#### 示例

1. 新建`title.xml`文件，并在其中编写相应控件代码。
2. 在`activity_main.xml`中使用`include`语句将新建的布局添加进去（就像添加常规布局一样）



### 自定义控件

> 引入布局解决了重复编写布局代码的问题，但布局中的响应事件常常还需要编写
>
> 自定义控件即编写布局中的响应事件，避免重复编写相同代码

#### 示例

在之前自定义布局的基础上，新建一个Java类，继承自`LinearLayout`，代码如下（即实现了该布局中控件的相关功能）：

```
public class TitleLayout extends LinearLayout {
    public TitleLayout(Context context, AttributeSet attrs) {
        super(context, attrs);
        LayoutInflater.from(context).inflate(R.layout.title, this);	// 进行加载

		// 编写控件中的按钮代码
        Button titleBack = (Button) findViewById(R.id.title_back);
        Button titleEdit = (Button) findViewById(R.id.title_edit);
        titleBack.setOnClickListener(new OnClickListener() {
            @Override
            public void onClick(View view) {
                ((Activity) getContext()).finish();
            }
        });
        titleEdit.setOnClickListener(new OnClickListener() {
            @Override
            public void onClick(View view) {
                Toast.makeText(getContext(), "You clicked Edit button",
                        Toast.LENGTH_SHORT).show();
            }
        });
    }
}
```

<img src="http://cdn.ziyedy.top/Android%E5%BC%80%E5%8F%91%E4%B8%89%EF%BC%9A%E5%B8%83%E5%B1%80%EF%BC%88layout%EF%BC%89/%E8%87%AA%E5%AE%9A%E4%B9%89%E6%8E%A7%E4%BB%B6.png" style="zoom:67%;" />





### 参考

第一行代码——Android（郭霖）