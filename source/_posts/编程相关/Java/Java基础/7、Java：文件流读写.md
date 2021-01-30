---
title: Java中的输入输出流
date: 2020-07-28 08:21:32
tags:
	- Java
categories:
	- 编程相关
	- Java
	- Java基础
fileName: java-input-output
---

## 概况

Java 的 I/O 大概可以分成以下几类：

- 磁盘操作：File
- 字节操作：InputStream 和 OutputStream
- 字符操作：Reader 和 Writer
- 对象操作：Serializable
- 网络操作：Socket
- 新的输入/输出：NIO

### 字节操作



从文件系统中的某个文件中获得输入字节

用于读取如图像数据之类的原始字节流



| 方法名                    | 描述                                                   |
| ------------------------- | ------------------------------------------------------ |
| public int read()         | 从输入流中读取一个数据字节                             |
| public int read(byte[] b) | 从输入流中将最多b.length个字节的数据据读入一个byte数组 |
| public void close()       | 关闭此文件输入流并释放与此流有关的所有系统资源         |

n表示长度



### 输入字节流：InputStream

> `InputStream`是一个抽象类，是所有输入流的超类，最重要的方法为`int read()`方法，返回字节的int值，返回`-1`表示无法继续读取。
>
> 常用的子类有`FileInputStream`，从文件流中读取数据。

#### 示例

```
public static void main(String[] args) throws IOException {
    InputStream input = new FileInputStream("aa.txt");
    while (true) {
        int n = input.read();
        if(n == -1)
            break;
        System.out.println(n);
    }
    input.close();	// 最后需要关闭
}
```

#### 常用写法

> 使用`try(resource)`语法，让编译器自动关闭资源
>
> 编译器只看`try(resource = ...)`中的对象是否实现了`java.lang.AutoCloseable`接口，如果实现了，就自动加上`finally`语句并调用`close()`方法。

```
try (InputStream input = new FileInputStream("src/readme.txt")) {
    int n;
    while ((n = input.read()) != -1) {
        System.out.println(n);
    }
}
```





### 输出字节流：OutputStream





### 缓冲输入流：BufferedInputStream





### 缓冲输出流：BufferedOutputStream

缓冲区满了自动写入，不满不写入，则需要调用`flush()`方法

```
BufferedOutputStream(OutputStream out)

// 创建size大小的buffer
BufferedOutputStream(OutputStream out, int size)
```



|              |            |
| ------------ | ---------- |
| void flush() | 清空缓冲区 |
|              |            |
|              |            |



### 字符输入流：Reader

字节字符转换流

InputStreamReader

```
InputStreamReader(InputSteam in)
```











### 参考链接

https://zhuanlan.zhihu.com/p/25418336

https://www.liaoxuefeng.com/

