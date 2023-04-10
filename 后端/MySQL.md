# 初识MySQL

E:\mysql\mysql-8.0.32-winx64\bin>mysqld --initialize --console
2023-04-10T08:31:28.151539Z 0 [Warning] [MY-010918] [Server] 'default_authentication_plugin' is deprecated and will be removed in a future release. Please use authentication_policy instead.
2023-04-10T08:31:28.151564Z 0 [System] [MY-013169] [Server] E:\mysql\mysql-8.0.32-winx64\bin\mysqld.exe (mysqld 8.0.32) initializing of server in progress as process 10568
2023-04-10T08:31:28.152979Z 0 [Warning] [MY-013242] [Server] --character-set-server: 'utf8' is currently an alias for the character set UTF8MB3, but will be an alias for UTF8MB4 in a future release. Please consider using UTF8MB4 in order to be unambiguous.
2023-04-10T08:31:28.167506Z 1 [System] [MY-013576] [InnoDB] InnoDB initialization has started.
2023-04-10T08:31:28.482455Z 1 [System] [MY-013577] [InnoDB] InnoDB initialization has ended.
2023-04-10T08:31:29.444147Z 6 [Note] [MY-010454] [Server] A temporary password is generated for root@localhost: efM<LThYN6Ho



### 安装

1. 官网安装对应版本mySQL

[MySQL :: Download MySQL Community Server](https://dev.mysql.com/downloads/mysql/)

2. 傻瓜式安装

3. 点击系统便好设置，会出现MySql图标

   ```
   PATH="$PATH":/usr/local/mysql/bin
   ```

4. 在终端里输入ls -a会出现一堆列表，如果列表里没有这个.bash_profile需要手动创建一下：

5. 输入`open .bash_profile`打开文档，复制这个路径并保存：

   ```
   export PATH=$PATH:/usr/local/mysql/bin
   
   export PATH=$PATH:/usr/local/mysql/support-files
   ```

   然后按`ESC: wq   `退出

   如果没有.bash_profile，就`vim ./.bash_profile`创建一个

6. 重新打开终端，输入`source .bash_profile`随机生效。

此时输入`mysql -u root -p`可以正常输入密码，然后进入mysql程序了：

7. 然后通过以下命令登陆MySQL

```
mysql -u root -p
```

8. 出现这个安装成功

```
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 8
Server version: 8.0.30 MySQL Community Server - GPL

Copyright (c) 2000, 2022, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
```

### 启动

1. 启动：`mysql -u root -P3306 -hlocalhost -p`：`-P`后面写端口号，`-h`后面写主机，`-p`后面添密码

### SQl语言

#### DDL：数据定义语言

创建数据库对象

CREATE、DROP、RENAME

#### DML：数据操作语言

INSERT、DELETE、UPDATE、SELECT（增删改查）

#### DCL：数据控制语言

COMMIT、ROLLBACK

#### 导入现有数据表，表的数据

1. 方式一：`source + 文件的全路径名`

   `source /Users/wangkun/Desktop/MySQL/atguigudb.sql `

2. 方式二：使用图形化界面工具

#### 进入指定数据库

`use atguigudb;` ：use 表名

#### 查看数据可结构

`show table;`

#### 查看具体表中数据

`select * from employees;`

### 3. 基本的select语句

##### SELECT...FROM...

`SELECT 字段一,字段二 FROM 表明`‘

1. `SELECT * FROM employees;`：查找employees中所有列的数据
2. `SELECT first_name, last_name FROM employees;`：查找employees中指定字段的列的数据

##### 设置别名

1. `SELECT first_name, last_name, Salary 薪水 `
2. `SELECT first_name, last_name, Salary as 薪水 `
3. `SELECT first_name, last_name, Salary "薪水" `

##### 去除重复行: DISTINCT

`SELECT DISTINCT department_id FROM employees e `：去除指定表的重复行

##### 着重号

SELECT * FROM  `order`;：如果表名与关键字重名，需要将表名用着重号包裹

##### 查询常数

`SELECT '尚硅谷', department_id FROM employees;`： 在表的最前部加一个常数列

##### 显示表结构

`DESCRIBE employees;`：查询指定表结构，将表每个字段的信息显示出来

##### 过滤

WHERE必须写在FROM后面。

```
SELECT *
FROM employees
WHERE last_name = 'King';
```

### 4. 运算符

##### 算术运算符

+、-、*、/、%

##### 比较运算符

1. `=  !=`：字符串会隐式转换，如果不能转换成数值，就变成0
2. null跟任何东西比结果都是null，用=的话（NULL = NULL）返回NULL
3. ​	安全等于 `<=>`:（NULL <=> NULL）返回1
4. IS NULL：判断是否是NULL：`WHERE comssion_pct IS NULL`会将commssion_pct中值为null的全都过滤出来，与3等价

5. IS NOT NULL：与4相反
6. BEWTTEN AND：`WHERE SALARY BETWEEN 6000 AND 8000;`  过滤出薪资6000到8000的员工。
7. IN :`WHERE department_id IN (10, 20, 30);`：过滤指定字段等于10,20,30的数据

8. LIKE:模糊查询，%：表示任意个字符，_表示一个字符，\ 转移字符可将\后面的符号转移成真正的符号

9. REGEXP：正则表达式

##### 逻辑运算符

1. || =  RO： 或（and优先级 高于 或）
2. && = AND：并
3. NOT = !： 取反

### 5. 排序

##### 语法

DESC 降序。ASC 升序

```sql
SELECT employee_id, salary
FROM employees
-- 升序
ORDER BY salary ASC;
```

可以在`ORDER BY`中使用别名，但是不能在WHERE中使用别名

因为语句执行顺序是：FROM > WHERE > SELECT > ORDER BY 

##### 多级排序

用`,`分隔开

```
SELECT employee_id, salary, department_id 
FROM employees
ORDER BY department_id DESC, salary ASC ;
```

### 分页

第一个参数是偏移量，表示从第几个数据开始

第二个参数是条数，表示显示多少条数据

公式：(pageNo - 1) * pageSize, pageSize

pageNo：第几页。  pageSize：显示条数

```sql
SELECT employee_id, last_name,salary
FROM employees
WHERE salary > 6000
ORDER BY salary DESC
-- 分页，从0条到30条
-- LIMIT 0, 30;
-- 或者 使用8.0新特性
LIMIT 30 OFFSET 0;
```

### 6.多表查询

1. 给表设置了别名，在WHERE中一定要用，不然会报错
2. WHERE每个条件用AND连接 

```
SELECT e.employee_id, d.department_name, l.city, e.department_id 
FROM employees e , departments d ,locations l 
WHERE e.department_id = d.department_id
AND d.location_id = l.location_id ;
```

##### 非等值连接

根据员工薪资在job_grade中等级的范围，判断出薪资等级

```
SELECT e.last_name, e.salary, jg.grade_level
FROM employees e , job_grades jg
WHERE e.salary BETWEEN jg.lowest_sal AND jg.highest_sal ;
```

##### 自连接

查询员工id，姓名，及其管理者的id和姓名

> 将一个表各自取不同的别名，注意要SELECT行添加表的别名

```
SELECT e1.employee_id, e1.last_name, e2.employee_id, e2.last_name
FROM employees e1, employees e2
WHERE e1.manager_id = e2.employee_id ;
```

##### 内连接与外连接

1. 内连接：上面学的都是内连接，就是两个表的交集，也就是两个表满足条件的部分
2. 外连接，将两个表不满足条件的也显示出来，

3. 外连接
   1. 左外连接
   2. 右外连接
   3. 满外连接

99实现内连接

```sql
SELECT employee_id, department_id, city
FROM employees e JOIN departments d
ON e.department_id = d.department_id
JOIN locations l
ON d.location_id = l.location_id
```

##### 99实现内外连接6张图

![1554979255233](/Users/wangkun/Desktop/MySQL/1554979255233.png)

```sql
-- 左上图
SELECT employee_id, department_name
FROM employees e LEFT JOIN departments d 
ON e.department_id = d.department_id;

-- 右上图
SELECT employee_id, department_name
FROM employees e RIGHT JOIN departments d 
ON e.department_id = d.department_id;

-- 左中图
SELECT employee_id, department_name
FROM employees e LEFT JOIN departments d 
ON e.department_id = d.department_id 
WHERE d.department_id IS NULL;

-- 右中图
SELECT employee_id, department_name
FROM employees e RIGHT JOIN departments d 
ON e.department_id = d.department_id 
WHERE e.department_id IS NULL;

-- 左下
SELECT employee_id, department_name
FROM employees e LEFT JOIN departments d 
ON e.department_id = d.department_id 
UNION ALL 
SELECT employee_id , department_name
FROM employees e2 RIGHT JOIN departments d2 ON e2.department_id = d2.department_id 
WHERE e2.department_id IS NULL;

-- 右下
SELECT employee_id, department_name
FROM employees e LEFT JOIN departments d 
ON e.department_id = d.department_id 
WHERE d.department_id IS NULL 
UNION ALL 
SELECT employee_id , department_name
FROM employees e2 RIGHT JOIN departments d2 
ON e2.department_id = d2.department_id 
WHERE e2.department_id IS NULL;
```

### 7.函数

#### 数值型函数

```
-- 向上取整：ceiling， 向下取整：floor， 指定位数四舍五入：round，指定位数截取不四舍五入truncate，随机0-1数值rand 
SELECT CEILING(12.3), FLOOR(12.3), ROUND(1.235, 2), TRUNCATE(1.235, 2), PI(), RAND() ; 
```

#### 字符串函数 

#### 日期函数

```
TIMESTAMPDIFF(YEAR, hire_date,now()) 

```

#### 流程控制函数

1. if

   `IF(条件,1,2)`：条件为真返回1，为假返回2

2. ifnull

   `IFNULL(1,2)`：1为null返回2，1不为null返回1

3. nullif

   `NULLIF(1,2)`：1等于2返回NULL，不想等返回1

4. case when then end

   自定义列，并根据when中的条件自动填内容

   ```
   SELECT last_name, salary, 
   CASE 
   	WHEN salary < 8000 THEN 'c'
   	WHEN salary >= 8000 AND salary < 10000 THEN 'b'
   	WHEN salary >= 10000 THEN 'a'
   END '评级'
   FROM employees;
   ```

#### 聚合函数

聚合之后，查询条件不能跟其他的列

1. Max：最大值

   ```
   -- 查出最高的工资
   SELECT MAX(salary)
   FROM employees e ;
   
   -- 将都是最高工资的人列出来
   SELECT last_name,salary
   FROM employees e 
   WHERE e.salary = 24000;
   ```

2. Min：最小值

3. avg：平均值
4. sum：总和
5. count(*)：一共有多少行

#### 分组

group by：将表按指定列分组，查询条件不能跟其他；列，通畅与聚合函数一起用

having：跟where功能差不多

having与where的区别

1. where要跟在from后面，并且比分组更先执行，
2. having是分完组在筛选根据条件筛选

```sql
-- 按部门id分组，并显示每个部门最高的工资
SELECT department_id,MAX(salary) , COUNT(*)
FROM employees e 
GROUP BY department_id 
HAVING e.department_id = 20;
```

### 8.子查询

两个子查询嵌套在一起

需求：查找工资比Abel高的员工

#### 是自查询也能实现，但是有局限性，有时候不能满足需求

```
SELECT e2.last_name, e2.salary
FROM employees e JOIN employees e2 
WHERE e.last_name = 'Abel'
AND e2.salary > e.salary ;
```

#### 用子查询实现

```
SELECT last_name, salary
FROM employees e 
WHERE e.salary > (
					SELECT salary 
					FROM employees e2 
					WHERE e2.last_name = 'Abel'
				)
```

##### 单行子查询

符号：`= != > < >= <=`

内查询返回单条数据

##### 多行子查询

符号：`in any all some(= any)`

内查询返回多条数据

##### 不相关子查询

内查询结果是单一的，不会根据外循环改变而改变

##### 相关子查询

内循环查询结果不单一，根据外循环的需要而改变

在select中，除了group by和 limit之外，其他位置都可以声明子查询

###### 在where中

```sql
SELECT *
FROM departments d 
WHERE department_id = (
						SELECT department_id
						FROM employees e 
						GROUP BY department_id 
						HAVING  AVG(salary) = (
											SELECT AVG(salary) avg_sal
											FROM employees e 
											GROUP BY department_id
											ORDER BY avg_sal
											LIMIT 1
											)
						);
```

###### 在from中

```sql
SELECT d.*
FROM departments d ,(
					SELECT AVG(salary) avg_sal,department_id 
					FROM employees e 
					GROUP BY department_id 
					ORDER BY avg_sal
					LIMIT 1
					) t_avg_sal
WHERE d.department_id = t_avg_sal.department_id;		
```

在select中

```sql
SELECT d.*, (SELECT AVG(salary) FROM employees e2  WHERE e2.department_id= d.department_id ) avg_sal
FROM departments d ,(
					SELECT AVG(salary) avg_sal,department_id 
					FROM employees e 
					GROUP BY department_id 
					ORDER BY avg_sal
					LIMIT 1
					) t_avg_sal
WHERE d.department_id = t_avg_sal.department_id;	
```

### 9.DDL：数据定义语言

#### 数据库的创建、修改、删除

mysql数据可从大到小依次是数据库服务器、数据库、数据表、数据表的行与列

##### 相关操作

```sql
-- 创建
CREATE DATABASE mytest1;
-- 查看当前连接中的数据库有哪些
SHOW DATABASES;
-- 创建数据库，如果存在则不创建，并制定字符集
CREATE DATABASE IS NOT EXISTS mytest1 CHARACTER SET 'UTF8';
-- 切换数据库
USE mytest1;
-- 查看当前数据库中保存的数据表
SHOW TABLES;
-- 查看当前使用的数据库
SELECT DATABASE();
-- 查看指定数据库保存的数据表
SHOW TABLE FROM mytest1;
```

##### 修改数据库

```sql
-- 修改数据库字符集
ALTER DATABASE mytest1 CHARACTER SET 'UTF8';
-- 查看创建数据库时的详细信息
SHOW CREATE DATABASE mytest1;
```

##### 删除数据库

```sql
-- 如果存在就删，不存在就不删
drop database if exists mytest1;
```

##### 创建表

```sql
-- 创建表
create table person(
name varchar(15),
age int
);

-- 查询表结构
DESC person;

-- 创建表，如果存在则不创建
create table if not exists person1 (
name varchar(15),
age int
);

-- 借助已存在的表创建新表
create table if not exists person2 
as
select last_name, employee_id
from employees e ;

```

##### 修改表，删除表

```sql
# 增加一个字段(一列)
DESC person1;
ALTER TABLE person1
ADD sex VARCHAR(2);

# 修改一个字段
ALTER TABLE person1
MODIFY name varchar(10);

# 重命名字段
ALTER TABLE person1 
CHANGE name uname VARCHAR(10);

# 删除一个字段，一列
ALTER TABLE person1 
DROP COLUMN uname;

-- 重命名表
RENAME TABLE person1 
TO person1_re;

-- 删除表
show tables;

-- 清空表
SELECT *
FROM person2 p ;
TRUNCATE TABLE  person2 ;
```

###### delete from 与 truncate的区别

DDL语句执行后会自动执行一边 commit，`SET autocommit = fALSE;`无效

DML(增删改查操作)不会自动执行一遍commit，可以通过`SET autocommit = fALSE;`实现ROLLBACK

```sql
-- TRUNCATE(DDL) 清空表数据，保留表结构，不可回
-- TELETE FROM(DML) 清空表数据，保留表结构，可以通过设置`SET autocommit = fALSE;`，进行回滚

-- DELETE FROM

SET autocommit = FALSE ;

DELETE FROM person3;

ROLLBACK;
-- 回滚成功，表数据恢复

-- TRUNCATE TABLE 
SELECT *
FROM person3;

TRUNCATE TABLE person3;

ROLLBACK;
-- 回滚失败，表数据不恢复
```

### 10.DML：数据库的增删改查

#####  向表中加入数据

```sql
SELECT *
FROM person2 p ;

-- 方式一，自定义一条一条加
INSERT INTO person2(last_name, employee_id) 
VALUES 
('wangkun', 123),
('chenghao', 456);

-- 方式二，借助现有表加数据
-- 需要保证新表的条件范围大于旧表
INSERT INTO person2(last_name, employee_id)
SELECT last_name , employee_id 
FROM employees  
WHERE department_id IN (70,90);
```

##### 更新表中数据

```sql
-- 更新表数据
SELECT *
FROM person2 p ;

UPDATE person2 
SET last_name = 'xiaohuang', employee_id = 1000
WHERE last_name = 'dahuang';
```

##### 删除表中数据(行)

```
DELETE FROM person2
WHERE last_name = 'xiaohuang';
```

##### 计算列

表中的一列是根据同一行他的列计算而来

```sql
CREATE TABLE IF NOT EXISTS test1(
a INT,
b INT,
c INT GENERATED ALWAYS AS (a + b) VIRTUAL  -- 计算列
);

INSERT INTO test1(a,b) 
VALUES
(1,2),
(3,5);
```

### 11.数据结构

通畅整形用：int

浮点数用：DECIMAL

时间用DATATIME

任何字段如果没有负数，必须是UNSIGNED

存储字符串长度基本相等，用char

### 12.约束

`NOT NULL`：非空约束

`UNIQUE`：唯一约束，取值不允许重复

`PRIMARY KEY`： 主键约束(主关键字)，自带非空、唯一、索引

`DEFAULT`：默认值(缺省值)

`FOREIGN KEY`：外键约束(外关键字)，关联另一张表的一列，本表列的值只能是关联列里有的值

```sql
CREATE TABLE auther(
aut_id INT ,
aut_name VARCHAR(50) UNIQUE NOT NULL,
aut_city VARCHAR(50) ,
aut_sex  char(1) DEFAULT '男',
PRIMARY KEY(aut_id)
) ;	

CREATE TABLE book(
aut_id INT,
FOREIGN KEY (aut_id) references auther(aut_id)
);
```

### 13.视图

相当于是一个虚拟表，一般只对视图进行查询操作

```
CREATE VIEW '视图名'
AS 查询语句
```

### 14.存储过程

```sql
CREATE PROCEDURE 存储过程名(IN | OUT | INOUT)
[charateristics...]
BEGIN
存储过程
END
```

### 15.存储函数

```
CREATE FUNCTION 函数名(参数名，参数类型)
[charateristics...]
BEGIN
函数体。函数体中一定要有 RETURN 语句
END
```

### 数据库设计

#### 三范式

1. 要求有主键，并且要求每一个字段原子性不可再分

2. 要求所有非主键字段完全依赖主键，不能产生部分依赖

3. 所有非主键字段和主键字段之间不能产生传递依赖

   





















