---
title: Java07：IO流读写
date: 2020-07-28 08:21:32
tags:
	- Java
categories:
	- Java
	- JavaSE
fileName: java-input-output-stream
---

# IO流概述

> Java程序中输入/输出操作以“流”（Stream方式进行），Java为获取不同种类的数据提供了各种各样的Stream类，基本位于`java.io`包中



Java 的 I/O 大概可以分成以下几类：

- 磁盘操作：File
- 字节操作：InputStream 和 OutputStream
- 字符操作：Reader 和 Writer
- 对象操作：Serializable
- 网络操作：Socket
- 新的输入/输出：NIO



## IO流分类

### 按流的方向分

* 输入流：数据源流向程序（以`InputStream`与`Reader`结尾）
* 输出流：程序流向目的目录（以`OutPutStream`、`Writer`结尾）

### 按处理的数据单元分

* 字节流：以字节为单位获取数据，命名上以Stream结尾的流一般是字节流，顶级类InputStream、OutputStream。可以读取包括图像音频等一切数据
* 字符流：以字符为单位获取数据，命名上以Reader/Writer结尾的流一般是字符流，顶级类Reader、Writer。

### 按处理对象不同分

* 节点流：可以直接从数据源或目的地读写数据，如FileInputStream、FileReader等。
* 处理流（包装流）：不直接连接到数据源或目的地，是**”处理流的流”**。通过对其他流的处理提高程序的性能，如BufferedInputStream、BufferedReader等。

## IO流体系结构

### 字节流

InputStream和OutputStream是Java语言中最基本的两个字节输入输出类。其他所有字节输入输出流类都继承自这两个基类。

l 这两个类都是抽象类，不能创建它们的实例，只能使用它们的子类.

l FilterInputStream和FilterOutputStream是所有包装流的父类

### 字符流

Reader和Writer

l 这两个类都是抽象类，不能创建它们的实例，只能使用它们的子类.



# 文件的常规读写

## File类的使用

> File类用来代表文件与路径及其相关处理，类似于Python中的`os.path`等相关功能

File类能够实现获取文件或者文件夹的属性，以及实现对文件、文件夹的创建和删除

常见方法如下（都是路径处理的常用单词），File对象可以作为创建流对象的输入

```java
File file = new File("D:\PackageInfo.txt");	// 输入绝对路径或相对路径
file.getName();			// 文件名
file.getAbsolutePath();	// 绝对路径
file.length();			// 长度（字节）
file.exists();			// 是否存在
file.canRead();			// 是否可写
file.canWrite();		// 是否可读
file.canExecute();		// 是否可执行
file.isFile();			// 是否是文件
file.isDirectory();		// 是否是目录

file.getParentFile();	// 获取上级文件夹
file.mkdirs();			// 在该路径下创建文件夹
```

## 字节流常规读写

### 常规使用

1、创建流

2、使用流（通过循环，可以使用字符数组存储也可以使用一些包装流）

3、关闭流

```java
public class TestFileStream {
    public static void main(String[] args) throws IOException {
        // 创建流
        File file1 = new File("e:/readme.txt");
        File file2 = new File("e:/readme2.txt");
        InputStream fis = new FileInputStream(file1);
        OutputStream fos = new FileOutputStream(file2);
        
        // 使用流
        int n;
        n = fis.read();
        while(n!=-1){	// n==1表示读到了文件的末尾
            fos.write(n);	// 写入
            n = fis.read();	// 读出
        }
        
        // 关闭流
        fis.close();
        fos.close();
    }
}
```

### 进行异常处理及使用字节数组

使用字节数组可以减少读写硬盘的次数，从而提高效率

而异常处理可以使用`try(resource)`简化代码

```java
public class TestFileStream {
    public static void main(String[] args) {
        try(InputStream  fis = new FileInputStream("e:/readme.txt");
            OutputStream fos = new FileOutputStream("e:/readme2.txt")){

            // 使用一个字节数组作为中转站
            byte [] buf = new byte[1024];
            int len = fis.read(buf);
            while(len!=-1){
                fos.write(buf,0,len);	// 防止字节数组没满造成的多写入问题
                len = fis.read(buf);
            }
        }catch (FileNotFoundException e){
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

### 使用缓冲字节流

缓冲字节流`BufferedInputStream`与`BufferedOutputStream`可以创建缓冲区（默认缓冲区大小为8192）从而大大提高读写的效率。

缓冲字节流的常规使用如下：

```java
public class Test {
    public static void main(String[] args) throws IOException {
      InputStream fis = new FileInputStream(new File("e:/JDK_API.CHM"));
      OutputStream fos = new FileOutputStream(new File("e:/JDK_API2.CHM"));
      BufferedInputStream bis = new BufferedInputStream(fis); 
      BufferedOutputStream bos = new BufferedOutputStream(fos);       

        int n;
        n = bis.read();
        while(n != -1){
            bos.write(n);
            n = bis.read();
        }
        bis.close();
        bos.close();
    }
}
```

缓冲字节流也可以传入所需构建缓冲区的大小：

```java
// 创建size大小的buffer
BufferedOutputStream(OutputStream out, int size)
```

同时需要注意缓冲流的**刷新机制**（刷新即将缓冲区内的数据写入程序）

默认当缓冲区满了就会自动刷新，关闭流的时候也会进行刷新，也可以使用`flush()`函数手动刷新



## 字符流常规读写

### 常规使用

与使用字节流读写步骤基本一致，只是是用来读取字符流

```java
public class TestFileReaderWriter {
    public static void main(String[] args) {
        try( Reader fr = new FileReader(new File("e:/readme.txt"));
             Writer fw = new FileWriter("e:/readm22.txt");){
            char [] cbuf = new char[1024];
            int len = fr.read(cbuf);
            while(len!= -1){
                fw.write(cbuf,0,len);
                len = fr.read(cbuf);
            }
        }catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

### 使用缓冲字符流

与缓冲字节流使用类似，但可以使用`readLine()`等方法简化编程。

```java
public class Test {
    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new FileReader(new File("e:/sqlnet.log")));
        BufferedWriter bw = new BufferedWriter(new FileWriter("e:/sqlnet2.log"));        

        String str =  br.readLine();	// 读一行数据
        while(str != null ){
            bw.write(str);
            bw.newLine();	// 写入换行符
            str = br.readLine();
        }
        br.close();
        bw.close();
    }
}
```



# 数据流与对象流

## 数据流

数据流`DataInputStream`与`DataOutputStream`

可以使用不同类型的函数实现不同类型的读写操作

```java
public class TestDataStream {
	// 写操作函数 
    public static void write() throws Exception{
        //创建输出流
    	OutputStream fos = new FileOutputStream("e:/readme.txt");
        BufferedOutputStream bos = new BufferedOutputStream(fos);
        DataOutputStream dos = new DataOutputStream(bos);        
        //使用输出流
        dos.writeInt(123);
        dos.writeDouble(3.14);
        dos.writeChar('A');
        dos.writeBoolean(true);
        dos.writeUTF("bjsxt");        
        //关闭输出流
        dos.close();
    }    
    
    // 读操作函数
    public static void read() throws Exception{
        //创建输入流
        DataInputStream dis = new DataInputStream(new BufferedInputStream(new FileInputStream(new File("e:/readme.txt"))));        
        //使用输入流
        int a = dis.readInt();
        double b = dis.readDouble();
        char c = dis.readChar();
        boolean d = dis.readBoolean();
        System.out.println(dis.readUTF());
        //关闭输入流
        dis.close();
    }
}
```



## 对象流

对象流 `ObjectInputStream` 和 `ObjectOutputStream` ，可以将Java类与对象写出到文件，实现对象的持久存储

```java
public class TestObjectStream {
    public static void write() throws Exception{
        //创建输出流
        OutputStream fos = new FileOutputStream("e:/readme.txt");
        BufferedOutputStream bos = new BufferedOutputStream(fos);
        ObjectOutputStream oos = new ObjectOutputStream(bos);        
        //使用输出流
        oos.writeInt(123);
        oos.writeDouble(3.14);
        oos.writeChar('A');
        oos.writeBoolean(true);
        oos.writeUTF("bjsxt");
        oos.writeObject(new Date());
        oos.writeObject(new Student(“ziye”));    // 写入自己创建的对象    
        //关闭输出流
        oos.close();
    }   
    public static void read() throws Exception{
        //创建输入流
        ObjectInputStream ois = new ObjectInputStream(new BufferedInputStream(new FileInputStream(new File("e:/readme2.txt"))));        
        //使用输入流
        System.out.println(ois.readInt());
        double d = ois.readDouble();
        System.out.println(d);
        System.out.println(ois.readChar());
        System.out.println(ois.readBoolean());
        System.out.println(ois.readUTF());
        Object date = (Date)ois.readObject();
        System.out.println(date);
        System.out.println(ois.readObject());    // 读出自己创建的对象    
        //关闭输入流
        ois.close();
    }
}
```





## 序列化与反序列化

存储与传输时，需要将Java程序中的类对象等信息序列化为**二进制字节流**进行传输，该字节序列包含对象的数据、对象的类型和对象中存储的属性等信息。字节序列写出到文件后，相当于文件中**持久保存**了一个对象的信息。

* 序列化：`Serialization` 将对象的状态信息转换为可以存储或传输的形式的过程
* 反序列化：从文件中读取出来，重构对象，对其进行反序列化

### 序列化实现条件

1、该类必须实现java.io.Serializable 接口，Serializable是一个标记接口，不实现此接口的类将不会使任何状态序列化或者反序列化，会抛出NotSerializableException异常

2、该类的所有属性必须是可序列化的。如果有一个属性不需要可序列化的，则该属性必须注明是瞬态的，使用transient关键字修饰

https://blog.csdn.net/Luojun13Class/article/details/88529622



### 输入字节流：InputStream

> `InputStream`是一个抽象类，是所有输入流的超类，最重要的方法为`int read()`方法，返回字节的int值，返回`-1`表示无法继续读取。
>
> 常用的子类有`FileInputStream`，从文件流中读取数据。





### 参考链接

https://zhuanlan.zhihu.com/p/25418336

https://www.liaoxuefeng.com/

