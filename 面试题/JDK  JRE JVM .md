# JDK  JRE JVM 

## 1.名词解释

**JRE** ：英文名称（Java Runtime Environment），我们叫它：Java 运行时环境。它主要包含两个部分，jvm 的标准实现和 Java 的一些基本类库。它相对于 jvm 来说，多出来的是一部分的 Java 类库。

**JDK** ：英文名称（Java Development Kit），Java 开发工具包。jdk 是整个 Java 开发的核心，它集成了 jre 和一些好用的小工具。例如：javac.exe，java.exe，jar.exe 等。

**JVM**：英文名称（Java Virtual Machine ),Java 虚拟机，它的作用就是能将.class文件转成计算机能识别的机器码，以供计算机执行。JVM是JRE的一部分，它是一个虚拟出来的计算机，是通过在实际的计算机上仿真模拟各种计算机功能来实现的。JVM有自己完善的硬件架构，如处理器、堆栈、寄存器等，还具有相应的指令系统。

面向对象区别：jdk面向程序开发者，jre面向程序使用者。

基本组成区别：

1. jre和和jdk都是有jvm的，所以两者都可以运行.class文件；但是jre没有javac包，即没有将.java文件编译成.class文件的编译功能；所以无法直接运行.java文件。
2. jdk有jdb（java debugger），所以jdk可以调试，进行开发，而jre不行。

显然，这三者的关系是：包含关系。JDK 包含了 JRE包含JVM。



> **JVM不能单独搞定class的执行，解释class的时候JVM需要调用解释所需要的类库lib。在JDK下面的的jre目录里面有两个文件夹bin和lib,在这里可以认为bin里的就是jvm，lib中则是jvm工作所需要的类库，而jvm和 lib和起来就称为jre。JVM+Lib=JRE。总体来说就是，我们利用JDK（调用JAVA API）开发了属于我们自己的JAVA程序后，通过JDK中的编译程序（javac）将我们的文本java文件编译成JAVA字节码，在JRE上运行这些JAVA字节码，JVM解析这些字节码，映射到CPU指令集或OS的系统调用。**

## 2.服务器上是否只安装 JRE 就可以了？

**服务器上只安装 JRE 的前提**：

- 发布到服务器上时所有文件都是编译好的文件，包括 JSP 文件
- 后期不在服务器上直接修改（因为导致修改后的文件未重新编译）

如果部署的项目都是编译后重新部署，不在服务器上直接修改的话是可以只安装 JRE 的。

**在服务器上安装 JDK 的好处：**

- 可以编译 java 文件，方便后期维护
- 保证 JSP 文件修改后稳定运行

综合考虑，为避免以后这样那样的麻烦事发生，服务器上还是安装 JDK 吧！毕竟项目后期维护才是主要的事情。

## 3.为什么Java语言可以一次编译 到处运行?

JVM是Java实现跨平台最核心的部分，所有的Java程序会首先被编译为.class的类文件，JVM的主要工作是解释自己的指令集（即字节码）并映射到本地的CPU的指令集或OS的系统调用。Java面对不同操作系统使用不同的虚拟机，依次实现了跨平台。JVM对上层的Java源文件是不关心的，它关心的只是由源文件生成的类文件。

## 4.独立安装的jre和jdk自带的jre区别

- 相同点：这两个JRE都可以作为开发Java程序的运行环境。
- 不同点：JDK自带的开发工具只能使用JDK自己目录下的JRE，不能使用JDK外面的。

## 5.jdk目录

1. bin文件夹：提供JDK工具程序，包括javac、java、javadoc、appletviewer等可执行程序。
2. jre文件夹：存放Jaca运行环境文件。 
3. lib文件夹：存放Java的类库文件，即工具程序使用的Java类库。JDK中的工具程序大多也是由Java编写而成
4. include文件夹：存放用于本地方法的文件。 