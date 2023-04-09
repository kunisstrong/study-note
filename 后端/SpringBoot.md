# 	SpringBoot

## Java知识补充

### 注解

就是对某些事物进行标注说明，存放更多的信息。
 在java层面，注解又叫java标注，java提供了一套机制，使得我们可以对方法、类、参数、包、域以及变量等添加标准(或附加信息，功能也算是信息的一部分)，并且通过某些定义好的实际触发此段信息。

### REST风格

表现形式状态转换

1. 传统风格资源描述形式
   - http://localhost/user/getById?id=1
   - http://localhost/user/saveUser
2. REST风格描述形式
   - http://localhost/user/1
   - http://localhost/user

3. 优点
   - 隐藏资源的访问行为，无法通过地址得知对资源是何种操作
   - 书写简化
4. 参数
   - `RequestBody`：接收JSON数据
   - `PathVariable`：接收路径参数，使用`/{参数名称}`描述路径参数
   - `RequestParam`：接收url地址穿惨或表单传参

```java
package com.itheima.controller;

import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping("books")
public class BookController {
    // get请求
    @GetMapping
    public String GetName() {
        System.out.println("get请求无参数");
        return "get请求无参数";
    }

    // get请求带参数   http://localhost:8080/books/1
    @GetMapping("/{id}")
    public String GetById(@PathVariable Integer id) {
        System.out.println("get请求有参数" + id);
        return "get请求有参数";
    }

    // post请求  要用JSON格式传参
    @PostMapping
    public String addBooks(@RequestBody String book) {
        System.out.println("post请求" + book);
        return "post请求";
    }
}
```

## 基础篇入门案例

### 1. 查看maven版本

- 进入seeting（command + ,），搜索maven

### 2. 创建springboot工程

- 点击Project Structure（command + ;）
- 创建新模块 
- 创建springboot工程的时候可以将https://start.spring.io替换成https://start.aliyun.com

### 3. springboot初体验

- 在java目录下创建`controller`文件夹，再创建BookController类

- 写入功能代码

  ```java
  package com.itheima.controller;
  
  import org.springframework.web.bind.annotation.GetMapping;
  import org.springframework.web.bind.annotation.RequestMapping;
  import org.springframework.web.bind.annotation.RestController;
  
  // 这两行向外暴露一个books接口，可以通过books访问BookController类
  @RestController
  @RequestMapping("books")
  public class BookController {
    	// 表示一个get请求
      @GetMapping
      public String GetById() {
          System.out.println("springboot is running");
          return "SpringBoot is running";
      }
  }
  ```

- 启动服务，访问

### 4.  SpringBoot程序优点

1. 起步依赖（简化依赖配置）
2. 自动配置（简化常用工程相关配置）
3. 辅助功能（内置服务器，。。。。）
   - starter：
     - springboot中常见项目名称，定义了当前项目使用的所有依赖坐标，已达到减少依赖配置的目的
   - parent：
     - 所有springboot项目要继承的项目，定义了若干个坐标版本号（依赖管理，并非依赖），以达到减少依赖冲突的目的
     - springboot-boot-starter-parent个版本间存在着诸多坐标版本不同
   - 引导类：
     - `SpringApplication.run(Springboot01QuickstartApplication.class, args);`
     - 引导类是boot工程的执行入口，运行main方法就可以启动项目
     - sringboot工程运行后初始化Spring容器，扫描引导类所在包加载bean
   - 实际开发
     - 使用任意坐标是，仅书写GAV中的G和A，V由springboot提供，除非springboot未提供对应版本V
     - 如发生坐标错误，再制定Version（要小心版本冲突）
3. 引导类
   - SpringBoot提供了一个引导类来启动项目
   - 启动时，会初始化Spring容器，并扫描引导类所在包以及子包加载bean，也就是将类组件交给Spring管理
   

### 5. 基础配置

在`application.properties`中进行配置，一共三种类型文件做配置文件（yml、properties、yaml）

优先级properties > yml > yaml，三者相互补充，相互并根据优先级相互覆盖

```
# 修改服务器端口
server.port=80
# 关闭运行日志图标（banner）
spring.main.banner-mode=off
#设置日志相关
logging.level.root=info
```

#### 现在在企业中主流是`.yml`文件做配置文件

**springboot内置属性可以去官方文档中参考文档第一项：`Application Properties`**

```java
# 修改服务器端口
server:
  port: 81
# 修改banner
spring:
  main:
    banner-mode: off
# 日志
logging:
  level:
    root: info
```

#### yaml读取

1. 可以在通过`@value(一级属性名.二级属性名)`

   ```
   # 修改服务器端口
   server:
     port: 81
   # 爱好
   likes: [ sing,ship ]
   ```

   ```java
   @RestController
   @RequestMapping("books")
   public class BookController {
       @Value("${server.port}")
       private String port;
   
       @Value("${likes[0]}")
       private String sing;
   
       // get请求
       @GetMapping
       public String GetName() {
           System.out.println(port);
           System.out.println(sing);
           System.out.println("没有参数的get请求");
           return "get请求无参数";
       }
   }
   ```

2. 在配置文件中可以使用属性名引用方式引用属性

   ```
   baseDir: /user/local/fire
   
   center: 
   	dataDir: @{baseDir}/data
   	tmpDir: ${baseDir}/tmp
   ```

3. 属性值如果出现转义字符，需要使用双引号包裹

   ```
   lesson: "Spring\tboot\nlesson"
   ```

4. 一次性获取全部yaml属性

   ```java
   @RestController
   @RequestMapping("books")
   public class BookController {
   
   		@Autowired
   		private Environment env;
   		
       @GetMapping
       public String GetName() {
   				System.out.println(env.getProperty("server.port"));
   				System.out.println(env.getProperty("likes[0]"));
           System.out.println("没有参数的get请求");
           return "get请求无参数";
       }
   }
   ```

5. 最常用，将yml中的属性用类来装，并且交给Spring管理

   > 自定义对象封装属性。

   - 创建类
   
     ```java
     package com.itheima;
     
     import org.springframework.boot.context.properties.ConfigurationProperties;
     import org.springframework.stereotype.Component;
     
     @Component
     @ConfigurationProperties(prefix = "person")
     public class Person {
         private String name;
         private String age;
         private String sex;
     
         @Override
         public String toString() {
             return "Person{" +
                     "name='" + name + '\'' +
                     ", age='" + age + '\'' +
                     ", sex='" + sex + '\'' +
                     '}';
         }
     
         public String getName() {
             return name;
         }
     
         public void setName(String name) {
             this.name = name;
         }
     
         public String getAge() {
             return age;
         }
     
         public void setAge(String age) {
             this.age = age;
         }
     
         public String getSex() {
             return sex;
         }
     
         public void setSex(String sex) {
             this.sex = sex;
         }
     }
     ```

   - 使用
   
     ```java
     @RestController
     @RequestMapping("books")
     public class BookController {
     
       	// 自动装配
     		@Autowired
     		private Person person;
     		
         @GetMapping
         public String GetName() {
     				System.out.println(person);
             System.out.println(person.getName());
             System.out.println("没有参数的get请求");
             return "get请求无参数";
         }
     }
     ```

## pom.xml

所有springboot自带的都是`spring-boot-starter-web`这种格式的

第三方的技术都是`mtbatis-spring-boot-starter`

引入的依赖不用写版本，由parent标签提供

### 整合任意第三方库

1. 导入对应starter
2. 配置对应的设置或采用默认配置

### 整合mybatis

#### 注意事项

1. 书写sql语句的时候注意关键字问题，比如where `id` = #{id}
2. mysql 8.x驱动强制要求设置时区
   - 修改url，添加serverTimezone设定
   - 修改mysql数据库配置(略)
3. 驱动类过时，更换为`com.mysql.cj.jdbc.Driver`
   - `driverClassName: com.mysql.jdbc.Driver`    # mysql-connector-java 5.x及之前版本中的驱动类名
   - `driverClassName: com.mysql.cj.jdbc.Driver` # mysql-connector-java 6.x及后续版本中的驱动类名

### 整合mybatis plus

1. 手动添加SpringBoot整合MyBatis-Plus的坐标，可以通过mvnrepository获取

   ```xml
   <dependency>
       <groupId>com.baomidou</groupId>
       <artifactId>mybatis-plus-boot-starter</artifactId>
       <version>3.4.3</version>
   </dependency>
   ```

2. 定义数据层接口与映射配置，继承BaseMapper(传入实体类作为范性)

   ```java
   @Mapper
   public interface PersonDao extends BaseMapper<Person> {
     
     	//  
    
   }
   ```

3. 测试

### 整合druid

1. 手动添加SpringBoot整合druid的坐标，可以通过mvnrepository获取

   ```xml
   <dependency>
       <groupId>com.alibaba</groupId>
       <artifactId>druid-spring-boot-starter</artifactId>
       <version>1.2.6</version>
   </dependency>
   ```

2. 添加配置文件

   ```yml
   spring:
     datasource:
       druid:
         driver-class-name: com.mysql.cj.jdbc.Driver
         url: jdbc:mysql://localhost:3306/node
         username: root
         password: wangkun1
   ```

## 项目练习

### 1. 创建模块

### 2. 书写实体类

1. 创建`domain`文件夹

2. 根据数据库创建属性

3. lombok的使用

   - 引入lombok依赖

   - 在实体类中使用

     ```java
     package com.example.domain;
     
     import lombok.Data;
     
     // 设置对应的get/set/toString/hashCode/equals等方法
     @Data
     public class Book {
         private Integer id;
         private String name;
         private String type;
         private String description;
     }
     ```

### 3. 数据层开发

mapper层

1. 技术选型：mybatis plus + rouid

2. 添加配置

   ```
   server:
     port: 80
   spring:
     datasource:
       druid:
         driver-class-name: com.mysql.cj.jdbc.Driver
         url: jdbc:mysql://localhost:3306/node
         username: root
         password: wangkun1
   
   mybatis-plus:
     global-config:
       db-config:
         id-type: auto
   ```

3. 写mapper，一个mapper文件对应一个sql的xml文件

   ```java
   package com.example.mapper;
   
   import com.baomidou.mybatisplus.core.mapper.BaseMapper;
   import com.example.domain.Book;
   import org.apache.ibatis.annotations.Mapper;
   
   @Mapper
   public interface BookMapper extends BaseMapper<Book> {
   
   }
   ```

4. 使用

   这里是用测试类测试

   ```java
   package com.example.mapper;
   
   import com.example.domain.Book;
   import org.junit.jupiter.api.Test;
   import org.springframework.beans.factory.annotation.Autowired;
   import org.springframework.boot.test.context.SpringBootTest;
   
   @SpringBootTest
   public class BookMapperTestCase {
       @Autowired
       BookMapper bookMapper;
   
       // 根据id查询book
       @Test
       void testGetBookById() {
           Book book = bookMapper.selectById(1);
           System.out.println(book);
       }
   
       // 增加book
       @Test
       void testAddBook() {
           Book book = new Book();
           book.setType("JavaScript");
           book.setName("JavaScript高级程序设计");
           book.setDescription("人称前端红宝书，是每个前端必读的书籍之一");
           int isSuccession = bookMapper.insert(book);
           System.out.println(isSuccession);
       }
   
       // 更新book
       @Test
       void testUpdateBook() {
           Book book = new Book();
           book.setId(20);
           book.setType("JavaScript123");
           book.setName("JavaScript高级程序设计");
           book.setDescription("人称前端红宝书，是每个前端必读的书籍之一");
           bookMapper.updateById(book);
       }
   
       // 删除book
       @Test
       void testDeleteBook() {
           bookMapper.deleteById(19);
       }
   }
   ```

#### 开启mybatis-plus日志

添加配置即可

```
mybatis-plus:
  global-config:
    db-config:
      id-type: auto
  configuration:
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
```

#### 分页功能

1. 添加MybatisPlus分页拦截器

   ```java
   package com.example.config;
   
   import com.baomidou.mybatisplus.extension.plugins.MybatisPlusInterceptor;
   import com.baomidou.mybatisplus.extension.plugins.inner.PaginationInnerInterceptor;
   import org.springframework.context.annotation.Bean;
   import org.springframework.context.annotation.Configuration;
   
   @Configuration
   public class MPConfig {
       @Bean
       public MybatisPlusInterceptor mybatisPlusInterceptor() {
           // 定义MP拦截器
           MybatisPlusInterceptor interceptor = new MybatisPlusInterceptor();
           // 添加具体拦截器
           interceptor.addInnerInterceptor(new PaginationInnerInterceptor());
           return interceptor;
       }
   }
   
   ```

2. 使用IPage封装分页数据

   ```java
   // 分页
   @Test
   void testGetPage() {
       IPage<Book> page = new Page<>(1, 5);
       bookMapper.selectPage(page, null);
       System.out.println(page.getCurrent());  // 第几页
       System.out.println(page.getSize());  // 一页多少数据
       System.out.println(page.getTotal());  // 总共多少数据
       System.out.println(page.getPages());  // 一共多少页
       System.out.println(page.getRecords());  // 取回来的数据
   }
   ```

#### 按条件查询

```java
// 按条件查询
@Test
void testGetBy() {
    String name = "1";
    LambdaQueryWrapper<Book> lqw = new LambdaQueryWrapper<>();
    lqw.like(name != null, Book::getName, name);
    bookMapper.selectList(lqw); 
}
```

### 4. 业务层开发

Server层接口定义与数据层接口定义具有很大区别，不要混用

- `selectByUserNameAndPassword(String username, String password);`
- `Login(String username, String password);`

#### 1. 业务接口

创建`service`包并且创建业务接口

```java
package com.example.service;

import java.awt.print.Book;
import java.util.List;

public interface BookService {
    Boolean addBook(Book book);

    Boolean deleteBook(Book book);

    Boolean updateBook(Book book);

    Book getById(Integer id);

    List<Book> getAll();
}
```

#### 实现类

定义业务层对应的bean

```java
package com.example.service.impl;

import com.baomidou.mybatisplus.core.metadata.IPage;
import com.baomidou.mybatisplus.extension.plugins.pagination.Page;
import com.example.mapper.BookMapper;
import com.example.service.BookService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import com.example.domain.Book;

import java.util.List;

@Service
public class BookServiceImpl implements BookService {
    @Autowired
    private BookMapper bookMapper;

    @Override
    public Boolean addBook(Book book) {
        return bookMapper.insert(book) > 0;
    }

    @Override
    public Boolean deleteBook(int id) {
        return bookMapper.deleteById(id) > 0;
    }

    @Override
    public Boolean updateBook(Book book) {
        return bookMapper.updateById(book) > 0;
    }

    @Override
    public Book getById(Integer id) {
        return bookMapper.selectById(id);
    }

    @Override
    public List<Book> getAll() {
        return bookMapper.selectList(null);
    }

    @Override
    public IPage<Book> getPage(int currentPage, int pageSize) {
        IPage<Book> page = new Page<>(currentPage, pageSize);
        return bookMapper.selectPage(page, null);
    }
}
```

### 5. 表现层

controller层

```java
package com.example.controller;

import com.baomidou.mybatisplus.core.metadata.IPage;
import com.example.domain.Book;
import com.example.service.BookService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.*;

import java.util.List;

@RestController
@RequestMapping("/books")
public class BookController {
    @Autowired
    private BookService bookService;

    @GetMapping("{id}")
    public Book getById(@PathVariable Integer id) {
        return bookService.getById(id);
    }

    @GetMapping
    public List<Book> getAll() {
        return bookService.getAll();
    }

    @PostMapping
    public Boolean addBook(@RequestBody Book book) {
        return bookService.addBook(book);
    }

    @DeleteMapping("{id}")
    public Boolean deleteBook(@PathVariable Integer id) {
        return bookService.deleteBook(id);
    }

    @PutMapping
    public Boolean updateBook(@RequestBody Book book) {
        return bookService.updateBook(book);
    }

    @GetMapping("{currentPage}/{pageSize}")
    public IPage<Book> getPage(@PathVariable Integer currentPage, @PathVariable Integer pageSize) {
        return bookService.getPage(currentPage, pageSize);
    }
}
```

#### 数据一致性处理

##### flag、data

flag：因为 查询id不存在数据 和 查询过程中抛出异常，在catch中都会返回null，所有设置flag表示请求是否成功

data：如果请求需要返回数据，就返回请求的数据，不然就返回null

##### 在controller创建`utils`包，并创建返回类型的类

```java
package com.example.controller.utils;

public class Result {
    public Boolean flag;
    public Object data;

    public Result() {
    }

    public Result(Boolean flag, Object data) {
        this.flag = flag;
        this.data = data;
    }

    public Result(Boolean flag) {
        this.flag = flag;
    }
}

```

##### 在表现层使用

```java
package com.example.controller;

import com.baomidou.mybatisplus.core.metadata.IPage;
import com.example.controller.utils.Result;
import com.example.domain.Book;
import com.example.service.BookService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.*;

import java.util.List;

@RestController
@RequestMapping("/books")
public class BookController {
    @Autowired
    private BookService bookService;

    @GetMapping("{id}")
    public Result getById(@PathVariable Integer id) {
        return new Result(true, bookService.getById(id));
    }

    @GetMapping
    public Result getAll() {
        return new Result(true, bookService.getAll());
    }

    @PostMapping
    public Result addBook(@RequestBody Book book) {
        return new Result(bookService.addBook(book));
    }

    @DeleteMapping("{id}")
    public Result deleteBook(@PathVariable Integer id) {
        return new Result(bookService.deleteBook(id));
    }

    @PutMapping
    public Result updateBook(@RequestBody Book book) {
        return new Result(bookService.updateBook(book));
    }

    @GetMapping("{currentPage}/{pageSize}")
    public Result getPage(@PathVariable Integer currentPage, @PathVariable Integer pageSize) {
        return new Result(true, bookService.getPage(currentPage, pageSize));
    }
}

```

#### 异常统一处理

在工具包utils中创建类

```java
package com.example.controller.utils;

import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.RestControllerAdvice;

@RestControllerAdvice
public class ProjectExceptionAdvice {
    // 拦截所有的异常信息
    @ExceptionHandler(Exception.class)
    public Result doException(Exception ex) {
        // 记录日志
        ex.printStackTrace();
        return new Result("服务器故障，请稍后再试");
    }
}
```

在表现层创造异常

```java
@GetMapping("{id}")
public Result getById(@PathVariable Integer id) throws IOException {
    if (true) throw new IOException();
    return new Result(true, bookService.getById(id));
}
```

## 添加分页

### 添加依赖

```xml
<dependency>
    <groupId>com.github.pagehelper</groupId>
    <artifactId>pagehelper</artifactId>
    <version>5.3.2</version>
</dependency>
<dependency>
    <groupId>com.github.pagehelper</groupId>
    <artifactId>pagehelper-spring-boot-autoconfigure</artifactId>
    <version>1.4.6</version>
</dependency>
<dependency>
    <groupId>com.github.pagehelper</groupId>
    <artifactId>pagehelper-spring-boot-starter</artifactId>
    <version>1.4.6</version>
</dependency>
```















