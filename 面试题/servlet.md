# 1、Servlet和JSP的区别？

JSP是Servlet技术的扩展，本质上就是Servlet的简易方式。JSP编译后是“类servlet”。

Servlet和JSP最主要的不同点在于，Servlet的应用逻辑是在Java文件中，并且完全从表示层中的HTML里分离开来。而JSP的情况是Java和HTML可以组合成一个扩展名为.jsp的文件。

JSP侧重于视图，Servlet主要用于控制逻辑。在struts框架中，JSP位于MVC设计模式的视图层，而Servlet位于控制层。

# 2、转发和重定向的区别？

### 请求转发：

客户首先发送一个请求到服务器端，服务器端发现匹配的servlet，并指定它去执行，当这个servlet执行完之后，它要调用getRequestDispacther()方法，把请求转发给指定的student_list.jsp,整个流程都是在服务器端完成的，而且是在同一个请求里面完成的，因此servlet和jsp共享的是同一个request，在servlet里面放的所有东西，在student_list中都能取出来，因此，student_list能把结果getAttribute()出来，getAttribute()出来后执行完把结果返回给客户端。整个过程是一个请求，一个响应。

### 重定向：

客户发送一个请求到服务器，服务器匹配servlet，servlet处理完之后调用了sendRedirect()方法，立即向客户端返回这个响应，响应行告诉客户端你必须要再发送一个请求，去访问student_list.jsp，紧接着客户端收到这个请求后，立刻发出一个新的请求，去请求student_list.jsp,这里两个请求互不干扰，相互独立，在前面request里面setAttribute()的任何东西，在后面的request里面都获得不了。可见，在sendRedirect()里面是两个请求，两个响应。（服务器向浏览器发送一个302状态码以及一个location消息头，浏览器收到请求后会向再次根据重定向地址发出请求）

请求转发：request.getRequestDispatcher("/test.jsp").forword(request,response);

重定向：response.sendRedirect("/test.jsp");

| 区别             | **转发forward()**  | **重定向sendRedirect()** |
| ---------------- | ------------------ | ------------------------ |
| **根目录**       | 包含项目访问地址   | 没有项目访问地址         |
| **地址栏**       | 不会发生变化       | 会发生变化               |
| **哪里跳转**     | 服务器端进行的跳转 | 浏览器端进行的跳转       |
| **请求域中数据** | 不会丢失           | 会丢失                   |
| **请求次数**     | 一次               | 两次                     |



## Servlet生命周期可分为5个步骤

1. **加载Servlet**。当Tomcat第一次访问Servlet的时候，**Tomcat会负责创建Servlet的实例**
2. **初始化**。当Servlet被实例化后，Tomcat会**调用init()方法初始化这个对象**
3. **处理服务**。当浏览器**访问Servlet**的时候，Servlet **会调用service()方法处理请求**
4. **销毁**。当Tomcat关闭时或者检测到Servlet要从Tomcat删除的时候会自动调用destroy()方法，**让该实例释放掉所占的资源**。一个Servlet如果长时间不被使用的话，也会被Tomcat自动销毁
5. **卸载**。当Servlet调用完destroy()方法后，等待垃圾回收。如果**有需要再次使用这个Servlet，会重新调用init()方法进行初始化操作**。

- 简单总结：**只要访问Servlet，service()就会被调用。init()只有第一次访问Servlet的时候才会被调用。destroy()只有在Tomcat关闭的时候才会被调用。**



## get方式和post方式有何区别

> get方式和post方式有何区别

数据携带上:

- GET方式：在URL地址后附带的参数是有限制的，其数据容量通常不能超过1K。
- POST方式：可以在请求的实体内容中向服务器发送数据，传送的数据量无限制。

请求参数的位置上:

- GET方式：请求参数放在URL地址后面，以?的方式来进行拼接
- POST方式:请求参数放在HTTP请求包中

用途上:

- GET方式一般用来获取数据

- POST方式一般用来提交数据

- - 原因:

  - - 首先是因为GET方式携带的数据量比较小，无法带过去很大的数量
    - POST方式提交的参数后台更加容易解析(使用POST方式提交的中文数据，后台也更加容易解决)
    - GET方式比POST方式要快