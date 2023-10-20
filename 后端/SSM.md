# 扩展提升

### 数据库截断与清空

- 清空：将表清空，再次添加时，id接着清空之前的表继续自增
- 截断：删除表，并重新创建一个一模一样的表，id重新计数

## MyBatis

Hibernate和JAP全自动框架

MyBatis半自动

### 1.MyBatis简介

#### 1.1 MyBatis历史

MyBatis最初是Apache的一个开源项目**iBatis**, 2010年6月这个项目由Apache Software Foundation迁

移到了Google Code。随着开发团队转投Google Code旗下， iBatis3.x正式更名为MyBatis。代码于

2013年11月迁移到Github。

iBatis一词来源于“internet”和“abatis”的组合，是一个基于Java的持久层框架。 iBatis提供的持久层框架

包括SQL Maps和Data Access Objects（DAO）

#### 1.2 MyBatis特性

1） MyBatis 是支持定制化 SQL、存储过程以及高级映射的优秀的持久层框架

2） MyBatis 避免了几乎所有的 JDBC 代码和手动设置参数以及获取结果集

3） MyBatis可以使用简单的XML或注解用于配置和原始映射，将接口和Java的POJO（Plain Old Java

Objects，普通的Java对象）映射成数据库中的记录

4） MyBatis 是一个 半自动的ORM（Object Relation Mapping）框架



#### 1.3 和其它持久化层技术对比

**JDBC：**

SQL 夹杂在Java代码中耦合度高，导致硬编码内伤

维护不易且实际开发需求中 SQL 有变化，频繁修改的情况多见

代码冗长，开发效率低

**Hibernate 和 JPA：**

操作简便，开发效率高

程序中的长难复杂 SQL 需要绕过框架

基于全映射的全自动框架，大量字段的 POJO 进行部分映射时比较困难。

反射操作太多，导致数据库性能下降

**MyBatis：**

轻量级，性能出色

SQL 和 Java 编码分开，功能边界清晰。Java代码专注业务、SQL语句专注数据

开发效率稍逊于HIbernate，但是完全能够接受

### 2. 搭建MyBatis

#### 2.1 注意事项

**MySQL不同版本的注意事项**

1.  驱动类driver-class-name 

- - MySQL 5版本使用jdbc5驱动，驱动类使用：com.mysql.jdbc.Driver
  - MySQL 8版本使用jdbc8驱动，驱动类使用：com.mysql.cj.jdbc.Driver

1.  连接地址url 

- - MySQL 5版本的url：jdbc:mysql://localhost:3306/ssm
  - MySQL 8版本的url：jdbc:mysql://localhost:3306/ssm?serverTimezone=UTC

否则运行测试用例报告如下错误：

```
java.sql.SQLException: The server time zone value 'ÖÐ¹ú±ê×¼Ê±¼ä' is unrecognized or represents more
```

#### 2.2 创建Maven工程

1.  打包方式：jar 
2.  引入依赖 

```java
<dependencies> <!-- Mybatis核心 -->
    <dependency>
        <groupId>org.mybatis</groupId>
        <artifactId>mybatis</artifactId>
        <version>3.5.7</version>
    </dependency> <!-- junit测试 -->
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.12</version>
        <scope>test</scope>
    </dependency> <!-- MySQL驱动 -->
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>8.0.16</version>
    </dependency>
</dependencies>
```

#### 2.3 创建MyBatis的核心配置文件

习惯上命名为mybatis-config.xml，这个文件名仅仅只是建议，并非强制要求。将来整合Spring之后，这个配置文件可以省略，所以大家操作时可以直接复制、粘贴。

核心配置文件主要用于配置连接数据库的环境以及MyBatis的全局配置信息。

核心配置文件存放的位置是src/main/resources目录下。

```java
<?xml version="1.0" encoding="UTF-8" ?> <!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">

<!--设置连接数据库的环境-->
<configuration>
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/ssm?serverTimezone=UTC"/>
                <property name="username" value="root"/>
                <property name="password" value="wangkun1"/>
            </dataSource>
        </environment>
    </environments>

    <!--引入映射文件-->
    <mappers>
        <mapper resource="mappers/UserMapper.xml"/>
    </mappers>
</configuration>
```

#### 2.4 创建mapper接口

MyBatis中的mapper接口相当于以前的dao。但是区别在于，mapper仅仅是接口，我们不需要提供实现类。

```java
public interface UserMapper { 
	/** 添加用户信息 */ 
	int insertUser(); 
}
```

#### 2.5 创建mapper映射文件

相关概念：**ORM**（**O**bject **R**elationship **M**apping）对象关系映射。

- 对象：Java的实体类对象
- 关系：关系型数据库
- 映射：二者之间的对应关系

| Java概念 | 数据库概念 |
| -------- | ---------- |
| 类       | 表         |
| 属性     | 字段/列    |
| 对象     | 记录/行    |



1、映射文件的命名规则：

表所对应的实体类的类名+Mapper.xml，例如：表t_user，映射的实体类为User，所对应的映射文件为UserMapper.xml

因此一个映射文件对应一个实体类，对应一张表的操作

**一张表 ==> 一个实体类 ==> 一个mapper接口 ==> 映射文件**

MyBatis映射文件用于编写SQL，访问以及操作表中的数据

MyBatis映射文件存放的位置是src/main/resources/mappers目录下

2、 MyBatis中可以面向接口操作数据，要保证两个一致：

-  mapper接口的全类名和映射文件的命名空间（namespace）保持一致 
-  mapper接口中方法的方法名和映射文件中编写SQL的标签的id属性保持一致 

```java
<?xml version="1.0" encoding="UTF-8" ?> <!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.wangkun.mybatis.mapper.UserMapper">

    <!--int insertUser();-->
    <insert id="insertUser">
        insert into t_user
        values (null, 'admin', '123456', 23, '男', '12345@qq.com')
    </insert>

</mapper>
```

#### 2.6 创建测试类

```java
package com.wangkun.mybatis.test;

import com.wangkun.mybatis.mapper.UserMapper;
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import org.junit.Test;

import java.io.IOException;
import java.io.InputStream;

public class MybatisTest {
    @Test
    public void testInsert() throws IOException {

        InputStream is = Resources.getResourceAsStream("mybatis-config.xml");
        // 创建SqlSessionFactoryBuilder对象
        SqlSessionFactoryBuilder sqlSessionFactoryBuilder = new SqlSessionFactoryBuilder();
        // 通过核心配置文件所对应的字节输入流创建工厂类SqlSessionFactory，生产SqlSession对象
        SqlSessionFactory sqlSessionFactory = sqlSessionFactoryBuilder.build(is);
        // 创建SqlSession对象，此时通过SqlSession对象所操作的sql都必须手动提交或回滚事务
        SqlSession sqlSession = sqlSessionFactory.openSession();
        // 创建SqlSession对象，此时通过SqlSession对象所操作的sql都会自动提交
        // SqlSession sqlSession = sqlSessionFactory.openSession(true);

        // 通过代理模式创建UserMapper接口的代理实现类对象
        UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
        // 调用UserMapper接口中的方法，就可以根据UserMapper的全类名匹配元素文件，通过调用的方法名匹配 映射文件中的SQL标签，并执行标签中的SQL语句
        int result = userMapper.insertUser();

        /*上面两行代码就相当于*/
        // sqlSession.insert("com.wangkun.mybatis.mapper.UserMapper.insertUser");
        sqlSession.commit();
        sqlSession.close();

        System.out.println("结果：" + result);
    }
}
```

#### 2.7 日志功能

1.  加入依赖 

```xml
<!-- log4j日志 -->
<dependency>
    <groupId>log4j</groupId>
    <artifactId>log4j</artifactId>
    <version>1.2.17</version>
</dependency>
```

1.  加入log4j配置文件 

```xml
<!--src/main/resources/log4j.xml-->
<?xml version="1.0" encoding="GB2312" ?>
<!DOCTYPE log4j:configuration SYSTEM
        "http://logging.apache.org/log4j/1.2/apidocs/org/apache/log4j/xml/doc-files/log4j.dtd">

<log4j:configuration debug="true">

    <appender name="STDOUT" class="org.apache.log4j.ConsoleAppender">
        <param name="Encoding" value="UTF-8"/>
        <layout class="org.apache.log4j.PatternLayout">
            <param name="ConversionPattern" value="%-5p %d{MM-dd HH:mm:ss,SSS} %m (%F:%L) \n"/>
        </layout>
    </appender>
    <logger name="java.sql">
        <level value="debug"/>
    </logger>
    <logger name="org.apache.ibatis">
        <level value="info"/>
    </logger>
  
    <root>
        <level value="debug"/>
        <appender-ref ref="STDOUT"/>
    </root>
  
</log4j:configuration>
```

**日志的级别：**

FATAL(致命) > ERROR(错误) > WARN(警告) > INFO(信息) > DEBUG(调试)

从左到右打印的内容越来越详细

### 3. 核心配置文件

```xml
<?xml version="1.0" encoding="UTF-8" ?> <!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">

<!--设置连接数据库的环境-->
<configuration>
    <!--MyBatis核心配置文件中，标签的顺序： properties?,settings?,typeAliases?,typeHandlers?, objectFactory?,objectWrapperFactory?,reflectorFactory?, plugins?,environments?,databaseIdProvider?,mappers? -->

    <!--1. properties: 引入properties文件，可以用${key}访问value-->
    <properties resource="jdbc.properties"/>

    <!--2. typeAliases: 类型别名-->
    <typeAliases>
        <!-- (1).给单个实体类设置别名 -->
        <!--<typeAlias type="com.wangkun.mybatis.pojo.User" alias="User"/>-->
        <!-- (2).给同一设置别别名，别名默认就是类名-->
        <package name="com.wangkun.mybatis.pojo"/>
    </typeAliases>

    <!--3. environments：配置多个连接数据库的环境 属性：default：设置默认使用的环境的id -->
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="${jdbc.driver}"/>
                <property name="url" value="${jdbc.url}"/>
                <property name="username" value="${jdbc.username}"/>
                <property name="password" value="${jdbc.password}"/>
            </dataSource>
        </environment>

    </environments>

    <!--4. 引入映射文件-->
    <mappers>
        <!--单个引入映射文件-->
        <!--<mapper resource="mappers/UserMapper.xml"/>-->
        <!--
            以包的形式引入映射文件
            (1). mapper接口与映射文件所在的包必须一致
            (2). 接口的名字与与映射文件的名字必须一致
        -->
        <package name="com.wangkun.mybatis.mapper"/>
    </mappers>
</configuration>
```



### 4. Mybatis获取参数



两种方式：`#{}`与`${}`

 

- `#{}`(常用)：本质就是占位符赋值，使用占位符的方式拼接sql，此时为字符串类型或日期类型的字段进行赋值时可以自动添加单引号
- `${}`：本质是字符串拼接sql，若为字符串类型或日期类型的字段进行赋值时，需要手动加单引号

 

**注：在3.5版本后，使用这两种方式没有太大的区别，只是需不需要加单引号**

#### 4.1 单个参数

```xml
<!--public User getUserByName(String username);-->
<select id="getUserByName" resultType="user">
    select * from t_user where username = '${username}'
</select>

<select id="getUserByName" resultType="user">
  	<!--这里不需要加单引号包裹变量-->
    select * from t_user where username = #{username}
</select>
```



#### 4.2 多个参数

MyBatis会自动将这些参数放在一个map集合中，以arg0，arg1...为键，以参数为值；以param1，param2...为键，以参数为值，使用时只需要通过`${}`或者`#{}`访问map集合中的键就可以获取对应的值，注意`${}`需要手动加单引号

```xml
<!--public User checkLogin(String username, String password);-->
<select id="checkLogin" resultType="user">
    select * from t_user where username = #{param1} and password = #{param2}
</select>

<select id="checkLogin" resultType="user">
    select * from t_user where username = '${arg0}' and password = '${arg1}'
</select>
```



#### 4.3 Map类型参数



当有多个参数的时候，我们可以用Map收集参数，这样我们就可以定义我们自己想用的key

 

**注：当我们在映射文件中使用不正确的key也不会报错，只是会查不到数据**



**测试类：**



```java
@Test
public void checkLoginByMap() {
    SqlSession sqlSession = SqlSessionUtil.getSqlSession();
    UserMapper mapper = sqlSession.getMapper(UserMapper.class);
    Map<String, Object> map = new HashMap<>();
    map.put("username", "admin");
    map.put("password", "123456");
    User user = mapper.checkLoginByMap(map);
    System.out.println(user);
}
```



**映射文件：**



```xml
<!--public User checkLoginByMap(Map<String, Object> map);-->
<select id="checkLoginByMap" resultType="user">
    select * from t_user where username = #{username} and password = #{password}
</select>
```



#### 4.4 实体类型参数



只需要用`#{}`或者`${}`访问实体类型属性就可以

 

如：新增用户信息



```xml
<!--public int insetUser(User user);-->
<insert id="insetUser">
    insert into t_user
    values (null, #{username}, #{password}, #{age}, #{gender}, #{email})
</insert>
```



#### 4.5 @Param注解参数



**mapper接口：**



```java
/**
 * 根据用户名与密码注解获取用户
 * @param username
 * @param password
 * @return
 */
public User checkLoginByParams(@Param("username") String username, @Param("password") String password);
```



**映射文件：**



```java
<!--public User checkLoginByParams(@Param("username") String username, @Param("password") String password);-->
<select id="checkLoginByParams" resultType="user">
    select * from t_user where username = #{username} and password = #{password}
</select>
```



#### 总结



以后只用两种方式就可以

 

- **@Param注解形式**：单个、多个参数
- **实体类型参数**：新增，修改



### 5. MyBatis查询功能



#### 5.1 查询单条实体对象



**mapper接口：**



```java
/**
 * 根据id查询用户信息
 * @param id
 * @return
 */
public User getUserById(@Param("id") int id);
```



**映射文件：**



```xml
<!--public User getUserById(@Param("id") int id);-->
<select id="getUserById" resultType="user">
    select * from t_user where id = #{id}
</select>
```



**测试类：**



```java
/**
 * 根据id查询用户信息
 */
@Test
public void testGetUserById() {
    SqlSession sqlSession = SqlSessionUtil.getSqlSession();
    SelectMapper mapper = sqlSession.getMapper(SelectMapper.class);
    User userById = mapper.getUserById(1);
    System.out.println(userById);
}
```



#### 5.2 查询一个List集合



**mapper接口：**



```java
/**
 * 查询所有用户信息
 * @return
 */
public List<User> getAllUser();
```



**映射文件：**



```xml
<!--public List<User> getAllUser();-->
<select id="getAllUser" resultType="user">
    select * from t_user
</select>
```



**测试类：**



```java
/**
 * 查询所有用户信息
 */
@Test
public void testGetAllUser() {
    SqlSession sqlSession = SqlSessionUtil.getSqlSession();
    SelectMapper mapper = sqlSession.getMapper(SelectMapper.class);
    List<User> allUser = mapper.getAllUser();
    for (User user : allUser) {
        System.out.println(user);
    }
}
```



#### 5.3 查询一条为map集合



**mapper接口：**



```java
/**
 * 根据id查询用户信息，并以Map类型接收
 * @param id
 * @return
 */
 Map<String, Object> getUserByIdToMap(@Param("id") int id);
```



**映射文件：**



```xml
<!--public Map<String, Object> getUserByIdToMap(@Param("id") int id);-->
<select id="getUserByIdToMap" resultType="map">
    select * from t_user where id = #{id}
</select>
```



**测试类：**



```java
/**
 * 根据id查询用户信息,以Map类型接收结果
 */
@Test
public void testGetUserByIdToMap() {
    SqlSession sqlSession = SqlSessionUtil.getSqlSession();
    SelectMapper mapper = sqlSession.getMapper(SelectMapper.class);
    Map<String, Object> userByIdToMap = mapper.getUserByIdToMap(1);
    System.out.println(userByIdToMap);
}
```



#### 5.4 查询一条为map集合



##### 方式一：用List集合



用map类型的list集合来接收结果，**日常开发常用**



**mapper接口：**



```java
/**
 * 查询所有用户信息,用Map类型List集合来接收结果
 */
 List<Map<String, Object>> getAllUserToMap();
```



**映射文件：**



```xml
<!--List<Map<String, Object>> getAllUserToMap();-->
<select id="getAllUserToMap" resultType="map">
    select * from t_user
</select>
```



**测试类：**



```java
/**
 * 查询所有用户信息,用map类型的list集合接收
 */
@Test
public void testGetAllUserToMap() {
    SqlSession sqlSession = SqlSessionUtil.getSqlSession();
    SelectMapper mapper = sqlSession.getMapper(SelectMapper.class);
    List<Map<String, Object>> allUserToMap = mapper.getAllUserToMap();
    for (Map<String, Object> stringObjectMap : allUserToMap) {
        System.out.println(stringObjectMap);
    }
}
```



##### 方式二：依旧使用Map集合



将每条数据转换的map集合放在一个大map中，但必要要通过**@MapKey注解**，将查询的某个字段的值作为大的map的键·



**mapper接口：**



```java
@MapKey("id")
Map<String, Object> getAllUserToMap();
```



**映射文件：**



```xml
<select id="getAllUserToMap" resultType="map">
    select * from t_user
</select>
```



**测试类：**



```java
@Test
public void testGetAllUserToMap() {

    SqlSession sqlSession = SqlSessionUtil.getSqlSession();
    SelectMapper mapper = sqlSession.getMapper(SelectMapper.class);
    Map<String, Object> allUserToMap = mapper.getAllUserToMap();
    System.out.println(allUserToMap);
  
}
```



### 6. 特殊的SQL执行



#### 6.1 模糊查询



模糊查询需要使用单引号包括like条件，不能使用#{}，因为#{}本质是占位符?，并且还是在单引号里，?不会被当成占位符，而是字符串



**mapper接口：**



```java
/**
 * 模糊查询username
 * @param mohu
 * @return
 */
public List<User> getUserByLike(@Param("mohu") String mohu);
```



**映射文件：**



```xml
<!--public List<User> getUserByLike(@Param("mohu") String mohu);-->
<select id="getUserByLike" resultType="user">
    <!--方式一：-->
    <!--select * from t_user where username like '%${mohu}%'-->
    <!--方式二：-->
    select * from t_user where username like "%"#{mohu}"%"
</select>
```



**测试类：**



```java
@Test
public void getUserByLike() {
  
    SqlSession sqlSession = SqlSessionUtil.getSqlSession();
    SpecialSQLMapper mapper = sqlSession.getMapper(SpecialSQLMapper.class);
    List<User> list = mapper.getUserByLike("a");
    for (User user : list) {
        System.out.println(user);
    }
}
```



#### 6.2 批量删除



**mapper接口：**



```java
/**
 * 批量删除用户信息
 * @param ids
 */
public void delUserByIds(@Param("ids") String ids);
```



**映射文件：**



```xml
<!--public void delUserByIds(@Param("ids") String ids);-->
<delete id="delUserByIds">
    delete from t_user where id in(${ids})
</delete>
```



**测试类：**



```java
/**
 * 批量删除
 */
@Test
public void delUserByIds() {
    SqlSession sqlSession = SqlSessionUtil.getSqlSession();
    SpecialSQLMapper mapper = sqlSession.getMapper(SpecialSQLMapper.class);
    mapper.delUserByIds("4,5");
}
```



#### 6.3 动态设置表名



**mapper接口：**



```java
/**
 * 根据表名获取所有用户信息
 * @param tableName
 */
public List<User> getAllUserByTableName(@Param("tableName") String tableName);
```



**映射文件：**



```xml
<!--public List<User> getAllUserByTableName(@Param("tableName") String tableName);-->
<select id="getAllUserByTableName" resultType="user">
    select * from ${tableName}
</select>
```



**测试类：**



```java
/**
 * 根据表名查询所有用户信息
 */
@Test
public void getAllUserByTableName() {
    SqlSession sqlSession = SqlSessionUtil.getSqlSession();
    SpecialSQLMapper mapper = sqlSession.getMapper(SpecialSQLMapper.class);

    List<User> t_user = mapper.getAllUserByTableName("t_user");
    t_user.forEach(System.out::println);
}
```



#### 6.4 获取自增主键



**mapper接口：**



```java
/**
 * 新增用户信息，并获取主键
 * @param user
 */
public int insertUser(User user);
```



**映射文件：**



```xml
<!--public int insertUser(User user);-->
<!--
		useGeneratedKeys: 表示当前添加功能使用自增主键
		keyProperty: 将添加数据的自增主键为实体类类型的参数的数据赋值
-->
<insert id="insertUser" useGeneratedKeys="true" keyProperty="id">
    insert into t_user values (null, #{username}, #{password}, #{age}, #{gender}, #{email})
</insert>
```



**测试类：**



```java
/**
 * 新增用户信息，并获取自增主键
 */
@Test
public void testInsertUser() {
    SqlSession sqlSession = SqlSessionUtil.getSqlSession();
    SpecialSQLMapper mapper = sqlSession.getMapper(SpecialSQLMapper.class);
    User user = new User(null, "xiaoming", "12312312", 24, "男", "123qq.com");
    int i = mapper.insertUser(user);
    System.out.println(user);
}
```



#### 总结：



有分号参与的语句，不用#{}



### 7. 自定义映射resultMap



#### 7.1 resultMap处理字段和属性的映射关系



当实体类字段与数据库字段不一致时，直接查询无法返回正确结果，可以通过一下两种方式解决



1.  在Mybatis配置文件中设置setting ==> mapUnderscoreToCamelCase 

```xml
<settings>
    <setting name="mapUnderscoreToCamelCase" value="true"/>
</settings>
```

 

1.  在映射文件中设置resultMap 

resultMap：设置自定义的映射关系

 

- id： 唯一标识
- type：处理映射关系的实体类的类型

 

常用的标签：

 

- id：处理主键与实体类中属性的映射关系
- result：处理普通字段与实体类中属性的映射关系 

- - column：设置映射关系中的字段名，必须是数据库中对应的字段
  - property：设置映射关系中属性的属性名，必须是实体类中对应的属性名

 

  



##### 实例



###### **方式一：配置MyBatis配置文件**



**MyBatis配置文件：**



```xml
<properties resource="jdbc.properties"/>

<settings>
    <setting name="mapUnderscoreToCamelCase" value="true"/>
</settings>

<typeAliases>
    <package name="com.wangkun.mybatis.pojo"/>
</typeAliases>
```



**mapper：**



```java
/**
 * 根据id查询员工信息
 */
Emp getEmpById(@Param("empId") int empId);
```



**映射文件：**



```xml
<select id="getEmpById" resultType="emp">
    select * from t_emp where emp_id = #{empId}
</select>
```



**测试类：**



```java
@Test
public void testGetEmpById() {

    SqlSession sqlSession = SqlSessionUtil.getSqlSession();
    EmpMapper mapper = sqlSession.getMapper(EmpMapper.class);
    Emp empById = mapper.getEmpById(1);
    System.out.println(empById);

}
```



###### **方式二：配置映射文件**



**映射文件：**



```xml
<resultMap id="empResultMap" type="emp">
    <id column="emp_id" property="empId"/>
    <result column="emp_name" property="empName"/>
    <result column="age" property="age"/>
    <result column="gender" property="gender"/>
    <result column="dept_id" property="deptId"/>
</resultMap>

<!--Emp getEmpById(@Param("empId") int empId);-->
<select id="getEmpById" resultMap="empResultMap">
    select * from t_emp where emp_id = #{empId}
</select>
```



#### 7.2 多对一映射关系



多个员工对应一个部门

 

二次查询之后是一个对象



以**查询员工信息以及员工对应的部门信息**为例，使用三种方法实现



##### 7.2.1 级联方式处理映射关系



**mapper：**



```java
/**
 * 根据empId查询员工信息与部门信息
 * @param empId
 * @return
 */
Emp getEmpAndDeptByEmpId(@Param("empId") int empId);
```



**映射文件(重点)：**



```xml
<resultMap id="empAndDeptResultMap" type="emp">
    <id column="emp_id" property="empId"/>
    <result column="emp_name" property="empName"/>
    <result column="age" property="age"/>
    <result column="gender" property="gender"/>
  	<!--这里解决了实体类的一个属性对sql中多个字段的问题-->
    <result column="dept_id" property="dept.deptId"/>
    <result column="dept_name" property="dept.deptName"/>
</resultMap>

<!--Emp getEmpAndDeptByEmpId(@Param("empId") int empId);-->
<select id="getEmpAndDeptByEmpId" resultMap="empAndDeptResultMap">
    select t_emp.*, t_dept.*
    from t_emp
             left join t_dept on t_emp.dept_id = t_dept.dept_id
    where t_emp.emp_id = #{empId}
</select>
```



##### 7.2.2 使用association处理映射关系



- association：处理多对一的映射关系（处理实体类属性的属性） 

- - property：设置需要处理映射关系的属性的属性名
  - javaType：设置要处理的属性的类型

 



**映射文件：**



```xml
<resultMap id="empAndDeptResultMap" type="emp">
    <id column="emp_id" property="empId"/>
    <result column="emp_name" property="empName"/>
    <result column="age" property="age"/>
    <result column="gender" property="gender"/>
    <association property="dept" javaType="Dept">
        <id column="dept_id" property="deptId"/>
        <result column="dept_name" property="deptName"/>
    </association>
</resultMap>

<!--Emp getEmpAndDeptByEmpId(@Param("empId") int empId);-->
<select id="getEmpAndDeptByEmpId" resultMap="empAndDeptResultMap">
    select t_emp.*, t_dept.*
    from t_emp
             left join t_dept on t_emp.dept_id = t_dept.dept_id
    where t_emp.emp_id = #{empId}
</select>
```



##### 7.2.3 分布查询



###### 第一步



根据emp_id查询出员工信息



**mapper（EmpMapper）：**



```java
/**
 * 根据empId查询员工信息与部门信息,分步查询
 *
 * @return
 */
Emp getEmpAndDeptByStepOne(@Param("empId") int empId);
```



**映射文件（EmpMapper.xml）**



```xml
<resultMap id="empAndDeptByStepResultMap" type="emp">
    <id column="emp_id" property="empId"/>
    <result column="emp_name" property="empName"/>
    <result column="age" property="age"/>
    <result column="gender" property="gender"/>
  	<!-- 将查询出的dept_id的值作为参数传给并调用getEmpAndDeptByStepTwo，再讲第二步的查询结果映射到实体类的dept属性 -->
    <association
            property="dept"
            column="dept_id"
            select="com.wangkun.mybatis.mapper.DeptMapper.getEmpAndDeptByStepTwo"
    >
    </association>
</resultMap>
<!--Emp getEmpAndDeptByStepOne(@Param("empId") int empId);-->
<select id="getEmpAndDeptByStepOne" resultMap="empAndDeptByStepResultMap">
    select * from t_emp  where emp_id = #{empId}
</select>
```



###### 第二步



根据dept_id查询出部门信息



**mapper（DeptMapper）：**



```java
/**
 * 分布查询二
 */
Dept getEmpAndDeptByStepTwo(@Param("deptId") int deptId);
```



**映射文件（DeptMapper.xml）：**



```xml
<!--Dept getEmpAndDeptByStepTwo(@Param("deptId") int deptId);-->
<select id="getEmpAndDeptByStepTwo" resultType="dept">
    select * from t_dept where dept_id = #{deptId}
</select>
```



**测试类：**



```java
/**
 * 根据id查询员工与部门信息，分步查询
 */
@Test
public void testGetEmpAndDeptByStep() {
    SqlSession sqlSession = SqlSessionUtil.getSqlSession();
    EmpMapper mapper = sqlSession.getMapper(EmpMapper.class);
    Emp empAndDeptByStepOne = mapper.getEmpAndDeptByStepOne(2);
    System.out.println(empAndDeptByStepOne);
}
```



##### 7.2.4 分布查询的优势



可以实现延迟加载，但我们没有用到第二步查询的结果时，Mybatis不会执行第二步的sql语句



**全局配置：**



在Mybatis全局配置文件中加入配置



```xml
<properties resource="jdbc.properties"/>

<settings>
    <!--将下划线映射为驼驼峰-->
    <setting name="mapUnderscoreToCamelCase" value="true"/>
    <!--开启延迟加载-->
    <setting name="lazyLoadingEnabled" value="true"/>
    <!--按需加载-->
    <setting name="aggressiveLazyLoading" value="false"/>
</settings>
```



**局部配置：**



在映射文件中配置



```xml
<resultMap id="empAndDeptByStepResultMap" type="emp">
    <id column="emp_id" property="empId"/>
    <result column="emp_name" property="empName"/>
    <result column="age" property="age"/>
    <result column="gender" property="gender"/>
  	<!--fetchType：在开启了延迟加载的环境中，该属性可以设置当前的分布查询是否使用延迟加载-->
  	<!--fetchType="eager(立即加载)|lazy(延迟加载)"-->
  
    <association
            fetchType="eager"
            property="dept"
            column="dept_id"
            select="com.wangkun.mybatis.mapper.DeptMapper.getEmpAndDeptByStepTwo"
    >
    </association>
</resultMap>
```



#### 7.3 一对多映射关系



一个实体类属性对应多个sql字段

 

一个部门对应多个员工



##### 7.3.1 collection





**mapper：**



```java
/**
 * 根据部门id查询部门与员工信息，一个部门可以有多个员工
 */
Dept getDeptAndEmpByDeptId(@Param("deptId") int deptId);
```



**映射文件：**



```xml
<resultMap id="deptAndEmpByDeptIdResultMap" type="dept">
    <id column="dept_id" property="deptId"/>
    <result column="dept_name" property="deptName"/>
    <collection property="empList" ofType="emp">
        <id column="emp_id" property="empId"/>
        <result column="emp_name" property="empName"/>
        <result column="age" property="age"/>
        <result column="gender" property="gender"/>
    </collection>
</resultMap>
<!--Dept getDeptAndEmpByDeptId(@Param("deptId") int deptId);-->
<select id="getDeptAndEmpByDeptId" resultMap="deptAndEmpByDeptIdResultMap">
    select *
    from t_dept
             left join t_emp on t_emp.dept_id = t_dept.dept_id
    where t_dept.dept_id = #{deptId}
</select>
```



**测试类：**



```java
/**
 * 根据部门id查询部门信息以及部门中的公园
 */
@Test
public void testGetDeptAndEmpByDeptId() {
  
    SqlSession sqlSession = SqlSessionUtil.getSqlSession();
    DeptMapper mapper = sqlSession.getMapper(DeptMapper.class);
    Dept deptAndEmpByDeptId = mapper.getDeptAndEmpByDeptId(1);
    System.out.println(deptAndEmpByDeptId);

}
```



##### 7.3.2 分步查询



###### 第一步：



**mapper（DeptMapper）：**



```java
/**
 * 根据部门id查询部门信息与部门中员工信息，分步查询
 */
Dept getDeptAndEmpByStepOne(@Param("deptId") int deptId);
```



**映射文件（DeptMapper.xml）：**



```xml
<resultMap id="DeptAndEmpByStepOneResultMap" type="dept">
    <id column="dept_id" property="deptId"/>
    <result column="dept_name" property="deptName"/>
    <collection
            property="empList"
            column="dept_id"
            select="com.wangkun.mybatis.mapper.EmpMapper.getDeptAndEmpByStepTwo"
    />
</resultMap>
<!--Dept getDeptAndEmpByStepOne(@Param("deptId") int deptId);-->
<select id="getDeptAndEmpByStepOne" resultMap="DeptAndEmpByStepOneResultMap">
    select *
    from t_dept
    where dept_id = #{deptId}
</select>
```



###### 第二步：



**mapper（EmpMapper）：**



```java
/**
 * 根据dept_id查询部门信息与部门中员工的信息
 */
List<Emp> getDeptAndEmpByStepTwo(@Param("deptId") int deptId);
```



**映射文件（EmpMapper.xml）：**



```xml
<!--List<Emp> getDeptAndEmpByStepTwo(@Param("deptId") int deptId);-->
<select id="getDeptAndEmpByStepTwo" resultType="emp">
    select * from t_emp where dept_id = #{deptId}
</select>
```



**测试类：**



```java
/**
 * 根据部门id查询部门信息与员工信息，分步查询
 */
@Test
public void testGetDeptAndEmpByStep(){
    SqlSession sqlSession = SqlSessionUtil.getSqlSession();
    DeptMapper mapper = sqlSession.getMapper(DeptMapper.class);
    Dept deptAndEmpByStepOne = mapper.getDeptAndEmpByStepOne(1);
    System.out.println(deptAndEmpByStepOne);
}
```



#### 7.4 总结



- 对一 对应对象，对多 对应集合
- 二次查询结果为对象，用association
- 二次查询结果为集合，用collection



### 8 动态SQL



MyBatis框架的动态SQl技术是一种根据特定条件动态拼接SQL语句的功能，他存在的意义是为了解决拼接SQL语句字符串时的痛点问题



#### 8.1 if 标签



根据test属性判断是否将if标签内部的sql语句进行拼接



```xml
<!--List<Emp> getEmpByCondition(Emp emp);-->
<select id="getEmpByCondition" resultType="emp">
    select * from t_emp where 
    <if test="empName != null and empName != ''">
        emp_name = #{empName}
    </if>
    <if test="age != null and age != ''">
        and age = #{age}
    </if>
    <if test="gender != null and gender != ''">
        and gender = #{gender}
    </if>
</select>
```



#### 8.2 where标签



1. 若where标签中有成立的条件，则自动生成where关键字
2. 会自动将where标签中多余的and去掉
3. 若where标签中没有任何一个条件成立，则会自动删除where关键字



```xml
<!--List<Emp> getEmpByCondition(Emp emp);-->
<select id="getEmpByCondition" resultType="emp">
    select * from t_emp
    <where>
        <if test="empName != null and empName != ''">
            emp_name = #{empName}
        </if>
        <if test="age != null and age != ''">
            and age = #{age}
        </if>
        <if test="gender != null and gender != ''">
            and gender = #{gender}
        </if>
    </where>
</select>
```



#### 8.3  trim标签



1. prefix、suffix：在标签前面或后面添加指定内容
2. prefixOverrides、suffixOverrides：在标签中内容前面或后面去掉指定内容



```xml
<!--List<Emp> getEmpByCondition(Emp emp);-->
<select id="getEmpByCondition" resultType="emp">
    select * from t_emp
    <trim prefix="where" suffixOverrides="and">
        <if test="empName != null and empName != ''">
            emp_name = #{empName} and
        </if>
        <if test="age != null and age != ''">
            age = #{age} and
        </if>
        <if test="gender != null and gender != ''">
            gender = #{gender}
        </if>
    </trim>
</select>
```



#### 8.4  choose when otherwise



相当于if else if

 

前面一旦有条件通过了，后面就不会继续执行拼接了



```xml
<!--List<Emp> getEmpByChoose(Emp emp);-->
<select id="getEmpByChoose" resultType="emp">
    select * from t_emp
    <where>
        <choose>
            <when test="empName != null and empName != ''">
                emp_name = #{empName}
            </when>
            <when test="age != null and age != ''">
                age = #{age}
            </when>
            <when test="gender != null and gender != ''">
                gender = #{gender}
            </when>
        </choose>
    </where>
</select>
```



#### 8.5 foreach标签



collection：设置要循环的数组或集合

 

item：用一个字符串表示数组或集合中的每一个数据

 

separator：设置每次循环的数据之间的分隔符

 

open：循环的内容以什么开始

 

close：循环的内容以什么结束



##### 新增多个员工信息



**映射文件：**



```xml
<!--void insertManyEmp(@Param("emps") List<Emp> emps);-->
<insert id="insertManyEmp">
    insert into t_emp values
    <foreach collection="emps" item="emp" separator=",">
        (null, #{emp.empName}, #{emp.age}, #{emp.gender}, null)
    </foreach>
</insert>
```



##### 删除多个员工信息



**mapper：**



```xml
<!--void delManyEmp(@Param("empIds") Integer[] empIds);-->
<delete id="delManyEmp">
    delete from t_emp where
    <foreach collection="empIds" item="empId" separator="or">
        emp_id = #{empId}
    </foreach>
</delete>
```



#### sql 标签



可以记录一段sql，在需要的地方使用include标签进行引用



```xml
<sql id="empColumns">
    emp_id
    ,emp_name, age, gender
</sql>
<!--List<Emp> getEmpByChoose(Emp emp);-->
<select id="getEmpByChoose" resultType="emp">
    select
    <include refid="empColumns"/>
    from t_emp
    <where>
        <choose>
            <when test="empName != null and empName != ''">
                emp_name = #{empName}
            </when>
            <when test="age != null and age != ''">
                age = #{age}
            </when>
            <when test="gender != null and gender != ''">
                gender = #{gender}
            </when>
        </choose>
    </where>
</select>
```



### 9 MyBatis缓存



#### 9.1 一级缓存



一级缓存是SqlSession级别的，通过同一个SqlSession查询的数据会被缓存，下次查询相同的数据，就会从缓存中获取，不用在执行一遍sql语句去数据库获取



**开启条件：**默认开启



**一级缓存失效的四种情况**



1. 不同的SqlSession对应不同的一级缓存
2. 同一个SqlSession但查询条件不同
3. 同一个SqlSession但两次查询期间执行了任何crud操作
4. 同一个SqlSession但两次查询期间手动清空缓存：`sqlSession.clearCache();`



#### 9.2 二级缓存



二级缓存是SqlSessionFactory级别的，通过同一个SqlSessionFactory创建的SqlSession查询的结果会被缓存；伺候若再次执行相同的查询语句，结果会从缓存中获取



**开启条件：**



- 在核心配置文件中，设置全局配置属性`cacheEnabled="true"`，默认为true，不需要配置
- 在映射文件中设置标签`<cache/>`
- 二级缓存中必须在SqlSession关闭或提交之后有效
- 查询的数据所转换的实体类类型必须实现序列化接口



**二级缓存失效的一种情况：**



两次查询之间执行了任意的增改，会使一级和二级缓存同时失效



#### 9.3 MyBatis缓存查询的顺序



先找范围大的，再找范围小的



先查询二级缓存，二级缓存中可能有其他程序已经查出来的数据，可以拿来直接使用



二级缓存没有命中，在查询一级缓存



一级缓存没有命中，再去查找数据库



SqlSession关闭之后，一级缓存中的数据会写入二级缓存



#### 9.4 MyBatis可以整合第三方缓存



略。。。。。。



### 10 MyBatis的逆向工程



**正向工程：**先创建Java实体类，由框架负责根据实体类生成数据库表。Hibernate是支持正向工程的

 

**逆向工程：**先创建数据库表，由框架负责根据数据库表，反向生成

 

- Java实体类
- Mapper接口
- Mapper映射文件



**pom.xml：**



```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.wangkun.mybatis</groupId>
    <artifactId>mybatis_mbg</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>jar</packaging>

    <properties>
        <maven.compiler.source>8</maven.compiler.source>
        <maven.compiler.target>8</maven.compiler.target>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.5.7</version>
        </dependency>
        <!-- junit测试 -->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
            <scope>test</scope>
        </dependency>
        <!-- log4j日志 -->
        <dependency>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
            <version>1.2.17</version>
        </dependency>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.16</version>
        </dependency>
    </dependencies>
    <!-- 构建过程中用到的插件 -->
    <build>
        <!-- 具体插件，逆向工程的操作是以构建过程中插件形式出现的 -->
        <plugins>
            <plugin>
                <groupId>org.mybatis.generator</groupId>
                <artifactId>mybatis-generator-maven-plugin</artifactId>
                <version>1.3.0</version>
                <!--插件的依赖-->
                <dependencies>
                    <!--逆向工程的核心依赖-->
                    <dependency>
                        <groupId>org.mybatis.generator</groupId>
                        <artifactId>mybatis-generator-core</artifactId>
                        <version>1.3.2</version>
                    </dependency>
                    <!--MySQL驱动-->
                    <dependency>
                        <groupId>mysql</groupId>
                        <artifactId>mysql-connector-java</artifactId>
                        <version>8.0.16</version>
                    </dependency>
                </dependencies>
            </plugin>
        </plugins>
    </build>
</project>
```



**resources/generatorConfig.xml**



```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration PUBLIC
        "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
        "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">
<generatorConfiguration>
    <!-- targetRuntime: 执行生成的逆向工程的版本
                MyBatis3Simple: 生成基本的CRUD（清新简洁版）
                MyBatis3: 生成带条件的CRUD（奢华尊享版） -->
    <context id="DB2Tables" targetRuntime="MyBatis3">
        <!-- 数据库的连接信息 -->
        <jdbcConnection
                driverClass="com.mysql.cj.jdbc.Driver"
                connectionURL="jdbc:mysql://localhost:3306/atguigu?serverTimezone=UTC"
                userId="root"
                password="wangkun1"
        />
        <!-- javaBean的生成策略-->
        <javaModelGenerator targetPackage="com.wangkun.mybatis.pojo" targetProject="./src/main/java">
            <property name="enableSubPackages" value="true"/>
            <property name="trimStrings" value="true"/>
        </javaModelGenerator>
        <!-- SQL映射文件的生成策略 -->
        <sqlMapGenerator targetPackage="com.wangkun.mybatis.mapper" targetProject="./src/main/resources">
            <property name="enableSubPackages" value="true"/>
        </sqlMapGenerator>
        <!-- Mapper接口的生成策略 -->
        <javaClientGenerator
                type="XMLMAPPER"
                targetPackage="com.wangkun.mybatis.mapper"
                targetProject="./src/main/java">

            <property name="enableSubPackages" value="true"/>
        </javaClientGenerator>
        <!-- 逆向分析的表 --> <!-- tableName设置为*号，可以对应所有表，此时不写domainObjectName -->
        <!-- domainObjectName属性指定生成出来的实体类的类名 -->
        <table tableName="t_emp" domainObjectName="Emp"/>
        <table tableName="t_dept" domainObjectName="Dept"/>
    </context>
</generatorConfiguration>
```



**生成文件：**



![image-20230317160201578](/Users/wangkun/Library/Application Support/typora-user-images/image-20230317160201578.png)



**使用：**



```java
import com.wangkun.mybatis.mapper.EmpMapper;
import com.wangkun.mybatis.pojo.Emp;
import com.wangkun.mybatis.pojo.EmpExample;
import com.wangkun.mybatis.utils.SqlSessionUtil;
import org.apache.ibatis.session.SqlSession;
import org.junit.Test;

import java.util.List;

public class TestMbg {

    /**
     * 根据主键id查询员工信息
     */
    @Test
    public void testGetEmpByExample1() {

        SqlSession sqlSession = SqlSessionUtil.getSqlSession();
        EmpMapper mapper = sqlSession.getMapper(EmpMapper.class);
        // 根据id查询员工信息
        Emp emp = mapper.selectByPrimaryKey(1);
        System.out.println(emp);

    }

    /**
     * 查询所有员工
     */
    @Test
    public void testGetEmpByExample2() {
        SqlSession sqlSession = SqlSessionUtil.getSqlSession();
        EmpMapper mapper = sqlSession.getMapper(EmpMapper.class);
        List<Emp> emps = mapper.selectByExample(null);
        for (Emp emp : emps) {
            System.out.println(emp);
        }

    }

    /**
     * 根据条件查询员工
     */
    @Test
    public void testGetEmpByExample3() {
        SqlSession sqlSession = SqlSessionUtil.getSqlSession();
        EmpMapper mapper = sqlSession.getMapper(EmpMapper.class);
        // 创建实例
        EmpExample empExample = new EmpExample();
        // 添加条件
        empExample.createCriteria().andEmpNameEqualTo("张三").andGenderEqualTo("女");
        empExample.or().andAgeLessThan(20);

        List<Emp> emps = mapper.selectByExample(empExample);
        for (Emp item : emps) {
            System.out.println(item);
        }
    }

    /**
     * 修改员工信息
     */
    @Test
    public void testGetEmpByExample4() {
        SqlSession sqlSession = SqlSessionUtil.getSqlSession();
        EmpMapper mapper = sqlSession.getMapper(EmpMapper.class);
        // 创建实例
        Emp emp = new Emp(1, "小白", 12, "女");
        // 1. 修改
        int i1 = mapper.updateByPrimaryKey(emp);
        // 2. 选择性修改，不赋值或者为null的字段不会修改数据库
        // int i = mapper.updateByPrimaryKeySelective(emp);

    }

}
```



#### 注意点：



- 逆向工程生成的实体类没有重写toString方法，直接打印会返回地址值



### 11 分页插件



#### 11.1 简介



**几个关键的属性：**



limit：sql语句中用于截取数据的关键字



pageSize：每页显示的条数



pageNum：当前页的页码



index：当前也的起始索引，index=(pageNum - 1) * pageSize



count:  总条数



totalPage：总页数



pageSize=4, pageNum=1, index=0, limit 0, 4



pageSize=4, pageNum=3, index=8, limit 8, 4



pageSize=4, pageNum=6, index=20, limil 20, 4



#### 11.2 安装与配置分页插件



-  

##### 添加依赖：

-  

```xml
<dependency>
    <groupId>com.github.pagehelper</groupId>
    <artifactId>pagehelper</artifactId>
    <version>5.2.0</version>
</dependency>
```

 

-  在MyBatis中加入配置 

```xml
<typeAliases>
    <package name="com.wangkun.mybatis.pojo"/>
</typeAliases>

<plugins>
    <!--设置分页插件-->
    <plugin interceptor="com.github.pagehelper.PageInterceptor"></plugin>
</plugins>
```

 



#### 11.3 使用



```java
public class TestPage {
    @Test
    public void testPage() {
        SqlSession sqlSession = SqlSessionUtil.getSqlSession();
        EmpMapper mapper = sqlSession.getMapper(EmpMapper.class);
        // 设置页码数与每页显示多少条，同时返回分页相关的数据
        Page<Object> objects = PageHelper.startPage(1, 4);
        List<Emp> emps = mapper.selectByExample(null);
        // 查询功能之后可以再获取分页相关的所有数据（比上面的objects更加详细）
        PageInfo<Emp> empPageInfo = new PageInfo<>(emps, 1);
        for (Emp emp : emps) {
            System.out.println(emp);
        }
        System.out.println(objects);
    }
}
```



## Spring



### 1. 简述



#### 1.1 Spring概述



- Spring 是最受欢迎的企业级 Java 应用程序开发框架，数以百万的来自世界各地的开发人员使用Spring 框架来创建性能好、易于测试、可重用的代码。
- Spring 框架是一个开源的 Java 平台，它最初是由 Rod Johnson 编写的，并且于 2003 年 6 月首次在 Apache 2.0 许可下发布。
- Spring 是轻量级的框架，其基础版本只有 2 MB 左右的大小。
- Spring 框架的核心特性是可以用于开发任何 Java 应用程序，但是在 Java EE 平台上构建 web 应用程序是需要扩展的。 Spring 框架的目标是使 J2EE 开发变得更容易使用，通过启用基于 POJO编程模型来促进良好的编程实践。



#### 1.2 Spring Framework



Spring 的基础框架，可以视为 Spring 基础设施，基本上任何其他 Spring 项目都是以 Spring Framework为基础的。



##### 1.2.1 特性



-  非侵入式：使用 Spring Framework 开发应用程序时，Spring 对应用程序本身的结构影响非常小。对领域模型可以做到零污染；对功能性组件也只需要使用几个简单的注解进行标记，完全不会破坏原有结构，反而能将组件结构进一步简化。这就使得基于 Spring Framework 开发应用程序时结构清晰、简洁优雅。 
-  控制反转：IOC——Inversion of Control，翻转资源获取方向。把自己创建资源、向环境索取资源变成环境将资源准备好，我们享受资源注入。面向切面编程：AOP——Aspect Oriented Programming，在不修改源代码的基础上增强代码功能。 
-  容器：Spring IOC 是一个容器，因为它包含并且管理组件对象的生命周期。组件享受到了容器化的管理，替程序员屏蔽了组件创建过程中的大量细节，极大的降低了使用门槛，大幅度提高了开发效率。 
-  组件化：Spring 实现了使用简单的组件配置组合成一个复杂的应用。在 Spring 中可以使用 XML和 Java 注解组合这些对象。这使得我们可以基于一个个功能明确、边界清晰的组件有条不紊的搭建超大型复杂应用系统。 
-  声明式：很多以前需要编写代码才能实现的功能，现在只需要声明需求即可由框架代为实现。 
-  一站式：在 IOC 和 AOP 的基础上可以整合各种企业应用的开源框架和优秀的第三方类库。而且Spring 旗下的项目已经覆盖了广泛领域，很多方面的功能性需求可以在 Spring Framework 的基础上全部使用 Spring 来实现。 



##### 1.2.2 Spring Framework五大功能模块

| 功能模块                | 功能介绍                                              |
| ----------------------- | ----------------------------------------------------- |
| Core Container          | 核心容器，在Spring环境下使用任何功能都必须基于IOC容器 |
| AOP&Aspects             | 面向切面编程                                          |
| Testing                 | 提供了对junit或TestNG测试框架的整合                   |
| Data Access/Integration | 提供了对数据访问/集成的功能                           |
| Spring MVC              | 提供了面向Web应用程序的集成功能                       |



### 2. IOC



#### 2.1 IOC容器



##### 2.1.1 IOC思想



IOC，即控制反转，是指把对象的创建、初始化、销毁交给 Spring 来管理，而不是由开发者控制，实现控制反转。IOC 思想基于 IOC 容器完成，IOC 容器底层就是对象工厂（BeanFactory 接口）。

 

Spring 通过 IOC 容器来管理所有 Java 对象的实例化和初始化，控制对象与对象之间的依赖关系。我们将由 IoC 容器管理的 Java 对象称为 Spring Bean。



-  **获取资源的传统方式**
  自己做饭：买菜、洗菜、择菜、改刀、炒菜，全过程参与，费时费力，必须清楚了解资源创建整个过程
  中的全部细节且熟练掌握。
  在应用程序中的组件需要获取资源时，传统的方式是组件**主动**的从容器中获取所需要的资源，在这样的模式下开发人员往往需要知道在具体容器中特定资源的获取方式，增加了学习成本，同时降低了开发效率。 
-  **反转控制方式获取资源**
  点外卖：下单、等、吃，省时省力，不必关心资源创建过程的所有细节。
  反转控制的思想完全颠覆了应用程序组件获取资源的传统方式：反转了资源的获取方向——改由容器主动的将资源推送给需要的组件，开发人员不需要知道容器是如何创建资源对象的，只需要提供接收资源的方式即可，极大的降低了学习成本，提高了开发的效率。这种行为也称为查找的**被动**形式。 
-  **DI**
  DI：Dependency Injection，翻译过来是**依赖注入**。
  DI 是 IOC 的另一种表述方式：即组件以一些预先定义好的方式（例如：setter 方法）接受来自于容器的资源注入。相对于IOC而言，这种表述更直接。 



**结论：IOC 就是一种反转控制的思想， 而 DI 是对 IOC 的一种具体实现。**



##### 2.1.2 IOC容器在Spring中的实现



Spring的IOC容器就是IOC思想的一个落地的产品实现、IOC容器中管理的组件也叫做bean。在创建bean之前，首先需要创建IOC容器。Spring提供了IOC容器的两种实现方式：



-  BeanFactory
  这是IOC容器的基本实现，是Spring内部使用的接口。面向Spring本身，不提供给开发人员使用 
-  ApplicationContext
  BeanFactory的子接口，提供了更多的高级特性。面向Spring的使用者，几乎所有场合都使用ApplicationContext而不是底层的BeanFactory。 
-  ApplicationContext的主要实现类
  ![image-20230318151145483](/Users/wangkun/Library/Application Support/typora-user-images/image-20230318151145483.png) 

| 类型名                          | 简介                                                         |
| ------------------------------- | ------------------------------------------------------------ |
| ClassPathXmlApplicationContext  | 通过读取类路径下的 XML 格式的配置文件创建 IOC 容器对象       |
| FileSystemXmlApplicationContext | 通过文件系统路径读取XML格式的配置文件创建IOC容器对象         |
| ConfigurableApplicationContext  | ApplicationContext 的子接口，包含一些扩展方法refresh() 和 close() ，让 ApplicationContext 具有启动、关闭和刷新上下文的能力。 |
| WebApplicationContext           | 专门为 Web 应用准备，基于 Web 环境创建 IOC 容器对象，并将对象引入存入 ServletContext 域中。 |

-  



#### 2.2 基于XML管理bean



##### 2.2.1 实验一：入门案例



spring管理的对象我们称为组件或者bean

 

Spring 底层默认通过反射技术调用组件类的无参构造器来创建组件对象，这一点需要注意。如果在需要无参构造器时，没有无参构造器，会抛出异常



1.  引入依赖 

```xml
<dependencies>
    <!-- 基于Maven依赖传递性，导入spring-context依赖即可导入当前所需所有jar包 -->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
        <version>5.3.1</version>
    </dependency>
    <!-- junit测试 -->
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.12</version>
        <scope>test</scope>
    </dependency>
</dependencies>
```

 

1.  创建类组件 

```java
package com.wangkun.spring.pojo;

public class HelloWorld {

    public void sayHello() {

        System.out.println("hello，spring");

    }
}
```

 

1.  创建spring配置文件
   ![image-20230318161923722](/Users/wangkun/Library/Application Support/typora-user-images/image-20230318161923722.png) 
2.  在spring配置文件中配置bean 

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!--
        bean：配置一个bean对象，将对象交给IOC容器管理
        属性：
        id：bean的唯一标识，不能重复
        class：设置bean对象所对应的类型
    -->
    <bean id="helloWorld" class="com.wangkun.spring.pojo.HelloWorld"/>

</beans>
```

 

1.  创建测试类测试 

```java
package com.wangkun.spring.test;

import com.wangkun.spring.pojo.HelloWorld;
import org.junit.Test;
import org.springframework.context.annotation.ClassPathBeanDefinitionScanner;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class TestHelloWorld {
    @Test
    public void test() {
        ClassPathXmlApplicationContext ioc = new ClassPathXmlApplicationContext("applicationContext.xml");

        HelloWorld helloWorld = (HelloWorld) ioc.getBean("helloWorld");
        helloWorld.sayHello();
    }
}
```

 



##### 2.2.2 获取bean的三种方式



1. 根据id获取
2. 根据类型获取（最常用）
3. 根据类型和id获取



```java
@Test
public void test() {
    ClassPathXmlApplicationContext ioc = new ClassPathXmlApplicationContext("applicationContext.xml");

    /*获取bean的三种方式*/
    // 1. 根据id获取
    // Student studentOne = (Student) ioc.getBean("studentOne");

    // 2. 根据类型获取 最常用
    // Student bean = ioc.getBean(Student.class);

    // 3. 根据类型和id获取
    Student bean = ioc.getBean("studentOne", Student.class);

    System.out.println(bean);
}
```



**注意：**



- 当根据类型获取bean时，要IOC容器中指定的类型的bean有且只能有一个
- 如果组件类实现了接口，根据接口类型可以获取到bean，但前提是有且只一个类实现了这个接口



**结论：**



根据类型来获取bean时，在满足bean唯一性的前提下，其实只是看：「对象 **instanceof** 指定的类型」的返回的结果，只要返回的是true就可以认定为和类型匹配，就能够获取到



#### 2.3 依赖注入



就是给类组件中的属性赋值。



**较完整的xml头部标签**



```xml
<?xml version="1.0" encoding="UTF-8"?>
  <beans xmlns="http://www.springframework.org/schema/beans"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xmlns:context="http://www.springframework.org/schema/context"
         xsi:schemaLocation="
         http://www.springframework.org/schema/beans
         http://www.springframework.org/schema/beans/spring-beans.xsd
         http://www.springframework.org/schema/context
         http://www.springframework.org/schema/context/spring-context.xsd">
  </beans>
```



##### 2.3.1 setting注入



```xml
<bean id="studentTwo" class="com.wangkun.spring.pojo.Student">
    <property name="SId" value="1"/>
    <property name="SName" value="张三"/>
    <property name="age" value="12"/>
    <property name="gender" value="男"/>
</bean>
```



##### 2.3.2 构造器注入



```xml
<bean id="studentThree" class="com.wangkun.spring.pojo.Student">
    <constructor-arg value="10001"/>
    <constructor-arg value="李四"/>
    <constructor-arg value="女"/>
    <!--可以通过nane指定给那个属性赋值-->
    <constructor-arg value="12" name="age"/>
</bean>
```



##### 2.3.3 特殊值处理



-  null 

```xml
<bean id="studentTwo" class="com.wangkun.spring.pojo.Student">
    <property name="SId" value="1"/>
    <property name="SName" value="张三"/>
    <property name="age" value="12"/>
    <property name="gender">
        <null/>
    </property>
    <property name="score" value="24.1"/>
</bean>
```

  

-  xml实体 

```xml
<!-- 小于号在XML文档中用来定义标签的开始，不能随便使用 --> 
<!-- 解决方案一：使用XML实体来代替 --> 
<property name="expression" value="a &lt; b"/>
```

 

-  CDATA节 

```xml
<property name="expression"> 
<!-- 解决方案二：使用CDATA节 --> 
<!-- CDATA中的C代表Character，是文本、字符的含义，CDATA就表示纯文本数据 --> 
<!-- XML解析器看到CDATA节就知道这里是纯文本，就不会当作XML标签或属性来解析 -->
<!-- 所以CDATA节中写什么符号都随意 --> 
		<value><![CDATA[a < b]]></value> 
</property>
```

 



##### 2.3.4 为类类型赋值



**创建实体类：**



```java
// Student
public class Student {
    private int sId;

    private String sName;

    private int age;

    private String gender;

    private double score;

    private Clazz clazz;
}

// Clazz
public class Clazz {
    private Integer cid;

    private String cname;
}
```



**方式一：引用外部声明的bean**



```xml
<!--为类属性赋值-->
<bean id="studentThree" class="com.wangkun.spring.pojo.Student">
    <property name="SId" value="1111"/>
    <property name="SName" value="程浩"/>
    <property name="age" value="27"/>
    <property name="gender" value="男"/>
    <property name="clazz" ref="clazzOne"/>
</bean>

<bean id="clazzOne" class="com.wangkun.spring.pojo.Clazz">
    <property name="cid" value="1001"/>
    <property name="cname" value="年入百万班"/>
</bean>
```



**方式二：内部bean**



内部bean，只能在当前bean的内部使用，无法通过ioc容器获取，所以设置内部bean的id也没有用



```xml
<bean id="studentThree" class="com.wangkun.spring.pojo.Student">
    <property name="SId" value="1111"/>
    <property name="SName" value="程浩"/>
    <property name="age" value="27"/>
    <property name="gender" value="男"/>
    <property name="clazz">
        <bean class="com.wangkun.spring.pojo.Clazz">
            <property name="cid" value="1002"/>
            <property name="cname" value="键盘敲碎班"/>
        </bean>
    </property>
</bean>
```



**方式三：级联**



级联的方式，要保证提前为clazz属性赋值或者实例化



```java
<bean id="studentThree" class="com.wangkun.spring.pojo.Student">
    <property name="SId" value="1111"/>
    <property name="SName" value="程浩"/>
    <property name="age" value="27"/>
    <property name="gender" value="男"/>
    <property name="clazz" ref="clazzOne"/>
    <property name="clazz.cid" value="1002"/>
    <property name="clazz.cname" value="前程无忧"/>
</bean>

<bean id="clazzOne" class="com.wangkun.spring.pojo.Clazz">
    <property name="cid" value="1001"/>
    <property name="cname" value="年入百万班"/>
</bean>
```



##### 2.3.5 为数组类型赋值



**实体类：**



```java
public class Student {
    private int sId;

    private String sName;

    private int age;

    private String gender;

    private double score;

    private Clazz clazz;

    private String[] hobby;

}
```



**配置bean：**



```xml
<bean id="studentThree" class="com.wangkun.spring.pojo.Student">
    <property name="SId" value="1111"/>
    <property name="SName" value="程浩"/>
    <property name="age" value="27"/>
    <property name="gender" value="男"/>
    <property name="clazz">
        <bean class="com.wangkun.spring.pojo.Clazz">
            <property name="cid" value="1002"/>
            <property name="cname" value="键盘敲碎班"/>
        </bean>
    </property>
    <property name="hobby">
        <array>
            <value>抽烟</value>
            <value>喝酒</value>
            <value>烫头</value>
        </array>
    </property>
</bean>
```



##### 2.3.6 为集合类型赋值



**实体类：**



```java
public class Clazz {
    private Integer cid;

    private String cname;

    private List<Student> students;
}
```



**方式一：内部list标签**



```xml
<bean id="studentTwo" class="com.wangkun.spring.pojo.Student">
    <property name="SId" value="1"/>
    <property name="SName" value="张三"/>
    <property name="age" value="12"/>
    <property name="gender">
        <null/>
    </property>
    <property name="score" value="24.1"/>
</bean>
<bean id="clazzTwo" class="com.wangkun.spring.pojo.Clazz">
    <property name="cid" value="1003"/>
    <property name="cname" value="1003"/>
    <property name="students">
        <list>
            <ref bean="studentOne"/>
            <ref bean="studentTwo"/>
            <ref bean="studentThree"/>
        </list>
    </property>
</bean>
```



**方式二：配置集合类型的bean(util:list)**



```xml
<bean id="clazzThree" class="com.wangkun.spring.pojo.Clazz">
    <property name="cid" value="5001"/>
    <property name="cname" value="键盘敲烂班"/>
    <property name="students" ref="studentList"/>
</bean>

<util:list id="studentList">
    <ref bean="studentOne"/>
    <ref bean="studentTwo"/>
    <ref bean="studentThree"/>
</util:list>
```



##### 2.3.7 为Map类型赋值



**方式一：内部map标签**



```xml
<bean id="studentThree" class="com.wangkun.spring.pojo.Student">
    <property name="sId" value="1111"/>
    <property name="sName" value="程浩"/>
    <property name="age" value="27"/>
    <property name="gender" value="男"/>
    <property name="clazz">
        <bean class="com.wangkun.spring.pojo.Clazz">
            <property name="cid" value="1002"/>
            <property name="cname" value="键盘敲碎班"/>
        </bean>
    </property>
    <property name="hobby">
        <array>
            <value>抽烟</value>
            <value>喝酒</value>
            <value>烫头</value>
        </array>
    </property>
  	<!-- 通过Map标签引入teacher -->
    <property name="teacherMap">
        <map>
            <entry key="语文老师" value-ref="teacherOne"/>
            <entry key="数学老师" value-ref="teacherTwo"/>
        </map>
    </property>
</bean>

<!--创建teavher的bean-->
<bean id="teacherOne" class="com.wangkun.spring.pojo.Teacher">
    <property name="tid" value="1001"/>
    <property name="tname" value="张三"/>
</bean>
<bean id="teacherTwo" class="com.wangkun.spring.pojo.Teacher">
    <property name="tid" value="1002"/>
    <property name="tname" value="李四"/>
</bean>
```



**方式二：配置Map类型的bean(util:map)**



```xml
<bean id="studentThree" class="com.wangkun.spring.pojo.Student">
    <property name="sId" value="1111"/>
    <property name="sName" value="程浩"/>
    <property name="age" value="27"/>
    <property name="gender" value="男"/>
    <property name="clazz">
        <bean class="com.wangkun.spring.pojo.Clazz">
            <property name="cid" value="1002"/>
            <property name="cname" value="键盘敲碎班"/>
        </bean>
    </property>
    <property name="hobby">
        <array>
            <value>抽烟</value>
            <value>喝酒</value>
            <value>烫头</value>
        </array>
    </property>
   <property name="teacherMap" ref="teacherMap"/>
</bean>

<util:map id="teacherMap">
    <entry key="语文老师" value-ref="teacherOne"/>
    <entry key="数学老师" value-ref="teacherTwo"/>
</util:map>

<bean id="teacherOne" class="com.wangkun.spring.pojo.Teacher">
    <property name="tid" value="1001"/>
    <property name="tname" value="张三"/>
</bean>

<bean id="teacherTwo" class="com.wangkun.spring.pojo.Teacher">
    <property name="tid" value="1002"/>
    <property name="tname" value="李四"/>
</bean>
```



##### 2.3.8 引入外部属性文件



已druid为例



**jdbc-properties.properties：**



```properties
jdbc.driver=com.mysql.cj.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/atguigu?serverTimezone=UTC
jdbc.username=root
jdbc.password=wangkun1
```



**spring-datasource.xml：**



```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="
                 http://www.springframework.org/schema/beans
                 http://www.springframework.org/schema/beans/spring-beans.xsd
                  http://www.springframework.org/schema/context
                  http://www.springframework.org/schema/context/spring-context.xsd">
  
  	<!--引入jdbc-properties.properties文件-->
    <context:property-placeholder location="jdbc-properties.properties"/>

    <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
        <property name="driverClassName" value="${jdbc.driver}"/>
        <property name="url" value="${jdbc.url}"/>
        <property name="username" value="${jdbc.username}"/>
        <property name="password" value="${jdbc.password}"/>
    </bean>

</beans>
```



##### 2.3.9 bean的作用域



在Spring中可以配置bean标签的scope属性来指定bean的作用域范围

| 取值              | 含义                                    | 创建对象的时机 |
| ----------------- | --------------------------------------- | -------------- |
| singleton（默认） | 在IOC容器中，这个bean的对象始终为单实例 | IOC容器初始化  |
| prototype         | 这个bean在IOC容器中可以有多个实例       | 获取bean时     |



**bean配置文件：**



```xml
<bean id="student" class="com.wangkun.spring.pojo.Student" scope="prototype">
    <property name="sName" value="程浩"/>
    <property name="age" value="19"/>
</bean>
```



##### 2.3.10 bean生命周期



- 实例化： 调用空参构造
- 依赖注入：为类组件的属性赋值
- 初始化：在bean配置中指定类组件中的初始化方法
- 销毁：在bean配置中指定类组件中的销毁方法（IOC容器关闭时销毁）

 

对于Spring中的单例(Singleton)bean，它的实例化是在容器启动时进行的，也就是在应用程序启动时，Spring容器会创建并初始化所有的单例bean。这个过程中，Spring容器会扫描应用程序上下文中所有的bean定义，创建并实例化它们。

 

当Spring容器启动时，会调用BeanFactoryPostProcessor和BeanPostProcessor，它们可以修改bean的定义和实例化过程。BeanFactoryPostProcessor用于修改bean的定义，而BeanPostProcessor则可以在bean实例化之后修改bean的属性和状态。

 

接下来，Spring容器会对所有的单例bean进行实例化、属性注入、初始化和销毁，这些bean将会一直存在于应用程序的整个生命周期中，直到应用程序关闭。

 

需要注意的是，对于非单例(Prototype)bean，它的实例化是在每次获取实例时进行的，也就是每次调用getBean()方法时都会创建一个新的实例。这个时候，Spring容器会对bean进行实例化、属性注入、初始化和销毁，但是每个实例的生命周期是独立的，不会影响其他实例。



**类组件：User**



```java
package com.wangkun.spring.pojo;

public class User {
    private Integer id;
    private String username;
    private String password;
    private Integer age;

    @Override
    public String toString() {
        return "User{" +
                "id=" + id +
                ", username='" + username + '\'' +
                ", password='" + password + '\'' +
                ", age=" + age +
                '}';
    }

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        System.out.println("生命周期2：依赖注入");
        this.id = id;
    }

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public String getPassword() {
        return password;
    }

    public void setPassword(String password) {
        this.password = password;
    }

    public Integer getAge() {
        return age;
    }

    public void setAge(Integer age) {
        this.age = age;
    }

    public User(Integer id, String username, String password, Integer age) {
        this.id = id;
        this.username = username;
        this.password = password;
        this.age = age;
    }

    public User() {
        System.out.println("生命周期1：实例化");
    }

    public void initMethod() {
        System.out.println("生命周期3：初始化");
    }

    public void destroyMethod() {
        System.out.println("生命周期4：销毁");
    }
}
```



**bean配置文件：**



```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="lifeCycle" class="com.wangkun.spring.pojo.User" init-method="initMethod" destroy-method="destroyMethod">
        <property name="id" value="1"/>
        <property name="username" value="王琨"/>
        <property name="password" value="1231231"/>
        <property name="age" value="23"/>
    </bean>

</beans>
```



##### 2.3.11 FactoryBean



FactoryBean是一个Spring提供的一种整合第三方框架的常用机制。

 

和普通bean不同，配置一个FactoryBean的bean，在获取bean的时候并不是class属性配置的这个对象类，而是getObject()方法的返回值。通过这种机制，Spring可以帮我们把复杂组件创建的详细过程和繁琐细节都屏蔽起来，只把最简洁的使用界面展示给我们。

 

将来我们整合Mybatis，Spring就是通过FactoryBean机制来帮我们创建SqlSessionFactory对象的



**创建工厂类：**



```java
package com.wangkun.spring.factory;

import com.wangkun.spring.pojo.User;
import org.springframework.beans.factory.FactoryBean;

/*
* FactoryBean是一个接口，需要创建一个类实现接口
* 其中有三个方法：
*   1. getObject(): 通过一个对象交给IOC容器管理
*   2. GetObjectType(): 设置所提供对象的类型
*   3. isSingleton(): 所提供的对象是否为单例
* */

public class UserFactoryBean implements FactoryBean<User> {
    @Override
    public User getObject() throws Exception {
        return new User();
    }
    @Override
    public Class<?> getObjectType() {
        return User.class;
    }
}
```



**bean配置：**



```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="userFactory" class="com.wangkun.spring.factory.UserFactoryBean"/>

</beans>
```



**测试类：使用，与打印结果**



```java
@Test
public void testFactory() {

    ClassPathXmlApplicationContext ioc = new ClassPathXmlApplicationContext("spring-factory.xml");

    User bean = ioc.getBean(User.class);
    System.out.println(bean);

}

// 打印结果，只有生命周期第一步，因为没有进行依赖注入，
// 生命周期1：实例化
// User{id=null, username='null', password='null', age=null}
```



##### 2.3.12 基于xml的自动装配



根据指定的策略，在IOC容器匹配某一个bean，自动为指定的bean中所依赖的类类型或者接口类型属性赋值

 

可以通过bean标签中的autowire属性设置自定装配策略

 

可选值：

 

- no、default：不装配
- bytype：根据要赋值的属性的类型，在IOC容器中匹配某个bean，为属性赋值



**byType**



```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">


    <bean id="userController" class="com.wangkun.spring.controller.UserController" autowire="byType">
<!--        <property name="userService" ref="userService"/>-->
    </bean>

    <bean class="com.wangkun.spring.service.impl.UserServiceImpl" id="userService" autowire="byType">
<!--        <property name="userDao" ref="userDao"/>-->
    </bean>

    <bean class="com.wangkun.spring.dao.impl.UserDaoImpl" id="userDao">
    </bean>
</beans>
```



###### **byName**



```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">


    <bean id="userController" class="com.wangkun.spring.controller.UserController" autowire="byName">
<!--        <property name="userService" ref="userService"/>-->
    </bean>

    <bean class="com.wangkun.spring.service.impl.UserServiceImpl" id="userService" autowire="byName">
<!--        <property name="userDao" ref="userDao"/>-->
    </bean>

    <bean class="com.wangkun.spring.dao.impl.UserDaoImpl" id="userDao">
    </bean>
</beans>
```



**总结：**当类型匹配的bean有多个时，此时可以使用byName实现自动装配



#### 2.4 基于注解管理bean



常用的注解：

 

- @Component：将类标识为普通组件
- @Controller：将类标识为控制层组件
- @Service：将类标识为业务层组件
- @Repository：将类标识为持久层组件



##### 2.4.1 **扫描组件：**



1.  情况一：最基本的扫描方式 

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       https://www.springframework.org/schema/context/spring-context.xsd"
>

    <context:component-scan base-package="com.wangkun.spring"/>
    
</beans>
```

 

1.  情况二：指定要配出的组件 

type：

 

- `annotation`：根据注解排除，expression中设置要排除的注解的全类名
- `assignable`：根据类型排除，expression中设置要排除的类型的全类名

  

1.  情况三：指定要扫描的组件 

context:include-filter标签：指定在原有扫描规则的基础上追加的规则

 

use-default-filters属性：取值false表示关闭默认扫描规则

 

此时必须设置use-default-filters="false"，因为默认规则即扫描指定包下所有类

 

type：

 

- `annotation`：根据注解扫描，expression中设置要扫描的注解的全类名
- `assignable`：根据类型扫描，expression中设置要扫描的类型的全类名

  



##### 2.4.2 自定义组件bean的id



基于注解的管理的bean默认id就是**首字母小写的类名**，我们可以通过注解中的value属性自定义id



```java
@Controller("controller")
public class UserController {
  
}

// 测试
ClassPathXmlApplicationContext ioc = new ClassPathXmlApplicationContext("applicationContext.xml");

// 默认id
Object controller1 = ioc.getBean("userController");
// 自定义id
Object controller2 = ioc.getBean("controller");
System.out.println(controller);
```



##### 2.4.3 基于注解的自动装配



**@Autowired注解能够标识的位置**

 

- 标识在成员变量上，此时不需要设置成员变量的set方法
- 标识在set方法上
- 标识在为当前成员变量赋值的有参构造上

 

**@Autowired注解原理**

 

- 默认通过byType的方式，在IOC容器中通过类型匹配某个bean为属性赋值
- 若有多了类型匹配的bean，此时会自动转换为byName的方式实现自动装备的效果，将要赋值的属性的属性名作为bean的id匹配某个bean为属性赋值
- 若byType与byName的方式都无法实现自动装配，即IOC容器中有多个类型匹配的bean，且这些bean的id和要赋值的属性的属性名都一致，会抛出异常
- 此时可以再要赋值的属性上，添加一个注解`@Qualifier`，通过该注解的value属性，指定某个bean的id，将这个bean为属性赋值

 

@Autowired中有属性required，默认值为true，因此在自动装配无法找到相应的bean时，会装配失败可以将属性required的值设置为true，则表示能装就装，装不上就不装，此时自动装配的属性为默认，但是实际开发时，基本上所有需要装配组件的地方都是必须装配的，用不上这个属性。



```java
@Controller
public class UserController {

    @Autowired
    private UserService userService;

    public void saveUser() {
      
        userService.saveUser();
      
    }
}
```



### 3. AOP



#### 3.1 AOP概念及相关术语



AOP（Aspect Oriented Programming）是一种设计思想，是软件设计领域中的面向切面编程，它是面向对象编程的一种补充和完善，它以通过预编译方式和运行期动态代理方式实现在不修改源代码的情况下给程序动态统一添加额外功能的一种技术。

 

我们需要做的事：创建切面，创建通知，通过切入点表达式，把通知作用在连接点



##### 3.1.1 相关术语



- **横切关注点：** 

- - 从每个方法中抽取出来的同一类非核心业务。在同一个项目中，我们可以使用多个横切关注点对相关方法进行多个不同方面的增强。这个概不是语法层面天然存在的，而是根据附加功能逻辑上的需要：有十个附加功能，就有十个横切关注点。（如上述案例的日志功能）

- **通知**：每一个横切关注点上要做的事情都需要写一个方法来实现，这样的方法就叫通知方法。（通知就是添加功能的方法，就是代理后的方法） 

- - 前置通知：在被代理的目标方法**前**执行
  - 返回通知：在被代理的目标方法**成功结束**后执行（**寿终正寝**）
  - 异常通知：在被代理的目标方法**异常结束**后执行（**死于非命**）
  - 后置通知：在被代理的目标方法**最终结束**后执行（**盖棺定论**）
  - 环绕通知：使用try...catch...finally结构围绕**整个**被代理的目标方法，包括上面四种通知对应的所有位置（相当于上述的「参数是。。。结果是。。。」）

- **目标：**被代理的目标对象。
- **代理：**向目标对象应用通知之后创建的代理对象。
- **连接点：**这也是一个纯逻辑概念，不是语法定义的。把方法排成一排，每一个横切位置看成x轴方向，把方法从上到下执行的顺序看成y轴，x轴和y轴的交叉点就是连接点。（连接点就是目标方法）
- **切入点：**定位连接点的方式。每个类的方法中都包含多个连接点，所以连接点是类中客观存在的事物（从逻辑上来说）。如果把连接点看作数据中的记录，那么切入点就是查询记录的 SQL 语句。Spring 的 AOP 技术可以通过切入点定位到特定的连接点。切点通过 org.springframework.aop.Pointcut 接口进行描述，它使用类和方法作为连接点的查询条件。（就是通知上面的注解）



##### 3.1.2 作用



- 简化代码：把方法中固定位置的重复的代码**抽取**出来，让被抽取的方法更专注于自己的核心功能，提高内聚性。
- 代码增强：把特定的功能封装到切面类中，看哪里有需要，就往上套，被**套用**了切面逻辑的方法就被切面给增强了。



#### 3.2基于注解的AOP



##### 3.2.1 步骤



- 将目标对象和切面交给IOC容器管理（注解+扫描）
- 开启AspectJ的自动代理，为目标对象自动生成代理
- 将切面类通过注解@Aspect标识



1.  **添加依赖**： 

```xml
<dependencies>
    <!-- 基于Maven依赖传递性，导入spring-context依赖即可导入当前所需所有jar包 -->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
        <version>5.3.1</version>
    </dependency>
    <!-- junit测试 -->
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.12</version>
        <scope>test</scope>
    </dependency>

    <!-- spring-aspects会帮我们传递过来aspectjweaver -->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-aspects</artifactId>
        <version>5.3.1</version>
    </dependency>
</dependencies>
```

 

1.  **准备被代理的目标资源**
   **接口：** 

```java
package com.wangkun.spring.aop.annotation;

public interface Calculator {
    int add(int i, int j);

    int sub(int i, int j);

    int mul(int i, int j);

    int div(int i, int j);
}
```


**实现类：**  

1.  **创建切面类并配置** 

```java
package com.wangkun.spring.aop.annotation;

import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.annotation.After;
import org.aspectj.lang.annotation.AfterReturning;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;
import org.springframework.stereotype.Component;

import java.util.Arrays;

@Component
@Aspect
public class LoggerAspect {

    /**
     * 
     * @param joinPoint
     */
    @Before("execution(* com.wangkun.spring.aop.annotation.CalculatorImpl.*(..))")
    public void beforeMethod(JoinPoint joinPoint) {
        // 获取连接点的方法名（签名信息）
        String methodName = joinPoint.getSignature().getName();
        // 获取参数
        String args = Arrays.toString(joinPoint.getArgs());

        // 添加前置通知
        System.out.println("Logger===> 前置通知，方法名" + methodName + "参数" + args);
    }
}
```

 

1.  **配置bean：** 

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd http://www.springframework.org/schema/aop https://www.springframework.org/schema/aop/spring-aop.xsd">

    <context:component-scan base-package="com.wangkun.spring.aop.annotation"/>

    <!--开区基于注解的AOP-->
    <aop:aspectj-autoproxy/>

</beans>
```

 



##### 3.2.2 重用切入点表达式



-  **声明：** 

```java
@Pointcut("execution(* com.wangkun.spring.aop.annotation.CalculatorImpl.*(..))")
public void pointCut() {}
```

 

-  **在同一切面使用：** 

```java
@Before("pointcut()")
public void beforeMethod(JoinPoint joinPoint) {
    // 获取连接点的方法名（签名信息）
    String methodName = joinPoint.getSignature().getName();
    // 获取参数
    String args = Arrays.toString(joinPoint.getArgs());

    // 添加前置通知
    System.out.println("Logger===> 前置通知，方法名" + methodName + "参数" + args);
}
```

 

-  在不同切面使用 

```java
@Before("com.atguigu.aop.CommonPointCut.pointCut()")
public void beforeMethod(JoinPoint joinPoint) {
    String methodName = joinPoint.getSignature().getName();
    String args = Arrays.toString(joinPoint.getArgs());
    System.out.println("Logger-->前置通知，方法名：" + methodName + "，参数：" + args);
}
```

 



##### 3.2.3 获取通知的相关信息



通知的执行机制与try catch一样

 

- 前置通知：`@Before`目标方法执行前执行	====	try
- 返回通知：`@AfterReturning`目标方法返回结果之后执行	====	try
- 异常通知：`@AfterThrowing`目标方法报错后执行	====	catch
- 后置通知：`@After`目标方法执行完毕后执行	====	finally



```java
package com.wangkun.spring.aop.annotation;

import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.*;
import org.springframework.stereotype.Component;

import java.util.Arrays;

@Component
@Aspect
public class LoggerAspect {
    @Pointcut("execution(* com.wangkun.spring.aop.annotation.CalculatorImpl.*(..))")
    public void pointCut() {
    }

    /**
     * 前置通知
     *
     * @param joinPoint
     */
    @Before("pointCut()")
    public void beforeMethod(JoinPoint joinPoint) {
        // 获取连接点的方法名（签名信息）
        String methodName = joinPoint.getSignature().getName();
        // 获取参数
        String args = Arrays.toString(joinPoint.getArgs());

        // 添加前置通知
        System.out.println("Logger===> 前置通知，方法名：" + methodName + "参数：" + args);
    }

    /**
     * 返回通知
     *
     * @param joinPoint
     * @param result
     */
    @AfterReturning(value = "pointCut()", returning = "result")
    public void afterReturningMethod(JoinPoint joinPoint, Object result) {
        // 获取方法（签名信息）
        String methodName = joinPoint.getSignature().getName();

        // 添加后置通知
        System.out.println("Logger===> 后置结果通知，方法名：" + methodName + "结果：" + result);
    }

    /**
     * 异常通知
     *
     * @param joinPoint
     * @param error
     */
    @AfterThrowing(value = "pointCut()", throwing = "error")
    public void throwingMethod(JoinPoint joinPoint, Exception error) {
        String methodName = joinPoint.getSignature().getName();

        System.out.println("Logger===> 报错通知，方法名：" + methodName + "报错：" + error);
    }

    /**
     * 后置通知
     *
     * @param joinPoint
     */
    @After("pointCut()")
    public void afterMethod(JoinPoint joinPoint) {
        String methodName = joinPoint.getSignature().getName();

        System.out.println("Logger===> 后置通知，方法名：" + methodName + " 执行结束");
    }


    /**
     * 环绕通知
     *
     * @param joinPoint
     * @return
     */
    @Around("pointCut()")
    public Object aroundMethod(ProceedingJoinPoint joinPoint) {

        Object proceed = null;
        try {
            System.out.println("环绕通知 ==== 前置通知");
            proceed = joinPoint.proceed();
            System.out.println("环绕通知 ==== 返回通知");
        } catch (Throwable e) {
            System.out.println("环绕通知 ==== 异常通知");
            throw new RuntimeException(e);
        } finally {
            System.out.println("环绕通知 ==== 后置通知");
        }

        return proceed;
    }
}
```



##### 3.2.4 切面优先级



可以通过@Other注解的value属性设置优先级，默认是Integer的最大值

 

@Order注解的value属性值越小，优先级越高



```java
package com.wangkun.spring.aop.annotation;

import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;
import org.springframework.core.annotation.Order;
import org.springframework.stereotype.Component;

@Component
@Aspect
@Order(1)
public class ValidateAspect {

    @Before("com.wangkun.spring.aop.annotation.LoggerAspect.pointCut()")
    public void afterMethod() {

        System.out.println("ValidateAspect ===  前置通知");

    }


}
```



#### 3.3 基于xml的AOP



略。。。。



### 4. 声明式事务



#### 4.1 jdbcTemplate



##### 4.1.1 简介



Spring 框架对 JDBC 进行封装，使用 JdbcTemplate 方便实现对数据库操作



##### 4.1.2准备工作



1.  加入依赖 

```xml
<dependencies>
    <!-- 基于Maven依赖传递性，导入spring-context依赖即可导入当前所需所有jar包 -->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
        <version>5.3.1</version>
    </dependency>
    <!-- Spring 持久化层支持jar包 --> <!-- Spring 在执行持久化层操作、与持久化层技术进行整合过程中，需要使用orm、jdbc、tx三个 jar包 --> <!-- 导入 orm 包就可以通过 Maven 的依赖传递性把其他两个也导入 -->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-orm</artifactId>
        <version>5.3.1</version>
    </dependency>
    <!-- Spring 测试相关 -->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-test</artifactId>
        <version>5.3.1</version>
    </dependency>
    <!-- junit测试 -->
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.12</version>
        <scope>test</scope>
    </dependency>
    <!-- MySQL驱动 -->
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>8.0.16</version>
    </dependency>
    <!-- 数据源 -->
    <dependency>
        <groupId>com.alibaba</groupId>
        <artifactId>druid</artifactId>
        <version>1.0.31</version>
    </dependency>
</dependencies>
```

 

1.  创建`jdbc.properties` 

```properties
jdbc.driver=com.mysql.cj.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/atguigu?serverTimezone=UTC
jdbc.username=root
jdbc.password=wangkun1
```

 

1.  配置spring配置文件 

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd">

    <context:property-placeholder location="jdbc-properties.properties"/>

    <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
        <property name="driverClassName" value="${jdbc.driver}"/>
        <property name="url" value="${jdbc.url}"/>
        <property name="username" value="${jdbc.username}"/>
        <property name="password" value="${jdbc.password}"/>
    </bean>

    <bean class="org.springframework.jdbc.core.JdbcTemplate">
        <property name="dataSource" ref="dataSource"/>
    </bean>

</beans>
```

 

1.  测试 

```java
package com.wangkun.spring.jdbc;

import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.jdbc.core.BeanPropertyRowMapper;
import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;

import java.util.List;
import java.util.stream.Stream;

@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration("classpath:spring-jdbc.xml")
public class TestJDBCTemplate {

    @Autowired
    private JdbcTemplate jdbcTemplate;

    /**
     * 新增
     */
    @Test
    public void testInsetUser() {
        String sql = "insert into t_user values (null, ?, ?, ?, ?, ?)";
        jdbcTemplate.update(sql, "root", "123", "23", "女", "123.qq.com");
    }

    /**
     * 根据id查用户
     */
    @Test
    public void testGetUserById() {
        String sql = "select * from t_user where id = ?";
        User user = jdbcTemplate.queryForObject(sql, new BeanPropertyRowMapper<>(User.class), 1);
        System.out.println(user);
    }

    /**
     * 查询所有用户
     */
    @Test
    public void testGetAllUser() {
        String sql = "select * from t_user";
        List<User> list = jdbcTemplate.query(sql, new BeanPropertyRowMapper<>(User.class));

        for (User user : list) {
            System.out.println(user);
        }
    }

    /**
     * 查询一共有多少条数据
     */
    @Test
    public void testGetAllCount() {
        String sql = "select count(*) from t_user";
        Integer integer = jdbcTemplate.queryForObject(sql, Integer.class);
        System.out.println(integer);
    }
}
```

 



#### 4.2 声明式事务概念



##### 4.2.1 编程式事务



事务功能的相关操作全部通过自己编写代码来实现



编程式的实现方式存在缺陷：



-  细节没有被屏蔽：具体操作过程中，所有细节都需要程序员自己来完成，比较繁琐。 
-  代码复用性不高：如果没有有效抽取出来，每次实现功能都需要自己编写代码，代码就没有得到复用 



##### 4.2.2 声明式事务



既然事务控制的代码有规律可循，代码的结构基本是确定的，所以框架就可以将固定模式的代码抽取出来，进行相关的封装。封装起来后，我们只需要在配置文件中进行简单的配置即可完成操作。



**优点：**



1. 提高开发效率
2. 消除了冗余的代码
3. 框架会综合考虑相关领域中在实际开发环境下有可能遇到的各种问题，进行了健壮性、性能等各个方面的优化



**总结：**



**编程式**：**自己写代码**实现功能



**声明式**：通过**配置**让**框架**实现功能



#### 4.3 基于注解的声明式事务



##### 4.3.1 准备工作



1.  加入依赖 

```xml
<dependencies>
    <!-- 基于Maven依赖传递性，导入spring-context依赖即可导入当前所需所有jar包 -->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
        <version>5.3.1</version>
    </dependency>
    <!-- Spring 持久化层支持jar包 --> <!-- Spring 在执行持久化层操作、与持久化层技术进行整合过程中，需要使用orm、jdbc、tx三个 jar包 --> <!-- 导入 orm 包就可以通过 Maven 的依赖传递性把其他两个也导入 -->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-orm</artifactId>
        <version>5.3.1</version>
    </dependency>
    <!-- Spring 测试相关 -->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-test</artifactId>
        <version>5.3.1</version>
    </dependency>
    <!-- junit测试 -->
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.12</version>
        <scope>test</scope>
    </dependency>
    <!-- MySQL驱动 -->
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>8.0.16</version>
    </dependency>
    <!-- 数据源 -->
    <dependency>
        <groupId>com.alibaba</groupId>
        <artifactId>druid</artifactId>
        <version>1.0.31</version>
    </dependency>
</dependencies>
```

 

1.  jdbc.properties 

```properties
jdbc.driver=com.mysql.cj.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/atguigu?serverTimezone=UTC
jdbc.username=root
jdbc.password=wangkun1
```

 

1.  配置spring配置文件 

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd">

    <!--扫描组件，将组件交给spring管理-->
    <context:component-scan base-package="com.wangkun.spring.jdbc"/>

    <!--引入外部文件-->
    <context:property-placeholder location="jdbc-properties.properties"/>

    <!--配置数据源-->
    <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
        <property name="driverClassName" value="${jdbc.driver}"/>
        <property name="url" value="${jdbc.url}"/>
        <property name="username" value="${jdbc.username}"/>
        <property name="password" value="${jdbc.password}"/>
    </bean>

    <!--配置 JDBCTemplate-->
    <bean class="org.springframework.jdbc.core.JdbcTemplate">
        <property name="dataSource" ref="dataSource"/>
    </bean>
</beans>
```

 

1.  创建数据库 

```sql
CREATE TABLE `t_book` (
    `book_id` int(11) NOT NULL AUTO_INCREMENT COMMENT '主键',
    `book_name` varchar(20) DEFAULT NULL COMMENT '图书名称',
    `price` int(11) DEFAULT NULL COMMENT '价格',
    `stock` int(10) unsigned DEFAULT NULL COMMENT '库存（无符号）',
    PRIMARY KEY (`book_id`)
) ENGINE=InnoDB AUTO_INCREMENT=3 DEFAULT CHARSET=utf8;
insert into `t_book`(`book_id`,`book_name`,`price`,`stock`) values (1,'斗破苍穹',80,100),(2,'斗罗大陆',50,100);
CREATE TABLE `t_user` (
    `user_id` int(11) NOT NULL AUTO_INCREMENT COMMENT '主键',
    `username` varchar(20) DEFAULT NULL COMMENT '用户名',
    `balance` int(10) unsigned DEFAULT NULL COMMENT '余额（无符号）',
    PRIMARY KEY (`user_id`)
) ENGINE=InnoDB AUTO_INCREMENT=2 DEFAULT CHARSET=utf8;
insert into `t_user`(`user_id`,`username`,`balance`) values (1,'admin',50);
```

 

1.  写Controller、Service、Dao三层结构 
2.  在Spring配置文件中加入配置 

```xml
<!--配置 JDBCTemplate-->
<bean class="org.springframework.jdbc.core.JdbcTemplate">
    <property name="dataSource" ref="dataSource"/>
</bean>

<!-- 配置事务管理器 -->
<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
    <property name="dataSource" ref="dataSource"/>
</bean>

<!-- 开启事务的注解驱动，将使用@Transactional注解所标识的方法或类中所有的方法使用事务进行管理 -->
<tx:annotation-driven transaction-manager="transactionManager"/>
```

 

1.  添加事务注解
   因为service层表示业务处理层，一个方法表示一个完成的功能，因此处理事务一般在service层处理在BookServiceImpl的buyBook()添加注解[@Transactional ]() 
   结果：因为使用了Spring的声明式事务，更新库存和更新余额都没有执行；只有全部成功才会执行 
2.  @Transactional注解标识的位置 

- - 标识在方法上，只会影响该方法
  - 标识在类上，会影响类中所有的方法



## Spring MVC



### 1. SpringMVC 简介



#### 1.1 什么是MVC



MVC是一种软件架构的思想，将软件按照：控制器、模型、视图 来划分

 

1. View：视图层，指前端的页面
2. Model：模型层，指工程中的JavaBean，作用是处理数据。包括两类： 

- - 实体类Bean：专门存储业务数据，如：User，Student
  - 业务处理的Bean：指Service，Dao对象，专门处理业务逻辑与数据访问

 

1. Controller：控制层，接收请求与响应请求



MVC的工作流程：用户通过视图层发送请求到达控制层，服务器进行请求分发，经过模型层（Service > Dao）处理，处理完毕后将结果返回Controller，Controller再根据请求处理的结果找到相应的View视图，渲染数据后最终响应给浏览器



#### 1.2 什么是SpringMVC

SpringMVC是Spring的一个后续产品，是Spring的一个子项目

SpringMVC 是 Spring 为表述层开发提供的一整套完备的解决方案。在表述层框架历经 Strust、WebWork、Strust2 等诸多产品的历代更迭之后，目前业界普遍选择了 SpringMVC 作为 Java EE 项目表述层开发的**首选方案**。

**注：**三层架构分为表述层（或表示层）、业务逻辑层、数据访问层

表述层表示 **前台页面** 和 **后台servlet**

#### 1.3 Spring MVC的特点

- **Spring** **家族原生产品**，与 IOC 容器等基础设施无缝对接
- 基于原生的Servlet，通过了功能强大的前端控制器DispatcherServlet，对请求和响应进行统一处理
- 表述层各细分领域需要解决的问题**全方位覆盖**，提供**全面解决方案**
- **代码清新简洁**，大幅度提升开发效率
- 内部组件化程度高，可插拔式组件**即插即用**，想要什么功能配置相应组件即可
- **性能卓著**，尤其适合现代大型、超大型互联网项目要求



### 2 案例入门

#### 2.1 创建Maven工程

1.  添加web模块 
2.  打包方式war包 
3.  引入依赖 

```xml
<dependencies>
    <!-- SpringMVC -->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-webmvc</artifactId>
        <version>5.3.1</version>
    </dependency>
    <!-- 日志 -->
    <dependency>
        <groupId>ch.qos.logback</groupId>
        <artifactId>logback-classic</artifactId>
        <version>1.2.3</version>
    </dependency>
    <!-- ServletAPI -->
    <dependency>
        <groupId>javax.servlet</groupId>
        <artifactId>javax.servlet-api</artifactId>
        <version>3.1.0</version>
        <scope>provided</scope>
    </dependency>
    <!-- Spring5和Thymeleaf整合包 -->
    <dependency>
        <groupId>org.thymeleaf</groupId>
        <artifactId>thymeleaf-spring5</artifactId>
        <version>3.0.12.RELEASE</version>
    </dependency>
</dependencies>
```

 



#### 2.2 配置web.xml



注册SpringMVC的前端控制器DispatcherServlet

 

url-pattern中/和/*的区别

 

- / : 匹配浏览器想服务器发送所有请求（不包括.jsp）
- /*：匹配浏览器想服务器发送所有请求（包括.jsp）



```xml
<!DOCTYPE web-app PUBLIC
        "-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN"
        "http://java.sun.com/dtd/web-app_2_3.dtd" >

<web-app>
    <display-name>Archetype Created Web Application</display-name>

    <servlet>
        <servlet-name>SpringMVC</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>

        <!-- 设置SpringMVC配置文件的位置 -->
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:springmvc.xml</param-value>
        </init-param>
      
        <!-- 将servlet初始化时间提前到服务器启动时 -->
        <load-on-startup>1</load-on-startup>
    </servlet>

    <servlet-mapping>
        <servlet-name>SpringMVC</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>
</web-app>
```



**拓展配置：**



-  可通过init-param标签设置SpringMVC配置文件的位置和名称 
-  通过load-on-startup标签设置SpringMVC前端控制器DispatcherServlet的初始化时间 



#### 2.3 创建请求控制器



由于前端控制器对浏览器发送的请求进行了统一的处理，但是具体的请求有不同的处理过程，因此需要创建处理具体请求的类，即请求控制器



请求控制器中每一个处理请求的方法成为控制器方法



因为SpringMVC的控制器由一个POJO（普通的Java类）担任，因此需要通过@Controller注解将其标识为一个控制层组件，交给Spring的IOC容器管理，此时SpringMVC才能够识别控制器的存在



```java
@Controller
public class HelloController {

    @RequestMapping("/")
    public String protal() {
        return "index";
    }

    @RequestMapping("/about")
    public String about() {
        return "about";
    }
}
```



#### 2.4 创建SpringMVC配置文件



```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd">


    <!-- 自动扫描包 -->
    <context:component-scan base-package="com.wangkun.controller"/>

    <!-- 配置Thymeleaf视图解析器 -->
    <bean id="viewResolver" class="org.thymeleaf.spring5.view.ThymeleafViewResolver">
        <property name="order" value="1"/>
        <property name="characterEncoding" value="UTF-8"/>
        <property name="templateEngine">
            <bean class="org.thymeleaf.spring5.SpringTemplateEngine">
                <property name="templateResolver">
                    <bean class="org.thymeleaf.spring5.templateresolver.SpringResourceTemplateResolver">
                        <!-- 视图前缀 -->
                        <property name="prefix" value="/WEB-INF/templates/"/>
                        <!-- 视图后缀 -->
                        <property name="suffix" value=".html"/>
                        <property name="templateMode" value="HTML5"/>
                        <property name="characterEncoding" value="UTF-8"/>
                    </bean>
                </property>
            </bean>
        </property>
    </bean>

</beans>
```



#### 2.5 总结



**自己理解：**用户请求服务器，如果路径符合前端控制器的url-pattern，就会被DispatchServlet所控制，然后读取SpringMVC配置文件，根据所匹配到的方法的返回值，去寻找资源



**较准确的解释：**浏览器发送请求，若请求地址符合前端控制器的url-pattern，该请求就会被前端控制器DispatcherServlet处理。前端控制器会读取SpringMVC的核心配置文件，通过扫描组件找到控制器，将请求地址和控制器中@RequestMapping注解的value属性值进行匹配，若匹配成功，该注解所标识的控制器方法就是处理请求的方法。处理请求的方法需要返回一个字符串类型的视图名称，该视图名称会被视图解析器解析，加上前缀和后缀组成视图的路径通过Thymeleaf对视图进行渲染，最终转发到视图所对应页面

### 3. @RequestMappering注解

#### 3.1 @RequestMappering注解的功能

从注解名称上我们可以看到，@RequestMapping注解的作用就是将请求和处理请求的控制器方法关联起来，建立映射关系。

SpringMVC 接收到指定的请求，就会来找到在映射关系中对应的控制器方法来处理这个请求。

#### 3.2 @RequestMappering注解的位置

-  @RequestMapping标识一个类：设置映射请求的请求路径的初始信息 
-  @RequestMapping标识一个方法：设置映射请求请求路径的具体信息 



```java
package com.wangkun.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;

@Controller
@RequestMapping("/test")
public class TestRequestMappingController {

    @RequestMapping("/")
    public String home() {
        return "index";
    }

    @RequestMapping("/about")
    public String about() {
        return "about";
    }
  
}
```



#### 3.3 获取参数



##### 3.3.1 获取普通参数



```java
@RequestMapping("/param")
public String getParam(String username, String password) {
    System.out.println("用户名：" + username + "密码：" + password);
    return "about";
}
```



3.3.2 获取实体类对象



**实体类**



```java
public class User {
    private Integer id;

    private String username;

    private String password;
}
```



**获取参数**



```java
@RequestMapping("/param/pojo")
public String getParamUser(User user) {

    System.out.println(user);
    return "about";

}
```



#### 3.4 解决参数乱码



**web.xml**



```xml
<!-- 配置Spring的编码过滤器 -->
<filter>
    <filter-name>CharacterEncodingFilter</filter-name>
    <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
    <init-param>
        <param-name>encoding</param-name>
        <param-value>UTF-8</param-value>
    </init-param>
    <init-param>
        <param-name>forceEncoding</param-name>
        <param-value>true</param-value>
    </init-param>
</filter>
<filter-mapping>
    <filter-name>CharacterEncodingFilter</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```



### 4. 域对象共享数据



#### 4.1 请求域共享数据



**Controller层**



```java
@Controller
public class TestScopeController {

    @RequestMapping("/test/model")
    public String testMode(Model model) {
        model.addAttribute("testRequestScope", "hello,Model");
        return "about";
    }
}
```



**about.html**



```html
<p th:text="${testRequestScope}"></p>  // hello,Model
```



#### 4.2 会话域、应用域共享数据



**Controller层**



```java
// 会话域
@RequestMapping("/test/session")
public String testSession(HttpSession session) {
    session.setAttribute("testScopeSession", "hello,session");
    return "about";
}

// 应用域
@RequestMapping("/test/application")
public String testApplication(HttpSession session) {
    ServletContext application = session.getServletContext();
    application.setAttribute("testScopeApplication", "hello,application");
    return "about";
}
```



**about.html**



```html
<p th:text="${testRequestScope}"></p>
<p th:text="${session.testScopeSession}"></p>
<p th:text="${application.testScopeApplication}"></p>
```

### 5. RESTful