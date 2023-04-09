## 概念

> 是Java连接数据库的一项技术，就像我们用数据库图形化界面操作数据库一样

![image-20230303075334957](/Users/wangkun/Library/Application Support/typora-user-images/image-20230303075334957.png)

### 总结

jdbc是java连接数据库技术的统称

**jdbc是由两部分组成：**

1.  Java提供的jdbc规范(接口)
2. 各个数据库厂商的实现驱动jar包

jdbc是一种典型的面向接口编程

**涉及具体核心类和接口：**

> 引入jar包 ==> 建立连接 ==> 发送SQL语句 ==> 获取结果

- Drivermanager
  1. 将第三方数据库厂商的实现驱动jar注册到程序中
  2. 可以根据数据库连接信息获取connection
- Connection
  1. 和数据库简历的连接，在连接对象上，可以多次执行数据库curd动作
  2. 可以获取statement和perparestatement，callablestatement对象
- statement ｜ perparestatement ｜ callablestatement
  1. 具体发送SQL语句到数据库管理软件的对象
  2. 不同发送方式稍有不同！perparedStatement使用为重点
- Result
  1. 面向对象思维的产物(抽象成数据库的查询结果表)
  2.  存储DQl插叙数据库结果的对象
  3. 需要我们进行解析，获取具体的数据库数据

## 基本使用

### 使用步骤

1. 导入相应版本jar包
2. 注册驱动
   - 驱动版本 8+ com..mysql.cj.jdbc.Driver
   - 驱动版本 5+ com..mysql.jdbc.Driver
3. 获取连接
4. 创建发送sql语句对象
5. 发送sql语句，获取结果
6. 结果集解析
7. 资源关闭

### statement

> 很少使用

```java
package com.wangkun.mjdbc;

import java.sql.*;
import java.util.Scanner;

public class statementQueryTest1 {
    public static void main(String[] args) throws ClassNotFoundException, SQLException {
        // 用户输入账号密码
        Scanner scanner = new Scanner(System.in);
        System.out.println("请输入账号");
        String count = scanner.nextLine();
        System.out.println("请输入密码");
        String password = scanner.nextLine();

        // 1. 注册驱动
        Class.forName("com.mysql.cj.jdbc.Driver");
        // 2. 连接数据库
        Connection connection = DriverManager.getConnection("jdbc:mysql://127.0.0.1:3306/atguigu", "root", "wangkun1");
        // 3. 创建sql对象，执行sql语句
        Statement statement = connection.createStatement();
        String sql = "SELECT * FROM t_user WHERE account = '" + count + "' AND password = '" + password + "';";
        // 4. 获取结果
        /**
         * 增删改：executeUpdate(sql);
         * 查：executeQuery(sql);
         */
        ResultSet resultSet = statement.executeQuery(sql);
        if (resultSet.next()) {
            System.out.println("登陆成功");
        } else {
            System.out.println("登陆失败");
        }
        // 5. 关闭资源
        // 关闭接收结果的容器
        resultSet.close();
        // 关闭连接通道
        connection.close();
        // 关闭sql对象
        statement.close();

    }
}
```

### prepareStatement

#### 基本使用

```java
package com.wangkun.mjdbc;

import java.sql.*;
import java.util.Scanner;

public class PrepareStatementTest {
    public static void main(String[] args) throws ClassNotFoundException, SQLException {
        // 获取用户账号密码
        Scanner scanner = new Scanner(System.in);
        System.out.println("请输入用户名：");
        String count = scanner.nextLine();
        System.out.println("请输入密码：");							
        String password = scanner.nextLine();

        // 1. 注册驱动
        Class.forName("com.mysql.cj.jdbc.Driver");
        // 2. 连接数据库
        Connection connection = DriverManager.getConnection("jdbc:mysql://127.0.0.1:3306/atguigu", "root", "wangkun1");
        // 3. 创建prepareStatement对象，并执行sql语句
        String sql = "SELECT * FROM t_user WHERE account = ? AND password = ? ";
        PreparedStatement preparedStatement = connection.prepareStatement(sql);
        // 设置参数
        preparedStatement.setObject(1, count);
        preparedStatement.setObject(2, password);
        // 4. 获取结果
        ResultSet resultSet = preparedStatement.executeQuery();
        if (resultSet.next()) {
            System.out.println("登陆成功");
        } else {
            System.out.println("登陆失败");
        }
        // 5. 关闭资源
        // 关闭通道
        connection.close();
        // 关闭发送sql的对象
        preparedStatement.close();
        // 关闭接收结果的对象
        resultSet.close();
    }
}
```

### crud

```java
package com.wangkun.mjdbc;

import org.junit.Test;

import java.sql.*;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

public class PrepareStatementTest2 {
    /**
     * 新增数据
     * 账号：test、密码：test、nickname：程浩
     */
    @Test
    public void testInsert() throws ClassNotFoundException, SQLException {

        // 1. 注册驱动
        Class.forName("com.mysql.cj.jdbc.Driver");
        // 2. 连接数据库
        Connection connection = DriverManager.getConnection("jdbc:mysql://127.0.0.1:3306/atguigu", "root", "wangkun1");

        // 3. 创建sql对象，发送sql语句
        String sql = "insert into t_user(account, password, nickname) value(?, ?, ?);";
        PreparedStatement preparedStatement = connection.prepareStatement(sql);
        preparedStatement.setObject(1, "test");
        preparedStatement.setObject(2, "test");
        preparedStatement.setObject(3, "程浩");
        int row = preparedStatement.executeUpdate();
        // 4. 获取结果
        if (row > 0) {
            System.out.println("插入成功");
        } else {
            System.out.println("插入失败");
        }
        // 5. 关闭资源
        connection.close();
        preparedStatement.close();
    }

    /**
     * 修改数据
     * 将id为3的nickname 改为三狗子
     */
    @Test
    public void testUpdate() throws ClassNotFoundException, SQLException {


        // 1. 注册驱动
        Class.forName("com.mysql.cj.jdbc.Driver");

        // 2. 连接数据库
        Connection connection = DriverManager.getConnection("jdbc:mysql://127.0.0.1:3306/atguigu", "root", "wangkun1");

        // 3. 创建sql对象
        String sql = "update t_user set nickname = ? where id = ?";
        PreparedStatement preparedStatement = connection.prepareStatement(sql);
        preparedStatement.setObject(1, "三狗子");
        preparedStatement.setObject(2, 3);

        // 4. 获取结果
        int i = preparedStatement.executeUpdate();
        if (i > 0) {
            System.out.println("修改成功");
        } else {
            System.out.println("修改失败");
        }

        // 5. 关闭资源
        connection.close();
        preparedStatement.close();

    }

    /**
     * 删除数据
     * 将id为3的数据删除
     */
    @Test
    public void testDel() throws ClassNotFoundException, SQLException {

        Class.forName("com.mysql.cj.jdbc.Driver");

        Connection connection = DriverManager.getConnection("jdbc:mysql://127.0.0.1:3306/atguigu", "root", "wangkun1");

        String sql = "delete from t_user where id = ?";
        PreparedStatement preparedStatement = connection.prepareStatement(sql);
        preparedStatement.setObject(1, 3);

        int i = preparedStatement.executeUpdate();

        if (i > 0) {
            System.out.println("删除成功");
        } else {
            System.out.println("删除失败");
        }

        preparedStatement.close();
        connection.close();
    }

    /**
     * 查找数据
     * 查询t_user表中所有的数据，并用MapList接收结果
     */
    @Test
    public void testSelect() throws ClassNotFoundException, SQLException {
        // 1. 注册驱动
        Class.forName("com.mysql.cj.jdbc.Driver");
        // 2. 连接数据库
        Connection connection = DriverManager.getConnection("jdbc:mysql://127.0.0.1:3306/atguigu", "root", "wangkun1");

        // 3. 创建sql对象
        String sql = "select * from t_user";
        PreparedStatement preparedStatement = connection.prepareStatement(sql);

        // 4. 获取结果
        List<Map> list = new ArrayList<>();
        // 获取行
        ResultSet resultSet = preparedStatement.executeQuery();
        // 获取列
        ResultSetMetaData metaData = resultSet.getMetaData();
        int columnCount = metaData.getColumnCount();

        while (resultSet.next()) {
            Map map = new HashMap();
            for (int i = 1; i <= columnCount; i++) {
								// System.out.println("resultSet====" + resultSet);
                System.out.println("resultSet.getObject(i)====" + resultSet.getObject(i));
                // 每列的value
                Object value = resultSet.getObject(i);
                // 每列的key
                String columnLabel = metaData.getColumnLabel(i);
                map.put(columnLabel, value);
            }

            list.add(map);
        }
        System.out.println("结果为" + list);

        // 5. 关闭资源
        connection.close();
        resultSet.close();
        preparedStatement.close();

    }
}
```

### 总结

#### 具体步骤

1. 注册驱动
2. 获取连接
3. 编写SQL语句
4. 创建preparedstatement并且传入SQL语句结构
5. 占位符赋值
6. 发送SQL语句,并且获取结果 
7. 结果集解析
8. 关闭资源

#### 代码实现与分析

```java
//1.注册驱动
方案1: 调用静态方法,但是会注册两次
DriverManager.registerDriver(new com.mysql.cj.jdbc.Driver());
方案2: 反射触发
Class.forName("com.mysql.cj.jdbc.Driver");

//2.获取连接

Connection connection = DriverManager.getConnection();

3 (String url,String user,String password)
2 (String url,Properties info(user password))
1 (String url?user=账号&password=密码)

//3.创建statement
//静态
Statement statement = connection.createStatement();
//预编译
PreparedStatement preparedstatement = connection.preparedStatement(sql语句结构);

//4.占位符赋值

preparedstatement.setObject(?的位置 从左到右 从1开始,值)

//5.发送sql语句获取结果

int rows = executeUpdate(); //非DQL
Resultset = executeQuery(); //DQL

//6.查询结果集解析

//移动光标指向行数据 next();  if(next())  while(next())
//获取列的数据即可   get类型(int 列的下角标 从1开始 | int 列的label (别名或者列名))
//获取列的信息   getMetadata(); ResultsetMetaData对象 包含的就是列的信息
                getColumnCount(); | getCloumnLebal(index)
//7.关闭资源
close(); 
```

## 扩展提升

### SQL事务

> 数据库事务就是一种SQL语句执行的缓存机制，不会单条执行完毕就更新数据库数据，最终根据缓存内的多条语句执行结果统一判定
>
> 一个事物内所有语句都成功及事务成功，我们可以出发commit提交事务来结束事务，更新数据
>
> 一个事物内任何一条语句失败，及事务失败，我们可以出发rollback回滚结束事务，数据回到事务之前状态
>

举个例子：

临近高考，你好吃懒做，偶尔瞎花钱，父亲也只会说：”你等着“，等到高考完毕

成绩600+，翻篇，庆祝

成绩200+，翻旧帐，男女混合双打

#### 开启事务的方式

针对自动提交：关闭自动提交即可，多条语句添加以后，最终手动提交或者回滚（推荐）

```
Set autocommit = off; // 关闭当前连接自动事务提交方式
// 只有当前连接有效
// 编写SQL语句即可
SQL
SQL
SQL
// 手动提交或者回滚（结束当前的事务）
COMMIT / ROLLBACK
```

## 连接池

用JDBC去操作数据库，会频繁的创建和销毁连接，造成很多性能消耗，可以用连接池将连接保存起来，然后用的时候取出来，会节约很多资源

![image-20230305133530900](/Users/wangkun/Library/Application Support/typora-user-images/image-20230305133530900.png)

### 主键回显

> 获取订单 mysql子增长的主键

















