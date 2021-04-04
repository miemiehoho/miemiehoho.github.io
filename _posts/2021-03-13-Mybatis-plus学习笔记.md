---
layout: post
title: Mybatis-plus学习笔记
date: 2021-03-13
Author: miemiehoho 
tags: [Mybatis]
comments: true
toc: true
---



> 项目源码：https://github.com/miemiehoho/Mybatis-plus-Learn

## (一)项目配置



### 导入常用依赖包：![微信截图_20210312172951](https://raw.githubusercontent.com/miemiehoho/blog/master/picture/2021/03/20210312173037.png)

- 额外导入依赖包

```groovy
	/* mybatis-plus 启动依赖 */
	compile group: 'com.baomidou', name: 'mybatis-plus-boot-starter', version: '3.3.2'
```

- 配置application.yml文件

```yaml
spring:
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/paddle?useUnicode=true&characterEncoding=utf8
    username: root
    password: root
```



### 配置**DataGrip**

点击 File --> new --> data Source --> 选择 mysql

![微信截图_20210312174433](https://raw.githubusercontent.com/miemiehoho/blog/master/picture/2021/03/20210312174512.png)

![微信截图_20210312174445](https://raw.githubusercontent.com/miemiehoho/blog/master/picture/2021/03/20210312174519.png)

### 创建实体类

- lombok  依赖包

**@Data**注解，简化开发自动生成get()、set()方法![微信截图_20210312175841](https://raw.githubusercontent.com/miemiehoho/blog/master/picture/2021/03/20210312180237.png)

创建Mapper接口

只需要创建接口，不需要手动实现，只需要继承**BaseMapper<T>**接口

**BaseMapper<T>**接口提供了很多方法，Mapper接口的实现类是(动态代理方式 )动态生成的

![微信截图_20210312180146](https://raw.githubusercontent.com/miemiehoho/blog/master/picture/2021/03/20210312180258.png)

### 启动类加**@MapperScan**注解

启动类需要添加 @MapperScan("mapper所在的包")，否则无法加载Mapper bean。

```java
package edu.gdut.learn;

import org.mybatis.spring.annotation.MapperScan;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
@MapperScan("edu.gdut.learn.core.mapper")
public class LearnApplication {

    public static void main(String[] args) {
        SpringApplication.run(LearnApplication.class, args);
    }

}
```

### 创建测试类

- 方法一

接口名>右键>Go To>Test![微信截图_20210312180517](https://raw.githubusercontent.com/miemiehoho/blog/master/picture/2021/03/20210312180833.png)

- 方法二

接口名>快捷键 Ctrl+Shift+T

![微信截图_20210312180626](https://raw.githubusercontent.com/miemiehoho/blog/master/picture/2021/03/20210312180837.png)

![微信截图_20210312181254](https://raw.githubusercontent.com/miemiehoho/blog/master/picture/2021/03/20210312182249.png)

**@SpringBootTest**注解：启动SpringBoot IOC容器

```java
package edu.gdut.learn.core.mapper;

import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;

import static org.junit.jupiter.api.Assertions.*;
@SpringBootTest
class UserMapperTest {

    @Autowired
    private UserMapper mapper;

    @Test
    void test(){
        mapper.selectList(null).forEach(System.out::println);
    }
}
```

### 运行测试类

![微信截图_20210312182223](https://raw.githubusercontent.com/miemiehoho/blog/master/picture/2021/03/20210312182325.png)

### 设置日志

- 在application.yml文件中加入如下代码，以便测试时在控制台打印出完整的SQL语句

```yaml
mybatis-plus:
  configuration:
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
```

- 运行测试类![微信截图_20210312183536](https://raw.githubusercontent.com/miemiehoho/blog/master/picture/2021/03/20210312183604.png)



## (二)常用注解



### @TableName

映射数据库表名

```java
package edu.gdut.learn.core.entity;

import com.baomidou.mybatisplus.annotation.TableName;
import lombok.Data;

@Data
@TableName(value = "user")
public class User {
    private Integer id;
    private String name;
    private Integer age;
}
```

### @TableID

映射主键

- value映射注解字段名
- type设置主键类型，主键的生成策略

```java
    /**
     * 数据库ID自增
     */
    AUTO(0),
    /**
     * 该类型为未设置主键类型(注解里等于跟随全局,全局里约等于 INPUT)
     */
    NONE(1),
    /**
     * 用户输入ID
     * <p>该类型可以通过自己注册自动填充插件进行填充</p>
     */
    INPUT(2),

    /* 以下3种类型、只有当插入对象ID 为空，才自动填充。 */
    /**
     * 分配ID (主键类型为number或string）,
     * 默认实现类 {@link com.baomidou.mybatisplus.core.incrementer.DefaultIdentifierGenerator}(雪花算法)
     *
     * @since 3.3.0
     */
    ASSIGN_ID(3),
    /**
     * 分配UUID (主键类型为 string)
     * 默认实现类 {@link com.baomidou.mybatisplus.core.incrementer.DefaultIdentifierGenerator}(UUID.replace("-",""))
     */
    ASSIGN_UUID(4),
    /**
     * @deprecated 3.3.0 please use {@link #ASSIGN_ID}
     */
    @Deprecated
    ID_WORKER(3),
    /**
     * @deprecated 3.3.0 please use {@link #ASSIGN_ID}
     */
    @Deprecated
    ID_WORKER_STR(3),
    /**
     * @deprecated 3.3.0 please use {@link #ASSIGN_UUID}
     */
    @Deprecated
    UUID(4);
```

| 值          | 描述                            |
| ----------- | ------------------------------- |
| AUTO        | 数据库自增                      |
| NONE        | MP set主键，雪花算法实现        |
| INPUT       | 需要开发者手动赋值              |
| ASSIGN_ID   | MP 分配ID,Long、integer、String |
| ASSIGN_UUID | 分配UUID，String                |

ASSIGN_ID MP自动赋值，采用雪花算法

ASSIGN_UUID 主键的数据类型必须是String，自动生成UUID进行赋值

### @TableField

 映射非主键字段

- value 映射字段名

- exist 表示是否为字符串字段，如果实体类的成员变量在数据库中没有对应的字段，则可以使用exist， 常用在VO、DTO
- select表示是否查询该字段
- fill 表示是否自动填充，将对象存入数据库的时候，由MP自动给某些字段赋值(比如创建订单信息时的 create_time、update_time)

![微信截图_20210312214743](https://raw.githubusercontent.com/miemiehoho/blog/master/picture/2021/03/20210312223500.png)

```java
/**
 * 字段填充策略枚举类
 *
 * <p>
 * 判断注入的 insert 和 update 的 sql 脚本是否在对应情况下忽略掉字段的 if 标签生成
 * <if test="...">......</if>
 * 判断优先级比 {@link FieldStrategy} 高
 * </p>
 *
 * @author hubin
 * @since 2017-06-27
 */
public enum FieldFill {
    /**
     * 默认不处理
     */
    DEFAULT,
    /**
     * 插入时填充字段
     */
    INSERT,
    /**
     * 更新时填充字段
     */
    UPDATE,
    /**
     * 插入和更新时填充字段
     */
    INSERT_UPDATE
}
```

​	**创建处理器**

```java
package edu.gdut.learn.core.handler;

import com.baomidou.mybatisplus.core.handlers.MetaObjectHandler;
import org.apache.ibatis.reflection.MetaObject;
import org.springframework.stereotype.Component;

import java.util.Date;

@Component
public class MyMetaObjectHandler implements MetaObjectHandler {
    @Override
    public void insertFill(MetaObject metaObject) {
        this.setFieldValByName("createTime",new Date(),metaObject);
        this.setFieldValByName("updateTime",new Date(),metaObject);
    }

    @Override
    public void updateFill(MetaObject metaObject) {
        this.setFieldValByName("updateTime",new Date(),metaObject);
    }
}

```

```java
package edu.gdut.learn.core.entity;

import com.baomidou.mybatisplus.annotation.*;
import lombok.Data;

import java.util.Date;

@Data
@TableName(value = "user")
public class User {
    @TableId
    private Integer id;
    @TableField(value = "name",select = false)
    private String name;
    private Integer age;
    @TableField(exist = false)
    private String gender;
    @TableField(fill = FieldFill.INSERT)
    private Date createTime;
    @TableField(fill = FieldFill.INSERT_UPDATE)
    private Date updateTime;
}
```

**测试**

```java
    @Test
    void update(){
        User user = mapper.selectById(7);
        user.setName("梅西");
        mapper.updateById(user);
    }
```



### @Version

- 标记乐观锁，通过version字段来保证数据的安全性，当修改数据的时候，会以version作为条件，当条件成立的时候才会修改成功。

**例：**

version = 1

线程1：update ... set version = 2 where version = 1

线程2：update ... set version = 2 where version = 1

**过程**

1. ​	数据库表添加version字段，默认值为1
2. 实体类添加version成员变量，并且添加@version注解

```java
package edu.gdut.learn.core.entity;

import com.baomidou.mybatisplus.annotation.*;
import lombok.Data;

import java.util.Date;

@Data
@TableName(value = "user")
public class User {
    @TableId
    private Long id;
    @TableField(value = "name",select = false)
    private String name;
    private Integer age;
    @TableField(exist = false)
    private String gender;
    @TableField(fill = FieldFill.INSERT)
    private Date createTime;
    @TableField(fill = FieldFill.INSERT_UPDATE)
    private Date updateTime;
    @Version
    private Integer version;
}
```

   3.注册配置类 **MPConfig**

```java
package edu.gdut.learn.core.config;

import com.baomidou.mybatisplus.extension.plugins.OptimisticLockerInterceptor;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration//配置类注解
public class MPConfig {

    @Bean//把实例化注解注入到IOC中
    public OptimisticLockerInterceptor optimisticLockerInterceptor(){
        return new OptimisticLockerInterceptor();
    }
}
```

​    4.测试

​			**操作一：**

![微信截图_20210312231052](https://raw.githubusercontent.com/miemiehoho/blog/master/picture/2021/03/20210312231145.png)

![微信截图_20210312231449](https://raw.githubusercontent.com/miemiehoho/blog/master/picture/2021/03/20210312231509.png)

​			**操作二：**

![微信截图_20210312231957](https://raw.githubusercontent.com/miemiehoho/blog/master/picture/2021/03/20210312232118.png)

![微信截图_20210312232054](https://raw.githubusercontent.com/miemiehoho/blog/master/picture/2021/03/20210312232138.png)



![微信截图_20210312232241](https://raw.githubusercontent.com/miemiehoho/blog/master/picture/2021/03/20210312232259.png)



### @EnumValue

通用枚举类注解，将数据库字段映射成实体类的枚举类型成员变量

```java
package edu.gdut.learn.core.enums;

import com.baomidou.mybatisplus.annotation.EnumValue;

public enum StatusEnum {
    WORK(1,"上班"),
    REST(0,"休息");

    @EnumValue
    private Integer code;
    private String msg;

    StatusEnum(Integer code, String msg) {
        this.code = code;
        this.msg = msg;
    }
}
```

```java
package edu.gdut.learn.core.entity;

import com.baomidou.mybatisplus.annotation.*;
import edu.gdut.learn.core.enums.StatusEnum;
import lombok.Data;

import java.util.Date;

@Data
@TableName(value = "user")
public class User {
    @TableId
    private Long id;
    @TableField(value = "name",select = false)
    private String name;
    private Integer age;
    @TableField(exist = false)
    private String gender;
    @TableField(fill = FieldFill.INSERT)
    private Date createTime;
    @TableField(fill = FieldFill.INSERT_UPDATE)
    private Date updateTime;
    @Version
    private Integer version;
    private StatusEnum status;
    //status 名字要与数据库字段名status匹配或加注解
//    @TableField(value = "status")
//    private StatusEnum statusEnum;
}
```

- 配置**application.yml**

```yaml
spring:
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/learn?useUnicode=true&characterEncoding=utf8
    username: root
    password: root
mybatis-plus:
  configuration:
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
  type-enums-package: edu.gdut.learn.core.enums
```

- 测试

![微信截图_20210313000844](https://raw.githubusercontent.com/miemiehoho/blog/master/picture/2021/03/20210313001049.png)

- 实现接口也可以完成映射

```java
package edu.gdut.learn.core.enums;

import com.baomidou.mybatisplus.annotation.EnumValue;
import com.baomidou.mybatisplus.core.enums.IEnum;

public enum AgeEnum implements IEnum<Integer> {
    ONE(1,"一岁"),
    TWO(2,"两岁"),
    THREE(3,"三岁");

    private Integer code;
    private String msg;

    AgeEnum(Integer code, String msg) {
        this.code = code;
        this.msg = msg;
    }

    @Override
    public Integer getValue() {
        return this.code;
    }
}
```

```java
package edu.gdut.learn.core.entity;

import com.baomidou.mybatisplus.annotation.*;
import edu.gdut.learn.core.enums.AgeEnum;
import edu.gdut.learn.core.enums.StatusEnum;
import lombok.Data;

import java.util.Date;

@Data
@TableName(value = "user")
public class User {
    @TableId
    private Long id;
    @TableField(value = "name",select = false)
    private String name;
    private AgeEnum age;
    @TableField(exist = false)
    private String gender;
    @TableField(fill = FieldFill.INSERT)
    private Date createTime;
    @TableField(fill = FieldFill.INSERT_UPDATE)
    private Date updateTime;
    @Version
    private Integer version;
    private StatusEnum status;
    //status 名字要与数据库字段名status匹配或加注解
//    @TableField(value = "status")
//    private StatusEnum statusEnum;
}
```

- 测试

![微信截图_20210313002503](https://raw.githubusercontent.com/miemiehoho/blog/master/picture/2021/03/20210313002605.png)

- **update**数据

  **update**数据时也只需要操作枚举类型就可以了

  ```java
      @Test
      void update(){
          User user1 = mapper.selectById(7);
          user1.setAge(AgeEnum.ONE);
          mapper.updateById(user1);
      }
  ```

  





### @TableLogic

映射逻辑删除

1. ​	数据表添加deleted字段
2. 实体类添加注解

```java
package edu.gdut.learn.core.entity;

import com.baomidou.mybatisplus.annotation.*;
import edu.gdut.learn.core.enums.AgeEnum;
import edu.gdut.learn.core.enums.StatusEnum;
import lombok.Data;

import java.util.Date;

@Data
@TableName(value = "user")
public class User {
    @TableId
    private Long id;
    @TableField(value = "name", select = false)
    private String name;
    private AgeEnum age;
    @TableField(exist = false)
    private String gender;
    @TableField(fill = FieldFill.INSERT)
    private Date createTime;
    @TableField(fill = FieldFill.INSERT_UPDATE)
    private Date updateTime;
    @Version
    private Integer version;
    private StatusEnum status;
    //status 名字要与数据库字段名status匹配或加注解
    //@TableField(value = "status")
    //private StatusEnum statusEnum;
    @TableLogic
    private Integer deleted;
}
```

​	3.application.yml添加配置

```yaml
spring:
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/learn?useUnicode=true&characterEncoding=utf8
    username: root
    password: root
mybatis-plus:
  configuration:
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
  type-enums-package: edu.gdut.learn.core.enums
  global-config:
    db-config:
      logic-not-delete-value: 0
      logic-delete-value: 1
```

​	4.测试

![微信截图_20210313141334](https://raw.githubusercontent.com/miemiehoho/blog/master/picture/2021/03/20210313141356.png)



## (三)CURD



### 查询

```java
    @Test
    void select() {

        //查询
        //不加条件，全部查询
//        mapper.selectList(null);
        QueryWrapper queryWrapper = new QueryWrapper();
//        queryWrapper.eq("name","张三");
//        Map<String,Object> map = new HashMap<>();
//        map.put("name","张三");
//        map.put("age",1);
//        queryWrapper.allEq(map);
//        queryWrapper.lt("age",2);
//        queryWrapper.gt("age",2);
//        queryWrapper.ne("name","张三");
//        queryWrapper.ge("age",2);

        //模糊查询  like
//        queryWrapper.like("name","三");
//        queryWrapper.likeLeft("name","军");
//        queryWrapper.likeRight("name","张");

        //联合查询 inSQL
//        queryWrapper.inSql("id","select id from user where id < 6");
//        queryWrapper.inSql("age","select age from user where age > 5");


        //排序 orderby
//        queryWrapper.orderByAsc("age");
//        queryWrapper.orderByDesc("age");

        queryWrapper.orderByAsc("age");
        queryWrapper.having("id > 3");
        System.out.println(mapper.selectList(queryWrapper));
    }
```

```java
    @Test
    void select() {
//        System.out.println(mapper.selectById(7));
//        mapper.selectBatchIds(Arrays.asList(7,8,9)).forEach(System.out::println);
        //Map 只能做等值判断，逻辑判断需要使用 Wrapper
//        Map<String, Object> map = new HashMap<>();
//        map.put("id", 7);
//        mapper.selectByMap(map).forEach(System.out::println);

//        QueryWrapper wrapper = new QueryWrapper();
//        wrapper.gt("id",2);
//        System.out.println(mapper.selectCount(wrapper));

        //将查询的结果封装到MAP中
//        QueryWrapper wrapper = new QueryWrapper();
//        wrapper.gt("id", 2);
//        mapper.selectMaps(wrapper).forEach(System.out::println);
//        System.out.println("-----------------------");
//        mapper.selectList(wrapper).forEach(System.out::println);


        //分页查询
//        Page<User> page = new Page<>(2,2);
//
//        Page<User> result = mapper.selectPage(page,null);
//        System.out.println(result.getSize());
//        System.out.println(result.getTotal());
//        result.getRecords().forEach(System.out::println);


//        Page<Map<String,Object>> page = new Page<>(1,2);
//        mapper.selectMapsPage(page,null).getRecords().forEach(System.out::println);

//        mapper.selectObjs(null).forEach(System.out::println);

        QueryWrapper wrapper = new QueryWrapper();
        wrapper.eq("id",7);
        System.out.println(mapper.selectOne(wrapper));
    }
}
```

- 自定义SQL（多表关联查询）

```java
package edu.gdut.learn.core.entity;

import lombok.Data;

@Data
public class ProductVO {
    private Integer category;
    private Integer count;
    private String description;
    private Integer userId;
    private String userName;
}
```

```java
package edu.gdut.learn.core.mapper;

import com.baomidou.mybatisplus.core.mapper.BaseMapper;
import edu.gdut.learn.core.entity.ProductVO;
import edu.gdut.learn.core.entity.User;
import org.apache.ibatis.annotations.Select;

import java.util.List;

public interface UserMapper extends BaseMapper<User> {
    @Select("select p.*,u.name userName from product p,user u where p.user_id = u.id AND u.id = #{id};")
    List<ProductVO> productList(Integer id);
}
```

```java
    @Test
    void product(){
        mapper.productList(7).forEach(System.out::println);
    }
}
```



### 添加

```java
    @Test
    void save() {
        User user = new User();
        user.setAge(22);
        user.setName("迪迦");
        mapper.insert(user);
        System.out.println(user);
    }
```



### 删除

```java
    @Test
    void delete() {
//        mapper.deleteById(1);


//        mapper.deleteBatchIds(Arrays.asList(1,2));


//        QueryWrapper queryWrapper = new QueryWrapper();
//        queryWrapper.eq("age",14);
//        mapper.delete(queryWrapper);


        Map<String, Object> map = new HashMap<>();
        map.put("id", 3);
        mapper.deleteByMap(map);
    }
```



### 修改

```java
    @Test
    void update(){
//         User user1 = mapper.selectById(7);
//        user1.setAge(3);
//        mapper.updateById(user1);

        User user = mapper.selectById(8);
        user.setName("小红");
        QueryWrapper queryWrapper = new QueryWrapper();
        queryWrapper.eq("age",22);
        mapper.update(user,queryWrapper);
    }
```



## (四)MyBatisPlus 代码生成器



根据数据表自动生成实体类、Mapper、Service、ServiceImpl、Controller

- 导入依赖包

```groovy
    /* 代码生成器依赖 */
    compile group: 'com.baomidou', name: 'mybatis-plus-generator', version: '3.3.2'
    /* Velocity 引擎依赖 */
    compile group: 'org.apache.velocity', name: 'velocity-engine-core', version: '2.3'
```

velocity（默认）、Freemarker、Beetl

- 创建启动类

```java
package edu.gdut.learn;

import com.baomidou.mybatisplus.annotation.DbType;
import com.baomidou.mybatisplus.generator.AutoGenerator;
import com.baomidou.mybatisplus.generator.config.DataSourceConfig;
import com.baomidou.mybatisplus.generator.config.GlobalConfig;
import com.baomidou.mybatisplus.generator.config.PackageConfig;
import com.baomidou.mybatisplus.generator.config.StrategyConfig;
import com.baomidou.mybatisplus.generator.config.rules.NamingStrategy;

public class Generator {
    public static void main(String[] args) {
        //创建generator对象
        AutoGenerator autoGenerator = new AutoGenerator();

        //数据源
        DataSourceConfig dataSourceConfig = new DataSourceConfig();
        dataSourceConfig.setDbType(DbType.MYSQL);
        dataSourceConfig.setUrl("jdbc:mysql://localhost:3306/learn?useUnicode=true&characterEncoding=utf8");
        dataSourceConfig.setUsername("root");
        dataSourceConfig.setPassword("root");
        dataSourceConfig.setDriverName("com.mysql.cj.jdbc.Driver");
        autoGenerator.setDataSource(dataSourceConfig);

        //全局配置
        GlobalConfig globalConfig = new GlobalConfig();
        globalConfig.setOutputDir(System.getProperty("user.dir")+"/src/main/java");
        globalConfig.setOpen(false);
        globalConfig.setAuthor("miemiehoho");
        globalConfig.setServiceName("%sService");
        autoGenerator.setGlobalConfig(globalConfig);

        //包信息
        PackageConfig packageConfig = new PackageConfig();
        packageConfig.setParent("edu.gdut.learn");
        packageConfig.setModuleName("generator");
        packageConfig.setController("controller");
        packageConfig.setService("service");
        packageConfig.setServiceImpl("service.impl");
        packageConfig.setMapper("mapper");
        packageConfig.setEntity("entity");
        autoGenerator.setPackageInfo(packageConfig);

        //配置策略
        StrategyConfig strategyConfig = new StrategyConfig();
        strategyConfig.setEntityLombokModel(true);
        strategyConfig.setColumnNaming(NamingStrategy.underline_to_camel);
        autoGenerator.setStrategy(strategyConfig);

        autoGenerator.execute();
    }
}
```



## (五)部署上线



- Controller层

```java
package edu.gdut.learn.generator.controller;


import edu.gdut.learn.generator.service.UserService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;

import org.springframework.stereotype.Controller;
import org.springframework.web.servlet.ModelAndView;

/**
 * <p>
 *  前端控制器
 * </p>
 *
 * @author miemiehoho
 * @since 2021-03-13
 */
@Controller
@RequestMapping("/generator/user")
public class UserController {

    @Autowired
    private UserService userService;

    @GetMapping("/index")
    public ModelAndView index(){
        ModelAndView modelAndView = new ModelAndView();
        modelAndView.setViewName("index");
        modelAndView.addObject("list",userService.list());
        return modelAndView;
    }

}
```

- 编写配置文件


```yaml
spring:
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/learn?useUnicode=true&characterEncoding=utf8
    username: root
    password: root
  thymeleaf:
    prefix: classpath:/templates/
    suffix: .html
mybatis-plus:
  configuration:
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
  type-enums-package: edu.gdut.learn.core.enums
  global-config:
    db-config:
      logic-not-delete-value: 0
      logic-delete-value: 1
```

- **resources/templates**目录下创建**html**文件

```html
<!DOCTYPE html>
<html lang="en">
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <table>
        <tr th:each="user:${list}">
            <td th:text="${user.id}"></td>
            <td th:text="${user.name}"></td>
            <td th:text="${user.age}"></td>
        </tr>
    </table>
</body>
</html>
```

### 项目打包

![微信截图_20210313202246](https://raw.githubusercontent.com/miemiehoho/blog/master/picture/2021/03/20210313202616.png)

![微信截图_20210313202326](https://raw.githubusercontent.com/miemiehoho/blog/master/picture/2021/03/20210313202626.png)

### 启动项目

- 进入jar包目录，在CMD命令行输入如下命令，启动项目


```java
java -jar learn-0.0.1-SNAPSHOT.jar
```

- 打开浏览器，输入http://localhost:8080/generator/user/index，可以看到项目启动成功


![微信截图_20210313202527](https://raw.githubusercontent.com/miemiehoho/blog/master/picture/2021/03/20210313202549.png)