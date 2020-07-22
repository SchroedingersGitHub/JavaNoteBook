# SpringDataJpa
## 01 JPA基本使用

### maven工程依赖 ：
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.max</groupId>
    <artifactId>jpa-day1</artifactId>
    <version>1.0-SNAPSHOT</version>

    <dependencies>
        <!--junit-->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
            <scope>test</scope>
        </dependency>

        <!-- hibernate对jpa的支持包 -->
        <dependency>
            <groupId>org.hibernate</groupId>
            <artifactId>hibernate-entitymanager</artifactId>
            <version>5.3.7.Final</version>
        </dependency>

        <!--c3p0-->
        <dependency>
            <groupId>c3p0</groupId>
            <artifactId>c3p0</artifactId>
            <version>0.9.1</version>
        </dependency>

        <!--log4j-->
        <dependency>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
            <version>1.2.17</version>
        </dependency>

        <!--jdbc-->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.13</version>
        </dependency>

        <!--lombok-->
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.18.8</version>
        </dependency>

    </dependencies>


</project>
```

### 实体类的创建 ：
```java
package com.max.domain;

import lombok.AllArgsConstructor;
import lombok.Builder;
import lombok.Data;
import lombok.NoArgsConstructor;

import javax.persistence.*;

/**
 *  客户实体类
 *  配置映射关系
 *
 *   1.实体类与表映射
 *  @Entity 声明实体类
 *  @Table 实体类与表映射
 *      name:数据库表名称
 *
 *   2.实体类属性与表字段映射
 *
 *
 */
@Data
@Builder
@AllArgsConstructor
@NoArgsConstructor
@Entity
@Table(name = "cst_customer")
public class Customer {

    /**
     *
     * @Id 声明主键配置
     * @GeneratedValue 主键生成策略
     *      strategy
     *          GenerationType.IDENTITY: 自增 mysql支持
     *          GenerationType.SEQUENCE: 序列 oracle支持
     *          GenerationType.TABLE: Jpa提供一种机制，通过一张数据库表的形式帮助我们自增
     *          GenerationType.AUTO: 由程序帮助我们选择自增策略
     *
     * @Column 配置属性和字段映射
     *       name数据库表字段名称
     */
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "cust_id")
    private Long custId;//客户主键

    @Column(name = "cust_name")
    private String custName;//客户名称

    @Column(name = "cust_source")
    private String custSource;//客户来源

    @Column(name = "cust_industry")
    private String custIndustry;//客户所属行业

    @Column(name = "cust_level")
    private String custLevel;//客户级别

    @Column(name = "cust_address")
    private String custAddress;//客户地址

    @Column(name = "cust_phone")
    private String custPhone;//客户联系方式


}

```

### 配置JPA的核心配置文件 ：
在java工程的src路径下创建一个名为META-INF的文件夹，在此文件夹下创建一个名为persistence.xml的配置文件：
![](SpringDataJpa.assets/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202019-07-03%20%E4%B8%8A%E5%8D%889.52.13.png)
```java
<?xml version="1.0" encoding="UTF-8"?>
<persistence xmlns="http://java.sun.com/xml/ns/persistence" version="2.0">


    <!--
    配置persistence-unit 节点
    name：结点名称
    transaction-type：事务管理的方式
                    JTA：分布式事务管理
                    RESOURCE_LOCAL：本地事务管理
    -->
    <persistence-unit name="myJpa" transaction-type="RESOURCE_LOCAL">
        <!--配置jpa的实现方式-->
        <provider>org.hibernate.jpa.HibernatePersistenceProvider</provider>

        <properties>
            <!--数据库信息
                用户名，javax.persistence.jdbc.user
                密码，  javax.persistence.jdbc.password
                驱动，  javax.persistence.jdbc.driver
                数据库地址   javax.persistence.jdbc.url
                -->
            <property name="javax.persistence.jdbc.user" value="root"/>
            <property name="javax.persistence.jdbc.password" value="li767787498"/>
            <property name="javax.persistence.jdbc.driver" value="com.mysql.jdbc.Driver"/>
            <property name="javax.persistence.jdbc.url" value="jdbc:mysql:///jpa"/>  <!--  ///代表 默认是3306 -->

            <!--可选配置：配置jpa实现方(hibernate)的配置信息
                显示sql           true|false
                自动创建更新表     hibernate.hbm2ddl.auto
                  create:   程序运行时创建数据表(如果有表，先删除再创建)
                  update:   程序运行时创建表(如果有表，不会创建表)
                  none:     不会创建表
            -->
            <property name="hibernate.show_sql" value="true"/>
            <property name="hibernate.hbm2ddl.auto" value="update"/>
            <property name="hibernate.format_sql" value="true"/>

        </properties>

    </persistence-unit>

</persistence>

```

### 通过抽取的 获取获取EntityManager对象
```java
package com.max.utils;

import javax.persistence.EntityManager;
import javax.persistence.EntityManagerFactory;
import javax.persistence.Persistence;

/**
 * 解决实体管理器工厂浪费资源的问题
 *      解决思路: 采用静态代码块的方式,当程序第一次访问时，创建一个公共的实体管理工厂对象
 *
 * 第一次访问getEntityManager方法时: 经过静态代码块创建EntityManagerFactory对象，再调用方法创建EntityManager对象
 * 第二次访问getEntityManager方法时: 直接通过调用好的EntityManagerFactory对象，创建EntityManager对象
 *
 */
public class JpaUtils {

    private static EntityManagerFactory factory;

    static {
        //1.加载配置文件 创建 EntityManagerFactory
        factory = Persistence.createEntityManagerFactory("myJpa");
    }

    //2.获取EntityManager对象
    public static EntityManager getEntityManager() {
        return factory.createEntityManager();
    }


}

```
## 02 常用注解的说明

`@Entity` :  指定当前类是实体类。

`@Table`: 指定实体类和表之间的对应关系。
 属性：  name-\>指定数据库表的名称

`@Id` : 指定当前字段是主键。

`@GeneratedValue`: 指定主键的生成方式:
 属性： strategy -\> 指定主键生成策略。
- GenerationType.IDENTITY: 自增 mysql支持
- GenerationType.SEQUENCE: 序列 oracle支持
- GenerationType.TABLE: Jpa提供一种机制，通过一张数据库表的形式帮助我们自增
- GenerationType.AUTO: 由程序帮助我们选择自增策略

`@Column` :  指定实体类属性和数据库表之间的对应关系:
属性：
- name：指定数据库表的列名称。
- unique：是否唯一  
- nullable：是否可以为空  
- inserttable：是否可以插入  
- updateable：是否可以更新  
- columnDefinition: 定义建表时创建此列的DDL  
- secondaryTable: 从表名。如果此列不建在主表上（默认建在主表），该属性定义该列所在从表的名字搭建开发环境[重点]


## 03 JPA的API介绍


### Persistence
 Persistence对象主要作用是用于获取`EntityManagerFactory`对象的。
通过调用该类的`createEntityManagerFactory`静态方法，根据配置文件中持久化单元名称创建`EntityManagerFactory`
![](SpringDataJpa.assets/%E6%97%A0%E6%A0%87%E9%A2%98.png)

### EntityManagerFactory
EntityManagerFactory 接口主要用来创建` EntityManager` 实例
于EntityManagerFactory 是一个`线程安全的对象`（即多个线程访问同一个EntityManagerFactory 对象不会有线程安全问题），并且EntityManagerFactory 的创建极其浪费资源，所以在使用JPA编程时，我们可以对EntityManagerFactory 的创建进行优化，只需要做到一个工程`只存在一个EntityManagerFactory` 即可
![](SpringDataJpa.assets/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202019-07-03%20%E4%B8%8A%E5%8D%8810.05.40.png)

### EntityManager
在 JPA 规范中, EntityManager是完成持久化操作的核心对象。实体类作为普通 java对象，`只有在调用 EntityManager将其持久化后才会变成持久化对象` EntityManager对象在一组实体类与底层数据源之间进行 O/R 映射的管理。它可以用来管理和更新 Entity Bean, 根椐主键查找 Entity Bean, 还可以通过JPQL语句查询实体。

方法说明：
- getTransaction : 获取事务对象
- persist ： 保存操作
- merge ： 更新操作
- remove ： 删除操作
- find/getReference ： 根据id查询

### EntityTransaction
在 JPA 规范中, EntityTransaction是完成事务操作的核心对象，对于EntityTransaction在我们的java代码中承接的功能比较简单
方法说明：
- begin：开启事务
- commit：提交事务
- rollback：回滚事务

## 04 使用JPA完成增删改查操作

![保存](SpringDataJpa.assets/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202019-07-03%20%E4%B8%8A%E5%8D%8810.15.27.png)

![修改](SpringDataJpa.assets/%E6%97%A0%E6%A0%87%E9%A2%98-1.png)

![删除](SpringDataJpa.assets/%E6%97%A0%E6%A0%87%E9%A2%98-2.png)

**查询**
### 根据id查询
![立即加载](SpringDataJpa.assets/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202019-07-03%20%E4%B8%8A%E5%8D%8810.22.44.png)

![懒加载](SpringDataJpa.assets/%E6%97%A0%E6%A0%87%E9%A2%98-3.png)


### JPQL查询
![查询全部](SpringDataJpa.assets/%E6%97%A0%E6%A0%87%E9%A2%98-4.png)

![排序查询](SpringDataJpa.assets/%E6%97%A0%E6%A0%87%E9%A2%98-5.png)

![统计查询](SpringDataJpa.assets/%E6%97%A0%E6%A0%87%E9%A2%98-6.png)

![分页查询](SpringDataJpa.assets/%E6%97%A0%E6%A0%87%E9%A2%98-7.png)

![条件查询](SpringDataJpa.assets/%E6%97%A0%E6%A0%87%E9%A2%98-8.png)





## 05 SpringDataJpa基本使用

### 引入pom文件
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.max</groupId>
    <artifactId>jpa-day2</artifactId>
    <version>1.0-SNAPSHOT</version>

    <properties>

        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>

        <spring.version>4.2.4.RELEASE</spring.version>
        <hibernate.version>5.0.7.Final</hibernate.version>
        <slf4j.version>1.6.6</slf4j.version>
        <log4j.version>1.2.12</log4j.version>
        <c3p0.version>0.9.1.2</c3p0.version>
        <mysql.version>5.1.6</mysql.version>
    </properties>


    <dependencies>
        <!-- junit单元测试 -->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
            <scope>test</scope>
        </dependency>

        <!-- spring beg -->
        <dependency>
            <groupId>org.aspectj</groupId>
            <artifactId>aspectjweaver</artifactId>
            <version>1.6.8</version>
        </dependency>

        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-aop</artifactId>
            <version>${spring.version}</version>
        </dependency>

        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>${spring.version}</version>
        </dependency>

        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context-support</artifactId>
            <version>${spring.version}</version>
        </dependency>

        <!-- spring对orm框架的支持包-->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-orm</artifactId>
            <version>${spring.version}</version>
        </dependency>

        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-beans</artifactId>
            <version>${spring.version}</version>
        </dependency>

        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-core</artifactId>
            <version>${spring.version}</version>
        </dependency>

        <!-- spring end -->

        <!-- hibernate beg -->
        <dependency>
            <groupId>org.hibernate</groupId>
            <artifactId>hibernate-core</artifactId>
            <version>${hibernate.version}</version>
        </dependency>
        <dependency>
            <groupId>org.hibernate</groupId>
            <artifactId>hibernate-entitymanager</artifactId>
            <version>${hibernate.version}</version>
        </dependency>
        <dependency>
            <groupId>org.hibernate</groupId>
            <artifactId>hibernate-validator</artifactId>
            <version>5.2.1.Final</version>
        </dependency>
        <!-- hibernate end -->

        <!-- c3p0 beg -->
        <dependency>
            <groupId>c3p0</groupId>
            <artifactId>c3p0</artifactId>
            <version>${c3p0.version}</version>
        </dependency>
        <!-- c3p0 end -->

        <!-- log end -->
        <dependency>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
            <version>${log4j.version}</version>
        </dependency>

        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-api</artifactId>
            <version>${slf4j.version}</version>
        </dependency>

        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-log4j12</artifactId>
            <version>${slf4j.version}</version>
        </dependency>
        <!-- log end -->

        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.16</version>
        </dependency>

        <!-- spring data jpa 的坐标-->
        <dependency>
            <groupId>org.springframework.data</groupId>
            <artifactId>spring-data-jpa</artifactId>
            <version>1.9.0.RELEASE</version>
        </dependency>

        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-test</artifactId>
            <version>${spring.version}</version>
        </dependency>

        <!-- el beg 使用spring data jpa 必须引入 -->
        <dependency>
            <groupId>javax.el</groupId>
            <artifactId>javax.el-api</artifactId>
            <version>2.2.4</version>
        </dependency>

        <dependency>
            <groupId>org.glassfish.web</groupId>
            <artifactId>javax.el</artifactId>
            <version>2.2.4</version>
        </dependency>
        <!-- el end -->
        <!--lombok-->
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.18.8</version>
        </dependency>

    </dependencies>

</project>
```

### Spring的配置 
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:jdbc="http://www.springframework.org/schema/jdbc" xmlns:tx="http://www.springframework.org/schema/tx"
       xmlns:jpa="http://www.springframework.org/schema/data/jpa" xmlns:task="http://www.springframework.org/schema/task"
       xsi:schemaLocation="
		http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd
		http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd
		http://www.springframework.org/schema/jdbc http://www.springframework.org/schema/jdbc/spring-jdbc.xsd
		http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx.xsd
		http://www.springframework.org/schema/data/jpa
		http://www.springframework.org/schema/data/jpa/spring-jpa.xsd">

    <!--spring 和 spring data jpa的配置-->

    <!-- 1.创建entityManagerFactory对象交给spring容器管理-->
    <bean id="entityManagerFactory" class="org.springframework.orm.jpa.LocalContainerEntityManagerFactoryBean">
        <property name="dataSource" ref="dataSource" />
        <!--配置的扫描的包（实体类所在的包） -->
        <property name="packagesToScan" value="com.max.domain" />
        <!-- jpa的实现厂家 -->
        <property name="persistenceProvider">
            <bean class="org.hibernate.jpa.HibernatePersistenceProvider"/>
        </property>

        <!--jpa的供应商适配器 -->
        <property name="jpaVendorAdapter">
            <bean class="org.springframework.orm.jpa.vendor.HibernateJpaVendorAdapter">
                <!--配置是否自动创建数据库表 -->
                <property name="generateDdl" value="false" />
                <!--指定数据库类型 -->
                <property name="database" value="MYSQL" />
                <!--数据库方言：支持的特有语法 -->
                <property name="databasePlatform" value="org.hibernate.dialect.MySQLDialect" />
                <!--是否显示sql -->
                <property name="showSql" value="true" />

            </bean>
        </property>

        <!--jpa的方言 ：高级的特性 -->
        <property name="jpaDialect" >
            <bean class="org.springframework.orm.jpa.vendor.HibernateJpaDialect" />
        </property>

    </bean>

    <!--2.创建数据库连接池 -->
    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <property name="user" value="root"/>
        <property name="password" value="li767787498"/>
        <property name="jdbcUrl" value="jdbc:mysql:///jpa"/>
        <property name="driverClass" value="com.mysql.jdbc.Driver"/>
    </bean>

    <!--3.整合SpringDataJpa-->
    <jpa:repositories base-package="com.max.dao" transaction-manager-ref="transactionManager"
                      entity-manager-factory-ref="entityManagerFactory"/>


    <!--4.配置事务管理器-->
    <bean id="transactionManager" class="org.springframework.orm.jpa.JpaTransactionManager">
        <property name="entityManagerFactory" ref="entityManagerFactory"/>

    </bean>

    <!--5.声明式事务-->


    <!--6.包扫描-->
     <context:component-scan base-package="com.max"/>

</beans>
```

### 创建Dao接口
接口继承 `JpaRepository<T, ID>,JpaSpecificationExecutor<T>`接口 
![](SpringDataJpa.assets/%E6%97%A0%E6%A0%87%E9%A2%98-9.png)

### 注入 调用
![](SpringDataJpa.assets/%E6%97%A0%E6%A0%87%E9%A2%98-10.png)

## 06 SpringDataJpa实现原理

### Dao接口是JDK代理对象
![](SpringDataJpa.assets/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202019-07-04%20%E4%B8%8A%E5%8D%8811.21.05.png)

### 声明一个代理对象
![](SpringDataJpa.assets/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202019-07-04%20%E4%B8%8A%E5%8D%8811.23.24.png)

### 生成代理对象 SimpleJpaRepository
![](SpringDataJpa.assets/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202019-07-04%20%E4%B8%8A%E5%8D%8811.25.09.png)

### SimpleJpaRepository 就是Dao接口的实现
![](SpringDataJpa.assets/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202019-07-04%20%E4%B8%8A%E5%8D%8811.27.16.png)

### 调用对应的SpringDataJpa实现方法    --\>  调用Jpa的方法
![](SpringDataJpa.assets/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202019-07-04%20%E4%B8%8A%E5%8D%8811.28.27.png)

### 流程图
![](SpringDataJpa.assets/03-springdatajpa%E7%9A%84%E8%BF%90%E8%A1%8C%E8%BF%87%E7%A8%8B.png)
1. 通过JdkDynamicAopProxy的invoke方法创建了一个动态代理对象
2. SimpleJpaRepository当中封装了JPA的操作（借助JPA的api完成数据库的CRUD）
3. 通过hibernate完成数据库操作（封装了jdbc）

## 07 SpringDataJpa简单CRUD

![查询](SpringDataJpa.assets/%E6%97%A0%E6%A0%87%E9%A2%98-11.png)

![保存与更新](SpringDataJpa.assets/%E6%97%A0%E6%A0%87%E9%A2%98-12.png)

![删除](SpringDataJpa.assets/%E6%97%A0%E6%A0%87%E9%A2%98-13.png)

![查询全部](SpringDataJpa.assets/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202019-07-04%20%E4%B8%8B%E5%8D%882.56.30.png)

## 08 查询方式 1.接口方法


1.借助接口中的定义好的方法完成查询
继承`JpaRepository<T,ID>`接口方法
![](SpringDataJpa.assets/%E6%97%A0%E6%A0%87%E9%A2%98-14.png)

继承`JpaSpecificationExecutor<T>`接口方法
![](SpringDataJpa.assets/%E6%97%A0%E6%A0%87%E9%A2%98-15.png)

注意的几个方法：
Exists ：
![](SpringDataJpa.assets/%E6%97%A0%E6%A0%87%E9%A2%98-16.png)

findOne 和  getOne： 
> getOne需要事务的支持
![](SpringDataJpa.assets/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202019-07-04%20%E4%B8%8B%E5%8D%887.43.33.png)


## 09 查询方式 2.注解JPQL方式

需要将JPQL语句配置到接口方法上:
1. 特有的查询：需要在dao接口上配置方法
2. 在新添加的方法上，使用注解的形式配置jpql查询语句
3. 注解 ： `@Query`  值： value= ‘查询语句’； nativeQuery = ’false(jpql) 默认‘ 、 true(sql) 本机查询 

### 普通查询
![](SpringDataJpa.assets/%E6%97%A0%E6%A0%87%E9%A2%98-17.png)

### 对于多个占位符参数
![](SpringDataJpa.assets/%E6%97%A0%E6%A0%87%E9%A2%98-18.png)


### 更新操作
1. 接口中需要加上@Modifying注解
2. 执行的需要加上事务 取消掉事务的回滚
![](SpringDataJpa.assets/%E6%97%A0%E6%A0%87%E9%A2%98-19.png)
![](SpringDataJpa.assets/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202019-07-04%20%E4%B8%8B%E5%8D%887.50.09.png)

## 10 查询方式 3.注解 SQL语句方式

- `@Query(value = “sql语句",nativeQuery = true)` 采用sql语句进行查询
-  **注意多个结果的返回值为Object数组**

![查询全部:](SpringDataJpa.assets/%E6%97%A0%E6%A0%87%E9%A2%98-20.png)

![条件查询:](SpringDataJpa.assets/%E6%97%A0%E6%A0%87%E9%A2%98-21.png)

## 11 查询方式 4.方法命名规则查询
![](SpringDataJpa.assets/11%20%E6%9F%A5%E8%AF%A2%E6%96%B9%E5%BC%8F%204.%E6%96%B9%E6%B3%95%E5%91%BD%E5%90%8D%E8%A7%84%E5%88%99%E6%9F%A5%E8%AF%A2.pdf)

**方法命名规则查询**
按照Spring Data JPA 定义的规则，查询方法以findBy开头，涉及条件查询时，条件的属性用条件关键字连接，要注意的是：条件属性首字母需大写。框架在进行方法名解析时，会先把方法名多余的前缀截取掉，然后对剩下部分进行解析。  

1.  findBy + 属性名称  根据属性名称进行完全匹配的形式查询
2.  findBy + 属性名称 + 查询条件(Like | Isnull)
3.  findBy + 属性名称 + 查询条件 + 多条件连接符(And | Or) + 属性名称 + 查询条件

![完全匹配的形式查询](SpringDataJpa.assets/%E6%97%A0%E6%A0%87%E9%A2%98-22.png)

![条件查询](SpringDataJpa.assets/%E6%97%A0%E6%A0%87%E9%A2%98-23.png)

![多条件查询](SpringDataJpa.assets/%E6%97%A0%E6%A0%87%E9%A2%98-24.png)




## 12 查询方式 5.Specifications动态查询

 有时我们在查询某个实体的时候，给定的条件是不固定的，这时就需要动态构建相应的查询语句，在Spring Data JPA中可以通过`JpaSpecificationExecutor`接口查询。相比JPQL,其优势是类型安全,更加的面向对象。
![](SpringDataJpa.assets/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202019-07-08%20%E4%B8%8B%E5%8D%884.39.28.png)

 对于JpaSpecificationExecutor，这个接口基本是围绕着`Specification接口`来定义的。我们可以简单的理解为，`Specification构造的就是查询条件`
![](SpringDataJpa.assets/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202019-07-08%20%E4%B8%8B%E5%8D%884.43.15.png)
CriteriaBuilder方法对应关系
![](SpringDataJpa.assets/%E6%97%A0%E6%A0%87%E9%A2%98-25.png)


![查询单个对象](SpringDataJpa.assets/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202019-07-08%20%E4%B8%8B%E5%8D%884.46.27.png)

![查询多个条件](SpringDataJpa.assets/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202019-07-08%20%E4%B8%8B%E5%8D%884.48.22.png)

![模糊查询 and 排序](SpringDataJpa.assets/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202019-07-08%20%E4%B8%8B%E5%8D%884.49.55.png)

![分页查询](SpringDataJpa.assets/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202019-07-08%20%E4%B8%8B%E5%8D%884.51.04.png)


## 13 OneToMany


在一对多关系中，我们习惯把一的一方称之为主表，把多的一方称之为从表。在数据库中建立一对多的关系，需要使用数据库的外键约束
在多的一方添加外键
![](SpringDataJpa.assets/%E6%97%A0%E6%A0%87%E9%A2%98-26.png)


### 配置关系
双方都可以维护外键
`多的一方`
![](SpringDataJpa.assets/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202019-07-09%20%E4%B8%8A%E5%8D%8810.37.37.png)
`一的一方`
![](SpringDataJpa.assets/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202019-07-09%20%E4%B8%8A%E5%8D%8810.40.26.png)

Customer.java
```java
package com.max.domain;

import lombok.*;

import javax.persistence.*;
import java.util.HashSet;
import java.util.Set;

@Builder
@AllArgsConstructor
@NoArgsConstructor
@Entity
@Table(name = "cst_customer")
@Getter@Setter@ToString(exclude = "linkmens")
public class Customer {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "cust_id")
    private Long custId;

    @Column(name = "cust_address")
    private String custAddress;

    @Column(name = "cust_industry")
    private String custIndustry;

    @Column(name = "cust_level")
    private String custLevel;

    @Column(name = "cust_name")
    private String custName;

    @Column(name = "cust_phone")
    private String custPhone;

    @Column(name = "cust_source")
    private String custSource;

    /**
     * 配置客户和联系人的关系(一对多)
     * 注解配置多表关系
     *      1.声明关系
     *          @OneToMany
     *              targetEntity: 对方对象的字节码
     *      2.配置外键
     *          @JoinColumn
     *              name:外键字段名称
     *              referencedColumnName:参照的主表的外键名称
     */

    @OneToMany(targetEntity = Linkman.class)
    @JoinColumn(name = "lkm_cust_id",referencedColumnName = "cust_id")
    private Set<Linkman> linkmens = new HashSet<>();



}

```

Linkman.java
```java
package com.max.domain;

import lombok.*;

import javax.persistence.*;

@Data
@Builder
@AllArgsConstructor
@NoArgsConstructor
@Entity
@Table(name = "cst_linkman")
public class Linkman {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "lkm_id")
    private Long lkmId; // 联系人编号(主键)

    @Column(name = "lkm_name")
    private String lkmName; // 联系人姓名

    @Column(name = "lkm_gender")
    private String lkmGender; // 联系人性别

    @Column(name = "lkm_phone")
    private String lkmPhone; // 联系人办公电话

    @Column(name = "lkm_mobile")
    private String lkmMobile; // 联系人手机

    @Column(name = "lkm_email")
    private String lkmEmail; // 联系人邮箱

    @Column(name = "lkm_position")
    private String lkmPosition; // 联系人职位

    @Column(name = "lkm_memo")
    private String lkmMemo; // 联系人备注

    /**
     * 配置联系人到客户的多对一关系
     *      使用注解配置多对一关系
     *          1.配置表的关系
     *              @ManyToOne 配置多对一的关系
     *                  targetEntity: 对方的字节码
     *          2.配置外键
     *              @JoinColumn
     *                  name:外键名称
     *                  referencedColumnName:当前的外键参照的主表的外键名称
     */

    @ManyToOne(targetEntity = Customer.class)
    @JoinColumn(name = "lkm_cust_id",referencedColumnName = "cust_id")
    private Customer customer;



}

```

### 保存操作
一的一方进行外键维护  ( 发送了两条insert语句 一条更新语句(更新外键))
![](SpringDataJpa.assets/%E6%97%A0%E6%A0%87%E9%A2%98-27.png)

多的一方进行外键维护(只发送了两条insert语句)
![](SpringDataJpa.assets/%E6%97%A0%E6%A0%87%E9%A2%98-28.png)

双方都进行维护(会有一条多余的update语句)
![](SpringDataJpa.assets/%E6%97%A0%E6%A0%87%E9%A2%98-29.png)

解决方法：在一的一方放弃外键维护权
![](SpringDataJpa.assets/%E6%97%A0%E6%A0%87%E9%A2%98-30.png)

### 级联添加删除
删除操作的说明如下：
`删除从表`数据：可以随时任意删除。
`删除主表`数据(有从表数据)：
1. 在默认情况下，它会把外键字段置为null，然后删除主表数据。如果在数据库的表结构上，外键字段有非空约束，默认情况就会报错了。
2. 如果配置了放弃维护关联关系的权利，则不能删除（与外键字段是否允许为null,没有关系）因为在删除时，它根本不会去更新从表的外键字段了。
3. 如果还想删除，使用级联删除引用

### 级联操作:
级联操作：`指操作一个对象同时操作它的关联对象`
使用方法：`只需要在操作主体的注解上配置cascade`
cascade:配置级联操作：
- CascadeType.MERGE    级联更新
- CascadeType.PERSIST    级联保存：
- CascadeType.REFRESH 级联刷新：
- CascadeType.REMOVE    级联删除：
- CascadeType.ALL          包含所有
![](SpringDataJpa.assets/%E6%97%A0%E6%A0%87%E9%A2%98-31.png)
`注意` :级联设置在当前操作的主体上



![添加](SpringDataJpa.assets/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202019-07-09%20%E4%B8%8B%E5%8D%883.26.13.png)

![（慎用）删除](SpringDataJpa.assets/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202019-07-09%20%E4%B8%8B%E5%8D%883.26.42.png)

## 14 ManyToMany

多对多的表关系建立靠的是中间表，其中用户表和中间表的关系是一对多，角色表和中间表的关系也是一对多
![](SpringDataJpa.assets/%E6%97%A0%E6%A0%87%E9%A2%98-32.png)

### 配置关系
双方都可以维护外键
`主动一方`
![](SpringDataJpa.assets/%E6%97%A0%E6%A0%87%E9%A2%98-33.png)
`被动选择一方`
![](SpringDataJpa.assets/%E6%97%A0%E6%A0%87%E9%A2%98-34.png)

User.java
```java
package com.max.domain;

import lombok.*;

import javax.persistence.*;
import java.util.HashSet;
import java.util.Set;

@Builder
@Getter@Setter
@AllArgsConstructor
@NoArgsConstructor
@Entity
@Table(name = "sys_user")
public class User {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "user_id")
    private Long userId;

    @Column(name = "user_name")
    private String userName;

    @Column(name = "user_age")
    private Integer age;

    /**
     * 配置多对多关系
     *      1.声明表关系
     *          @ManyToMany  多对多
     *              targetEntity：对方实体类字节码
     *      2.配置中间表
     *          @JoinTable
     *              name：中间表名称
     *              joinColumns：配置当前对象在中间表的外键
     *                  name：外键名
     *                  referencedColumnName：参照主表的主键名
     *
     *              inverseJoinColumns：配置对方对象在中间表的外键
     *                      丄️
     */
    @ManyToMany(targetEntity = Role.class,cascade = CascadeType.ALL)
    @JoinTable(name = "sys_user_role",
            // joinColumns 当前对象在中间表的外键
            joinColumns = {@JoinColumn(name = "sys_user_id",referencedColumnName = "user_id")},
            // inverseJoinColumns 对方对象在中间表的外键
            inverseJoinColumns = {@JoinColumn(name = "sys_role_id",referencedColumnName = "role_id")}
    )
    @Builder.Default
    private Set<Role> roles = new HashSet<>();


}

```

Role.java
```java
package com.max.domain;

import lombok.*;

import javax.persistence.*;
import java.util.HashSet;
import java.util.Set;

@Builder
@Getter@Setter
@AllArgsConstructor
@NoArgsConstructor
@Entity
@Table(name = "sys_role")
public class Role {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "role_id")
    private Long roleId;

    @Column(name = "role_name")
    private String roleName;

    @ManyToMany(targetEntity = User.class)
    @JoinTable(name = "sys_user_role",
            // joinColumns 当前对象在中间表的外键
            joinColumns = {@JoinColumn(name = "sys_role_id",referencedColumnName = "role_id")},
            // inverseJoinColumns 对方对象在中间表的外键
            inverseJoinColumns = {@JoinColumn(name = "sys_user_id",referencedColumnName = "user_id")}
    )
    @Builder.Default
    private Set<User> users = new HashSet<>();


}

```


### 保存操作
单项维护 (发送三条Insert 语句 保存成功)
![](SpringDataJpa.assets/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202019-07-09%20%E4%B8%8B%E5%8D%885.26.31.png)
双向维护(发送四条insert语句 抛出主键重复异常)
![](SpringDataJpa.assets/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202019-07-09%20%E4%B8%8B%E5%8D%885.31.16.png)
解决办法：在被动选择的一方放弃外键维护
![](SpringDataJpa.assets/%E6%97%A0%E6%A0%87%E9%A2%98-35.png)


## 级联添加删除
级联设置到当前操作的主体上
![](SpringDataJpa.assets/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202019-07-09%20%E4%B8%8B%E5%8D%885.41.51.png)

![保存](SpringDataJpa.assets/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202019-07-09%20%E4%B8%8B%E5%8D%885.43.28.png)

![删除（慎用）](SpringDataJpa.assets/%E6%97%A0%E6%A0%87%E9%A2%98-36.png)

## 15 多表之间配置总结

多表之间配置总结
![](SpringDataJpa.assets/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202019-07-09%20%E4%B8%8B%E5%8D%885.50.20.png)

### 一对多配置
 一般在一的一方放弃外键维护
![](SpringDataJpa.assets/%E6%97%A0%E6%A0%87%E9%A2%98-37.png)
在一的一方 也就是被选择的一方设置级联的属性



### 多对多的配置
 一般在被动选择的一方放弃外键维护  (例如：用户和角色的关系  一般 角色较少 用户比较多 这时 角色就属于被动选择的一方 )

### 级联操作
 一般配置在当前操作的主体上 
双项级联删除不能配置



## 16 对象导航查询

查询一个对象的同时，通过此对象`查询他的关联对象`
从`一方查询多方`    默认：使用延迟加载 
从`多方查询一方`    默认：使用立即加载

配置延迟加载
![](SpringDataJpa.assets/%E6%97%A0%E6%A0%87%E9%A2%98-38.png)


