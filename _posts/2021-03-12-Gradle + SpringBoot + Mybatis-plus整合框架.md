---
layout: post
title: Gradle + SpringBoot + Mybatis-plus整合框架
date: 2021-03-12
Author: miemiehoho 
tags: [gradle, Mybatis]
comments: true
toc: true
---





## 新建Gradle项目

 - 新建springboot项目

![](https://raw.githubusercontent.com/miemiehoho/blog/master/picture/2021/03/20210312135628.png)

![](https://raw.githubusercontent.com/miemiehoho/blog/master/picture/2021/03/20210312144355.png)



 - 运行项目

运行DemoApplication文件

![](https://raw.githubusercontent.com/miemiehoho/blog/master/picture/2021/03/20210312144431.png)

## gradle整合mybatis plus
- 添加依赖

`mybatis-plus`启动依赖，添加至 `dependencies{...}`中

```
    /* mybatis-plus 启动依赖 */
    compile group: 'com.baomidou', name: 'mybatis-plus-boot-starter', version: '3.3.2'
```

- 配置application.properties文件

```
# mysql 数据源配置
spring.datasource.url=jdbc:mysql://localhost:3306/paddle?serverTimezone=Asia/Shanghai&useSSL=false&useUnicode=true&characterEncoding=utf8&characterSetResults=utf8
spring.datasource.username=root
spring.datasource.password=root
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
```

- 运行项目

## 创建代码生成器

- 添加依赖包

```
    /* 代码生成器依赖 */
    compile group: 'com.baomidou', name: 'mybatis-plus-generator', version: '3.3.2'
    /* Velocity 引擎依赖 */
    compile group: 'org.apache.velocity', name: 'velocity-engine-core', version: '2.3'
    /* freemarker 引擎依赖 */
    compile group: 'org.freemarker', name: 'freemarker', version: '2.3.30'
```

完整**build.gradle**配置

```groovy
plugins {
    id 'org.springframework.boot' version '2.4.3'
    id 'io.spring.dependency-management' version '1.0.11.RELEASE'
    id 'java'
}

group = 'com.example'
version = '0.0.1-SNAPSHOT'
sourceCompatibility = '11'

configurations {
    compileOnly {
        extendsFrom annotationProcessor
    }
}

repositories {
    //指定阿里云镜像
    maven {
        url 'http://maven.aliyun.com/nexus/content/groups/public/'
    }
    maven { url 'https://oss.sonatype.org/content/repositories/snapshots/' }
    mavenCentral()
}

dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-web'
    compileOnly 'org.projectlombok:lombok'
    developmentOnly 'org.springframework.boot:spring-boot-devtools'
    runtimeOnly 'mysql:mysql-connector-java'
    annotationProcessor 'org.projectlombok:lombok'
    testImplementation 'org.springframework.boot:spring-boot-starter-test'

    /* mybatis-plus 启动依赖 */
    compile group: 'com.baomidou', name: 'mybatis-plus-boot-starter', version: '3.3.2'

    /* 代码生成器依赖 */
    compile group: 'com.baomidou', name: 'mybatis-plus-generator', version: '3.3.2'
    /* Velocity 引擎依赖 */
    compile group: 'org.apache.velocity', name: 'velocity-engine-core', version: '2.3'
    /* freemarker 引擎依赖 */
    compile group: 'org.freemarker', name: 'freemarker', version: '2.3.30'

}

test {
    useJUnitPlatform()
}
```



- 创建**MpGenerator.java**配置文件

**数据源配置**

```java
        dsc.setDbType(DbType.MYSQL);//数据库类型
        dsc.setUrl("jdbc:mysql://localhost:3306/paddle?serverTimezone=Asia/Shanghai&useSSL=false&useUnicode=true&characterEncoding=utf8&characterSetResults=utf8&allowPublicKeyRetrieval=true");
        dsc.setDriverName("com.mysql.cj.jdbc.Driver");//驱动名称
        dsc.setUsername("root");//数据库连接用户名
        dsc.setPassword("root");//数据库连接密码
```

**包配置**

```java
        pc.setParent("com.example.demo.core");//com.example.demo
```

**完整代码**

```java
package com.example.demo;

import java.util.Scanner;

import com.baomidou.mybatisplus.annotation.DbType;
import com.baomidou.mybatisplus.core.exceptions.MybatisPlusException;
import com.baomidou.mybatisplus.core.toolkit.StringUtils;
import com.baomidou.mybatisplus.generator.AutoGenerator;
import com.baomidou.mybatisplus.generator.InjectionConfig;
import com.baomidou.mybatisplus.generator.config.DataSourceConfig;
import com.baomidou.mybatisplus.generator.config.GlobalConfig;
import com.baomidou.mybatisplus.generator.config.PackageConfig;
import com.baomidou.mybatisplus.generator.config.StrategyConfig;
import com.baomidou.mybatisplus.generator.config.rules.NamingStrategy;
import com.baomidou.mybatisplus.generator.engine.FreemarkerTemplateEngine;

/**
 * 代码生成器展示
 */
public class MpGenerator {

    /**
     * <p>
     * 读取控制台内容
     * </p >
     */
    public static String scanner(String tip) {
        Scanner scanner = new Scanner(System.in);
        StringBuilder help = new StringBuilder();
        help.append("请输入" + tip + "：");
        System.out.println(help.toString());
        if (scanner.hasNext()) {
            String ipt = scanner.next();
            if (StringUtils.isNotBlank(ipt)) {
                return ipt;
            }
        }
        throw new MybatisPlusException("请输入正确的" + tip + "！");
    }

    public static void main(String[] args) {
        // 代码生成器
        AutoGenerator mpg = new AutoGenerator();

        // 1. 全局配置
        GlobalConfig gc = new GlobalConfig();
        String projectPath = System.getProperty("user.dir"); //项目Project地址
        gc.setOutputDir(projectPath + "/src/main/java");  //输出地址
//        gc.setOutputDir("C:\\Users\\miemiehoho\\OneDrive\\源码\\IdeaProjects\\Mybatis-plus\\src\\main\\java");  //绝对路径
        gc.setFileOverride(false);  // 是否覆盖同名文件，默认是false
        gc.setOpen(false);//是否打开输出目录
        gc.setEnableCache(false);   // XML 二级缓存
        gc.setAuthor("miemiehoho");  //作者
        gc.setSwagger2(false);//开启Swagger2模式
        gc.setActiveRecord(true);   // ActiveRecord 模式，默认表名为类名的复数形式
        gc.setBaseResultMap(true);  // XML ResultMap 生成基本的 ResultMap
        gc.setBaseColumnList(true);    // XML ColumnList 生成基本的sql片段
        /* 自定义文件命名， %s 会自动填充表实体属性！ */
        gc.setEntityName("%s");//实体命名方式
        gc.setMapperName("%sDao");
        gc.setXmlName("%sMapper");
        gc.setServiceName("%sService");
        gc.setServiceImplName("%sServiceImpl");
        gc.setControllerName("%sController");
//        gc.setIdType(IdType.AUTO);  // 主键策略
        mpg.setGlobalConfig(gc);


        // 2. 数据源配置
        DataSourceConfig dsc = new DataSourceConfig();
        dsc.setDbType(DbType.MYSQL);//数据库类型
        dsc.setUrl("jdbc:mysql://localhost:3306/paddle?serverTimezone=Asia/Shanghai&useSSL=false&useUnicode=true&characterEncoding=utf8&characterSetResults=utf8&allowPublicKeyRetrieval=true");
        dsc.setDriverName("com.mysql.cj.jdbc.Driver");//驱动名称
        dsc.setUsername("root");//数据库连接用户名
        dsc.setPassword("root");//数据库连接密码
        mpg.setDataSource(dsc);


        // 3. 包配置
        PackageConfig pc = new PackageConfig();
        pc.setParent("com.example.demo.core");//com.example.demo
        mpg.setPackageInfo(pc);

        // 4. 自定义配置
        InjectionConfig cfg = new InjectionConfig() {
            @Override
            public void initMap() {
                // to do nothing
            }
        };


        // 5. 策略配置
        StrategyConfig strategy = new StrategyConfig();
        strategy.setNaming(NamingStrategy.underline_to_camel);  //表名生成策略 (驼峰)
        strategy.setColumnNaming(NamingStrategy.underline_to_camel);
        //strategy.setSuperEntityClass("你自己的父类实体,没有就不用设置!");
        strategy.setEntityLombokModel(false);    //以lombok注解生成实体类
        strategy.setRestControllerStyle(true);  //controller以RestFlue风格
        // 公共父类
        //strategy.setSuperControllerClass("你自己的父类控制器,没有就不用设置!");
        // 写于父类中的公共字段
        strategy.setSuperEntityColumns("id");
        strategy.setInclude(scanner("表名，多个英文逗号分割").split(","));
        strategy.setControllerMappingHyphenStyle(true);
        strategy.setTablePrefix("tbl_"); //表名前缀
        mpg.setStrategy(strategy);

//        // 如果模板引擎是 freemarker
//        String templatePath = "/templates/mapper.xml.ftl";
//        // 如果模板引擎是 velocity
//        // String templatePath = "/templates/mapper.xml.vm";
//
//        // 自定义输出配置
//        List<FileOutConfig> focList = new ArrayList<>();
//        // 自定义配置会被优先输出
//        focList.add(new FileOutConfig(templatePath) {
//            @Override
//            public String outputFile(TableInfo tableInfo) {
//                //由于projectPath 获取的是 项目地址，实际使用的为model，所以使用全地址替代
//                //return "C:\\Users\\miemiehoho\\OneDrive\\源码\\IdeaProjects\\Mybatis-plus\\src\\main\\resources\\mapper"
//                //+ "/" + tableInfo.getEntityName() + "Mapper" + StringPool.DOT_XML;
//                // 自定义输出文件名 ， 如果你 Entity 设置了前后缀、此处注意 xml 的名称会跟着发生变化！！
//                return projectPath + "/src/main/resources/mapper/"
//                        + "/" + tableInfo.getEntityName() + "Mapper" + StringPool.DOT_XML;
//            }
//        });
        /*
        cfg.setFileCreate(new IFileCreate() {
            @Override
            public boolean isCreate(ConfigBuilder configBuilder, FileType fileType, String filePath) {
                // 判断自定义文件夹是否需要创建
                checkDir("调用默认方法创建的目录，自定义目录用");
                if (fileType == FileType.MAPPER) {
                    // 已经生成 mapper 文件判断存在，不想重新生成返回 false
                    return !new File(filePath).exists();
                }
                // 允许生成模板文件
                return true;
            }
        });
        */
//        cfg.setFileOutConfigList(focList);
//        mpg.setCfg(cfg);

//        // 配置模板
//        TemplateConfig templateConfig = new TemplateConfig();
//
//
//        // 配置自定义输出模板
//        //指定自定义模板路径，注意不要带上.ftl/.vm, 会根据使用的模板引擎自动识别
//        // templateConfig.setEntity("templates/entity2.java");
//        // templateConfig.setService();
//        // templateConfig.setController();
//        //templateConfig.setXml(null);//设置为空代表不需要生成
//        mpg.setTemplate(templateConfig);
// set freemarker engine
        mpg.setTemplateEngine(new FreemarkerTemplateEngine());


        mpg.execute();
    }

}

```

- 运行代码生成器(MpGenerator.java)

  输入数据库表名后回车

  ![](https://raw.githubusercontent.com/miemiehoho/blog/master/picture/2021/03/20210312144454.png)

  ![](https://raw.githubusercontent.com/miemiehoho/blog/master/picture/2021/03/20210312144501.png)

- 为启动类添加**@MapperScan**注解

```java
@MapperScan(basePackages = "com.example.demo.core.mapper") //扫描Mapper接口(xxxDAO)
```

- 运行成功！

  ![](https://raw.githubusercontent.com/miemiehoho/blog/master/picture/2021/03/20210312144547.png)