#### 1.service实现类自动注入报红

```java
@Autowired
private ArticleMapper articleMapper;
```

原因：一旦使用关于Spring的注解出现在类里，例如我在实现类中用到了@Autowired注解，被注解的这个类是从Spring容器中取出来的，那调用的实现类也需要被Spring容器管理，加上@Component

解决方案：ArticleMapper类加@Component注解

@Controller 控制器（注入服务）
 用于标注控制层，相当于struts中的action层

@Service 服务（注入dao）
 用于标注服务层，主要用来进行业务的逻辑处理

@Repository（实现dao访问）
 用于标注数据访问层，也可以说用于标注数据访问组件，即DAO组件

@Component （把普通pojo实例化到spring容器中，相当于配置文件中的泛指各种组件，就是说当我们的类不属于各种归类的时候（不属于@Controller、@Services等的时候），我们就可以使用@Component来标注这个类。

#### 2.提示端口被占用

```
Description:

Web server failed to start. Port 8889 was already in use.

Action:

Identify and stop the process that's listening on port 8889 or configure this application to listen on another port.


Process finished with exit code 1
```

解决方案：

1. cmd命令：netstat -ano或netstat -aon|findstr 8888,查看端口进程ID
2. 杀死进程：taskkill /F /pid PID

#### 3.提示端口被占用，但端口却没有被占用？

  解决方案：换端口吧！

#### 4.关于spring boot自动注入出现Consider defining a bean of type ‘xxx‘ in your configuration问题解决方案

根据英文的提示是在配置中找不到一个指定自动注入类型的bean，经过多方排查得出结论：
　　正常情况下加上@Component注解的类会自动被Spring扫描到生成Bean注册到spring容器中，既然他说没找到，也就是该注解被没有被spring识别，问题的核心关键就在application类的注解SpringBootApplication上

原文链接：https://blog.csdn.net/a532672728/article/details/77702772

#### 5.**Reason: The elements [mybatis-plus.mapper-locations[0].classpath] were left unbound.**

报错如下：

```
Binding to target [Bindable@1bde767a type = java.lang.String[], value = 'provided', annotations = array<Annotation>[[empty]]] failed:
 
    Property: mybatis-plus.mapper-locations[0].classpath
    Value: mapper/**/*.xml
    Origin: class path resource [application-dev.yml] - 32:18
    Reason: The elements [mybatis-plus.mapper-locations[0].classpath] were left unbound.
 
Action:
 
Update your application's configuration
```

原因：- classpath: mapper/**/*.xml，中间有空格导致的。

#### 6.Spring boot 集成 mybatis遇到的问题 No MyBatis mapper was found in ‘[com.example.demo]‘ package

解决方案：去掉启动类中：@ComponentScan(basePackages ={"com.mszlu.blog.dao.mapper"})

#### 7. Invalid bound statement (not found)

**我的问题：**

配置类中@MapperScan("mapper")没有写完整

```
@MapperScan("com.mszlu.blog.dao.mapper")
```

**网上说法：**

1.检查xml文件的namespace是否正确

2.Mapper.java的方法在Mapper.xml中没有，然后执行Mapper的方法会报此

3.xxxMapper.java的方法返回值是List,而select元素没有正确配置ResultMap,或者只配置ResultType

4.如果你确认没有以上问题,请任意修改下对应的xml文件,比如删除一个空行,保存.问题解决

5.看下mapper的XML配置路径是否正确

#### 8.IDEA 格式化sql语句出错

**报错:**

```
You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near '*) as count
```

```xml
<select id="listArchives" resultType="com.mszlu.blog.dao.dos.Archives">
    SELECT YEAR (FROM_UNIXTIME(create_date/1000)) as year,
        MONTH (FROM_UNIXTIME(create_date/1000)) as month, COUNT (*) as count
    FROM ms_article
    GROUP BY year, month;
</select>
```

**问题:**COUNT (*)之间多了一个空格