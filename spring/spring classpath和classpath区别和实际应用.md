classpath:和classpath*:的含义
classpath: ：表示从类路径中加载资源，classpath:和classpath:/是等价的，都是相对于类的根路径。资源文件库标准的在文件系统中，也可以在JAR或ZIP的类包中。
classpath*:：假设多个JAR包或文件系统类路径都有一个相同的配置文件，classpath:只会在第一个加载的类路径下查找，而classpath*:会扫描所有这些JAR包及类路径下出现的同名文件。
