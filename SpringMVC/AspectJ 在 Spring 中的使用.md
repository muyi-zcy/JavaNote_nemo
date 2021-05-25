# AspectJ 在 Spring 中的使用

来源：https://juejin.im/post/5b0f51426fb9a009d1621b00

首先明白一点，AOP思想的实现框架或者说库有很多，最知名的无非就是AspectJ、Cglib、JDK动态代理三种了，归根到底它们都是使用了代理这种设计模式，区别在于下面表格:

| 名称        | 代理类型 | 基本原理                                                     | 特性                                                   |
| ----------- | -------- | ------------------------------------------------------------ | ------------------------------------------------------ |
| AspectJ     | 静态代理 | 代理类在编译期间就生成，但是在生成时将相应的切面`织入`到代理类中 |                                                        |
| Cglib       | 动态代理 | 代理类在运行时动态生成，底层使用字节码处理框架ASM，来转换字节码并生成新的类 | 弥补JDK动态代理只能代理接口的不足，cglib可以动态代理类 |
| JDK动态代理 | 动态代理 | 代理类在运行时动态生成，源码级别会调用一个Native方法         | 只能代理接口                                           |

### 一、Spring 中使用 AspectJ

#### 1、xml 中开启 aspectJ注解支持

```java
applicationContext.xml
<aop:aspectj-autoproxy>
</aop:aspectj-autoproxy>
```

#### 2、声明切面类

```java
@Aspect
@Component
public class ExecutionAspect {

    @Before("execution(* wokao666.club.myapp.aspectJ.*.before*(..))")
    public void doBefore(JoinPoint joinPoint) throws Throwable {
        System.err.println("这是一个前置通知，在方法调用之前被执行！！！");
    }

    @After("execution(* wokao666.club.myapp.aspectJ.*.after*(..))")
    public void doAfter(JoinPoint joinPoint) throws Throwable {
        System.err.println("这是一个后置通知，在方法调用之后被执行！！！");
    }

    @Around("execution(* wokao666.club.myapp.aspectJ.*.around*(..))")
    public void doAround(ProceedingJoinPoint joinPoint) throws Throwable {
        System.err.println("这是一个环绕通知，在方法调用前后都会执行！！！");
        System.err.println("执行前");
        joinPoint.proceed();
        System.err.println("执行后");
    }
}
```

说明下上面的切入点表达式，`execution`表示连接点类型，第一个`*`表示拦截的方法但返回值类型，`*`表示任意类型返回值；第二部分`wokao666.club.myapp.aspectJ.*.before*(..)`表示以`wokao666.club.myapp.aspectJ`包下的任意类的所有以`before`开头的方法。

#### 3、测试类`Main.java`

```java
public class Main {

    private static ClassPathXmlApplicationContext ctx = null;

    public static void main(String[] args) {
    ctx = new ClassPathXmlApplicationContext("applicationContext.xml");
    TestMethod test = (TestMethod) ctx.getBean("bean");
    test.before("before");
    System.err.println("=====================================================");
    test.after("after");
    System.err.println("=====================================================");
    test.around("around");
}
```

#### 4、bean类

~~~xml
@Component("bean")
public class TestMethod {

    public void before(String name) {
        System.err.println("the param Name is " + name);
    }

    public void after(String name) {
        System.err.println("the param Name is " + name);
    }

    public void around(String name) {
        System.err.println("the param Name is " + name);
    }
}

~~~
```xml
pom.xml
<dependency>
    <groupId>aspectj</groupId>
    <artifactId>aspectjrt</artifactId>
    <version>1.5.4</version>
</dependency>

<dependency>
    <groupId>org.aspectj</groupId>
    <artifactId>aspectjweaver</artifactId>
    <version>1.9.1</version>
</dependency>

~~~

执行结果：

```
这是一个前置通知，在方法调用之前被执行！！！
the param Name is before
=====================================================
the param Name is after
这是一个后置通知，在方法调用之后被执行！！！
=====================================================
这是一个环绕通知，在方法调用前后都会执行！！！
执行前
the param Name is around
执行后

```

### 二、基于自定义注解的拦截实现

有时候我们会使用自定义注解来标识我们的业务方法，下面将讲解AspectJ拦截基于注解的实现，跟生面的差别在于切入点表达式的区别而已。

#### 1、创建一个自定义注解`RpcService`

​```java
/**
 * 远程服务注解，主要用于拦截日志、错误码等方面处理
 */
@Documented
@Retention(RetentionPolicy.RUNTIME)
@Target(value = { ElementType.METHOD, ElementType.TYPE })
public @interface RpcService {

}

```

#### 2、声明业务方法

```java
@Component("bean")
public class TestMethod {

	@RpcService
	public void around(String name) {
		System.err.println("the param Name is " + name);
	}
}

```

#### 3、声明切面类

```java
@Aspect
@Component
public class AnnotationAspect {
    @Around("@annotation(wokao666.club.myapp.annotation.RpcService)")
        public void doAround(ProceedingJoinPoint joinPoint) throws Throwable {
        System.err.println("这是一个环绕通知，在方法调用前后都会执行！！！");
        System.err.println("执行前");
        joinPoint.proceed();
        System.err.println("执行后");
    }
}

```

#### 4、测试类

```java
public class Main {

private static ClassPathXmlApplicationContext ctx = null;

    public static void main(String[] args) {
        ctx = new ClassPathXmlApplicationContext("applicationContext.xml");
        TestMethod test = (TestMethod) ctx.getBean("bean");
        test.around("around");
    }
}

```

#### 5、执行结果

```
这是一个环绕通知，在方法调用前后都会执行！！！
执行前
the param Name is around
执行后

```

毕业了，怎么感觉有点心浮气躁，稳住稳住！

如果我们想获取被切方法的返回值，那么我们可以使用

```java
MethodSignature si = (MethodSignature) joinPoint.getSignature();
System.err.println(si.getReturnType());
```