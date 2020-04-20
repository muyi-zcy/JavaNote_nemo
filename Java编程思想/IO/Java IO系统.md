# Java 的 I/O系统

Java 的 I/O 操作类在包 java.io 下，大概可以分成四组，分别是：

1. 基于字节操作的 I/O 接口：InputStream 和 OutputStream
2. 基于字符操作的 I/O 接口：Writer 和 Reader
3. 基于磁盘操作的 I/O 接口：File
4. 基于网络操作的 I/O 接口：Socket

## 流

#### 按流向分类：

输入流: 程序可以从中读取数据的流。

输出流: 程序能向其中写入数据的流。

#### 按数据传输单位分类：

字节流：以字节（8位二进制）为单位进行处理。主要用于读写诸如图像或声音的二进制数据。

字符流：以字符（16位二进制）为单位进行处理。

都是通过字节流的方式实现的。字符流是对字节流进行了封装，方便操作。在最底层，所有的输入输出都是字节形式的。

#### 按功能分类：

节点流：从特定的地方读写的流类，如磁盘或者一块内存区域。

过滤流：使用节点流作为输入或输出。过滤流是使用一个已经存在的输入流或者输出流连接创建的。



![img](https://pomole.oss-cn-beijing.aliyuncs.com/nemo_pic/20200223034656.png)

### 字节输入流

1. InputStream 是以字节为单位的输入流的超类。InputStream提供了read()接口从输入流中读取字节数据。
2. ByteArrayInputStream 是字节数组输入流。它包含一个内部缓冲区，该缓冲区包含从流中读取的字节；通俗点说，它的内部缓冲区就是一个字节数组，而ByteArrayInputStream本质就是通过字节数组来实现的。 
3. PipedInputStream 是管道输入流，它和PipedOutputStream一起使用，能实现多线程间的管道通信。
4. FilterInputStream 是过滤输入流。它是DataInputStream和BufferedInputStream的超类。
5. DataInputStream 是数据输入流。它是用来装饰其它输入流，它“允许应用程序以与机器无关方式从底层输入流中读取基本 Java 数据类型”。 
6. BufferedInputStream 是缓冲输入流。它的作用是为另一个输入流添加缓冲功能。 
7.  File 是“文件”和“目录路径名”的抽象表示形式。关于File，注意两点： 
   1.  File不仅仅只是表示文件，它也可以表示目录！ 
   2.  File虽然在io保重定义，但是它的超类是Object，而不是InputStream。
8. FileDescriptor 是“文件描述符”。它可以被用来表示开放文件、开放套接字等。
9.  FileInputStream 是文件输入流。它通常用于对文件进行读取操作。
10. ObjectInputStream 是对象输入流。它和ObjectOutputStream一起，用来提供对“基本数据或对象”的持久存储。

### 字节输出流

1. OutputStream 是以字节为单位的输出流的超类。OutputStream提供了write()接口从输出流中读取字节数据。
2. ByteArrayOutputStream 是字节数组输出流。写入ByteArrayOutputStream的数据被写入一个 byte 数组。缓冲区会随着数据的不断写入而自动增长。可使用 toByteArray() 和 toString() 获取数据。 
3. PipedOutputStream 是管道输出流，它和PipedInputStream一起使用，能实现多线程间的管道通信。
4. FilterOutputStream 是过滤输出流。它是DataOutputStream，BufferedOutputStream和PrintStream的超类。 
5. DataOutputStream 是数据输出流。它是用来装饰其它输出流，它“允许应用程序以与机器无关方式向底层写入基本 Java 数据类型”。
6. BufferedOutputStream 是缓冲输出流。它的作用是为另一个输出流添加缓冲功能。
7. PrintStream 是打印输出流。它是用来装饰其它输出流，能为其他输出流添加了功能，使它们能够方便地打印各种数据值表示形式。
8. FileOutputStream 是文件输出流。它通常用于向文件进行写入操作。
9. ObjectOutputStream 是对象输出流。它和ObjectInputStream一起，用来提供对“基本数据或对象”的持久存储。

### 字符输入流

1. Reader 是以字符为单位的输入流的超类。它提供了read()接口来取字符数据。
2. CharArrayReader 是字符数组输入流。它用于读取字符数组，它继承于Reader。操作的数据是以字符为单位！ 
3. PipedReader 是字符类型的管道输入流。它和PipedWriter一起是可以通过管道进行线程间的通讯。在使用管道通信时，必须将PipedWriter和PipedReader配套使用。 
4.  FilterReader 是字符类型的过滤输入流。
5. BufferedReader 是字符缓冲输入流。它的作用是为另一个输入流添加缓冲功能。
6. InputStreamReader 是字节转字符的输入流。它是字节流通向字符流的桥梁：它使用指定的 charset 读取字节并将其解码为字符。
7. FileReader 是字符类型的文件输入流。它通常用于对文件进行读取操作。

### 字符输出流

1. Writer 是以字符为单位的输出流的超类。它提供了write()接口往其中写入数据。
2. CharArrayWriter 是字符数组输出流。它用于读取字符数组，它继承于Writer。操作的数据是以字符为单位
3. PipedWriter 是字符类型的管道输出流。它和PipedReader一起是可以通过管道进行线程间的通讯。在使用管道通信时，必须将PipedWriter和PipedWriter配套使用
4. FilterWriter 是字符类型的过滤输出流。
5. BufferedWriter 是字符缓冲输出流。它的作用是为另一个输出流添加缓冲功能。
6. OutputStreamWriter 是字节转字符的输出流。它是字节流通向字符流的桥梁：它使用指定的 charset 将字节转换为字符并写入
7. FileWriter 是字符类型的文件输出流。它通常用于对文件进行读取操作。
8. PrintWriter 是字符类型的打印输出流。它是用来装饰其它输出流，能为其他输出流添加了功能，使它们能够方便地打印各种数据值表示形式。

### 字节转换为字符流

![img](https://pomole.oss-cn-beijing.aliyuncs.com/nemo_pic/20200223050014.png)

1. FileReader继承于InputStreamReader，而InputStreamReader依赖于InputStream。具体表现在InputStreamReader的构造函数是以InputStream为参数。我们传入InputStream，在InputStreamReader内部通过转码，将字节转换成字符
2. FileWriter继承于OutputStreamWriter，而OutputStreamWriter依赖于OutputStream。具体表现在OutputStreamWriter的构造函数是以OutputStream为参数。我们传入OutputStream，在OutputStreamWriter内部通过转码，将字节转换成字符。

