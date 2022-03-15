# 1、Spring

## 简介

+ Spring ——> 春天，为开源软件带来了春天
+ 2002，首次推出了Spring框架的雏形：interface21框架！
+ Spring框架以interface21框架为基础，经过重新设计，并不断丰富其内涵，于2004年3月24日发布了1.0正式版
+ Spring的理念：使用现有的技术更加容易使用，本身是一个大杂烩，整合了现有的技术框架！



+ SSH：Struct2 + Spring + Hibernate（全自动持久化框架）！
+ SSM：SpringMVC + Spring + MyBatis（半自动持久化框架，可自定义性质更强）！



spring官网： https://spring.io/projects/spring-framework#overview

官方下载： https://repo.spring.io/release/org/springframework/spring/

GitHub： https://github.com/spring-projects/spring-framework

Spring Web MVC： [spring-webmvc最新版](https://mvnrepository.com/artifact/org.springframework/spring-webmvc/5.2.7.RELEASE)



Spring Web MVC和Spring-JDBC的pom配置文件：

```xml
<!-- https://mvnrepository.com/artifact/org.springframework/spring-webmvc -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-webmvc</artifactId>
    <version>5.2.7.RELEASE</version>
</dependency>

<!-- https://mvnrepository.com/artifact/org.springframework/spring-jdbc -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-jdbc</artifactId>
    <version>5.2.7.RELEASE</version>
</dependency>
```



## 1.1 优点

* Spring是一个开源的免费框架（容器）
* Spring是一个轻量级的、非入侵的框架
* 控制反转（IOC），面向切面编程（AOP）
* 支持事务的处理，对框架的整合的支持

总结：==Spring就是一个轻量级的控制反转（IOC），面向切面编程（AOP）的框架==

## 1.2 组成

![](https://img-blog.csdnimg.cn/e6ad26c455dc478e8138fed29ce1752c.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6ZuE5a6J54y_,size_20,color_FFFFFF,t_70,g_se,x_16)

## 1.3 拓展

在Spring的官网有这个介绍：现代化的Java开发！ 说白就是基于Spring的开发！
![](https://img-blog.csdnimg.cn/e20000bdec504be5a5831daf3b865e6f.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6ZuE5a6J54y_,size_20,color_FFFFFF,t_70,g_se,x_16)

* Spring Boot
  - 一个快速开发的脚手架
  - 基于Spring Boot可以快速开发单个微服务
  - 约定大于配置！
* Spring Cloud
  - Spring Cloud 是基于Spring Boot实现的

现在大多数公司都在使用Spring Boot进行快速开发，学习SpringBoot的前提，需要掌握Spring及SpringMVC！ 承上启下的作用！

弊端：发展了太久后，违背了原来的理念！ 配置十分繁琐 人称："配置地狱"

# 2、IOC理论推导

1.UserDao接口
2.UserDaoImpl实现类
3.UserService业务接口
4.UserServiceImpl业务实现类

在我们之前的业务中，用户的需求可能会影响我们原来的代码，我们需要根据用户的需求去修改原代码！如果程序代码量十分大，修改一次的成本代价十分昂贵！

我们使用一个Set接口实现,已经发现革命性的变化

```java
   private UserDao userDao;

    public void setUserDao(UserDao userDao) {
        this.userDao = userDao;
    }
```

* 之前，程序时主动创建对象！控制权在程序员手上！
* 使用set注入程序不再具有主动下，而是变成了被动的接受对象！

这种思想，从本质上解决了问题，我们程序员不用再去管理对象的创建了。系统的耦合性大大降低，可以更加专注的在业务的实现上。这是IOC的原型！



![image-20220224232357180](https://raw.githubusercontent.com/miemiehoho/blog/master/picture/2022/02/202202242323282.png)

## IOC本质

**控制反转IoC，是一种设计思想，DI（依赖注入）是实现IoC的一种方法，**也有人认为DI只是IoC的另一种说法。没有IoC的程序中，我们使用面向对象编程，对象的创建与对象间的依赖关系完全硬编码在程序中，也就是需要一个对象就new出来，对象的创建由程序自己控制，控制反转后将对象的创建转移给第三方，也就是获得依赖对象的方式反转了。不需要new了，需要什么依赖对象只管问第三方要。个人认为所谓控制反转就是：获得依赖对象的方式反转了。

在Spring中实现控制反转的是IoC容器，其实现方法是依赖注入。IoC容器最低的接口是BeanFactory。

![image-20220224233014882](https://raw.githubusercontent.com/miemiehoho/blog/master/picture/2022/02/202202242330014.png)

# 3、HelloSpring

![image-20220224233143668](https://raw.githubusercontent.com/miemiehoho/blog/master/picture/2022/02/202202242331737.png)

![image-20220224233232578](https://raw.githubusercontent.com/miemiehoho/blog/master/picture/2022/02/202202242332646.png)

Hello实体类

```java
package com.du.pojo;

public class Hello {
    private String str;

    public String getStr() {
        return str;
    }

    public void setStr(String str) {
        this.str = str;
    }

    @Override
    public String toString() {
        return "Hello{" +
                "str='" + str + '\'' +
                '}';
    }
}
```

beans.xml配置文件

```xml

<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="hello" class="com.du.pojo.Hello">
        <property name="str" value="String">property>
    bean>
beans>

```

测试类

```java
import com.du.pojo.Hello;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class MyTest {
    public static void main(String[] args) {

        ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");

        Hello hello = (Hello) context.getBean("hello");
        System.out.println(hello.toString());
    }
}
```

### **思考问题**

* Hello对象是谁创建的？
hello对象由Spring创建
* Hello对象属性是怎么设置的？
由Spring设置

**这个过程叫反转：**

- 控制∶谁来控制对象的创建，传统应用程序的对象是由程序本身控制创建的，使用Spring后，**对象是由Spring来创建的**．


- 反转:程序本身不创建对象，而变成被动的接收对象．

- 依赖注入:就是**利用set方法来进行注入的**.

- IOC是一种编程思想，由主动的编程变成被动的接收．


- 可以通过newClassPathXmIApplicationContext去浏览一下底层源码．


OK，到了现在，我们彻底不用再程序中去改动了**，要实现不同的操作，只需要在xml配置文件中进行修改，所谓的loc,一句话搞定:对象由Spring 来创建，管理，装配!**


spring规定了8种基本类型和String为基本类型，赋值用value，引用类型用ref赋值



# 4、IOC创建对象的方式

1. 使用无参构造创建对象，默认。
2. 使用有参构造（如下）

下标赋值

index指的是有参构造中参数的下标，下标从0开始;

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="user" class="pojo.User">
        <constructor-arg index="0" value="chen"/>
    </bean>
</beans>
```

类型赋值（不建议使用）

```xml
<bean id="user" class="pojo.User">
    <constructor-arg type="java.lang.String" value="kuang"/>
</bean>
```

直接通过参数名（掌握）

```xml
<bean id="user" class="pojo.User">
    <constructor-arg name="name" value="kuang"></constructor-arg>
</bean>
<!-- 比如参数名是name，则有name="具体值" -->
```

注册bean之后就对象的初始化了（**类似 new 类名()**）

弹幕评论：

name方式还需要无参构造和set方法,index和type只需要有参构造

就算是new 两个对象，也是只有一个实例（**单例模式：全局唯一**）

```java
User user = (User) context.getBean("user");
User user2 = (User) context.getBean("user");
system.out.println(user == user2)//结果为true
```

**总结：在配置文件加载的时候，容器(< bean>)中管理的对象就已经初始化了**

# 5、Spring配置

## 5.1 别名

如果添加了别名，也可以通过别名获取

```xml
<alias name="user" alias="aaa"/>
```

## 5.2 Bean配置

![image-20220225095239926](C:\Users\13686\AppData\Roaming\Typora\typora-user-images\image-20220225095239926.png)

```xml
<bean id="userT" class="com.du.pojo.UserT" name="userT2 u3,u4;u5">
    <property name="name" value="杜老师"/>
<bean>
```

## 5.3 import

这个import，一般用于团队开发使用，可以将多个配置文件，导入合并一个
假设项目中有多个人开发，这三个人负责不同的类开发，不同的类需要注册在不同的bean中，我们可以利用import将所有人的beans.xml合并为一个总的！

* 张三
* 李四
* 王五
* applicationContext.xml

```xml
  <import resource="beans.xml"/>
    <import resource="beans2.xml"/>
    <import resource="beans3.xml"/>
```

使用的时候，直接使用总配置就可以！

# 6. 依赖注入的三种方式

## 什么是Bean?

Spring Bean是被实例的，组装的及被Spring 容器管理的**Java对象。**

Spring 容器会自动完成@bean对象的实例化。

创建应用对象之间的协作关系的行为称为：装配(wiring)，这就是依赖注入的本质。

### Spring 三种配置方案

1.在[XML](https://so.csdn.net/so/search?q=XML&spm=1001.2101.3001.7020)中进行显示配置
2.使用[Java](https://so.csdn.net/so/search?q=Java&spm=1001.2101.3001.7020)代码进行显示配置
3.隐式的bean发现机制和自动装配
**推荐方式：** 3>2>1

### 依赖注入三种方式：

1. 构造器注入
2. set方式注入
3. 拓展方式注入

## 6.1 构造器注入

前面已经说过了

## 6.2 Set方式注入多种方式【重点】

* 依赖注入：Set注入！
  - 依赖 ： Bean对象的创建依赖于容器
  - 注入 ： Bean对象中的所有属性，由容器来注入

Set注入方式：



【环境搭建】

1. 复杂类型

```java
package com.du.pojo;
public class Address {
    private String address;

    public String getAddress() {
        return address;
    }
    public void setAddress(String address) {
        this.address = address;
    }
}
```

1. 真实测试对象

```java
public class Student {
    private String name;
    private Address address;
    private String[] books;
    private List<String> hobbys;
    private Map<String,String> card;
    private Set<String> games;
    private String wife;
    private Properties  info;
}
```

3.beans.xml

```xml

<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="student" class="com.du.pojo.Student">
        
        <property name="name" value="杜老师"/>
         
        
    bean>
beans>
```

4.测试类

```java
import com.du.pojo.Student;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class MyTest {
    public static void main(String[] args) {
        ClassPathXmlApplicationContext classPathXmlApplicationContext = new ClassPathXmlApplicationContext("beans.xml");
        Student student = (Student) classPathXmlApplicationContext.getBean("student");
        System.out.println(student);
    }
}
```

完善注入信息

```xml

<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="address" class="com.du.pojo.Address">
        <property name="address" value="河北"/>
    bean>

    <bean id="student" class="com.du.pojo.Student">
<!--第一种，普通值注入，value-->
        <property name="name" value="杜老师"/>
<!--第二种，Bean注入，ref-->
        <property name="address" ref="address"/>
<!--第三种，数组注入-->
        <property name="books">
             <array>
                 <value>红楼梦value>
                 <value>西游记value>
                 <value>三国演义value>
                 <value>水浒传value>
             array>
        property>
<!--第四种，list注入-->
        <property name="hobbys">
            <list>
                <value>听歌value>
                <value>看电影value>
                <value>打游戏value>
            list>
        property>
<!--第五种，map注入-->
        <property name="card">
            <map>
                <entry key="身份证" value="111"/>
                <entry key="银行卡" value="22"/>
                <entry key="电话" value="333"/>
            map>
        property>
<!--第六种，set注入-->
        <property name="games">
            <set>
                <value>lol</value>
                <value>王者</value>
                <value>cf</value>
            <set>
        </property>
<!--null-->
        <property name="wife">
            <null>null</null>
        </property>
<!--properties-->
        <property name="info">
            <props>
                <prop key="学号">123prop>
                <prop key="姓名">杜老师prop>
           </props>
        </property>
    </bean>
</beans>
```

## 6.3 拓展方式

**使用P命名空间和C命名空间注入**
官方解释：
![](https://img-blog.csdnimg.cn/5296ddb695054eb7a0159cb338081ebe.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6ZuE5a6J54y_,size_20,color_FFFFFF,t_70,g_se,x_16)



**p命名就是set方式注入，需要无参构造和set方法，c命名就是构造器注入，需要有参构造**

使用：

```xml

<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:p="http://www.springframework.org/schema/p"
       xmlns:c="http://www.springframework.org/schema/c"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">
<!--p命名空间注入，可以直接注入属性的值-->
   <bean id="user" class="com.du.pojo.User" p:name="杜老师" p:age="18"/>
<!--c命名空间注入，可以直接注入属性的值-->
   <bean id="user2" class="com.du.pojo.User" c:age="18" c:name="杜老师"/>

</beans>
```

测试：

```java
 @Test
    public void test2(){
        ClassPathXmlApplicationContext classPathXmlApplicationContext = new ClassPathXmlApplicationContext("userbeans.xml");
        User user = (User) classPathXmlApplicationContext.getBean("user");
        System.out.println(user);
    }

    @Test
    public void test3(){
        ClassPathXmlApplicationContext classPathXmlApplicationContext = new ClassPathXmlApplicationContext("userbeans.xml");
        User user = (User) classPathXmlApplicationContext.getBean("user2");
        System.out.println(user);
    }
```

注意点：P命名空间和C命名空间不能直接命名需要导入xml约束

```xml
 xmlns:p="http://www.springframework.org/schema/p"
  xmlns:c="http://www.springframework.org/schema/c"
```

## 6.4 bean的作用域

![](https://img-blog.csdnimg.cn/98a7d958989c491ebcafd5ac1af6ff77.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6ZuE5a6J54y_,size_20,color_FFFFFF,t_70,g_se,x_16)
翻译（仅供参考）：
![](https://img-blog.csdnimg.cn/13a625a05e4f4fe49b35184defe63bbd.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6ZuE5a6J54y_,size_20,color_FFFFFF,t_70,g_se,x_16)
1.单例模式（Spring默认机制）：共用一个对象

```xml
 <bean id="user2" class="com.du.pojo.User" c:age="18" c:name="杜老师" scope="singleton"/>
```

测试：

```java
  @Test
   public void test4(){
       ClassPathXmlApplicationContext classPathXmlApplicationContext = new ClassPathXmlApplicationContext("userbeans.xml");
       User user = (User) classPathXmlApplicationContext.getBean("user2");
       User user2 = (User) classPathXmlApplicationContext.getBean("user2");
       System.out.println(user.hashCode());
       System.out.println(user2.hashCode());
       System.out.println(user == user2);
   }
```

结果：
![](https://img-blog.csdnimg.cn/a7afafdb4e2a4eb3bd76732c1802f70d.png)

2.原型模式:每次容器中get的时候，都会产生一个新对象

```xml
<bean id="user2" class="com.du.pojo.User" c:age="18" c:name="杜老师" scope="prototype"/>
```

测试：

```java
   public void test4(){
       ClassPathXmlApplicationContext classPathXmlApplicationContext = new ClassPathXmlApplicationContext("userbeans.xml");
       User user = (User) classPathXmlApplicationContext.getBean("user2");
       User user2 = (User) classPathXmlApplicationContext.getBean("user2");
       System.out.println(user.hashCode());
       System.out.println(user2.hashCode());
       System.out.println(user == user2);
   }
```

结果：
![](https://img-blog.csdnimg.cn/263a517801a94bf58d70abc9c13afa17.png)
3.其余的request、session、application 这些只能在web开发中使用到

# 7、bean的自动装配

* 自动装配是Spring满足bean依赖的一种方式！
* Spring会在上下文中自动寻找，并自动给bean装配属性！

在Spring中有三种装配方式：

* 在xml中显示的配置
* 在java中显示配置
* 隐式 的自动装配bean【重要】



- byType自动装配：byType会自动查找，和自己对象set方法参数的类型相同的bean
  - 保证所有的class唯一(类为全局唯一)

- byName自动装配：byName会自动查找，和自己对象set对应的值对应的id
  - 保证所有id唯一，并且和set注入的值一致

```xml
<!-- 找不到id和多个相同class -->
<bean id="cat1" class="pojo.Cat"/>
<bean id="cat2" class="pojo.Cat"/>
<!-- 找不到 id=cat，且有两个Cat -->
```



## 7.1测试：自动装配

pojo的Cat类

```java
public class Cat {
    public void shut(){
        System.out.println("miao");
    }
}
```

pojo的Dog类

```java
public class Dog {

    public void shut(){
        System.out.println("wow");
    }

}
```

pojo的People类

```java
package pojo;
public class People {
    
    private Cat cat;
    private Dog dog;
    private String name;

    public Cat getCat() {
        return cat;
    }

    public void setCat(Cat cat) {
        this.cat = cat;
    }

    public Dog getDog() {
        return dog;
    }

    public void setDog(Dog dog) {
        this.dog = dog;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
    @Override
    public String toString() {
        return "People{" +
                "cat=" + cat +
                ", dog=" + dog +
                ", name='" + name + '\'' +
                '}';
    }
}
```

xml配置 -> byType 自动装配

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="cat" class="pojo.Cat"/>
    <bean id="dog" class="pojo.Dog"/>
    
    <!--byType会在容器自动查找，和自己对象属性相同的bean
		例如，Dog dog; 那么就会查找pojo的Dog类，再进行自动装配
	-->
    <bean id="people" class="pojo.People" autowire="byType">
        <property name="name" value="cbh"></property>
    </bean>

</beans>
```

xml配置 -> byName 自动装配

```xml
<bean id="cat" class="pojo.Cat"/>
<bean id="dog" class="pojo.Dog"/>
<!--byname会在容器自动查找，和自己对象set方法的set后面的值对应的id
  例如:setDog()，取set后面的字符作为id，则要id = dog 才可以进行自动装配
  
 -->
<bean id="people" class="pojo.People" autowire="byName">
	<property name="name" value="cbh"></property>
</bean>
```

弹幕评论：byName只能取到小写，大写取不到

## 7.2、使用注解实现自动装配

jdk1.5支持的注解，spring2.5支持的注解

The introduction of annotation-based configuration raised the question of whether this approach is “better” than XML.（翻译：基于注释的配置的引入提出了一个问题，即这种方法是否比XML“更好”）

1. 导入context约束

**xmlns:context="http://www.springframework.org/schema/context"**

1. 配置注解的支持：< context:annotation-config/>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd">

    <context:annotation-config/>
</beans>
```

### 7.2.1、@Autowired

**默认是byType方式，如果匹配不上，就会byName**

在属性上个使用，也可以在set上使用

我们可以不用编写set方法了，前提是自动装配的属性在Spring容器里，且要符合ByName 自动装配

```java
public class People {
    @Autowired
    private Cat cat;
    @Autowired
    private Dog dog;
    private String name;
}
```

> @Nullable 字段标记了这个注解，说明该字段可以为空
>
> public name(@Nullable String name){
>
> }

```java
//源码
public @interface Autowired { 
	boolean required() default true; 
}
```

如果定义了Autowire的require属性为false，说明这个对象可以为null，否则不允许为空（false表示找不到装配，不抛出异常）

### 7.2.2、@Autowired+@Qualifier

**@Autowired不能唯一装配时，需要@Autowired+@Qualifier**

如果@Autowired自动装配环境比较复杂。自动装配无法通过一个注解完成的时候，可以使用@Qualifier(value = “dog”)去配合使用，指定一个唯一的id对象

```java
public class People {
    @Autowired
    private Cat cat;
    @Autowired
    @Qualifier(value = "dog")
    private Dog dog;
    private String name;
}
```

弹幕评论：

如果xml文件中同一个对象被多个bean使用，Autowired无法按类型找到，可以用@Qualifier指定id查找

### 7.2.3、@Resource

**默认是byName方式，如果匹配不上，就会byType**

```java
public class People {
    Resource(name="cat")
    private Cat cat;
    Resource(name="dog")
    private Dog dog;
    private String name;
}
```

弹幕评论：

Autowired是byType，@Autowired+@Qualifier = byType || byName

Autowired是先byteType,如果唯一則注入，否则byName查找。resource是先byname,不符合再继续byType

## 7.3、小结：

**@Resource和@Autowired的区别：**

* 都是用来自动装配的都可以放在属性字段上
* @Autowired 通过byType的方式实现，必须要求这个对象存在！（autowired默认通过byType方式，如果有多个类型相同的，在通过byName）
* @Resource 默认通过byname的方法实现，如果找不到，则通过byType实现，如果都找不到，报错！
* 执行顺序不同：@Autowired 通过byType的方式实现，@Resource 默认通过byname的方法实现

​	

# 8、使用注解开发

**Spring4之后，要使用注解开发，必须要保证aop的包导入成功**



![](https://img-blog.csdnimg.cn/d585f31499c34b959ef35adb41f3f3f8.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6ZuE5a6J54y_,size_20,color_FFFFFF,t_70,g_se,x_16)



在使用注解需要导入context约束，增加注解支持

## 8.1、bean

```xml
<!--指定要扫描的包，这个包下面的注解才会生效
	别只扫一个com.kuang.pojo包--> 
<context:component-scan base-package="com.kuang"/> 
<context:annotation-config/>
```

```java
//@Component 组件
//等价于<bean id="user" classs"pojo.User"/> 
@Component
public class User {  
     public String name ="秦疆";
}
```



## 8.2、属性如何注入

```java
@Component
public class User { 
    //相当于<property name="name" value="kuangshen"/> 
    @value("kuangshen") 
    public String name; 
    
    //也可以放在set方法上面
    //@value("kuangshen")
    public void setName(String name) { 
        this.name = name; 
    }
}
```

![image-20220225112043797](C:\Users\13686\AppData\Roaming\Typora\typora-user-images\image-20220225112043797.png)

## 8.3、衍生的注解

@Component 有几个衍生注解，我们在web开发中，会按照mvc三层架构分层

  - dao [@Repository]
  - service[@Service]
  - controller [@Controller]
**这4个注解功能一样，都是代表某个类注册到Spring中，装配Bean**
## 8.4、自动配置

  - @Autowired：默认是byType方式，如果匹配不上，就会byName
  - @Nullable：字段标记了这个注解，说明该字段可以为空
  - @Resource：默认是byName方式，如果匹配不上，就会byType

## 8.5、作用域

```java
//原型模式prototype，单例模式singleton
//scope("prototype")相当于<bean scope="prototype"></bean>
@Component 
@scope("prototype")
public class User { 
    
    //相当于<property name="name" value="kuangshen"/> 
    @value("kuangshen") 
    public String name; 
    
    //也可以放在set方法上面
    @value("kuangshen")
    public void setName(String name) { 
        this.name = name; 
    }
}
```

## 8.6、小结

**xml与注解：**

  - xml更加万能，适用于任何场合！维护简单方便
  - 注解 不是自己类使用不了，维护相对复杂



**xml与注解的最佳实践：**

  - xml用来管理bean
  - 注解只负责完成属性的注入
  - 在使用的过程中，只需要注意一个问题：必须让注解生效，就需要开启注解的支持

```xml
<!--需要指定扫描的包，让注解生效-->
<context:component-scan base-package="com.du"/>

<context:annotation-config/>
```

* @Component ： 组件，放在类上，说明这个类被Spring管理了，就是bean！

# 9.、使用java的方式配置Spring

我们现在要完全不使用Spring的xml配置，全权交给Java来做！
JavaConfig是Spring的一个子项目，在Spring 4 之后，成为一个核心功能



![](https://img-blog.csdnimg.cn/8d79dd9fadcc4fdc862ce732c54a2825.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6ZuE5a6J54y_,size_20,color_FFFFFF,t_70,g_se,x_16)

![image-20220225112938695](C:\Users\13686\AppData\Roaming\Typora\typora-user-images\image-20220225112938695.png)



![image-20220225113438638](C:\Users\13686\AppData\Roaming\Typora\typora-user-images\image-20220225113438638.png)

## 弹幕总结 - -> 

**使用@Configuration声明配置类时有两种方法来生成Bean**

方法1:在配置类中定义一个方法，并使用@Bean注解声明

方法2:在User类上使用@Component注解，并在配置类上声明@ComponentScan("User类的路径")，这样会自动扫描@Component并生成Bean



**@Configuration注解 默认的作用域是单例**

实体类：

```java
package com.chif.pojo;

import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Component;

@Component
public class User {
    @Value("chif")
    private String name;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    @Override
    public String toString() {
        return "User{" +
                "name='" + name + '\'' +
                '}';
    }
}
```

配置文件：

```java
package com.chif;

import com.chif.pojo.User;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;

@Configuration
//@Configuration代表是一个配置类，代替之前的beans.xml
//这个类也会被Spring容器托管，注册到容器中，因为它本来就是一个@Commponent
@ComponentScan("com.chif.pojo")
public class MyConfig {

    //注册一个bean，就相当于之前的一个bean标签
    //这个方法的名字相当于bean标签中的id
    //这个方法的返回值，就相当于bean标签中的class属性
    @Bean
    public User getUser(){
        return new User();
    }
}

```

测试类：

```java
import com.chif.MyConfig;
import com.chif.pojo.User;
import org.junit.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

public class myTest {
    @Test
    public void test(){
        //如果完全使用了配置类的方式去做，我们就只能通过 AnnotationConfig 上下文来获取容器，通过配置类的class对象加载
        ApplicationContext context = new AnnotationConfigApplicationContext(MyConfig.class);
        User getUser = (User) context.getBean("getUser");
        System.out.println(getUser.getName());
    }
}

```

这种纯Java的配置方式，在SpringBoot中随处可见

# 10、代理模式

适配器重点在于衔接不同类型的类或接口

代理的重点在于处理不同类之间的逻辑实现

## 关于代理模式

### 什么是代理模式？

代理会代理真实角色，代理真实角色后，可以做一些附属操作

### 为什么要学习代理模式？

因为这即使SpringAOP的底层 【SpringAOP和SpringMVC】
代理模式的分类：

* 静态代理
* 动态代理
![](https://img-blog.csdnimg.cn/d44ad24afff54738a4752be034d7a698.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6ZuE5a6J54y_,size_20,color_FFFFFF,t_70,g_se,x_16)

## 10.1、 静态代理

角色分析：

* 抽象角色：一半会使用接口或者抽象类来解决
* 真实角色：被代理的角色
* 代理角色： 代理真实角色，代理真实角色后，我们会做一些附属操作
* 客户 ： 访问代理对象的人

代码步骤：

1、接口

```java
package com.du.demo01;

public interface Rent {
    public void rent();
}

```

2、真实角色

```java
package com.du.demo01;

public class Host implements Rent{

    public void rent() {
        System.out.println("房东要出租房子！");
    }
}
```

3、代理角色

```java
package com.du.demo01;

public class Proxy implements Rent{
    private Host host;

    public Proxy() {
    }

    public Proxy(Host host) {
        this.host = host;
    }

    public void rent() {
       seeHouse();
       host.rent();
       fare();
       hetong();
    }

    public void seeHouse(){
        System.out.println("中介带你看房");
    }

    public void fare(){
        System.out.println("收中介费");
    }

    public void hetong(){
        System.out.println("欠租品合同");
    }
}
``
5. 客户端访问代理角色
```java
package com.du.demo01;

public class Client {
    public static void main(String[] args) {

        Host host = new Host();

        Proxy proxy = new Proxy(host);

        proxy.rent();
    }
}
```

代理模式的好处：

* 可以使真是角色的操作更加纯粹！不用去关注一些公共的业务
* 公共业务也就交给代理角色！实现了业务的分工！
* 公共业务发生扩展的时候，方便集中管理

缺点：

* 一个真实角色就会产生一个代理角色，代码量翻倍 开发效率变低

## 10.2、 加深理解

聊聊AOP
![](https://img-blog.csdnimg.cn/97021ba0358c4a7a9b97370325df6cd1.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6ZuE5a6J54y_,size_20,color_FFFFFF,t_70,g_se,x_16)

## 10.3、 动态代理

* 动态代理和静态代理角色一样
* 动态代理的代理类是动态生成的，不是我们直接写好的！
* 动态代理分为两大类：基于接口的动态代理，基于类的动态代理
  - 基于接口----JDK动态代理 【我们在这里使用】
  - 基于类：cglib
  - java字节码实现： javassist

需要了解的两个类 ： Proxy ： 代理 ，InvocationHandler：调用处理程序



- `Proxy`提供了**创建动态代理类和实例的静态方法**，它也是由这些方法创建的所有动态代理类的超类。

- `InvocationHandler`是由代理实例的*调用处理程序*实现的*接口* 。

  - 每个代理实例都有一个关联的**调用处理程序**。  当在代理实例上调用方法时，方法调用将被编码并分派到其调用处理程序的`invoke`方法。

  

**动态代理的好处：**

* 可以使真是角色的操作更加纯粹！不用去关注一些公共的业务
* 公共也就交给代理角色！实现了业务的分工！
* 公共业务发生扩展的时候，方便集中管理
* 一个动态代理类代理的是一个接口，一般就是对应的一类业务
* 一个动态代理类可以代理多个类，只要是实现了一个接口就行



实例：

接口 Host.java

```java
//接口
package pojo2;
public interface Host {
	public void rent();
	
}
```

接口Host实现类 HostMaster.java

```java
//接口实现类
package pojo2;
public class HostMaster implements Host{	
	public void rent() {
		System.out.println("房东要租房子");
	}
}
```

代理角色的处理程序类 ProxyInvocationHandler.java

```java
package pojo2;

import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;

///用这个类，自动生成代理
public class ProxyInvocationHandler implements InvocationHandler {

	// Foo f =(Foo) Proxy.NewProxyInstance(Foo. Class.GetClassLoader(),
	// new Class<?>[] { Foo.Class },
	// handler);

	// 被代理的接口
	public HostMaster hostMaster ;
	
	public void setHostMaster(HostMaster hostMaster) {
		this.hostMaster = hostMaster;
	}

	// 得到生成的代理类 
	public Object getProxy() {
		// newProxyInstance() -> 生成代理对象，就不用再写具体的代理类了
        
        
		// this.getClass().getClassLoader() -> 找到加载类的位置
		// hostMaster.getClass().getInterfaces() -> 代理的具体接口
		// this -> 代表了接口InvocationHandler的实现类ProxyInvocationHandler
		return Proxy.newProxyInstance(this.getClass().getClassLoader(), hostMaster.getClass().getInterfaces(), this);


	// 处理代理实例并返回结果
	public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
		seeHouse();
		// 动态代理的本质，就是使用反射机制实现的
        // invoke()执行它真正要执行的方法
		Object result = method.invoke(hostMaster, args);
		fee();
		return result;
	}

	public void seeHouse() {
		System.out.println("看房子");
	}

	public void fee() {
		System.out.println("收中介费");
	}

}

```

用户类 My2.java

```java
package holle4_proxy;

import pojo2.Host;
import pojo2.Host2;
import pojo2.HostMaster;
import pojo2.ProxyInvocationHandler;

public class My2 {

	public static void main(String[] args) {
        
		//真实角色
		HostMaster hostMaster = new HostMaster();
        
		//代理角色，现在没有；用代理角色的处理程序来实现Host接口的调用
		ProxyInvocationHandler pih = new ProxyInvocationHandler();
        
        //pih -> HostMaster接口类 -> Host接口
		pih.setHostMaster(hostMaster);
        
		//获取newProxyInstance动态生成代理类
		Host proxy = (Host) pih.getProxy();
		
		proxy.rent();

	}
}
```

弹幕评论：
什么时候调用invoke方法的?
代理实例调用方法时invoke方法就会被调用，可以debug试试

改为**万能代理类**

```java
///用这个类，自动生代理
public class ProxyInvocationHandler implements InvocationHandler {

	// 被代理的接口
	public Object target;

	public void setTarget(Object target) {
		this.target = target;
	}

	// 得到生成的代理类 -> 固定的代码
	public Object getProxy() {
		return Proxy.newProxyInstance(this.getClass().getClassLoader(), target.getClass().getInterfaces(), this);
	}

	// 处理代理实例并返回结果
	public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
		// 动态代理的本质，就是使用反射机制实现的
		// invoke()执行它真正要执行的方法
		Object result = method.invoke(target, args);
		return result;
	}

}
```



# 11、AOP

## 11.1、什么是AOP

AOP( Aspect Oriented Programming)意为:**面向切面编程**,通过预编译方式和运行期动态代理实现程序功能的统一维护的一种技术。AOP是OOP的延续,是软件开发中的一个热点,也是 Spring框架中的一个重要内容,是函数式编程的一种衍生范型。利用AOP可以对业务逻辑的各个部分进行隔离,从而使得业务逻辑各部分之间的耦合度降低,提高程序的可重用性,同时提高了开发的效率
![](https://img-blog.csdnimg.cn/9ed0429f489149008714a7ea9b0ee8e4.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6ZuE5a6J54y_,size_20,color_FFFFFF,t_70,g_se,x_16)

## 11.2、AOP在Spring中得作用

提供声明式事务；允许用户自定义切面

* 横切关注点:跨越应用程序多个模块的方法或功能。即是,与我们业务逻辑无关的,但是我们需要关注的部分,就是横切关注点。如日志,安全,缓存,事务等等
* 切面( ASPECT):横切关注点被模块化的特殊对象。即,它是一个类。**(切面，即要用于增强的类 Log类)**
* 通知( Advice):切面必须要完成的工作。即,它是类中的一个方法。  **（通知，即对切入点进行的一些增强的方法 Log类中的方法）**
* 目标( Target):被通知对象
* 代理( Proxy):向目标对象应用通知之后创建的对象。
* 切入点( PointCut):切面通知执行的"地点的定义   **（切入点，即需要增强的方法）**
* 连接点 (JointPoint):与切入点匹配的执行点

![](https://img-blog.csdnimg.cn/60705816be904bf7a1887584aebcbb5e.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6ZuE5a6J54y_,size_20,color_FFFFFF,t_70,g_se,x_16)
![](https://img-blog.csdnimg.cn/b856a838e2af42dcaae0721f360f930a.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6ZuE5a6J54y_,size_20,color_FFFFFF,t_70,g_se,x_16)

**即AOP在不改变原有代码的情况下，去增加新的功能。**（代理）

## 11.3、使用Spring实现AOP

导入jar包

```xml
<!-- https://mvnrepository.com/artifact/org.aspectj/aspectjweaver -->
<dependency>
    <groupId>org.aspectj</groupId>
    <artifactId>aspectjweaver</artifactId>
    <version>1.9.4</version>
</dependency>
```

### 11.3.1、方法一：spring接口（主要是spring api 接口实现）

springAPI接口实现

applicationContext.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
		https://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/aop
		https://www.springframework.org/schema/aop/spring-aop.xsd">

    <!--注册bean-->
    <bean id="userservice" class="service.UserServiceImpl"/>
    <bean id="log" class="log.Log"/>
    <bean id="afterLog" class="log.AfterLog"/>
	<!--方式一，使用原生Spring API接口-->
    <!--配置aop,还需要导入aop约束-->
    <aop:config>
        <!--切入点：expression:表达式，execution（要执行的位置）-->
        <aop:pointcut id="pointcut" expression="execution(* service.UserServiceImpl.*(..))"/>
        <!--UserServiceImpl.*(..) -》 UserServiceImpl类下的所以方法(参数)-->
        <!--执行环绕增加-->
        <aop:advisor advice-ref="log" pointcut-ref="pointcut"/>
        <aop:advisor advice-ref="afterLog" pointcut-ref="pointcut"/>
        <!-- 环绕,在id="pointcut"的前后切入 -->
    </aop:config>

</beans>
```

execution(返回类型，类名，方法名(参数)) -> execution(* com.service.*,*(…))

UserService.java

```java
package service;
public interface UserService {   
	    public void add() ;
	    public void delete() ;
	    public void query() ;
	    public void update();
}
```

UserService 的实现类 UserServiceImp.java

```java
package service;

public class UserServiceImpl implements UserService {

    public void add() {
        System.out.println("add增");
    }
    public void delete() {
        System.out.println("delete删");
    }
    public void update() {
        System.out.println("update改");
    }
    public void query() {
        System.out.println("query查");
    }
}
```

前置Log.java

```java
package log;
import org.springframework.aop.MethodBeforeAdvice;
import java.lang.reflect.Method;

public class Log implements MethodBeforeAdvice {
    //method：要执行的目标对象的方法
    //args：参数
    //target：目标对象
    public void before(Method method, Object[] args, Object target) throws Throwable {
        System.out.println(target.getClass().getName()+"的"+method.getName()+"被执行了");
    }
}
```

后置AfterLog.java

```java
package log;
import java.lang.reflect.Method;
import org.springframework.aop.AfterReturningAdvice;

public class AfterLog implements AfterReturningAdvice {
    //returnVaule: 返回值
    public void afterReturning(Object returnValue, Method method, Object[] args, Object target) throws Throwable {
    	System.out.println("执行了"+method.getName()+"方法，返回值是"+returnValue);
    }
}
```

测试类MyTest

```java
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;
import service.UserService;

public class MyTest {
    public static void main(String[] args) {
        ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
        //注意:动态代理代理的是接口
//        UserService userService = (UserService) context.getBean("userservice");
        UserService userService = context.getBean("userservice", UserService.class);
        userService.add();
    }
}
```

### 11.3.2、方法二：自定义类实现AOP（主要是切面定义）

```java
package diy;

/**
 * @author miemiehoho
 * @date 2022/2/25 15:37
 */
public class DiyPointCut {

    public void before(){
        System.out.println("方法执行前");
    }

    public void after(){
        System.out.println("方法执行后");
    }
}

```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
		https://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/aop
		https://www.springframework.org/schema/aop/spring-aop.xsd">

    <!--注册bean-->
    <bean id="userservice" class="service.UserServiceImpl"/>
    <bean id="log" class="log.Log"/>
    <bean id="afterLog" class="log.AfterLog"/>
    <!--    &lt;!&ndash;方式一，使用原生Spring API接口&ndash;&gt;-->
    <!--    &lt;!&ndash;配置aop,还需要导入aop约束&ndash;&gt;-->
    <!--    <aop:config>-->
    <!--        &lt;!&ndash;切入点：expression:表达式，execution（要执行的位置）&ndash;&gt;-->
    <!--        <aop:pointcut id="pointcut" expression="execution(* service.UserServiceImpl.*(..))"/>-->
    <!--        &lt;!&ndash;UserServiceImpl.*(..) -》 UserServiceImpl类下的所以方法(参数)&ndash;&gt;-->
    <!--        &lt;!&ndash;执行环绕增加&ndash;&gt;-->
    <!--        <aop:advisor advice-ref="log" pointcut-ref="pointcut"/>-->
    <!--        <aop:advisor advice-ref="afterLog" pointcut-ref="pointcut"/>-->
    <!--        &lt;!&ndash; 环绕,在id="pointcut"的前后切入 &ndash;&gt;-->
    <!--    </aop:config>-->

    <!--方式二：自定义类-->
    <bean id="diy" class="diy.DiyPointCut"></bean>
    <aop:config>
        <!--        自定义切面， ref:要引用的类   -->
        <aop:aspect ref="diy">
            <!--            切入点-->
            <aop:pointcut id="point" expression="execution(* service.UserServiceImpl.*(..))"/>
            <!--            通知-->
            <aop:before method="before" pointcut-ref="point"></aop:before>
            <aop:after method="after" pointcut-ref="point"></aop:after>

        </aop:aspect>
    </aop:config>

</beans>
```

测试结果：
![](https://img-blog.csdnimg.cn/e78eeb6cc80c4bfc8cad3f8b85be29e9.png)



### 11.3.3、方法三：使用注解实现 

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
		https://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/aop
		https://www.springframework.org/schema/aop/spring-aop.xsd">

    <!--注册bean-->
    <bean id="userservice" class="service.UserServiceImpl"/>
    <bean id="log" class="log.Log"/>
    <bean id="afterLog" class="log.AfterLog"/>
    <!--    &lt;!&ndash;方式一，使用原生Spring API接口&ndash;&gt;-->
    <!--    &lt;!&ndash;配置aop,还需要导入aop约束&ndash;&gt;-->
    <!--    <aop:config>-->
    <!--        &lt;!&ndash;切入点：expression:表达式，execution（要执行的位置）&ndash;&gt;-->
    <!--        <aop:pointcut id="pointcut" expression="execution(* service.UserServiceImpl.*(..))"/>-->
    <!--        &lt;!&ndash;UserServiceImpl.*(..) -》 UserServiceImpl类下的所以方法(参数)&ndash;&gt;-->
    <!--        &lt;!&ndash;执行环绕增加&ndash;&gt;-->
    <!--        <aop:advisor advice-ref="log" pointcut-ref="pointcut"/>-->
    <!--        <aop:advisor advice-ref="afterLog" pointcut-ref="pointcut"/>-->
    <!--        &lt;!&ndash; 环绕,在id="pointcut"的前后切入 &ndash;&gt;-->
    <!--    </aop:config>-->

    <!--    &lt;!&ndash;方式二：自定义类&ndash;&gt;-->
    <!--    <bean id="diy" class="diy.DiyPointCut"></bean>-->
    <!--    <aop:config>-->
    <!--        &lt;!&ndash;        自定义切面， ref:要引用的类   &ndash;&gt;-->
    <!--        <aop:aspect ref="diy">-->
    <!--            &lt;!&ndash;            切入点&ndash;&gt;-->
    <!--            <aop:pointcut id="point" expression="execution(* service.UserServiceImpl.*(..))"/>-->
    <!--            &lt;!&ndash;            通知&ndash;&gt;-->
    <!--            <aop:before method="before" pointcut-ref="point"></aop:before>-->
    <!--            <aop:after method="after" pointcut-ref="point"></aop:after>-->

    <!--        </aop:aspect>-->
    <!--    </aop:config>-->

    <!--    方式三-->
    <bean id="annotationPointCut" class="diy.AnnotationPointCut"/>
    <!--    开启注解支持 JDK(默认  proxy-target-class="false") cglib（proxy-target-class="true"） -->
    <aop:aspectj-autoproxy />


</beans>
```

```java
package diy;

import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.Signature;
import org.aspectj.lang.annotation.After;
import org.aspectj.lang.annotation.Around;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;

/**
 * @author miemiehoho
 * @date 2022/2/25 15:50
 */
// 方式三：使用注解方式实现AOP
@Aspect // 标注这个类是一个切面
public class AnnotationPointCut {

    @Before("execution(* service.UserServiceImpl.*(..))")
    public void before() {
        System.out.println("方法执行前");
    }

    @After("execution(* service.UserServiceImpl.*(..))")
    public void after() {
        System.out.println("方法执行后");
    }

    // 在环绕增强中，可以给定一个参数，代表要处理切入的点
    @Around("execution(* service.UserServiceImpl.*(..))")
    // ProceedingJoinPoint:连接点
    // 在无法确保线程安全的前提下不要用环绕增强
    public void around(ProceedingJoinPoint pjp) throws Throwable {
        System.out.println("环绕前 " + pjp);

        // 获得签名
        Signature signature = pjp.getSignature();
        System.out.println("signature： " + signature);


        // 执行方法
        Object proceed = pjp.proceed();
        System.out.println("环绕后 " + proceed);
    }
}

```

测试结果：
![image-20220225160813775](C:\Users\13686\AppData\Roaming\Typora\typora-user-images\image-20220225160813775.png)



### 总结

横向编程的思想，AOP就是在不影响原来业务类的情况下实现动态的增强

# 12. 整合Mybatis

步骤：

1. 导入相关jar包
  - junit 
  - mybatis
  - mysql
  - spring
  - aop织入
  - mybatis-spring [new ]
2. 编写配置文件
3. 测试

## 12.1、回忆mybatis

1. 编写实体类  （User.java）
2. 编写核心配置文件(mybatis-config.xml)
3. 编写接口  （UserMapper.java）
4. 编写Mapper.xml
5. 测试

## 12.2、Mybatis-spring

#### 什么是 MyBatis-Spring？

MyBatis-Spring 会帮助你将 MyBatis 代码无缝地整合到 Spring 中。它将允许 MyBatis 参与到 Spring 的事务管理之中，创建映射器 mapper 和 SqlSession 并注入到 bean 中，以及将 Mybatis 的异常转换为 Spring 的 DataAccessException。 最终，可以做到应用代码不依赖于 MyBatis，Spring 或 MyBatis-Spring。

```java
package com.du.mapper;

import com.du.pojo.User;

import java.util.List;

public interface UserMapper {
    public List<User> selectUser();
}
```

方式一

```java
package com.du.mapper;

import com.du.pojo.User;
import org.mybatis.spring.SqlSessionTemplate;

import java.util.List;

public class UserMapperImpl implements UserMapper{

    private SqlSessionTemplate sqlSessionTemplate;

    public void setSqlSessionTemplate(SqlSessionTemplate sqlSessionTemplate) {
        this.sqlSessionTemplate = sqlSessionTemplate;
    }

    public List<User> selectUser() {
        UserMapper mapper = sqlSessionTemplate.getMapper(UserMapper.class);
        return mapper.selectUser();
    }
}
```

方式二

```java
package com.du.mapper;

import com.du.pojo.User;
import org.mybatis.spring.support.SqlSessionDaoSupport;

import java.util.List;

public class UserMapperImpl2 extends SqlSessionDaoSupport implements UserMapper {

    public List<User> selectUser() {
        return getSqlSession().getMapper(UserMapper.class).selectUser();
    }
}
```

```xml

DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.du.mapper.UserMapper">
    <select id="selectUser" resultType="user">
        select * from user;
    select>
mapper>
```

```java
package com.du.pojo;

import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

@Data
@AllArgsConstructor
@NoArgsConstructor
public class User {
    private int id;
    private String name;
    private String password;
}
```

```xml

<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/aop
        https://www.springframework.org/schema/aop/spring-aop.xsd"
>
    <import resource="spring-dao.xml"/>

    <bean id="userMapper" class="com.du.mapper.UserMapperImpl">
        <property name="sqlSessionTemplate" ref="sqlSession"/>
    bean>

    <bean id="userMapper2" class="com.du.mapper.UserMapperImpl2">
        <property name="sqlSessionFactory" ref="sqlSessionFactory"/>
    bean>
beans>

```

```xml

DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">

<configuration>
    <typeAliases>
        <package name="com.du.pojo"/>
    typeAliases>

configuration>
```

```xml

<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/aop
        https://www.springframework.org/schema/aop/spring-aop.xsd"
>

    <bean id="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
        <property name="driverClassName" value="com.mysql.cj.jdbc.Driver"/>
        <property name="url" value="jdbc:mysql://localhost:3306/mybatis?useUnicode=true&characterEncoding=UTF-8"/>
        <property name="username" value="root"/>
        <property name="password" value="root"/>
    bean>

    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="dataSource" ref="dataSource" />

        <property name="configLocation" value="classpath:mybatis-config.xml"/>
        <property name="mapperLocations" value="classpath:com/du/mapper/*.xml"/>

    bean>
    <bean id="sqlSession" class="org.mybatis.spring.SqlSessionTemplate">

        <constructor-arg index="0" ref="sqlSessionFactory"/>
    bean>
    <bean id="userMapper" class="com.du.mapper.UserMapperImpl">
        <property name="sqlSessionTemplate" ref="sqlSession"/>
    bean>
beans>
```

```java
import com.du.mapper.UserMapper;
import com.du.pojo.User;
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import org.junit.Test;
import org.springframework.context.support.ClassPathXmlApplicationContext;

import java.io.IOException;
import java.io.InputStream;
import java.util.List;

public class MyTest {
    @Test
    public void test() throws IOException {

        ClassPathXmlApplicationContext classPathXmlApplicationContext = new ClassPathXmlApplicationContext("applicationContext.xml");

        UserMapper userMapper = classPathXmlApplicationContext.getBean("userMapper2", UserMapper.class);

        for (User user : userMapper.selectUser()) {
            System.out.println(user);
        }
    }
}
```

1. 编写数据源配置
2. sqlSessionFactory
3. sqlSessionTemplate
4. 需要给接口加实现类【】
5. 将自己写的实现类，注入到Spring中
6. 测试

# 13、声明式事务

## 13.1、回顾事务

  - 把一组业务当成一个业务来做；**要么成功 要么都失败**
  - 事务在项目开发中 十分的重要，涉及到数据的一致性，不能马虎
  - 确保完整性和一致性

ACID参考文章：https://www.cnblogs.com/malaikuangren/archive/2012/04/06/2434760.html

事务ACID原则：

* 原子性
  * 

* 一致性
  * 

* 隔离性
  - 多个业务可能操作同一个资源，防止暑假损坏
* 持久性
  - 事务一旦提交，无论系统发生什么问题，结果都不会被影响，被持久化的写到存储器中！

## 13.2、spring 中的事务管理

  - 声明式事务： AOP（常用，要考）
  - 编程式事务 ： 需要在代码中，进行事务的管理



## 13.3、事务的传播特性



## 13.4、为什么需要事务？

* 如果不配置事务，可能存在数据提交不一致的情况下
* 如果我们不在Spring中配置声明式事务，我们就需要在代码中手动配置事务
* 事务在项目的开发中十分重要，涉及到数据的一致性和完整性问题，不容马虎

**注意：**除了XML配置事务，还有基于注解的事务配置
