# SpringBoot
## 01 SpringBoot的基本使用

- 创建Maven工程
- 引入springboot依赖
- 添加spring-boot启动器web依赖
- 编写启动类
	- 加上`@SpringBootApplication` 注解
	- main方法 添加 `SpringApplication.run(Application.class,args);`
- 编写Controller直接访问  （返回值为字符串..）
- 测试 

pom.xml
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.max</groupId>
    <artifactId>DelSpringBootTest</artifactId>
    <version>1.0-SNAPSHOT</version>

    <!--SpringBoot依赖-->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.3.RELEASE</version>
    </parent>


    <dependencies>

    <!--添加启动器后 web工程常用的依赖会自动帮你引入-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <!--热部署-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
        </dependency>

    </dependencies>


</project>
```
## 02 SpringBootJava配置类的编写

- SpringBoot当中java配置主要靠java类和一些注解代替xml
	- `@Configuration`：声明一个类作为配置类，代替xml文件
	- `@Bean`：声明在方法上，将方法的返回值加入Bean容器，代替\<bean\>标签
	- `@value`：属性注入
	- `@PropertySource`：指定外部属性文件，

- 引入druid和lombok依赖
```xml
	<!--druid-->
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid</artifactId>
            <version>1.1.9</version>
        </dependency>

        <!--lombok-->
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
        </dependency>
```


- 配置druid数据源
	- 创建配置类,引入jdbc.properties 
```propertie
jdbc.driver=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/springboot?characterEncoding=utf-8
jdbc.username=root
jdbc.password=li767787498
```
- 配置类
![](SpringBoot.assets/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202019-05-09%20%E4%B8%8B%E5%8D%883.26.49.png)
- 测试 采用Debug查
![](SpringBoot.assets/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202019-05-09%20%E4%B8%8B%E5%8D%883.27.13.png)
![](SpringBoot.assets/%E6%97%A0%E6%A0%87%E9%A2%98.png)

## 03 SpringBoot属性注入
### 01单独放到一个类当中
- 把properties名称改为application.properties
- 创建一个类 名为JDBCPropertis
- 把所有属性添加到类当中
- 在类上添加注解
 ![](SpringBoot.assets/%E6%97%A0%E6%A0%87%E9%A2%98-1.png)
- 在config中直接使用
![](SpringBoot.assets/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202019-05-09%20%E4%B8%8B%E5%8D%883.48.21.png)
- 也可以使用属性注入方式,也可以使用构造方法的形式
![](SpringBoot.assets/%E6%97%A0%E6%A0%87%E9%A2%98-2.png)

### 02直接注入
 创建application.properties属性文件
![](SpringBoot.assets/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202019-05-09%20%E4%B8%8B%E5%8D%883.57.33.png)
直接在方法上配置注解形式
![](SpringBoot.assets/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202019-05-09%20%E4%B8%8B%E5%8D%883.58.33.png)


### 属性文件使用yaml文件方式
- 3.属性文件使用yaml文件方式 
	- 配置文件除了使用application.properties类型,还可以是后缀名为:.yml或.yaml的类型
	- 也会自动的加载
	- YAML是一种简洁的非标记语言,是以数据为中心, 使用空白缩进,分行组织数据,
	- 从而使得表示更加简洁易读
![](SpringBoot.assets/%E6%97%A0%E6%A0%87%E9%A2%98-3.png)

- 注意事项:
	- 如果有两个配置文件一个.properties和一个.yml
	-  会取两个并集,如果有相同的名称,会以properties为主







## 04 属性文件使用yml文件方式

注意名字如果为 Application.yml 是自动扫描的
![](SpringBoot.assets/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202019-05-09%20%E4%B8%8B%E5%8D%884.08.57.png)


![](SpringBoot.assets/%E6%97%A0%E6%A0%87%E9%A2%98-4.png)


```yaml
jdbc:
  driverClassName: com.mysql.jdbc.Driver
  url: jdbc:mysql://localhost:3306/springboot?characterEncoding=utf-8
  username: root
  password: li767787498
  arrPros: 1,2,3,4
  listPros:
    - list1
    - list2
  mapPros:
    Key1: value1
    Key2: value2
    Key3: value3

  listMapPro:
    - mapKey1: mapKey1
      mapKey2: mapKey2
    - mapKey2: mapKey3
      mapKey4: mapKey4

  Maplist:
    key1:
      - list1
      - list2
      - list3
    key2:
      - list3
      - list4
      - list5


```


```java
package com.max.config;

import lombok.Data;
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.stereotype.Component;

import java.util.List;
import java.util.Map;

@Component
@ConfigurationProperties(prefix = "jdbc")
@Data
public class MyProperties {
    private String url;

    private String driverClassName;

    private String username;

    private String password;

    private String[] arrPros;

    private List listPros;

    private Map<String,String> mapPros;

    private List<Map<String,String>> listMapPro;

    private Map<String,List> Maplist;

}

```
## 05 SpringBoot单元测试

- 引入依赖
![](SpringBoot.assets/%E6%97%A0%E6%A0%87%E9%A2%98-5.png)

- 使用：
	- @RunWith(SpringRunner.class)
	- @SpringBootTest(classes = Application.class)
	![](SpringBoot.assets/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202019-05-09%20%E4%B8%8B%E5%8D%884.52.12.png)

## 06 SpringBoot3个比较重点的注解

- 查看@SpringBootApplication源码
![](SpringBoot.assets/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202019-05-09%20%E4%B8%8B%E5%8D%885.12.12.png)

### @SpringBootConfiguration
1. 在这个注解上面，又有一个@Configuration注解
	2. 这个注解的作用就是声明当前类是一个配置类
	3. 然后Spring会自动扫描到添加了@Configuration的类
	4. 读取其中的配置信息
	5. 而@SpringBootConfiguration是来声明当前类是SpringBoot应用的配置类，项目中只能有一个。所以一般我们无需自己添加。
![](SpringBoot.assets/%E6%97%A0%E6%A0%87%E9%A2%98-6.png)



### @EnableAutoConfiguration
1. 开启自动配置
	2. 告诉SpringBoot基于所添加的依赖，去“猜测”你想要如何配置Spring。
	3. 比如我们引入了spring-boot-starter-web，而这个启动器中帮我们添加了tomcat、SpringMVC的依赖
	4. 此时自动配置就知道你是要开发一个web应用，所以就帮你完成了web及SpringMVC的默认配置了！
	5. SpringBoot内部对大量的第三方库或Spring内部库进行了默认配置
	6. 这些配置是否生效，取决于我们是否引入了对应库所需的依赖
	7. 如果有那么默认配置就会生效
	8. 我们使用SpringBoot构建一个项目，只需要引入所需框架的依赖，配置就可以交给SpringBoot处理了

### @ComponentScan
1. 配置组件扫描的指令
	2. 提供了类似与\<context:component-scan\>标签的作用
	3. 通过basePackageClasses或者basePackages属性来指定要扫描的包。
	4. 如果没有指定这些属性，那么将从声明这个注解的类所在的包开始，扫描包及子包
	5. 而我们的@SpringBootApplication注解声明的类就是main函数所在的启动类，
	6. 因此扫描的包是该类所在包及其子包。因此，一般启动类会放在一个比较前的包目录中。



## 07 ⚠️搭建SSM应用
添加所需pom依赖

### 配置spring-mvc

端口配置：
1. server:    #端口
2. port: 80

静态资源访问  默认情况下,它是在以下目录当中进行查找静态资源文件：
![](SpringBoot.assets/%E6%97%A0%E6%A0%87%E9%A2%98-7.png)
 所以要自己手动在resource文件当中创建静态资源目录

日志级别：
- logging:
-  level:
- com.max: debug
- path: "/Users/lilei/Desktop/ssm/smm.log"

### 配置拦截器：
> 详情 SpringMVC 22 拦截器
- 创建一个类使用@Configuration 注解标记为配置类
- 覆盖 WebMvcConfigurer 接口里的 addInterceptors 方法
![](SpringBoot.assets/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202019-05-05%20%E4%B8%8A%E5%8D%8810.26.38.png)

其他：
- 注解扫描-自动配置完成
- 包扫描-内部自带了包扫描
- 视图解析器-springboot当中不支持jsp,无需配置

### 配置JDBC：
1. 添加jdbc启动器 pom
1. 添加Mysql数据库驱动 pom
2. 配置连接参数


### 配置mybatis：
1. 添加Mybatis启动器  采用通用Mapper 内置了mybatis启动器
	2. 别名 `type-aliases-package: com.max.domain`
	3. 配置mapper文件地址  `mapper-locations: mapper/\*.xml`
	4. mapper接口扫描  在main方法上添加`@MapperScan("包名")` 注解  注意包：`import tk.mybatis.spring.annotation.MapperScan;` 下的
		 

文件：
pom.xml
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.max</groupId>
    <artifactId>DelSpringBootSSM</artifactId>
    <version>1.0-SNAPSHOT</version>

    <!--SpringBoot依赖-->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.3.RELEASE</version>
    </parent>

    <dependencies>

        <!--添加启动器后 web工程常用的依赖会自动帮你引入-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <!--热部署-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
        </dependency>

        <!--druid-->
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid</artifactId>
            <version>1.1.9</version>
        </dependency>

        <!--lombok-->
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
        </dependency>

        <!--单元测试-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
        </dependency>

        <!--添加jdbc启动器-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-jdbc</artifactId>
        </dependency>
        <!--Mysql数据库驱动-->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
        </dependency>

        <!--通用Mapper-->
        <dependency>
            <groupId>tk.mybatis</groupId>
            <artifactId>mapper-spring-boot-starter</artifactId>
            <version>2.1.5</version>
        </dependency>


    </dependencies>



</project>
```

application.yml
```yaml
server:
  port: 80

spring:
  mvc:
    servlet:
      load-on-startup: 1
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/springboot?characterEncoding=utf-8
    username: root
    password: li767787498


logging:
  level:
    com.max: debug
  path: "/Users/lilei/Desktop/ssm/smm.log"


mybatis:
  type-aliases-package: com.max.domain
  mapper-locations: classpath:mapper/*.xml
  configuration:
    map-underscore-to-camel-case: true



```

HeroMapper.xml
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
<mapper namespace="com.max.mapper.HeroMapper">

    <select id="getAllHero" resultType="com.max.domain.Hero">
        select * from tb_hero
    </select>
</mapper>
```

![结构图](SpringBoot.assets/%E6%97%A0%E6%A0%87%E9%A2%98-8.png)












## 08 通用mapper

1.引入启动器
```xml
    <dependency>
            <groupId>tk.mybatis</groupId>
            <artifactId>mapper-spring-boot-starter</artifactId>
            <version>2.1.5</version>
        </dependency><dependency>
            <groupId>tk.mybatis</groupId>
            <artifactId>mapper-spring-boot-starter</artifactId>
            <version>2.1.5</version>
        </dependency>
```

2.使用时, 直接在接口上继续Mapper\<实体类\>
![](SpringBoot.assets/%E6%97%A0%E6%A0%87%E9%A2%98-9.png)

3.在Pojo中指定表名与相关主键属性
![](SpringBoot.assets/%E6%97%A0%E6%A0%87%E9%A2%98-10.png)

![](SpringBoot.assets/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202019-05-10%20%E4%B8%8A%E5%8D%889.20.51.png)
