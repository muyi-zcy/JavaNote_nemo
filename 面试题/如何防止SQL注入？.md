# 如何防止SQL注入？

## 1、什么是SQL注入？

SQL注入（SQLi）是一种注入攻击，，可以执行恶意SQL语句。它通过将任意SQL代码插入数据库查询，使攻击者能够完全控制Web应用程序后面的数据库服务器。攻击者可以使用SQL注入漏洞绕过应用程序安全措施；可以绕过网页或Web应用程序的身份验证和授权，并检索整个SQL数据库的内容；还可以使用SQL注入来添加，修改和删除数据库中的记录。

## 2、SQL注入类型

### 2.1、带内注入

这是典型的攻击，攻击者可以通过相同的通信通道发起攻击并获得结果。这是通过两种带内技术完成的：

●　基于错误的SQL注入：从显示的错误消息中获取有关数据库的信息

●　基于联合的SQL注入：依赖于攻击者能够将UNION ALL被盗信息的结果与合法结果连接起来。

这两种技术都依赖于攻击者修改应用程序发送的SQL，以及浏览器中显示的错误和返回的信息。如果应用程序开发人员或数据库开发人员无法正确地参数化他们在查询中使用的值，那么它会成功。两者都是试错法，可以检测到错误。

### 2.2、盲注入

也称为推理SQL注入，盲注入攻击不会直接从目标数据库中显示数据；相反，攻击者会仔细检查行为中的间接线索。HTTP响应中的详细信息，某些用户输入的空白网页以及数据库响应某些用户输入需要多长时间，这些都可以是线索，具体取决于攻击者的目标。他们还可以指向攻击者尝试的另一个SQLi攻击途径。

### 2.3、带外注入

这种攻击有点复杂，当攻击者无法在单个直接查询 - 响应攻击中实现其目标时，攻击者可能会使用此攻击。通常，攻击者会制作SQL语句，这些语句在呈现给数据库时会触发数据库系统创建与攻击者控制的外部服务器的连接。以这种方式，攻击者可以收集数据或可能控制数据库的行为。

二阶注入就是一种带外注入攻击。在这种情况下，攻击者将提供SQL注入，该注入将由数据库系统的单独行为存储和执行。当二级系统行为发生时（它可能类似于基于时间的作业或由其他典型管理员或用户使用数据库触发的某些事情）并且执行攻击者的SQL注入，那就是当“伸出”到系统时攻击者控制发生了。

## 3、解决办法

### 3.1、采用预编译语句集，它内置了处理SQL注入的能力，只要使用它的setString方法传值即可：

```java
String sql= "select * from users where username=? and password=?;
PreparedStatement preState = conn.prepareStatement(sql);
preState.setString(1, userName);
preState.setString(2, password);
ResultSet rs = preState.executeQuery();
```

### 3.2、采用正则表达式将包含有 单引号(')，分号(;) 和 注释符号(--)的语句给替换掉来防止SQL注入

```java
public static String TransactSQLInjection(String str){
     return str.replaceAll(".*([';]+|(--)+).*", " ");
}
	userName=TransactSQLInjection(userName);
    password=TransactSQLInjection(password);
    String sql="select * from users where username='"+userName+"' and password='"+password+"' "
	Statement sta = conn.createStatement();
	ResultSet rs = sta.executeQuery(sql);
```

### 3.3、字符串过滤

​    比较通用的一个方法：（||之间的参数可以根据自己程序的需要添加）

```java
public static boolean sql_inj(String str){
	String inj_str = "'|and|exec|insert|select|delete|update|      
	count|*|%|chr|mid|master|truncate|char|declare|;|or|-|+|,";
	String inj_stra[] = split(inj_str,"|");
	for (int i=0 ; i < inj_stra.length ; i++ ){
		if (str.indexOf(inj_stra[i])>=0){
			return true;
		}      
	}     
	return false;     
  }
```


### 3.4、JSP页面判断代码

使用javascript在客户端进行不安全字符屏蔽
功能介绍：检查是否含有”‘”,”\\”,”/”
参数说明：要检查的字符串
返回值：0：是1：不是

```javascript
 function check(a){
     fibdn = new Array (”‘” ,”\\”,”/”);
     i=fibdn.length;
     j=a.length;
     for (ii=0; ii＜i; ii++){ 
         for (jj=0; jj＜j; jj++){ 
            temp1=a.charAt(jj);
			temp2=fibdn[ii];
            if (tem'; p1==temp2){ 
                return 0;
            }
         }
     }
     return 1;
}
```


## 4、Mybatis中防止SQL注入

在编写MyBatis的映射语句时，尽量采用#{xxx}这样的格式。若不得不使用${xxx}这样的参数，要手工地做好过滤工作，来防止SQL注入攻击。

-   #{}：相当于JDBC中的PreparedStatement
-   ${}：是输出变量的值

简单说，#{}是经过预编译的，是安全的；${}是未经过预编译的，仅仅是取变量的值，是非安全的，存在SQL注入。
