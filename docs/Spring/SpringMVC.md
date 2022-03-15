---
link: https://blog.csdn.net/xiaoding520/article/details/115461495
title: 狂神说Java SpringMVC笔记
description: 回顾MVC​	回顾MVC什么是MVC？MVC是模型(Model)、视图(View)、控制器(Controller)的简写，是一种软件设计规范是将业务逻辑、数据、显示分离的方法来组织代码MVC主要作用是降低了视图与业务逻辑间的双向耦合MVC不是一种设计模式，MVC是一种架构模式。当然不同的MVC存在差异Model（模型）：数据模型，提供要展示的数据，因此包含数据和行为，可以认为是领域模型或JavaBean组件（包含数据和行为）不过现在一般都是分离开来：Value Object（数据Da
keywords: 狂神说springmvc笔记
author: 误念 Csdn认证博客专家 Csdn认证企业博客 码龄3年 暂无认证
date: 2021-04-06T07:29:25.000Z
publisher: null
stats: paragraph=978 sentences=751, words=4647

---

# 1、什么是SpringMVC

## 1、回顾MVC

### 1.1、回顾MVC

什么是MVC？

* MVC是模型(Model)、视图(View)、控制器(Controller)的简写，是一种软件设计规范
* 是将业务逻辑、数据、显示分离的方法来组织代码
* MVC主要作用是降低了视图与业务逻辑间的双向耦合
* MVC不是一种设计模式，MVC是一种架构模式。当然不同的MVC存在差异

**Model（模型）：**数据模型，提供要展示的数据，因此包含数据和行为，可以认为是领域模型
或JavaBean组件（包含数据和行为）不过现在一般都是分离开来：Value Object（数据Dao）
和服务层（行为Service）。也就是模型提供了模型数据查询和模型数据的状态更新等功能，包括
数据和业务。

**View（视图）：**负责进行模型的展示，一般就是我们见到的用户界面，客户想看到的东西。

**Controller（控制器）：**接收用户请求，委托给模型进行处理（状态改变），处理完毕后把
返回的模型数据返回给视图，由视图负责展示。也就是说控制器做了个调度员的工作。

**最经典的MVC就是：JSP+Servlet+bean的模式**

![](https://img-blog.csdnimg.cn/20210406151101129.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3hpYW9kaW5nNTIw,size_16,color_FFFFFF,t_70#pic_center)

### 1.2、Model1时代

* 在web早期的开发中，通常采用的都是Model
* Model1中，主要分为两层，视图和模型层

![](https://img-blog.csdnimg.cn/20210406152749829.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3hpYW9kaW5nNTIw,size_16,color_FFFFFF,t_70#pic_center)

Model1优点：架构简单，比较适合小型项目开发

Model1缺点：JSP职责不单一，职责过重，不便于维护

### 1.3、Molde2时代

Model2把一个项目分成三部分：包括视图、控制、模型。

![](https://img-blog.csdnimg.cn/20210406152812253.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3hpYW9kaW5nNTIw,size_16,color_FFFFFF,t_70#pic_center)

1、用户发请求

2、Servlet接收请求的数据，并调用对应的业务逻辑方法

3、业务处理完毕，返回更新后的数据给Servlet

4、Servlet转向到JSP，由JSP来渲染页面

5、响应给前端更新后的页面

职责分析：

Controller：控制器

 1、取得表单数据

 2、调用业务逻辑

 3、转向指定的页面

Model：模型

 1、业务逻辑

 2、保存数据的状态

View模型

 1、显示页面

Model2这样不仅提高了代码的复用率与项目的扩展性，且大大降低了项目的维护成本。
Model1模式的实现比较简单，适用于快速开发小规模项目，Model1中jsp页面身兼View
和Controller两种角色，将控制逻辑和表现逻辑混杂在一起，从而导致代码的重用性非常低，
增加了应用的扩展性和维护的难度。Model2消除了Model1的缺点

回顾Servlet

新建一个Maven工程当做父工程！pom依赖！

```xml
<dependencies>
   <dependency>
       <groupId>junitgroupId>
       <artifactId>junitartifactId>
       <version>4.13version>
   dependency>
   <dependency>
       <groupId>org.springframeworkgroupId>
       <artifactId>spring-webmvcartifactId>
       <version>5.3.5version>
   dependency>
   <dependency>
       <groupId>javax.servletgroupId>
       <artifactId>servlet-apiartifactId>
       <version>2.5version>
   dependency>
   <dependency>
       <groupId>javax.servlet.jspgroupId>
       <artifactId>jsp-apiartifactId>
       <version>2.2version>
   dependency>
   <dependency>
       <groupId>javax.servlet.jsp.jstlgroupId>
       <artifactId>jstl-apiartifactId>
       <version>1.2version>
   dependency>
dependencies>
```

2、简历一个Moudel:SpringMVC-01-Servlet，添加web app的支持

3、导入jsp和servlet的jar包依赖，如果父级pox.xml文件已经导入，那么子级是可以不导入的

4、编写一个Servlet类，用来处理用户的请求

```java
public class HelloServlet extends HttpServlet{
    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {

        String str = req.getParameter("list");

        if (str.equals("add")) {
            req.getSession().setAttribute("msg","添加用户");
        }else if(str.equals("delete")) {
            req.getSession().setAttribute("msg","删除用户");
        }

        req.getRequestDispatcher("/WEB-INF/jsp/helloServlet.jsp").forward(req,resp);
    }

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        doPost(req, resp);
    }
}
```

5、编写helloServlet.jsp，在WEB-INF目录下新建一个jsp的文件夹，新建hello.jsp

```jsp
    跳转页面

${sessionScope.msg}

```

6、在web.xml中注册Servlet

```xml
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">

    <servlet>
        <servlet-name>helloServletservlet-name>
        <servlet-class>com.njxh.servlet.HelloServletservlet-class>
    servlet>

    <servlet-mapping>
        <servlet-name>helloServletservlet-name>
        <url-pattern>/userurl-pattern>
    servlet-mapping>
web-app>
```

7、创建form表单数据提交页面

```jsp
    首页

```

8、配置Tomcat启动测试

 也可以直接在浏览器输入servlet地址传参

*  http://localhost:8080/user?list=add
* http://localhost:8080/user?list=delete

## 2、什么是SpringMVC

### 2.1、概述

![](https://img-blog.csdnimg.cn/20210406142018202.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3hpYW9kaW5nNTIw,size_16,color_FFFFFF,t_70#pic_center)

Spring MVC是Spring Framework的一部分，是基于JAVA实现实现的MVC的轻量级Web框架

查观官方文档：https://docs.spring.io/spring/docs/5.2.0.RELEASE/spring-framework-reference/web.html#spring-web

我们为什么学习Spring MVC呢？

Spring MVC的特点

 1、轻量级、简单易学

 2、高效、基于请响应的MVC框架

 3、与Spring兼容性好，无缝融合

 4、约定大于配置

 5、功能强大：RESTful、数据验证、格式化、本地化、主题等

 6、简洁灵活

Spring的web框架围绕 **DispatcherServlet** [ 调度Servlet ] 设计。

DispatcherServlet的作用是将请求分发到不同的处理器。从Spring 2.5开始，使用Java 5或者以上版本的用户可以采用基于注解形式进行开发，十分简洁；

正因为SpringMVC好 , 简单 , 便捷 , 易学 , 天生和Spring无缝集成(使用SpringIoC和Aop) , 使用约定优于配置 . 能够进行简单的junit测试 . 支持Restful风格 .异常处理 , 本地化 , 国际化 , 数据验证 , 类型转换 , 拦截器 等等...所以我们要学习

最重要的一点还是用的人多 , 使用的公司多

### 2.2、中心控制器

Spring的web框架围绕DispatcherServlet设计。DispatcherServlet的作用是将请求分发到不同的处理器。从Spring 2.5开始，使用Java 5或者以上版本的用户可以采用基于注解的controller声明方式。

 Spring MVC框架像许多其他MVC框架一样, **以请求为驱动** , **围绕一个中心Servlet分派请求及提供其他功能**， **DispatcherServlet是一个实际的Servlet (它继承自HttpServlet 基类)**。

![](https://img-blog.csdnimg.cn/20210406142048770.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3hpYW9kaW5nNTIw,size_16,color_FFFFFF,t_70#pic_center)

SpringMVC的原理如下图所示：

 当发起请求时被前置的控制器拦截到请求，根据请求参数生成代理请求，找到请求对应的实际控制器，控制器处理请求，创建数据模型，访问数据库，将模型响应给中心控制器，控制器使用模型与视图渲染视图结果，将结果返回给中心控制器，再将结果返回给请求者。
![](https://img-blog.csdnimg.cn/20210406142110756.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3hpYW9kaW5nNTIw,size_16,color_FFFFFF,t_70#pic_center)

### 2.3、SpringMVC的执行原理（重点）

![](https://img-blog.csdnimg.cn/20210406142137126.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3hpYW9kaW5nNTIw,size_16,color_FFFFFF,t_70#pic_center)

图为SpringMVC的一个较完整的流程图，实线表示SpringMVC框架提供的技术，不需要开发者实现，虚线表示需要开发者实现。

简要分析执行流程

1. DispatcherServlet表示前置控制器，是整个SpringMVC的控制中心。用户发出请求，DispatcherServlet接收请求并拦截请求。 
   - 我们假设请求的url为 : http://localhost:8080/SpringMVC/hello 
   - 如上url拆分成三部分： 
     - http://localhost:8080服务器域名 
     - SpringMVC部署在服务器上的web站点 
     - hello表示控制器 
     - 通过分析，如上url表示为：请求位于服务器localhost:8080上的SpringMVC站点的hello控制器。
2. HandlerMapping为处理器映射。DispatcherServlet调用HandlerMapping,HandlerMapping根据请求url查找Handler。
3. HandlerExecution表示具体的Handler,其主要作用是根据url查找控制器，如上url被查找控制器为：hello。
4. HandlerExecution将解析后的信息传递给DispatcherServlet,如解析控制器映射等。
5. HandlerAdapter表示处理器适配器，其按照特定的规则去执行Handler。
6. Handler让具体的Controller执行。
7. Controller将具体的执行信息返回给HandlerAdapter,如ModelAndView。
8. HandlerAdapter将视图逻辑名或模型传递给DispatcherServlet。
9. DispatcherServlet调用视图解析器(ViewResolver)来解析HandlerAdapter传递的逻辑视图名。
10. 视图解析器将解析的逻辑视图名传给DispatcherServlet。
11. DispatcherServlet根据视图解析器解析的视图结果，调用具体的视图。
12. 最终视图呈现给用户。

在这里先听一遍原理，不理解没有关系，我们马上来写一个对应的代码实现大家就明白了，如果不明白，那就写10遍，没有笨人，只有懒人！

# 2、第一个Spring MVC程序

## 配置版

1. 新建一个Moudle ， springmvc-02-hello ， 添加web的支持！
2. 确定导入了SpringMVC 的依赖！
3. 配置web.xml ， 注册DispatcherServlet

```xml
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">

    <servlet>
        <servlet-name>springmvcservlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServletservlet-class>

        <init-param>
            <param-name>contextConfigLocationparam-name>
            <param-value>classpath:springmvc-servlet.xmlparam-value>
        init-param>

        <load-on-startup>1load-on-startup>
    servlet>

    <servlet-mapping>
        <servlet-name>springmvcservlet-name>
        <url-pattern>/url-pattern>
    servlet-mapping>
web-app>
```

4、编写SpringMVC 的 配置文件！名称：springmvc-servlet.xml : [servletname]-servlet.xml

说明，这里的名称要求是按照官方来的

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd">

beans>
```

5、添加 处理映射器

```xml
<bean class="org.springframework.web.servlet.handler.BeanNameUrlHandlerMapping"/>
```

6、添加 处理器适配器

```xml
<bean class="org.springframework.web.servlet.mvc.SimpleControllerHandlerAdapter"/>
```

7、添加 视图解析器

```xml
<bean class="org.springframework.web.servlet.view.InternalResourceViewResolver" id="InternalResourceViewResolver">

   <property name="prefix" value="/WEB-INF/jsp/"/>

   <property name="suffix" value=".jsp"/>
bean>
```

8、编写我们要操作业务Controller ，要么实现Controller接口，要么增加注解；需要返回一个ModelAndView，装数据，封视图；

```java
package com.njxh.controller;

import org.springframework.web.servlet.ModelAndView;
import org.springframework.web.servlet.mvc.Controller;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

public class HelloController implements Controller {

    @Override
    public ModelAndView handleRequest(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse) throws Exception {

        ModelAndView mv = new ModelAndView();

        mv.addObject("msg","HelloSpringMVC");

        mv.setViewName("hello");
        return mv;
    }
}

```

9、将自己的类交给SpringIOC容器，注册bean

```xml
<bean id="/hello" class="com.kuang.controller.HelloController"/>
```

10、写要跳转的jsp页面，显示ModelandView存放的数据，以及我们的正常页面；

```jsp
    跳转页面

${msg}

```

11、配置Tomcat 启动测试！

![](https://img-blog.csdnimg.cn/20210406142207192.png#pic_center)

可能遇到的问题：访问出现404，排查步骤：

1. 查看控制台输出，看一下是不是缺少了什么jar包。
2. 如果jar包存在，显示无法输出，就在IDEA的项目发布中，添加lib依赖！
3. 重启Tomcat 即可解决！

小结：看这个估计大部分同学都能理解其中的原理了，但是我们实际开发才不会这么写，不然就疯了，还学这个玩意干嘛！我们来看个注解版实现，这才是SpringMVC的精髓，到底有多么简单，看这个图就知道了。

## 注解版

1、新建一个Moudle、springmvc-servlet-03-hello-annotation，添加web支持

2、由于Maven可能存在资源过滤的问题，我们将配置完善

```xml
<build>
  <resources>
      <resource>
          <directory>src/main/javadirectory>
          <includes>
              <include>**/*.propertiesinclude>
              <include>**/*.xmlinclude>
          includes>
          <filtering>falsefiltering>
      resource>
      <resource>
          <directory>src/main/resourcesdirectory>
          <includes>
              <include>**/*.propertiesinclude>
              <include>**/*.xmlinclude>
          includes>
          <filtering>falsefiltering>
      resource>
  resources>
build>
```

3、在pom.xml文件引入相关的依赖：主要有Spring框架核心库、Spring MVC、servlet , JSTL等。我们在父依赖中已经引入了！

4、配置web.xml

注意点：

```xml
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">

    <servlet>
        <servlet-name>SpringMVCservlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServletservlet-class>

        <init-param>
            <param-name>contextConfigLocationparam-name>
            <param-value>classpath:springmvc-servlet.xmlparam-value>
        init-param>

        <load-on-startup>1load-on-startup>
    servlet>

    <servlet-mapping>
        <servlet-name>SpringMVCservlet-name>
        <url-pattern>/url-pattern>
    servlet-mapping>
web-app>
```

**/ 和 /* 的区别：**< url-pattern > / 不会匹配到.jsp， 只针对我们编写的请求；即：.jsp 不会进入spring的 DispatcherServlet类 。< url-pattern > /* 会匹配 *.jsp，会出现返回 jsp视图 时再次进入spring的DispatcherServlet 类，导致找不到对应的controller所以报404错。

* 注意web.xml版本问题，要最新版！
* 注册DispatcherServlet
* 关联SpringMVC的配置文件
* 启动级别为1
* 映射路径为 / 【不要用/*，会404】

5、添加Spring MVC配置文件

在resource目录下添加springmvc-servlet.xml配置文件，配置的形式与Spring容器配置基本类似，为了支持基于注解的IOC，设置了自动扫描包的功能，具体配置信息如下：

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       https://www.springframework.org/schema/context/spring-context.xsd
       http://www.springframework.org/schema/mvc
       https://www.springframework.org/schema/mvc/spring-mvc.xsd">

    <context:component-scan base-package="com.njxh.controller"/>

    <mvc:default-servlet-handler/>

    <mvc:annotation-driven/>

    <bean id="InternalResourceViewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">

        <property name="prefix" value="/WEB-INF/jsp/"/>

        <property name="suffix" value=".jsp"/>
    bean>
beans>
```

在视图解析器中我们把所有的视图都存放在/WEB-INF/目录下，这样可以保证视图安全，因为这个目录下的文件，客户端不能直接访问。

* 让IOC的注解生效
* 静态资源过滤 ：HTML . JS . CSS . 图片 ， 视频 ...

* MVC的注解驱动
* 配置视图解析器

6、创建Controller

编写一个Java控制类：com.kuang.controller.HelloController , 注意编码规范

```java
package com.njxh.controller;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.RequestMapping;

@Controller
@RequestMapping("/HelloController")
public class HelloController {

    @RequestMapping("/h1")
    public String hello1(Model mv) {

        mv.addAttribute("msg","HelloSpringMVCAnnotation");

        return "hello";
    }
}

```

* @Controller是为了让Spring IOC容器初始化时自动扫描到；
* @RequestMapping是为了映射请求路径，这里因为类与方法上都有映射所以访问时应该
  是/HelloController/h1；
* 方法中声明Model类型的参数是为了把Action中的数据带到视图中；
* 方法返回的结果是视图的名称hello，加上配置文件中的前后缀变成WEB-INF/jsp/ **hello**.jsp。

7、创建视图层

1. 在WEB-INF/ jsp目录中创建hello.jsp ， 视图可以直接取出并展示从Controller带回的信息；
2. 可以通过EL表示取出Model中存放的值，或者对象；

```xml
<html>
<head>
    <title>跳转页面title>
head>
<body>
${msg}
body>
html>
```

8、配置Tomcat运行

配置Tomcat，开启服务器，访问对应的请求路径！

![](https://img-blog.csdnimg.cn/20210406142251774.png#pic_center)

OK，运行成功

## 小结

实现步骤其实非常的简单：

1. 新建一个web项目
2. 导入相关jar包
3. 编写web.xml , 注册DispatcherServlet
4. 编写springmvc配置文件
5. 接下来就是去创建对应的控制类 , controller
6. 最后完善前端视图和controller之间的对应
7. 测试运行调试

使用springMVC必须配置的三大件：

处理器映射器、处理器适配器、视图解析器

通常，我们只需要手动配置视图解析器，而处理器映射器和处理器适配器只需开启注解驱动即可，而省去了大段的xml配置

再来回顾下原理吧~

![](https://img-blog.csdnimg.cn/20210406142334292.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3hpYW9kaW5nNTIw,size_16,color_FFFFFF,t_70#pic_center)

# 3、RestFul和控制器

## 控制器

> 控制器Controller

* 控制器复杂提供访问应用程序的行为，通常通过接口定义或注解定义两种方法实现。
* 控制器负责解析用户的请求并将其转换为一个模型。
* 在Spring MVC中一个控制器类可以包含多个方法
* 在Spring MVC中，对于Controller的配置方式有很多种

> 实现Controller接口

Controller是一个接口，在org.springframework.web.servlet.mvc包下，接口中只有一个方法；

```xml
//实现该接口的类获得控制器功能
public interface Controller {
   //处理请求且返回一个模型与视图对象
   ModelAndView handleRequest(HttpServletRequest var1, HttpServletResponse var2) throws Exception;
}
```

测试

1. 新建一个Moudle，SpringMVC-04-Controller 。将刚才的03 拷贝一份, 我们进行操作！
   2.

  - 删掉HelloController
  - mvc的配置文件只留下 视图解析器！

3. 编写一个Controller类，HelloController

```java
public class HelloController implements Controller {
    @Override
    public ModelAndView handleRequest(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse) throws Exception {

        ModelAndView mv = new ModelAndView();

        mv.addObject("msg","HelloSpringMVC");

        mv.setViewName("hello");
        return mv;
    }
}
```

3、编写完毕后，去Spring配置文件中注册请求的bean；name对应请求路径，class对应处理请求的类

```xml
<bean id="/hello" class="com.njxh.controller.HelloController"/>
```

4、编写前端hello.jsp，注意在WEB-INF/jsp目录下编写，对应我们的视图解析器

```jsp
    跳转页面

${msg}

```

5、配置Tomcat运行测试，我这里没有项目发布名配置的就是一个 / ，所以请求不用加项目名，OK！

![](https://img-blog.csdnimg.cn/2021040614240225.png#pic_center)

说明：

* 实现接口Controller定义控制器是较老的办法
* 缺点是：一个控制器中只有一个方法，如果要多个方法则需要定义多个Controller；定义的方式比较麻烦；

> 使用注解@Controller

* @Controller注解类型用于声明Spring类的实例是一个控制器（在讲IOC时还提到了另外3个注解）；
* Spring可以使用扫描机制来找到应用程序中所有基于注解的控制器类，为了保证Spring能找到你的控制器，需要在配置文件中声明组件扫描。

```xml
<context:component-scan base-package="com.kuang.controller"/>
```

* 增加一个ControllerTest2类，使用注解实现；

```java
@Controller
@RequestMapping("/hello1")
public class HelloController2 {

    @RequestMapping("/h1")
    public String hello1(Model mv) {

        mv.addAttribute("msg","helloSrpingAnnotation");
        return "hello";
    }
}
```

* 运行Tomcat测试 ![](https://img-blog.csdnimg.cn/20210406142427102.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3hpYW9kaW5nNTIw,size_16,color_FFFFFF,t_70#pic_center)

可以发现，我们的两个请求都可以指向一个视图，但是页面结果的结果是不一样的，从这里可以看出视图是被复用的，而控制器与视图之间是弱偶合关系

注解方式是平时使用的最多的方式！

## RequestMapping

> @RequestMapping

* @RequestMapping注解用于映射url到控制器类或一个特定的处理程序方法。可用于类或方法上。
  用于类上，表示类中所有的响应请求的方法都是以该地址作为父路径。
* 为了测试结论更加准确，我们可以加上项目测试myweb
* 注解在方法上面

```xml
@Controller
public class HelloController2 {

    @RequestMapping("/h22")
    public String hello1(Model mv) {
        //添加数据
        mv.addAttribute("msg","helloRequestMapping");
        return "hello";
    }
}
```

访问路径：http://localhost:8080 / 项目名 / h1

* 注解在类上

```java
@Controller
@RequestMapping("/hello2")
public class HelloController2 {

    @RequestMapping("/h1")
    public String hello1(Model mv) {

        mv.addAttribute("msg","helloRequestMapping");
        return "hello";
    }
}
```

访问路径：http://localhost:8080 / 项目名/ admin /h1 , 需要先指定类的路径再指定方法的路径；

## RestFul风格

> 概念

Restful就是一个资源定位及资源操作的风格。不是标准也不是协议，只是一种风格。基于这个风格设计的软件可以更简洁，更有层次，更易于实现缓存等机制。

> 功能

资源：互联网所有的事物都可以被抽象为资源

资源操作：使用POST、DELETE、PUT、GET，使用不同方法对资源进行操作。

分别对应 添加、 删除、修改、查询。

> 传统方法操作资源

通过不同的参数来实现不同的效果！方法单一，post 和 get

http://127.0.0.1/item/queryItem.action?id=1 查询,GET

 http://127.0.0.1/item/saveItem.action 新增,POST

 http://127.0.0.1/item/updateItem.action 更新,POST

 http://127.0.0.1/item/deleteItem.action?id=1 删除,GET或POST

> 使用RestFul风格操作资源

可以通过不同的请求方式来实现不同的效果！如下：请求地址一样，但是功能可以不同！

 http://127.0.0.1/item/1 查询,GET

 http://127.0.0.1/item 新增,POST

 http://127.0.0.1/item 更新,PUT

 http://127.0.0.1/item/1 删除,DELETE

> 学习测试

1、新建一个ControllerTest

```java
@Controller
public class ControllerTest {

}
```

2、在SpringMVC中可以使用 @PathVariable注解，让方法参数的值对应绑定到一个url模板变量上

注意：@PathVariable注解只能在地址传入参数时使用。

```xml
//使用RestFul风格,默认是get
@RequestMapping("/h1/{a}/{b}")
public String test(@PathVariable int a,@PathVariable int b, Model mv) {
    //存储数据
    mv.addAttribute("msg","RestFul风格："+a+b);
    return "hello";
}
```

3、我们来测试请求查看下

![](https://img-blog.csdnimg.cn/20210406142503866.png#pic_center)

4、思考：使用路径变量的好处？

* 使路径变得更加简洁；
* 获得参数更加方便，框架会自动进行类型转换。
* 通过路径变量的类型可以约束访问参数，如果类型不一样，则访问不到对应的请求方法，如这里访问是的路径是/h1/1/a，则路径与方法不匹配，而不会是参数转换失败

![](https://img-blog.csdnimg.cn/20210406142524913.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3hpYW9kaW5nNTIw,size_16,color_FFFFFF,t_70#pic_center)

5、重新定义一个方法，修改下对应的参数类型，再次测试

```java
@RequestMapping(value = "h2/{a}/{b}")
public String index(@PathVariable int a,@PathVariable String b,Model mv) {

    mv.addAttribute("msg","RestFul风格："+a+b);
    return "hello";
}
```

![](https://img-blog.csdnimg.cn/20210406142544887.png#pic_center)

6、使用method属性指定请求类型

* 新增一个方法

```java
@RequestMapping(value = "h3/{name}/{pws}",method = RequestMethod.POST)
public String index2(@PathVariable String name,@PathVariable String pws,Model mv) {

    mv.addAttribute("msg","RestFul风格："+name+pws);
    return "hello";
}
```

* 定义表单form

```jsp
    $Title$

```

* 我们使用浏览器地址栏进行访问默认是Get请求，会报错405： ​ ![](https://img-blog.csdnimg.cn/20210406142612442.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3hpYW9kaW5nNTIw,size_16,color_FFFFFF,t_70#pic_center)
* 如果将POST修改为GET则正常了；

```java
@RequestMapping(value = "h3/{name}/{pws}",method = RequestMethod.GET)
public String index2(@PathVariable String name,@PathVariable String pws,Model mv) {

    mv.addAttribute("msg","RestFul风格："+name+pws);
    return "hello";
}
```

8、运行测试

![](https://img-blog.csdnimg.cn/20210406142646445.png#pic_center)

小结：

Spring MVC 的 @RequestMapping 注解能够处理 HTTP 请求的方法, 比如 GET, PUT, POST, DELETE 以及 PATCH

所有的地址栏请求默认都是HTTP GET类型的。

方法级别的注解变体有如下几个：组合注解

```xml
@GetMapping
@PostMapping
@PutMapping
@DeleteMapping
@PatchMapping
```

@GetMapping 是一个组合注解，平时使用的会比较多！

它所扮演的是 @RequestMapping(method =RequestMethod.GET) 的一个快捷方式。

```xml
@GetMapping("/h4/{a}/{b}")
public String test4(@PathVariable int a,@PathVariable int b,Model mv) {
     //存储数据
     mv.addAttribute("msg","RestFul风格："+a+b);
     return "hello";
}
```

扩展：小黄鸭扩展法

场景一： _我们都有过向别人（甚至可能向完全不会编程的人）提问及解释编程问题的经历，但是很多时候就在我们解释的过程中自己却想到了问题的解决方案，然后对方却一脸茫然。_

场景二：你的同行跑来问你一个问题，但是当他自己把问题说完，或说到一半的时候就想出答案走了，留下一脸茫然的你。

其实上面两种场景现象就是所谓的小黄鸭调试法（Rubber Duck Debuging），又称橡皮鸭调试法，它是我们软件工程中最常使用调试方法之一

此概念据说来自《程序员修炼之道》书中的一个故事，传说程序大师随身携带一只小黄鸭，在调试代码的时候会在桌上放上这只小黄鸭，然后详细地向鸭子解释每行代码，然后很快就将问题定位修复了。

# 4、数据处理及跳转

## 页面跳转

> ModelAndView

设置ModelAndView对象 , 根据view的名称 , 和视图解析器跳到指定的页面 .

页面 : {视图解析器前缀} + viewName +{视图解析器后缀}

```xml
<bean class="org.springframework.web.servlet.view.InternalResourceViewResolver" id="InternalResourceViewResolver">

   <property name="prefix" value="/WEB-INF/jsp/"/>

   <property name="suffix" value=".jsp"/>
bean>
```

对应的controller类

```java
public class ControllerTest2 implements Controller {

    @Override
    public ModelAndView handleRequest(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse) throws Exception {

        ModelAndView mv = new ModelAndView();

        mv.addObject("msg","数据跳转方式一：ModelAndView");

        mv.setViewName("hello");
        return mv;
    }
}
```

> Servlet API接口

通过设置ServletAPI , 不需要视图解析器 .

1、通过HttpServletResponse进行输出

2、通过HttpServletResponse实现重定向

3、通过HttpServletResponse实现转发

```java
@Controller
@RequestMapping("controller1")
public class ControllerTest3 {

    @GetMapping("test1")
    public void test(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

        String name = request.getParameter("name");

        request.setAttribute("msg",name);

        request.getRequestDispatcher("/WEB-INF/jsp/hello.jsp").forward(request,response);
    }

    @GetMapping("test2")
    public void test2(HttpServletRequest request, HttpServletResponse response) throws IOException {

        response.getWriter().println("天哥牛逼");
    }

    @GetMapping("test3")
    public void test3(HttpServletRequest request, HttpServletResponse response) throws IOException {

        response.sendRedirect( "/index.jsp");
    }
}
```

> Spring MVC实现（无需视图解析器）

测试前，需要将视图解析器注释掉

```java
@Controller
@RequestMapping("controller2")
public class ControllerTest4 {

    @GetMapping("test1")
    public String test1() {
        return "/index.jsp";
    }

    @GetMapping("test2")
    public String test2() {
        return "forward:/index.jsp";
    }

    @GetMapping("test3")
    public String test3() {
        return "redirect:/index.jsp";
    }
}
```

> Spring MVC实现（需要视图解析器）

重定向 , 不需要视图解析器 , 本质就是重新请求一个新地方嘛 , 所以注意路径问题.

可以重定向到另外一个请求实现 .

```java
@Controller
@RequestMapping("controller3")
public class ControllerTest5 {

    @GetMapping("test1")
    public String test1(Model mv) {

        mv.addAttribute("msg","Spring MVC配合视图解析器跳转页面");
        return "hello";
    }

    @Controller
@RequestMapping("controller3")
public class ControllerTest5 {

    @GetMapping("test1")
    public String test1(Model mv) {

        mv.addAttribute("msg","Spring MVC配合视图解析器跳转页面");
        return "hello";
    }

    @GetMapping("test2")
    public String test2() {

        return "redirect:/index.jsp";

    }

}

}
```

## 数据处理

### 接收请求的数据

1、提交的url路劲中的参数名称和处理方法上的参数名一致

提交数据：http://localhost:8080/controller4/test1?name=小丁

处理方法：

```java
@GetMapping("test1")
public String test1(String name) {
    System.out.println(name);
    return "hello";
}
```

2、提交的url路劲中的参数名称和处理方法的参数名不一致

提交请求：http://localhost:8080/controller4/test2?username=小丁

定义方法的参数时，建议加上@RequestParam注解，不管请求的url参数名和处理方法的参数名是否相同，必须加上。这样方便我们后期维护时，可以一眼看出这个参数是需要前端传输数据的。

处理方法：

```java
@GetMapping("test2")
public String test2(@RequestParam("username") String name) {
    System.out.println(name);
    return "hello";
}
```

3、提交表单，post请求，处理方法使用对象参数进行接收

提交请求：http://localhost:8080/controller4/test3?id=0&name=小丁&age=25

注意：

 1、form表单中input的name属性值必须和对象的属性名一致

 2、属性名一致，则自动注入值

 3、属性名不一致，不一致的属性名，则不会注入值

实体类：

在阅读以Java类代码之前，请先看看这两篇文章

* lombok常用注解：https://blog.csdn.net/weixin_44019720/article/details/109146452?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522161685679816780255221607%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fall.%2522%257D&request_id=161685679816780255221607&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2allfirst_rank_v2~rank_v29-1-109146452.pc_search_positive&utm_term=Spring+MVC+lombok%E5%B8%B8%E7%94%A8%E6%B3%A8%E8%A7%A3
* lombok注解失效解决：https://www.freesion.com/article/6978526256/

```java
@Data
@NoArgsConstructor
@AllArgsConstructor
public class User {
    private int id;
    private String name;
    private int age;
}
```

处理方法

```java
@PostMapping("test3")
public String test3(User user) {
   System.out.println(user);
   return "hello";
}
```

### 将请求的数据显示到前端页面上

> 通过ModelAndView方式

我们前面一直都是如此 . 就不过多解释

```java
@GetMapping("test1")
public ModelAndView test1(String name) {

     ModelAndView mv = new ModelAndView();

     mv.addObject("msg",name);

     mv.setViewName("hello");

     return mv;
}
```

> 通过ModelMap方法

```java
@GetMapping("test2")
public String test3(@RequestParam("username") String name,ModelMap map) {

     map.addAttribute("msg",name);
     return "hello";
}
```

> 通过Model方法

```java
@GetMapping("test3")
public String test3(User user, Model mv) {

   mv.addAttribute("msg",user);
   return "hello";
}
```

对比：

就对于新手而言简单来说使用区别就是：

```
Model &#x53EA;&#x6709;&#x5BE5;&#x5BE5;&#x51E0;&#x4E2A;&#x65B9;&#x6CD5;&#x53EA;&#x9002;&#x5408;&#x7528;&#x4E8E;&#x50A8;&#x5B58;&#x6570;&#x636E;&#xFF0C;&#x7B80;&#x5316;&#x4E86;&#x65B0;&#x624B;&#x5BF9;&#x4E8E;Model&#x5BF9;&#x8C61;&#x7684;&#x64CD;&#x4F5C;&#x548C;&#x7406;&#x89E3;&#xFF1B;

ModelMap &#x7EE7;&#x627F;&#x4E86; LinkedMap &#xFF0C;&#x9664;&#x4E86;&#x5B9E;&#x73B0;&#x4E86;&#x81EA;&#x8EAB;&#x7684;&#x4E00;&#x4E9B;&#x65B9;&#x6CD5;&#xFF0C;&#x540C;&#x6837;&#x7684;&#x7EE7;&#x627F; LinkedMap &#x7684;&#x65B9;&#x6CD5;&#x548C;&#x7279;&#x6027;&#xFF1B;

ModelAndView &#x53EF;&#x4EE5;&#x5728;&#x50A8;&#x5B58;&#x6570;&#x636E;&#x7684;&#x540C;&#x65F6;&#xFF0C;&#x53EF;&#x4EE5;&#x8FDB;&#x884C;&#x8BBE;&#x7F6E;&#x8FD4;&#x56DE;&#x7684;&#x903B;&#x8F91;&#x89C6;&#x56FE;&#xFF0C;&#x8FDB;&#x884C;&#x63A7;&#x5236;&#x5C55;&#x793A;&#x5C42;&#x7684;&#x8DF3;&#x8F6C;&#x3002;
```

当然更多的以后开发考虑的更多的是性能和优化，就不能单单仅限于此的了解。

请使用80%的时间打好扎实的基础，剩下18%的时间研究框架，2%的时间去学点英文，框架的官方文档永远是最好的教程

## 乱码处理

测试步骤：

1、我们可以在首页编写一个提交的表单

```jsp

```

2、后台编写对应的处理类

```java
@Controller
public class Encoding {
   @RequestMapping("/e/t")
   public String test(Model model,String name){
       model.addAttribute("msg",name);
       return "hello";
  }
}
```

3、测试结果

![](https://img-blog.csdnimg.cn/20210406142750682.png#pic_center)

不得不说，乱码问题是在我们开发中十分常见的问题，也是让我们程序猿比较头大的问题！

以前乱码问题通过过滤器解决 , 而SpringMVC给我们提供了一个过滤器 , 可以在web.xml中配置 .

修改了xml文件需要重启服务器！

```xml
<filter>
   <filter-name>encodingfilter-name>
   <filter-class>org.springframework.web.filter.CharacterEncodingFilterfilter-class>
   <init-param>
       <param-name>encodingparam-name>
       <param-value>utf-8param-value>
   init-param>
filter>
<filter-mapping>
   <filter-name>encodingfilter-name>
   <url-pattern>/*url-pattern>
filter-mapping>
```

但是我们发现 , 有些极端情况下.这个过滤器对get的支持不好 .

处理方法 :

1、修改tomcat配置文件 ：设置编码！

```xml
<Connector URIEncoding="utf-8" port="8080" protocol="HTTP/1.1"
          connectionTimeout="20000"
          redirectPort="8443" />
```

2、自定义过滤器

```java
package com.njxh.filter;

import javax.servlet.*;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletRequestWrapper;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.io.UnsupportedEncodingException;
import java.util.Map;

public class GenericEncodingFilter implements Filter {

   @Override
   public void destroy() {
  }

   @Override
   public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {

       HttpServletResponse myResponse=(HttpServletResponse) response;
       myResponse.setContentType("text/html;charset=UTF-8");

       HttpServletRequest httpServletRequest = (HttpServletRequest) request;

       HttpServletRequest myrequest = new MyRequest(httpServletRequest);
       chain.doFilter(myrequest, response);
  }

   @Override
   public void init(FilterConfig filterConfig) throws ServletException {
  }

}

class MyRequest extends HttpServletRequestWrapper {

   private HttpServletRequest request;

   private boolean hasEncode;

   public MyRequest(HttpServletRequest request) {
       super(request);
       this.request = request;
  }

   @Override
   public Map getParameterMap() {

       String method = request.getMethod();
       if (method.equalsIgnoreCase("post")) {

           try {

               request.setCharacterEncoding("utf-8");
               return request.getParameterMap();
          } catch (UnsupportedEncodingException e) {
               e.printStackTrace();
          }
      } else if (method.equalsIgnoreCase("get")) {

           Map<String, String[]> parameterMap = request.getParameterMap();
           if (!hasEncode) {
               for (String parameterName : parameterMap.keySet()) {
                   String[] values = parameterMap.get(parameterName);
                   if (values != null) {
                       for (int i = 0; i < values.length; i++) {
                           try {

                               values[i] = new String(values[i]
                                      .getBytes("ISO-8859-1"), "utf-8");
                          } catch (UnsupportedEncodingException e) {
                               e.printStackTrace();
                          }
                      }
                  }
              }
               hasEncode = true;
          }
           return parameterMap;
      }
       return super.getParameterMap();
  }

   @Override
   public String getParameter(String name) {
       Map<String, String[]> parameterMap = getParameterMap();
       String[] values = parameterMap.get(name);
       if (values == null) {
           return null;
      }
       return values[0];
  }

   @Override
   public String[] getParameterValues(String name) {
       Map<String, String[]> parameterMap = getParameterMap();
       String[] values = parameterMap.get(name);
       return values;
  }
}
```

这个也是我在网上找的一些大神写的，一般情况下，SpringMVC默认的乱码处理就已经能够很好的解决了！

然后在web.xml中配置这个过滤器即可！

乱码问题，需要平时多注意，在尽可能能设置编码的地方，都设置为统一编码 UTF-8！

# 5、JSON交互处理

## 什么是JSON？

* JSON(JavaScript Object Notation,JS 对象标记) 是一种轻量级的数据交换格式，目前使用特别广泛
* 采用完全独立于编程语言的文本格式来存储和表示数据
* 简洁和清晰的层次结构使得JSON成为理想的数据交换语言
* 易于人阅读和编写，同时也易于机器解析和生成，并有效地提升网络传输效率。

在JavaScirpt 语言中，一切都是对象。因此，任何JavaScript支持的类型都可以通过JSON来表示，例如字符串、数字、
数组等。看看他的要求和语法格式。

* 对象表示为键值对，数据由逗号分隔
* 花括号保存对象
* 方括号保存数据

JSON键值对是用来保存JavaScript对象的一种方式，和JavaScript对象的写法也大同小异，键/值对组合中的键名写在前面并使用
双引号""包裹，使用冒号：分隔，然后紧接着值：

```json
{"name":"xiaoding"}
{"age":"12"}
{"sex":"男"}
```

很多人搞不清楚JSON和Java Scirpt对象的关系，甚至连谁是谁都分不清楚。

其实，可以这么理解：

JSON是Java Script对象的字符串表示法，它使用文本表示一个JS对象的信息，本质是一个字符串。

```html
var a = {name:"小丁",age:12,sex:"男"} //这是一个对象，注意键名也是可以使用引号包裹的
var b = '{"name":"小丁","age":"12","sex":"男"}' //这是一个 JSON 字符串，本质是一个字符串
```

JSON和JavaScirpt对象互转

要实现从JSON字符串转换为Java Script对象，使用JSON.parse() 方法

```json
var obj = JSON.parse('{"a": "Hello", "b": "World"}');

```

## 使用Jackson 解析JSON数据

### 使用Jackson 解析JSON数据

Jackson应该是目前比较好的json解析工具了

当然工具不止这一个，比如还有阿里巴巴的 fastjson 等等。

我们这里使用Jackson，使用它需要导入它的jar包；

```xml
<dependency>
   <groupId>com.fasterxml.jackson.coregroupId>
   <artifactId>jackson-databindartifactId>
   <version>2.12.2version>
dependency>
```

配置web.xml

```xml
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">

    <servlet>
        <servlet-name>springmvcservlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServletservlet-class>

        <init-param>
            <param-name>contextConfigLocationparam-name>
            <param-value>classpath:springmvc-servlet.xmlparam-value>
        init-param>

        <load-on-startup>1load-on-startup>
    servlet>

    <servlet-mapping>
        <servlet-name>springmvcservlet-name>
        <url-pattern>/url-pattern>
    servlet-mapping>

    <filter>
        <filter-name>encodingfilter-name>
        <filter-class>org.springframework.web.filter.CharacterEncodingFilterfilter-class>
        <init-param>
            <param-name>encodingparam-name>
            <param-value>utf-8param-value>
        init-param>
    filter>
    <filter-mapping>
        <filter-name>encodingfilter-name>
        <url-pattern>/url-pattern>
    filter-mapping>
web-app>
```

配置springmvc-servlet.xml文件

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       https://www.springframework.org/schema/context/spring-context.xsd
       http://www.springframework.org/schema/mvc
       https://www.springframework.org/schema/mvc/spring-mvc.xsd">

    <context:component-scan base-package="com.njxh.controller"/>

    <bean id="InternalResourceViewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">

        <property name="prefix" value="/WEB-INF/jsp/"/>

        <property name="suffix" value=".jsp"/>
    bean>
beans>
```

编写一个User的实体类

```java
@Data
@NoArgsConstructor
@AllArgsConstructor
public class User {
    private int id;
    private String  name;
    private int age;
}
```

这里我们需要两个新东西，一个是@ResponseBody，一个是ObjectMapper对象，我们看下具体的用法

编写一个Controller类

```java
@Controller
public class JSONController {

    @RequestMapping("/test1")
    @ResponseBody
    public String test() throws JsonProcessingException {

       ObjectMapper mapper = new ObjectMapper();

       User user = new User(0, "小丁", 25);

       String str = mapper.writeValueAsString(user);

       return str;
    }
}
```

配置Tomcat ， 启动测试一下！

http://localhost:8080/test1

![](https://img-blog.csdnimg.cn/20210406142831821.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3hpYW9kaW5nNTIw,size_16,color_FFFFFF,t_70#pic_center)

发现出现了乱码问题，我们需要设置一下他的编码格式为utf-8，以及它返回的类型；

通过@RequestMaping的produces属性来实现，修改下代码

```xml
//produces:指定响应体返回类型和编码
@RequestMapping(value = "/test1",produces = "application/json;charset=utf-8")
```

再次测试， http://localhost:8080/test1 ， 乱码问题OK！

![](https://img-blog.csdnimg.cn/20210406142940778.png#pic_center)

【注意：使用JSON记得处理乱码问题】

### JSON数据统一乱码解决方案

上一种方法比较麻烦，如果项目中有许多请求则每一个都要添加，可以通过Spring配置统一指定，这样就不用每次都去处理了！

我们可以在springmvc的配置文件上添加一段消息StringHttpMessageConverter转换配置！

```xml
<mvc:annotation-driven>
   <mvc:message-converters register-defaults="true">
       <bean class="org.springframework.http.converter.StringHttpMessageConverter">
           <constructor-arg value="UTF-8"/>
       bean>
       <bean class="org.springframework.http.converter.json.MappingJackson2HttpMessageConverter">
           <property name="objectMapper">
               <bean class="org.springframework.http.converter.json.Jackson2ObjectMapperFactoryBean">
                   <property name="failOnEmptyBeans" value="false"/>
               bean>
           property>
       bean>
   mvc:message-converters>
mvc:annotation-driven>
```

> 返回JSON字符统一解决

在类上直接使用 @RestController ，这样子，里面所有的方法都只会返回 json 字符串了，不用再每一个都添加@ResponseBody ！我们在前后端分离开发中，一般都使用 @RestController ，十分便捷！

```xml
@RestController
public class JSONController {

    @RequestMapping("/test1")
    @ResponseBody //当前方法不走视图解析器，并向请求页面返回一个String
    public String test() throws JsonProcessingException {
        //创建一个jackson的对象映射器，用来解析数据
       ObjectMapper mapper = new ObjectMapper();
       //创建一个对象
       User user = new User(0, "小丁", 25);
       //将我们的对象解析成为json格式
       String str = mapper.writeValueAsString(user);
       //由于@ResponseBody注解，这里会将str转成json格式返回；十分方便
       return str;
    }
}
```

> 集合测试

增加一个新方法

```java
@RequestMapping("/test2")
public String test2() throws JsonProcessingException {

   ObjectMapper mapper = new ObjectMapper();

   User user = new User(0,"你好",0);
   User user2 = new User(0,"你好",0);
   User user3 = new User(0,"你好",0);
   User user4 = new User(0,"你好",0);
   List<User> list = new ArrayList<User>();
   list.add(user);
   list.add(user2);
   list.add(user3);
   list.add(user4);

   String str = mapper.writeValueAsString(list);
   return str;
}
```

测试结果：

![](https://img-blog.csdnimg.cn/20210406143017245.png#pic_center)

> 时间测试

新增一个方法

```java
@RequestMapping("/test3")
public String test3() throws JsonProcessingException {

   ObjectMapper mapper = new ObjectMapper();

   Date date = new Date();

   String str = mapper.writeValueAsString(date);
   return str;
}
```

测试结果：

![](https://img-blog.csdnimg.cn/20210406143036203.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3hpYW9kaW5nNTIw,size_16,color_FFFFFF,t_70#pic_center)

* 默认日期格式会变成一个数字，是1970年1月1日到当前日期的毫秒数！
* Jackson 默认是会把时间转成timestamps形式

解决方案：取消timestamps形式 ， 自定义时间格式

```java
@RequestMapping("/test4")
public String test4() throws JsonProcessingException {

   ObjectMapper mapper = new ObjectMapper();

   mapper.configure(SerializationFeature.WRITE_DATES_AS_TIMESTAMPS, false);

   SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");

   mapper.setDateFormat(sdf);

   Date date = new Date();
   String str = mapper.writeValueAsString(date);

   return str;
}
```

运行结果：成功的输出了时间！

![](https://img-blog.csdnimg.cn/2021040614311210.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3hpYW9kaW5nNTIw,size_16,color_FFFFFF,t_70#pic_center)

### 定义一个工具类来解决JSON数据转换的问题

如果要经常使用的话，这样是比较麻烦的，我们可以将这些代码封装到一个工具类中；我们去编写下

```java
package com.njxh.utils;

import com.fasterxml.jackson.core.JsonProcessingException;
import com.fasterxml.jackson.databind.ObjectMapper;
import com.fasterxml.jackson.databind.SerializationFeature;

import java.text.SimpleDateFormat;

public class JSONUtils {

    public static String getJson(Object o) throws JsonProcessingException {
        return getJson(o,"yyyy-MM-dd HH:mm:ss");
    }

    public static String getJson(Object o,String dateFormat) throws JsonProcessingException {

        ObjectMapper mapper = new ObjectMapper();

        mapper.configure(SerializationFeature.WRITE_DATES_AS_TIMESTAMPS,false);

        SimpleDateFormat dateFormat1 = new SimpleDateFormat(dateFormat);

        mapper.setDateFormat(dateFormat1);

        return mapper.writeValueAsString(o);
    }
}

```

我们使用工具类，代码就更加简洁了！

```java
@RequestMapping("/test5")
public String test5() throws JsonProcessingException {
   Date date = new Date();
   String json = JsonUtils.getJson(date);
   return json;
}
```

## fastjson 解析JSON数据

fastjson.jar是阿里开发的一款专门用于Java开发的包，可以方便的实现json对象与JavaBean对象的转换，实现JavaBean对象与json字符串的转换，实现json对象与json字符串的转换。实现json的转换方法很多，最后的实现结果都是一样的。

导入fastjson的依赖

```xml
<dependency>
   <groupId>com.alibabagroupId>
   <artifactId>fastjsonartifactId>
   <version>1.2.75version>
dependency>
```

fastjson 三个主要的类：

JSONObject 代表 json 对象

* JSONObject实现了Map接口, 猜想 JSONObject底层操作是由Map实现的。
* JSONObject对应json对象，通过各种形式的get()方法可以获取json对象中的数据，也可利用诸如size()，isEmpty()等方法获取"键：值"对的个数和判断是否为空。其本质是通过实现Map接口并调用接口中的方法完成的。

JSONArray 代表 json 对象数组

* 内部是有List接口中的方法来完成操作的。

JSON代表 JSONObject和JSONArray的转化

* JSON类源码分析与使用
* 仔细观察这些方法，主要是实现json对象，json对象数组，javabean对象，json字符串之间的相互转化

代码测试，我们新建一个FasJsonDemo类

```java
package com.njxh.controller;

import com.alibaba.fastjson.JSON;
import com.alibaba.fastjson.JSONObject;
import com.njxh.pojo.User;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import java.util.Date;

@RestController
@RequestMapping("/fastjson")
public class JSONController2 {

    @RequestMapping("/test1")
    public String test1() {

        User user = new User(0,"小丁",25);

        String str = JSON.toJSONString(user);
        System.out.println(str);
        System.out.println("===========");

        User user1 = JSON.parseObject(str,User.class);
        System.out.println(user1);
        System.out.println("===========");

        JSONObject jsonObject = (JSONObject) JSON.toJSON(user);
        System.out.println(jsonObject.get("name"));
        System.out.println("=============");

        User user2 = JSON.toJavaObject(jsonObject,User.class);
        System.out.println(user2);

        System.out.println(JSON.toJSONStringWithDateFormat(new Date(),"yyyy-MM-dd HH:mm:ss"));

        return JSON.toJSONString(user);
    }
}
```

这种工具类，我们只需要掌握使用就好了，在使用的时候在根据具体的业务去找对应的实现。

和以前的commons-io那种工具包一样，拿来用就好了！

Json在我们数据传输中十分重要，一定要学会使用！

# 6、SSM框架整合

## 整合MyBatis

环境搭建：

* IDEA
* Tomcat 9.0.44
* JDK14
* Maven 3.6
* MySql 8.0.22

要求：熟练掌握MySql数据库，Spring、JavaWeb以及MyBtais知识，简单的前端知识

1、过滤静态资源

```
<build>
        <resources>
            <resource>
                <directory>src/main/java</directory>
                <includes>
                    <include>**/*.properties</include>
                    <include>**/*.xml</include>
                </includes>
                <filtering>false</filtering>
            </resource>
            <resource>
                <directory>src/main/resources</directory>
                <includes>
                    <include>**/*.properties</include>
                    <include>**/*.xml</include>
                </includes>
                <filtering>false</filtering>
            </resource>
        </resources>
    </build>
```

如果运行程序时，提示不支持发行版本5或者其他版本

* 解决步骤1：https://blog.csdn.net/weixin_42638349/article/details/108310611
* 将解决步骤1的最后一步，替换我这个

```xml
<properties>
        <project.build.sourceEncoding>UTF-8project.build.sourceEncoding>
        <project.reporting.outputEncoding>UTF-8project.reporting.outputEncoding>
        <java.version>14java.version>
        <maven.compiler.source>14maven.compiler.source>
        <maven.compiler.target>14maven.compiler.target>
        <encoding>UTF-8encoding>
properties>
```

2、导入依赖

```xml
<dependencies>

    <dependency>
        <groupId>junitgroupId>
        <artifactId>junitartifactId>
        <version>4.13version>
    dependency>

    <dependency>
        <groupId>org.springframeworkgroupId>
        <artifactId>spring-webmvcartifactId>
        <version>5.3.5version>
    dependency>
    <dependency>
        <groupId>org.springframeworkgroupId>
        <artifactId>spring-jdbcartifactId>
        <version>5.3.5version>
    dependency>

    <dependency>
        <groupId>javax.servletgroupId>
        <artifactId>servlet-apiartifactId>
        <version>2.5version>
    dependency>
    <dependency>
        <groupId>javax.servlet.jspgroupId>
        <artifactId>jsp-apiartifactId>
        <version>2.2version>
    dependency>
    <dependency>
        <groupId>javax.servlet.jsp.jstlgroupId>
        <artifactId>jstl-apiartifactId>
        <version>1.2version>
    dependency>
    <dependency>
        <groupId>apache-taglibsgroupId>
        <artifactId>standardartifactId>
        <version>1.1.2version>
    dependency>

    <dependency>
        <groupId>org.projectlombokgroupId>
        <artifactId>lombokartifactId>
        <version>1.18.18version>
        <scope>providedscope>
    dependency>

    <dependency>
        <groupId>com.alibabagroupId>
        <artifactId>fastjsonartifactId>
        <version>1.2.75version>
    dependency>

    <dependency>
        <groupId>org.aspectjgroupId>
        <artifactId>aspectjweaverartifactId>
        <version>1.9.6version>
    dependency>
    <dependency>
        <groupId>aopalliancegroupId>
        <artifactId>aopallianceartifactId>
        <version>1.0version>
    dependency>
    <dependency>
        <groupId>javax.annotationgroupId>
        <artifactId>javax.annotation-apiartifactId>
        <version>1.3.2version>
    dependency>

    <dependency>
        <groupId>mysqlgroupId>
        <artifactId>mysql-connector-javaartifactId>
        <version>8.0.22version>
    dependency>

    <dependency>
        <groupId>org.mybatisgroupId>
        <artifactId>mybatisartifactId>
        <version>3.5.6version>
    dependency>
    <dependency>
        <groupId>org.mybatisgroupId>
        <artifactId>mybatis-springartifactId>
        <version>2.0.6version>
    dependency>

    <dependency>
        <groupId>com.mchangegroupId>
        <artifactId>c3p0artifactId>
        <version>0.9.5.5version>
    dependency>
dependencies>
```

3、建立基本项目架构和配置架构

* com.njxh.pojo
* com.njxh.dao
* com.njxh.service
* com.njxh.controller

创建MyBtais配置文件 mybatis.xml（注意：所有配置文件统一放在resource目录汇总）

```xml
<configuration>
   <typeAliases>
        <package name="com.njxh.pojo"/>
    typeAliases>

    <mappers>
        <package name="com.njxh.dao"/>
    mappers>
configuration>
```

配置数据库文件，database.properties

```properties
jdbc.driver=com.mysql.cj.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/mybatis?useUnicode=true&characterEncoding=utf-8&allowPublicKeyRetrieval=true&useSSL=false&serverTimezone=UTC
jdbc.username=root
jdbc.password=dingtianci123
```

定义Spring总配置文件（用于管理其他Spring配置文件）

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd">

beans>
```

4、运行SQL语句，创建表

```sql
CREATE DATABASE `ssmbuild`;

USE `ssmbuild`;

DROP TABLE IF EXISTS `books`;

CREATE TABLE `books` (
`bookID` INT(10) NOT NULL AUTO_INCREMENT COMMENT '书id',
`bookName` VARCHAR(100) NOT NULL COMMENT '书名',
`bookCounts` INT(11) NOT NULL COMMENT '数量',
`detail` VARCHAR(200) NOT NULL COMMENT '描述',
KEY `bookID` (`bookID`)
) ENGINE=INNODB DEFAULT CHARSET=utf8

INSERT  INTO `books`(`bookID`,`bookName`,`bookCounts`,`detail`)VALUES
(1,'Java',1,'从入门到放弃'),
(2,'MySQL',10,'从删库到跑路'),
(3,'Linux',5,'从进门到进牢');
```

5、创建数据库表的实体类 Books （项目的pojo层）

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class Books {
    private int bookId;
    private String bookName;
    private int bookCounts;
    private String detail;
}
```

6、创建映射文件和映射接口 （项目的dao层）

```java
public interface BookMapper {

    public Books queryBookById(int bookId);

    public List<Books> queryAllBook();

    public List<Books> queryBookName(String bookName);

    public int addBook(Books books);

    public int updateBook(Books books);

    public int deleteBook(int bookId);
}
```

```xml
<mapper namespace="com.njxh.dao.BookMapper">
    <select id="queryBookById" parameterType="int" resultType="com.njxh.pojo.Books">
        select * from books where bookId = #{bookId}
    select>

    <select id="queryAllBook" resultType="com.njxh.pojo.Books">
        select * from books
    select>

    <select id="queryBookName" parameterType="String" resultType="com.njxh.pojo.Books">
        select * from books where bookName like #{bookName}
    select>

    <insert id="addBook" parameterType="com.njxh.pojo.Books">
        insert into books(bookName,bookCounts,detail) values (#{bookName},#{bookCounts},#{detail})
    insert>

    <update id="updateBook" parameterType="com.njxh.pojo.Books">
        update books set bookName = #{bookName},bookCounts=#{bookCounts},detail=#{detail} where bookId = #{bookId}
    update>

    <delete id="deleteBook" parameterType="int">
        delete from books where bookId = #{bookId}
    delete>
mapper>
```

注意：

* xml文件名和映射接口名称大小写要一直，除了后缀名
* xml文件中的 namespace 的值 对应映射接口所在的路径和名称

7、定义业务逻辑成（项目的service层）

定义业务接口

```java
public interface BookService {

    public Books queryBookById(int bookId);

    public List<Books> queryAllBook();

    public List<Books> queryBookName(String bookName);

    public int addBook(Books books);

    public int updateBook(Books books);

    public int deleteBook(int bookId);
}
```

定义业务接口实现类

```java
@Service("bookServicetImpl")
public class BookServiceImpl implements BookService {

    @Resource(name = "bookMapper")
    private BookMapper bookMapper;

    public void setBookMapper(BookMapper bookMapper) {
        this.bookMapper = bookMapper;
    }

    @Override
    public Books queryBookById(int bookId) {
        return bookMapper.queryBookById(bookId);
    }

    @Override
    public List<Books> queryAllBook() {
        return bookMapper.queryAllBook();
    }

    @Override
    public List<Books> queryBookName(String bookName) {
        return bookMapper.queryBookName(bookName);
    }

    @Override
    public int addBook(Books books) {
        return bookMapper.addBook(books);
    }

    @Override
    public int updateBook(Books books) {
        return bookMapper.updateBook(books);
    }

    @Override
    public int deleteBook(int bookId) {
        return bookMapper.deleteBook(bookId);
    }
}
```

到此底层需求基本编写完成！

## 整合Spring层

1、配置整合mybatis的Spring的文件，spring-jdbc.xml

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       https://www.springframework.org/schema/context/spring-context.xsd">

    <context:property-placeholder location="classpath:db.properties"/>

    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">

        <property name="driverClass" value="${jdbc.driver}"/>
        <property name="jdbcUrl" value="${jdbc.url}"/>
        <property name="user" value="${jdbc.username}"/>
        <property name="password" value="${jdbc.password}"/>

        <property name="maxPoolSize" value="30"/>
        <property name="minPoolSize" value="10"/>

        <property name="autoCommitOnClose" value="false"/>

        <property name="checkoutTimeout" value="10000"/>

        <property name="acquireRetryAttempts" value="2"/>
    bean>

    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">

        <property name="dataSource" ref="dataSource"/>

        <property name="configLocation" value="classpath:mybatis.xml"/>
    bean>

    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">

        <property name="sqlSessionFactoryBeanName" value="sqlSessionFactory"/>

        <property name="basePackage" value="com.njxh.dao"/>
    bean>
beans>
```

2、配置整合service层的配置文件，spring-service.xml

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:tx="http://www.springframework.org/schema/tx"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
   http://www.springframework.org/schema/beans/spring-beans.xsd
   http://www.springframework.org/schema/context
   http://www.springframework.org/schema/context/spring-context.xsd
   http://www.springframework.org/schema/tx
   http://www.springframework.org/schema/tx/spring-tx.xsd
   http://www.springframework.org/schema/aop
   http://www.springframework.org/schema/aop/spring-aop.xsd">

    <context:component-scan base-package="com.njxh.service"/>

    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">

        <property name="dataSource" ref="dataSource"/>
    bean>

    <tx:advice id="txAdvice" transaction-manager="transactionManager">
        <tx:attributes>
            <tx:method name="add*" propagation="REQUIRED"/>
            <tx:method name="delete*" propagation="REQUIRED"/>
            <tx:method name="update*" propagation="REQUIRED"/>
            <tx:method name="query*" read-only="true"/>
            <tx:method name="*" read-only="true"/>
        tx:attributes>
    tx:advice>

    <aop:config>

        <aop:pointcut id="pointcut" expression="execution(* com.njxh.service.*.*(..))"/>

        <aop:advisor advice-ref="txAdvice" pointcut-ref="pointcut"/>
    aop:config>
beans>
```

到此Spring层编写完成！

## 整合MVC层

1、配置web.xml （设置拦截器、乱码过滤器）

```xml
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">

    <servlet>
        <servlet-name>DispatcherServletservlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServletservlet-class>
        <init-param>
            <param-name>contextConfigLocationparam-name>

            <param-value>classpath:spring-context.xmlparam-value>
        init-param>
        <load-on-startup>1load-on-startup>
    servlet>
    <servlet-mapping>
        <servlet-name>DispatcherServletservlet-name>
        <url-pattern>/url-pattern>
    servlet-mapping>

    <filter>
        <filter-name>encodingFilterfilter-name>
        <filter-class>
            org.springframework.web.filter.CharacterEncodingFilter
        filter-class>
        <init-param>
            <param-name>encodingparam-name>
            <param-value>utf-8param-value>
        init-param>
    filter>
    <filter-mapping>
        <filter-name>encodingFilterfilter-name>
        <url-pattern>/*url-pattern>
    filter-mapping>

    <session-config>
        <session-timeout>15session-timeout>
    session-config>
web-app>
```

2、配置整合controller文件，spring-mvc.xml

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
   http://www.springframework.org/schema/beans/spring-beans.xsd
   http://www.springframework.org/schema/context
   http://www.springframework.org/schema/context/spring-context.xsd
   http://www.springframework.org/schema/mvc
   https://www.springframework.org/schema/mvc/spring-mvc.xsd">

    <context:component-scan base-package="com.njxh.controller"/>

    <mvc:annotation-driven />

    <mvc:default-servlet-handler/>

    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="viewClass" value="org.springframework.web.servlet.view.JstlView" />
        <property name="prefix" value="/WEB-INF/jsp/" />
        <property name="suffix" value=".jsp" />
    bean>
beans>
```

3、定义controller层，定义图书控制层，BookController

```java
@Controller
@RequestMapping("/BookController")
public class BookController {

    @Resource(name = "bookServicetImpl")
    private BookService bookService;

    public void setBookService(BookService bookService) {
        this.bookService = bookService;
    }

}
```

4、BookController类，编写获取全部图书的方法

```jsp
@GetMapping("/queryAllBook")
public String queryAllBook(Model mv) {
  //调用查询的方法
  List list = bookService.queryAllBook();
  //添加数据
  mv.addAttribute("list",list);
  return "indexBook";
}
```

5、编写前端页面，定义初始页面

```jsp
    首页

      h3{
        width: 250px;
        height: 60px;
        margin: 100px auto;
      }
      a{
        text-decoration: none;
        color: black;
        font-size: 30px;
      }

      前往图书展示页面

```

6、定义视图页面，在WEB-INF层里面创建jsp目录，在jsp里面创建，indexBook.jsp页面

```xml
<html>
<head>
    <title>图书展示页面title>
    <meta name="viewport" content="width=device-width, initial-scale=1.0">

    <link href="https://cdn.bootcss.com/bootstrap/3.3.7/css/bootstrap.min.css" rel="stylesheet">
head>
<body>
    <div class="container">
        <div class="row">
            <div class="col-md-12 column">
                <h3>图书列表————显示所有书籍h3>
            div>
        div>
        <div class="row">
            <div class="col-md-12 column">
                <table class="table table-striped">

                    <thead>
                        <tr>
                            <td>书籍编号：td>
                            <td>书籍名字td>
                            <td>书籍数量td>
                            <td>书籍详情td>
                            <td>操作td>
                        tr>
                    thead>

                    <tbody>
                       <c:forEach items="${list}" var="list">
                                    <tr>
                                        <td>${list.bookId}td>
                                        <td>${list.bookName}td>
                                        <td>${list.bookCounts}td>
                                        <td>${list.detail}td>
                                        <td><a href="">修改a>|<a href="">删除a>td>
                                    tr>
                    tbody>
                table>
            div>
        div>
    div>
body>
html>
```

7、BookController类，编写添加书籍的方法

```java
@GetMapping("/bookAddPage")
public String bookAddPage() {
   return "addBook";
}

@PostMapping("/addBook")
public String addBook(Books books) {

    bookService.addBook(books);
    return "redirect:/BookController/queryAllBook";
}
```

8、在indexBook.jsp文件，编写添加书籍模块

```jsp
                添加

```

9、在jsp目录中，编写添加书籍的页面，addBook,jsp

```jsp
    添加书籍

添加书籍

        图书名称:

        图书数量:

        图书详情:

    Submit

```

10、在BookController类中，编写修改图书的方法

```java
@GetMapping("/bookUpdatePage")
public String bookUpdatePage(int bookId,Model mv) {

    Books books = bookService.queryBookById(bookId);

    mv.addAttribute("books",books);
    return "updateBook";
}

@PostMapping("/updateBook")
public String updateBook(Books books) {

    bookService.updateBook(books);

    return "redirect:/BookController/queryAllBook";
}
```

11、在indexBook.jsp页面中，修改按钮的地方，添加响应请求路径

```jsp
修改
```

12、在jsp目录中，编写修改书籍的页面，updateBook.jsp

```jsp
    修改书籍

        图书名称:

        图书数量:

        图书详情:

    Submit

```

13、在BookController类中，编写删除图书的方法

```java
@GetMapping("deleteBook")
public String deleteBook(int bookId) {

     bookService.deleteBook(bookId);

     return "redirect:/BookController/queryAllBook";
}
```

14、在indexBook.jsp页面中，删除按钮的地方，添加响应请求路径

```jsp
删除
```

15、在BookController类中，编写根据图书名称查询的方法

```java
@PostMapping("queryBookName")
public String queryBookName(String queryBookName, Model mv) throws IOException {

     List<Books> list = bookService.queryBookName("%"+queryBookName+"%");

     if (list.isEmpty()) {
          mv.addAttribute("err","未查询到数据");
          return "indexBook";
     }

     mv.addAttribute("list",list);
     return "indexBook";
}
```

16、在indexBook.jsp页面中，新增根据图书名称查询的模块

```jsp
                添加

```

17、在indexBook.jsp页面，由于新增了图书搜索模块，所以我们在获取数据时，需要判断一下，数据是否查询到

新增判断功能，将替换indexBook.jsp 里面的tbody标签的内容

原先内容代码

```jsp
               ${list.bookId}
               ${list.bookName}
               ${list.bookCounts}
               ${list.detail}
               修改|删除

```

需要替换的代码

```jsp
    	${err}

                   	${list.bookId}
                   	${list.bookName}
                   	${list.bookCounts}
                   	${list.detail}
                   	修改|删除

```

18、如果图书没有搜索到，返回图书数据，新增返回所有图书数据模块

```jsp
                添加

                返回全部数据

```

配置Tomcat并运行！

到目前为止，这个SSM项目整合已经完全的OK了，可以直接运行进行测试！这个练习十分的重要，大家需要保证，不看任何东西，自己也可以完整的实现出来！

项目结构图：

![](https://img-blog.csdnimg.cn/20210406143213293.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3hpYW9kaW5nNTIw,size_16,color_FFFFFF,t_70#pic_center)
![](https://img-blog.csdnimg.cn/20210406143232586.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3hpYW9kaW5nNTIw,size_16,color_FFFFFF,t_70#pic_center)

## 小结与展望

这个是同学们的第一个SSM整合案例，一定要烂熟于心！

SSM框架的重要程度是不言而喻的，学到这里，大家已经可以进行基本网站的单独开发。但是这只是增删改查的基本操作。可以说学到这里，大家才算是真正的步入了后台开发的门。也就是能找一个后台相关工作的底线。

或许很多人，工作就做这些事情，但是对于个人的提高来说，还远远不够！

我们后面还要学习一些 SpringMVC 的知识！

* Ajax 和 Json
* 文件上传和下载
* 拦截器

# 7、AJAX交互与请求

## ajax介绍

* AJAX = Asynchronous JavaScript and XML（异步的 JavaScript 和 XML）>
* AJAX 是一种在无需重新加载整个网页的情况下，能够更新部分网页的技术。
* Ajax 不是一种新的编程语言，而是一种用于创建更好更快以及交互性更强的Web应用程序的技术
* 在 2005 年，Google 通过其 Google Suggest 使 AJAX 变得流行起来。Google Suggest能够自动帮你完成搜索单词。
* Google Suggest 使用 AJAX 创造出动态性极强的 web 界面：当您在谷歌的搜索框输入关键字时，JavaScript 会把这些字符发送到服务器，然后服务器会返回一个搜索建议的列表。
* 就和国内百度的搜索框一样!

* 传统的网页(即不用ajax技术的网页)，想要更新内容或者提交一个表单，都需要重新加载整个网页。
* 使用ajax技术的网页，通过在后台服务器进行少量的数据交换，就可以实现异步局部更新。
* 使用Ajax，用户可以创建接近本地桌面应用的直接、高可用、更丰富、更动态的Web用户界面。

## 使用iframe伪造ajax

我们可以使用前端的一个标签来伪造一个ajax的样子。iframe标签

1、新建一个module ：SpringMVC-08-AJAX ， 导入web支持！

2、编写一个 ajax.jsp 使用 iframe 测试，感受下效果

```jsp
    使用iframe伪造一个ajax

        $(function () {
            //设置点击事件
            $("#anNiu").click(function () {
                //获取文本框的内容
                var input = $("#Inputs").val();
                //给iframe设置src属性
                $("#iframes").attr("src",input);
            })
        })

```

利用AJAX可以做

* 注册时，输入用户名自动检测用户是否已经存在。
* 登陆时，提示用户名密码错误
* 删除数据行时，将行ID发送到后台，后台在数据库中删除，数据库删除成功后，在页面DOM中将数据行也删除。
* ...等等

## 使用ajax获取数据

纯JS原生实现Ajax我们不去讲解这里，直接使用jquery提供的，方便学习和使用，避免重复造轮子，有兴趣的同学可以去了解下JS原生XMLHttpRequest ！

Ajax的核心是XMLHttpRequest对象(XHR)。XHR为向服务器发送请求和解析服务器响应提供了接口。能够以异步方式从服务器获取新数据。

jQuery 提供多个与 AJAX 有关的方法。

通过 jQuery AJAX 方法，您能够使用 HTTP Get 和 HTTP Post 从远程服务器上请求文本、HTML、XML 或 JSON – 同时您能够把这些外部数据直接载入网页的被选元素中。

jQuery 不是生产者，而是大自然搬运工。

jQuery Ajax本质就是 XMLHttpRequest，对他进行了封装，方便调用！

```xml
jQuery.ajax(...)
      部分参数：
            url：请求地址
            type：请求方式，GET、POST（1.9.0之后用method）
        headers：请求头
            data：要发送的数据
    contentType：即将发送信息至服务器的内容编码类型(默认: "application/x-www-form-urlencoded; charset=UTF-8")
          async：是否异步
        timeout：设置请求超时时间（毫秒）
      beforeSend：发送请求前执行的函数(全局)
        complete：完成之后执行的回调函数(全局)
        success：成功之后执行的回调函数(全局)
          error：失败之后执行的回调函数(全局)
        accepts：通过请求头发送给服务器，告诉服务器当前客户端可接受的数据类型
        dataType：将服务器端返回的数据转换成指定类型
          "xml": 将服务器端返回的内容转换成xml格式
          "text": 将服务器端返回的内容转换成普通文本格式
          "html": 将服务器端返回的内容转换成普通文本格式，在插入DOM中时，如果包含JavaScript标签，则会尝试去执行。
        "script": 尝试将返回值当作JavaScript去执行，然后再将服务器端返回的内容转换成普通文本格式
          "json": 将服务器端返回的内容转换成相应的JavaScript对象
        "jsonp": JSONP 格式使用 JSONP 形式调用函数时，如 "myurl?callback=?" jQuery 将自动替换 ? 为正确的函数名，以执行回调函数
```

我们可以使用最原始的HttpServletResqonse处理，最简单，最通过

1、配置web.xml和springmvc的配置文件，复制上面的案例即可【记得静态资源过滤和注解驱动配置上】

2、编写一个AjaxController类

```java
@RestController
public class AjaxController {

    @GetMapping(value = "/test1",produces = {"text/html;charset=utf-8"})
    public void test1(HttpServletResponse response) throws IOException {
        response.setContentType("text/html;charset=UTF-8");
        response.getWriter().println("你好");
        response.getWriter().close();
    }
}
```

3、编写一个index.jsp文件

```jsp
    使用最原始的HttpServletRespone发送数据

      $(function () {
        //设置点击事件
        $("#anNiu").click(function () {
            $.ajax({
              url:"${pageContext.request.contextPath}/test1/",
              success:function (data){
                $("p").html(data);
              }
            })
        })
      })

      点我获取数据

```

4、运行Tomcat测试，当我们点击按钮，看到页面上显示传回来的信息，表示运行成功

Spring MVC实现AJAX

1、定义实体类

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class User {
    private String name;
    private int age;
    private char sex;
}
```

2、在AjaxController中，新增一个方法

```java
@GetMapping("/test2")
public List<User> test2() {

     List<User> list = new ArrayList<>();

     list.add(new User("小丁",25,'男'));
     list.add(new User("小王",18,'男'));
     list.add(new User("小红",18,'女'));

     return list;
}
```

3、定义一个ajax2.jsp文件

```jsp
    使用JQ封装的ajax

        $(function() {
          //设置点击事件
          $("#anNiu").click(function () {
              //使用ajax从后台获取数据
              $.ajax({
                  url:"${pageContext.request.contextPath}/test2",
                  success:function (data){
                      //定义变量接收内容
                      var obj = "";
                      //使用for循环遍历数据
                      for (var i = 0;i<data.length;i++) {
                          obj += "<tr>"+"<td>"+data[i].name+"</td>"+"<td>"+data[i].age+"</td>"+"<td>"+data[i].sex+"</td>"+"</tr>";
                      }
                      //将结果添加到表格中
                      $("#tbody1").html(obj);
                  }
              })
          });
        })

    点我获取数据

                姓名：
                年龄：
                性别：

```

4、Tomcat运行测试

## 使用ajax实现注册效果

1、在AjaxController中，新增一个方法

```java
@RequestMapping("/test3")
public String test2(String name,String pws) {
    String msg = null;

    if (name != null) {

         if (name.equals("admin")) {
              msg = "ok";
         }else {
              msg = "err";
          }
     }

     if (pws != null) {

     	if (pws.equals("123456")) {
            msg = "ok";
         }else {
            msg = "err";
         }
     }

    return msg;
}
```

2、定义一个login.jsp文件

```jsp
    使用ajax判断用户名或密码是否正确

        $(function () {
            //给文本框设置失去焦点事件
            $("#name").blur(function () {
                $.post({
                    url:"${pageContext.request.contextPath}/test3/",
                    data:{"name":$("#name").val()},
                    success:function (data){

                        if (data == "ok") {
                            $("#span1").css("color","green");
                            $("#span1").html(data);
                        }else {
                            $("#span1").css("color","red");
                            $("#span1").html(data);
                        }
                    }
                })
            })
            $("#pws").blur(function () {
                $.post("${pageContext.request.contextPath}/test3/",{"pws":$("#pws").val()},function (data){
                    if (data == "ok") {
                        $("#span2").css("color","green");
                        $("#span2").html(data);
                    }else {
                        $("#span2").css("color","red");
                        $("#span2").html(data);
                    }
                })
            })
        })

    用户名：

    密码：

```

【记得处理json乱码问题】

测试一下效果，动态请求响应，局部刷新，就是如此！

![](https://img-blog.csdnimg.cn/20210406143320873.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3hpYW9kaW5nNTIw,size_16,color_FFFFFF,t_70#pic_center)

# 8、拦截器+文件上传下载

## 拦截器

### 什么是拦截器？

Spring MVC的处理器拦截器类似于Servlet开发中的过滤器Filter，用于对处理器进行预处理和后处理。

开发者可以自己定义一些拦截器来实现特定的功能。

**过滤器与拦截器的区别：拦截器是AOP思想具体应用。**

**过滤器：**

* servlet规范的一部分，任何JavaWeb工程都可以使用
* 在url-pattern中配置/*之后，**可以对所有要访问的资源进行拦截**

拦截器：

* **拦截器是Spring MVC框架自己的**，只要使用了Spring MVC框架的工程才能使用。
* **拦截器只会拦截访问的控制器方法**，如果访问的是jsp/html/css/image/js是不会进行拦截的。

### 自定义拦截器

如果实现拦截器呢？

想要自定义拦截器，必须要实现HandlerInterceptor接口。

1、新建一个Moudule，SpringMVC-09-Interceptro，添加web支持

2、配置web.xml和spring-context-xml文件

3、编写一个拦截器

```java
package com.njxh.filter;

import org.springframework.web.servlet.HandlerInterceptor;
import org.springframework.web.servlet.ModelAndView;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

public class MyInterceptor implements HandlerInterceptor {

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        System.out.println("请求前，拦截处理");
        System.out.println("拦截的路径："+request.getRequestURI());
        return true;
    }

    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        System.out.println("处理后");
    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        System.out.println("清理");
    }
}

```

4、spring-context.xml配置文件，配置拦截器

```xml
<mvc:interceptors>
   <mvc:interceptor>

       <mvc:mapping path="/**"/>

       <bean class="com.njxh.filter.MyInterceptor"/>
   mvc:interceptor>
mvc:interceptors>
```

5、定义一个Controller请求一下

```java
package com.njxh.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.ResponseBody;

@Controller
public class ControllerTest {

    @GetMapping("/test1")
    @ResponseBody
    public String test1() {
        System.out.println("控制器中的方法执行了");
        return "hello";
    }
}
```

6、前端index.jsp

```jsp
拦截器测试！
```

7、启动Tomcat测试一下

![](https://img-blog.csdnimg.cn/20210406143353763.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3hpYW9kaW5nNTIw,size_16,color_FFFFFF,t_70#pic_center)

### 验证用户是否登录（认证用户）

实现思路：

1. 有一个登录页面，需要写一个controller访问页面
2. 登录页面有一提交表单的动作，需要在controller中处理。判断用户名密码是否正确。
   如果正确，像session中写入用户信息。返回登录成功
3. 拦截用户请求，判断用户是否登录。如果已登录，放行，如果没有登录，跳转到登录页面。

测试：

1、定义一个controller用来处理用户名和密码的问题

```java
package com.njxh.controller;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

@Controller
@RequestMapping("/LoginController")
public class LoginController {

    @GetMapping("/goLogin")
    public String goLogin() {
        return "login";
    }

    @PostMapping("/login")
    public String login(String name, String pws, Model mv, HttpServletRequest req){

        mv.addAttribute("name",name);

        req.getSession().setAttribute("loginUser",name);
        return "index2";
    }

    @GetMapping("/index")
    public String index() {
        return "index2";
    }

    @GetMapping("exit")
    public String exit(HttpServletRequest request) {

        request.getSession().removeAttribute("loginUser");
        return "redirect:/LoginController/goLogin";
    }
}

```

2、定义登录页面

```jsp
    登录

```

3、定义首页

```jsp
    首页

    ${name}
    注销

```

4、在index.jsp页面，新增2个a链接，一个访问首页，一个访问登录页面

```jsp
首页
登录
```

5、在spring-context.xml文件里面配置拦截器

```xml
<mvc:interceptors>
   <mvc:interceptor>
       <mvc:mapping path="/LoginController/**"/>
       <bean id="loginInterceptor" class="com.njxh.filter.LoginInterceptor"/>
   mvc:interceptor>
mvc:interceptors>
```

6、启动Tomcat进行测试！

## 文件上传

> 准备工作

文件上传是项目开发中最常见的功能之一 ,springMVC 可以很好的支持文件上传，但是SpringMVC上下文中默认没有装配MultipartResolver，因此默认情况下其不能处理文件上传工作。如果想使用Spring的文件上传功能，则需要在上下文中配置MultipartResolver。

前端表单要求：为了能上传文件，必须将表单的method设置为POST，并将enctype设置为multipart/form-data。只有在这样的情况下，浏览器才会把用户选择的文件以二进制数据发送给服务器；

对表单中的enctype属性做个详细的说明：

* application/x-www=form-urlencoded：默认方式，只处理表单域中的 value 属性值，采用这种编码方式的表单会将表单域中的值处理成 URL 编码方式。
* multipart/form-data：这种编码方式会以二进制流的方式来处理表单数据，这种编码方式会把文件域指定文件的内容也封装到请求参数中，不会对字符编码。
* text/plain：除了把空格转换为 "+" 号外，其他字符都不做编码处理，这种方式适用直接通过表单发送邮件。

```jsp

```

一旦设置了enctype为multipart/form-data，浏览器即会采用二进制流的方式来处理表单数据，而对于文件上传的处理则涉及在服务器端解析原始的HTTP响应。在2003年，Apache Software Foundation发布了开源的Commons FileUpload组件，其很快成为Servlet/JSP程序员上传文件的最佳选择。

* Servlet3.0规范已经提供方法来处理文件上传，但这种上传需要在Servlet中完成。
* 而Spring MVC则提供了更简单的封装。
* Spring MVC为文件上传提供了直接的支持，这种支持是用即插即用的MultipartResolver实现的。
* Spring MVC使用Apache Commons FileUpload技术实现了一个MultipartResolver实现类：
* CommonsMultipartResolver。因此，SpringMVC的文件上传还需要依赖Apache Commons FileUpload的组件。

> 文件上传

1、导入文件上传的jar包，commons-fileupload ， Maven会自动帮我们导入他的依赖包 commons-io包；

```xml
<dependency>
   <groupId>commons-fileuploadgroupId>
   <artifactId>commons-fileuploadartifactId>
   <version>1.4version>
   <scope>providedscope>
dependency>

<dependency>
   <groupId>javax.servletgroupId>
   <artifactId>javax.servlet-apiartifactId>
   <version>4.0.1version>
dependency>
```

2、配置bean：multipartResolver

【注意！！这个bean的id必须为：multipartResolver，否则上传文件会报400的错误！在这里栽过坑，教训！】

```xml
<bean id="multipartResolver"  class="org.springframework.web.multipart.commons.CommonsMultipartResolver">

        <property name="defaultEncoding" value="utf-8"/>

        <property name="maxUploadSize" value="10485760"/>
        <property name="maxInMemorySize" value="40960"/>
 bean>
```

CommonsMultipartFile 的 常用方法：

* String getOriginalFilename()：获取上传文件的原名
* InputStream getInputStream()：获取文件流
* void transferTo(File dest)：将上传文件保存到一个目录文件中

我们去实际测试一下

3、编写前端页面

```jsp

```

4、定义Controller

```java
package com.njxh.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.*;
import org.springframework.web.multipart.commons.CommonsMultipartFile;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.*;
import java.net.URLEncoder;

@Controller
public class ControllerTest {

    @PostMapping("/fileUpload")
    public String fileUpload(@RequestParam("file") CommonsMultipartFile file, HttpServletRequest request, HttpServletResponse response) throws IOException {

        String uploadFileName = file.getOriginalFilename();

        if ("".equals(uploadFileName)) {
            return "redirect:/index.jsp";
        }
        System.out.println("上传文件名："+uploadFileName);

        String path = request.getServletContext().getRealPath("/upload");

        File realPath = new File(path);
        if (!realPath.exists()) {
            realPath.mkdir();
        }
        System.out.println("上传路径保存地址："+realPath);

        InputStream is = file.getInputStream();
        OutputStream os = new FileOutputStream(new File(realPath,uploadFileName));

        int len = 0;
        byte[] buffer = new byte[1024];

        while((len = is.read(buffer)) != -1) {
            os.write(buffer);
            os.flush();
        }

        os.close();
        is.close();

        return "redirect:/index.jsp";
    }
}

```

5、测试上传文件，ok！

采用file.Transto来保存上传的文件

1、编写Controller

```java
@PostMapping("/upload2")
    public String upload2(@RequestParam("file") CommonsMultipartFile file, HttpServletResponse response, HttpServletRequest request) throws IOException {

        String path = request.getServletContext().getRealPath("/upload");
        File realPath = new File(path);
        if (!realPath.exists()){
            realPath.mkdir();
        }

        System.out.println("上传文件保存地址："+realPath);

        file.transferTo(new File(realPath +"/"+ file.getOriginalFilename()));

        return "redirect:/index.jsp";
    }
```

2、前端表单提交地址修改

3、访问提交测试，ok！

## 文件下载

> 文件下载

文件下载步骤：

1、设置 response 响应头

2、读取文件 – InputStream

3、写出文件 – OutputStream

4、执行操作

5、关闭流（先开后关）

代码实现：

```jsp
@GetMapping("/download")
    @ResponseBody
    public String download(HttpServletResponse response ,HttpServletRequest request) throws IOException {
        //要下载的图片地址
        String  path = request.getServletContext().getRealPath("/upload");
        String  fileName = "1.png";

        //1、设置response 响应头
        response.reset(); //设置页面不缓存,清空buffer
        response.setCharacterEncoding("UTF-8"); //字符编码
        response.setContentType("multipart/form-data"); //二进制传输数据
        //设置响应头
        response.setHeader("Content-Disposition",
                "attachment;fileName="+ URLEncoder.encode(fileName, "UTF-8"));

        File file = new File(path,fileName);
        //2、 读取文件--输入流
        InputStream input=new FileInputStream(file);
        //3、 写出文件--输出流
        OutputStream out = response.getOutputStream();

        byte[] buff =new byte[1024];
        int index=0;
        //4、执行 写出操作
        while((index= input.read(buff))!= -1){
            out.write(buff, 0, index);
            out.flush();
        }
        out.close();
        input.close();
        return null;
    }
```

前端

```jsp
下载文件
```

测试，文件下载OK，大家可以和我们之前学习的JavaWeb原生的方式对比一下，就可以知道这个便捷多了!

拦截器及文件操作在我们开发中十分重要，一定要学会使用！



# 9、总结

## SSM回顾

![image-20220225204921230](C:\Users\13686\AppData\Roaming\Typora\typora-user-images\image-20220225204921230.png)

![image-20220225204859332](C:\Users\13686\AppData\Roaming\Typora\typora-user-images\image-20220225204859332.png)

![image-20220225205003849](C:\Users\13686\AppData\Roaming\Typora\typora-user-images\image-20220225205003849.png)