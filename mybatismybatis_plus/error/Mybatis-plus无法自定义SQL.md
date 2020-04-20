 # [Mybatis-plus无法自定义SQL](https://mp.baomidou.com/guide/faq.html#%E5%87%BA%E7%8E%B0-invalid-bound-statement-not-found-%E5%BC%82%E5%B8%B8)

问题描述：指在 XML 中里面自定义 SQL，却无法调用。本功能同 `MyBatis` 一样需要配置 XML 扫描路径：

- Spring MVC 配置（参考[mybatisplus-spring-mvc](https://gitee.com/baomidou/mybatisplus-spring-mvc/blob/dev/src/main/resources/spring/spring-mybatis.xml)）

```xml
<bean id="sqlSessionFactory" class="com.baomidou.mybatisplus.extension.spring.MybatisSqlSessionFactoryBean">
    <property name="dataSource" ref="dataSource" />
    <property name="typeAliasesPackage" value="xxx.entity" />
    <property name="mapperLocations" value="classpath*:/mybatis/*/*.xml"/>
    ...
</bean>
```

- Spring Boot 配置（参考[mybatisplus-spring-boot](https://gitee.com/baomidou/mybatisplus-spring-boot/blob/2.x/src/main/resources/application.yml)）

```yaml
mybatis-plus:
  mapper-locations: classpath*:/mapper/**/*.xml
```

- 对于`IDEA`系列编辑器，XML 文件是不能放在 java 文件夹中的，IDEA 默认不会编译源码文件夹中的 XML 文件，可以参照以下方式解决：

  - 将配置文件放在 resource 文件夹中
  - 对于 Maven 项目，可指定 POM 文件的 resource

  ```xml
  <build>
    <resources>
        <resource>
            <!-- xml放在java目录下-->
            <directory>src/main/java</directory>
            <includes>
                <include>**/*.xml</include>
            </includes>
        </resource>
        <!--指定资源的位置（xml放在resources下，可以不用指定）-->
        <resource>
            <directory>src/main/resources</directory>
        </resource>
    </resources>
  </build>
  ```

注意！Maven 多模块项目的扫描路径需以 `classpath*:` 开头 （即加载多个 jar 包下的 XML 文件）

