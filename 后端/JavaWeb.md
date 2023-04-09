

jdbc是后端与数据库的交互

Tomcat + Servlet是客户端与后端（服务端）的交互

Tomcat开启web服务器，Servlet根据请求路径进行动态资源分发

Tomcat实现给客户端呈现静态页面

Tomcat + Servlet实现根据客户端请求的资源地址和请求方法响应不同的页面

JDBC + Tomcat + Servlet实现根据客户端请求的资源地址和请求方法响应不同的页面，并且可以去数据库去请求数据

## Tomcat

![image-20230305163116203](/Users/wangkun/Library/Application Support/typora-user-images/image-20230305163116203.png)

### 简介

Tomcat是Apache开源组织共享的Jakarta项目中的一个核心项目，Tomcat开源免费、支持Java中的动态网页技术Servlet和JAP规范，因此Java开发者多使用Tomcat

Tomcat在企业主流使用8.5和9，我们的学习主要基于8.5.72版本；各个版本之间的最大区别就是对于JavaEE版本及Servlet规范的支持、依赖的JDK版本。Tomcat8.x全面支持Servlet3.x规范及JavaEE4规范

#### Tomcat的目录结构

- **`bin：`**存放可执行二进制文件（startup.bat用于启动Tomcat、shutdown.bat用于停止Tomcat）
- **`conf:`**存放Tomcat的配置文件（server.xml可以配置Tomcat的端口，web.xml）
- **`lib：`**存放Tomcat服务器运行web项目所需的基础类库
- **`logs：`**存放了Tomcat服务器运行日志、记录了服务器启动、运行异常及关闭等操作的记录
- **`temp：`**临时目录，存放Tomcat运行过程中产生的临时文件
- **`webapps：`**存放Tomcat管理的web项目的目录，此目录中默认部署了Tomcat管理器等几个web项目
- **`work:`**Tomcat可以运行动态网页，动态网页就是在服务器上将数据加载到网页生成的页面，次目录就是存放Tomcat生成的文件

### 启动

1. 进入`tomcat`的`bin`目录：`cd /Library/Tomcat/bin ` 或者打开Tomcat文件夹，把bin文件夹直接拖拉到终端，当然前提是先输入cd+空格

2. 启动服务：`sudo sh ./startup.sh`

   ```java
   Using CATALINA_BASE:   /Library/Tomcat
   Using CATALINA_HOME:   /Library/Tomcat
   Using CATALINA_TMPDIR: /Library/Tomcat/temp
   Using JRE_HOME:        /Library/Java/JavaVirtualMachines/jdk-18.0.2.1.jdk/Contents/Home
   Using CLASSPATH:       /Library/Tomcat/bin/bootstrap.jar:/Library/Tomcat/bin/tomcat-juli.jar
   Using CATALINA_OPTS:
   Tomcat started.
   ```

   出现这个就代表启动成功

3. 关闭服务：`sh ./shutdown.sh`

   ```java
   Using CATALINA_BASE:   /Library/Tomcat
   Using CATALINA_HOME:   /Library/Tomcat
   Using CATALINA_TMPDIR: /Library/Tomcat/temp
   Using JRE_HOME:        /Library/Java/JavaVirtualMachines/jdk-18.0.2.1.jdk/Contents/Home
   Using CLASSPATH:       /Library/Tomcat/bin/bootstrap.jar:/Library/Tomcat/bin/tomcat-juli.jar
   Using CATALINA_OPTS:   
   NOTE: Picked up JDK_JAVA_OPTIONS:  --add-opens=java.base/java.lang=ALL-UNNAMED --add-opens=java.base/java.io=ALL-UNNAMED --add-opens=java.base/java.util=ALL-UNNAMED --add-opens=java.base/java.util.concurrent=ALL-UNNAMED --add-opens=java.rmi/sun.rmi.transport=ALL-UNNAMED
   ```

   出现这个就代表关闭成功

### web项目部署

> web项目部署：将web项目交给Tomcat管理，当用户访问Tomcat时，Tomcat可以将web项目中的资源响应给用户浏览器

方法：直接将web项目拷贝到Tomcat的webapps目录

打开网站：http://host-ip:tomcat-port/web-Path/    (test地址：http://localhost:8080/test/index.html)

- `http://`：web服务的htpp传输协议
- `host-ip`：服务器所在计算机的IP
- `tomcat-port`：Tomcat服务器占用的网络端口（默认8080，可以在conf.server.xml中修改端口）
- `web-Path`：Tomcat中部署的web项目的访问路径

示例图：

![image-20230305220758464](/Users/wangkun/Library/Application Support/typora-user-images/image-20230305220758464.png)



## Servlet

> Servlet是服务器端的Java程序、能够接收HTTP请求、处理HTTP请求、并对HTTP请求进行相应的动态网页技术。

Servlet是JavaEE（JavaWeb）规范的一个重要组成部分

Servlet的作用：

- 接收客户端的HTTP请求（浏览器）
- 根据用户请求进行数据处理
- 动态生成网页（网页中的数据是根据客户端请求动态改变的）
- 将生成的包含动态数据的网页响应给客户端

![image-20230306073320802](/Users/wangkun/Library/Application Support/typora-user-images/image-20230306073320802.png)

### 创建Java Web工程

> Servlet是JavaEE规范的一部分，Servlet的开发需要依赖JavaEE环境，之前创建的单纯的Java应用已经不能满足Serclet开发所需的环境需求，我们要创建Java Web工程
>
> - Java工程：只引入JDK的标准库（JavaSE）
> - Java Web工程：引入了Java企业级开发环境（JavaEE）

#### 创建Servlet类

> Servlet是一个java程序，是一个能够接收HTTP请求的Java类，因此需要实现HTTP请求
>
> 在JavaEE库中有一个类`javax.servlet.http.HttpServlet`实现了HTTP协议，我们创建的类只要继承这个HttpServlet类，就实现了HTTP协议，就能接受HTTP请求

1. 创建一个类继承`javax.servlet.http.HttpServlet`
2. 继承HttpServlet的类就能够接收HTTP请求，我们把这样的类称之为Servlet类，类似`****Servlet`格式命名
3. 在我们创建的Servlet类中，重写`doPost/doGet`用于处理用户不同的请求

#### 配置Servlet类的URL

> Servlet创建完成之后，需要配置url访问路径，然后将web项目运行在Tomcat之上，就能够通过配重的url访问Servlet类。Servlet自3.0规范开始支持两种配置方式
>
> - 基于web.xml配置文件进行配置
> - 基于注解配置

##### 基于web.xml配置Servlet

- 打开Java Web工程中`web/WEB_INF/web.xml`文件

- 配置如下

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
           version="4.0">
  
      <!--  servlet标签：配置类标签  -->
      <servlet>
          <servlet-name>BookListServlet</servlet-name>
          <servlet-class>com.example.learnservlet.BookListServlet</servlet-class>
      </servlet>
   
      <!--servlet-mapping标签：配置url-->
      <servlet-mapping>
          <!--  通过name与上面servlet标签进行匹配，展示响应的类-->
          <servlet-name>BookListServlet</servlet-name>
          <!--  用户通过此url来访问对应的类  -->
          <url-pattern>/book-list</url-pattern>
      </servlet-mapping>
  </web-app>
  ```

##### 基于注解配置Servlet

- 在创建的Servlet类上添加`@WebServlet`注解，在注解后的参数中配置url，url也必须是以`/`开头

- 配置如下

  ```java
  // 注解
  @WebServlet("/book-query")
  public class BookQueryServlet extends HttpServlet {
  
      @Override
      protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
          // super.doGet(req, resp);
          System.out.println("BookQueryServlet===========doGet");
      }
  
      @Override
      protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
          // super.doPost(req, resp);
          System.out.println("BookQueryServlet===========doPost");
      }
    
  }
  ```

#### IDEA部署web项目

1. 配置项目

   ![image-20230306162758304](/Users/wangkun/Library/Application Support/typora-user-images/image-20230306162758304.png)

2. 部署项目

   ![image-20230306161802563](/Users/wangkun/Library/Application Support/typora-user-images/image-20230306161802563.png)

   

3. 配置web项目的访问路径

   ![image-20230306162427687](/Users/wangkun/Library/Application Support/typora-user-images/image-20230306162427687.png)

4. 添加展示的页面

   ![image-20230310070211657](/Users/wangkun/Library/Application Support/typora-user-images/image-20230310070211657.png)
   
4. 启动Tomcat

   > 点击IDEA启动按钮即可
   >
   > - 播放按钮：直接运行
   > - 甲壳虫：debug运行

5. Tomcat启动完成
   - 构建web项目
   - 将web项目拷贝到Tomcat
   - 启动Tomcat
   - 打开浏览器，访问当前项目首页（如：http://localhost:8080/demo1/book-list）

示例图：

![image-20230306163359888](/Users/wangkun/Library/Application Support/typora-user-images/image-20230306163359888.png)

#### 响应给客户端数据

```java
@WebServlet("/book-query")
public class BookQueryServlet extends HttpServlet {

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        // super.doGet(req, resp);
        System.out.println("BookQueryServlet===========doGet");

        // 1. 接收到浏览器请求时传递的图书ID（bookId）
        String bookId = req.getParameter("bookId");

        // 2. 根据bid查询数据库图书编（伪代码）
        Map<String, Book> bookMap = new HashMap<>();
        bookMap.put("1001", new Book("1001", "Java", "张三", 111.2, "/src"));
        bookMap.put("1002", new Book("1002", "JavaScript", "李四", 110.2, "/src"));
        bookMap.put("1003", new Book("1003", "Go", "王五", 210.2, "/src"));

        Book book = bookMap.get(bookId);
        System.out.println(book.getBookAuthor());
        // 3. 将网页响应给浏览器
        PrintWriter writer = resp.getWriter();
        writer.println(book.getBookId() + "  bookId");
        writer.println(book.getBookName() + "   bookName");
        writer.println(book.getBookAuthor() + "  bookAuthor");
        writer.println(book.getBookPrice() + "   bookPrice");
        writer.println(book.getBookImgPath() + "   bookImgPath");
        writer.flush();
        writer.close();
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        // super.doPost(req, resp);
        System.out.println("BookQueryServlet===========doPost");
    }
}
```

### Servlet原理解析

#### Servlet响应流程

>  Servlet是Tomcat上的一个资源

![image-20230307071219700](/Users/wangkun/Library/Application Support/typora-user-images/image-20230307071219700.png)

#### Servlet实例的生命周期

> 当客户端的请求到达Tomcat，Tomcat会创建一个线程来接收、处理、响应客户端请求，客户端在请求某个Servlet类时，线程需要通过这个Servlet类的实例来调用Servlet方法，调用doGet/doPost...方法来处理响应请求，最后调用destroy销毁实例
>
> servlet实例的生命周期的是一个Servlet类的实例从创建到销毁的过程：加载 ==> 初始化 ==> 服务 ==> 销毁

**加载：**
用户请求Servlet的时候，Servlet容器会检查是否已经加载并实例化该Servlet类，如果没有就会加载并初始化Servlet类。

**初始化：**
Servlet实例创建成功，Servlet容器就会调用Serlvet的init方法，在这个方法中，Servlet可以做一些初始化的事情，如：读取配置文件，连接数据库，创建数据库连接池。

**服务：**

请求到达Servlet，容器就会调用Servlet的service方法，在这个方法中，Servlet可以根据请求方法去调用不同的方法，如：doGet/doPost。，然后生成相应的响应。

**销毁：**

当关闭服务器时，容器就是调用Servlet上的destroy方法，用来销毁Servlet实例，在这个方法中，可以用来做一些收尾的工作，比如：释放资源、关闭数据库连接、关闭线程等。



```java
public class BookListServlet extends HttpServlet {
  
  	// 初始化
    @Override
    public void init(ServletConfig config) throws ServletException {
        System.out.println("初始化Servlet");
        super.init(config);
    }
  		
  	// 2. 服务 GenericServlet类上的
    @Override
    public void service(ServletRequest req, ServletResponse res) throws ServletException, IOException {
        super.service(req, res);
    }

  	// 3. 服务 HttpServlet类上的
    @Override
    protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        super.service(req, resp);
    }

  	// 4. 处理请求
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        // 在这里可以进行jdbc的数据库操作
        System.out.println("BookListServlet==========doGet");
    }
  
  	// 	5. 销毁
    @Override
    public void destroy() {
        System.out.println("实例被销毁了");
    }
}
```



1. Servlet类是单实例多线程的，一个Servlet类自始至终只会创建一个对象

2. 如果当前Servlet类**没有配置**`<load-on-startup>1</load-on-startup>`：

   当客户端第一次请求Servlet时，创建当前Servlet类的实例，然后使用这个实例调用service(ServletRequest、ServletResponse)方法--service(HttpServletRequest、HttpServletResponse)方法--doGet/doPost处理客户端请求；当客户端请求再次到达时将不会重新创建Servlet实力，直接视同第一次创建的实例调用方法进行响应

3. 如果当前Servlet类**配置了`<load-on-startup>1</load-on-startup>`**：

   当服务器启动时就会创建Servlet类的实例，无论客户端是第一次请求这个Servlet类，还是再次请求都不会创建Servlet类实例，直接使用服务器启动时创建的Servlet实例来接收、处理、响应客户端请求

**配置load-on-startup的两种方式：**

- xml配置

  ```xml
  <!--  servlet标签：配置类标签  -->
  <servlet>
      <servlet-name>BookListServlet</servlet-name>
      <servlet-class>com.example.learnservlet.BookListServlet</servlet-class>
      <!-- 如果有多个Serlvet都配置了load-on-startup，里面的数字就是在服务器中创建实例的顺序-->
      <load-on-startup>1</load-on-startup>
  </servlet>
  
  <!--servlet-mapping标签：配置url-->
  <servlet-mapping>
      <!--  通过name与上面servlet标签进行匹配，展示响应的类-->
      <servlet-name>BookListServlet</servlet-name>
      <!--  用户通过此url来访问对应的类  -->
      <url-pattern>/book-list</url-pattern>
  </servlet-mapping>
  ```

- 注解配置

  ```java
  @WebServlet(value = "/book-query",loadOnStartup = 1)
  ```

#### 线程安全问题

> 因为Servlet实例是单例模式，当多个客户端并发访问同一个Servlet类时，Tomcat会创建多个线程，多个线程会使用同一个Servlet实例，有可能会导致线程安全问题

##### 方案一：实现SingleThreadMode接口

> 我们可以让Servlet类实现SingleThreadMode接口，每个线程都会创建servlet实例，避免了多线程使用通过Servlet实例的请求，但是使用这种方式会导致对客户端的请求响应效率变低，增加了服务器因频繁创建和销毁Serlvet实例的开销，因为此种方式不建议使用，已经过时

##### 方案二：使用syncchron同步锁

```java
@Override
protected void doGet(HttpServletRequest req, HttpServletResponse resp) {
    synchronized (this) {
        // 业务代码
    }
}
```

##### 建议：在Servlet实例中尽量不使用成员变量

> 如果将变量定义为成员变量，则这个变量在多个线程中是共享的，就有可能因为多个线程同时修改这个变量导致并发问题，因此我们可以将变量定义在处理业务的doXxx方法中，定义为局部变量之后，每个线程都有属于自己的局部变量

### Servlet开发技术

#### Request对象

> 也就是`(HttpServletRequest)`对象
>
> 我们在Servlet类中的doGet/doPost/doXxx方法中通过request对象接收客户端请求信息
>
> 客户端向服务器发送的请求信息都会被封装到request对象，request(HttpServeltRequest类)提供了多个方法可以用于获取http请求中的数据

##### get请求

```java
@Override
protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    System.out.println("get请求");

    /* 获取请求行数据 */
    // 获取请求方法
    String method = req.getMethod();
    // 获取请求url
    String requestURI = req.getRequestURI();
    // 获取请求行中url参数，根据参数的key获取参数的value
    String k1 = req.getParameter("k1");
    // 获取客户端提交数据的协议及版本
    String protocol = req.getProtocol();

    /* 获取请求头数据 */
    // 获取请求头中所有key
    Enumeration<String> headerNames = req.getHeaderNames();
    while (headerNames.hasMoreElements()) {
        String key = headerNames.nextElement();
        String header = req.getHeader(key);

        // 打印结果
        System.out.println("key" + key);
        System.out.println("header" + header);
    }

    // 打印结果
    System.out.println("getMethod======" + method);  // getMethod======GET
    System.out.println("getRequestURI======" + requestURI);  // getRequestURI======/demo1/test
    System.out.println("getParameter======" + k1);  // getParameter======v1
    System.out.println("getProtocol======" + protocol);  // getProtocol======HTTP/1.1s
}
```

##### post请求

> post请求也可以用req.getParameter("key")接收参数

```java
@Override
protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {

    /* 获取请求正文 */
    ServletInputStream inputStream = req.getInputStream();
    BufferedReader reader = new BufferedReader(new InputStreamReader(inputStream));

    // 读取请求正文
    String s = reader.readLine();
    System.out.println(s);
  
    /* 也可以用getParameter接收参数*/
    String firstname = req.getParameter("firstname");
    String lastname = req.getParameter("lastname");

    System.out.println(firstname);
    System.out.println(lastname);
}
```

##### request对象处理中文乱码问题

> 客户端向服务器的Serlvet类提交数据中包含中文，可能会出现中文乱码

**1. 为什么会出现乱码**

![image-20230307150532869](/Users/wangkun/Library/Application Support/typora-user-images/image-20230307150532869.png)

客户端提交的数据通过网络发送给服务器，传输的过程数据通常会进行编码，服务器会对数据进行解码；如果服务器使用的解码方法与网页的编码方式不同，将会导致服务器的解码出现乱码

**2. get方式提交数据的乱码问题**

```xml
<Connector port="8080" protocol="HTTP/1.1"
           connectionTimeout="20000"
           redirectPort="8443"
     URIEncoding="utf-8" />
```

**3. post方式提交数据的乱码问题**

```java
@Override
protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {

    /* 处理中文乱码问题*/
    req.setCharacterEncoding("utf-8");

    /* 也可以用getParameter接收参数 */
    String firstname = req.getParameter("firstname");
    String lastname = req.getParameter("lastname");

    System.out.println(firstname);
    System.out.println(lastname);
}
```

#### Response对象

> Servlet类中doGet/doPost等方法都有一个HttpServletResponse对象，用于响应客户端请求

**常用方法：**

```java
@Override
protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    /* 设置状态行*/
    resp.setStatus(200);

    /* 设置响应头 */
    // 设置响应客户端的数据格式
    resp.setContentType("text/html");
    // 设置响应客户端的数据长度(一般无需设置)
    // resp.setContentLength(1024);
    // 设置其他响应头属性
    resp.setHeader("connection", "keep-alive");

    /* 设置响应正文 */
    // 设置响应客户端的数据编码格式
    resp.setCharacterEncoding("utf-8");
    // 通过response对象获取输出流
    // 字节流（如果要响应文件数据给客户端，则需要使用字节流）
    // ServletOutputStream outputStream = resp.getOutputStream();
    // 字符流（如果响应文本数据-HTML文档，则使用字符流）
    PrintWriter writer = resp.getWriter();
    // 通过流学出的数据，就会以响应正文的形式传输给客户端浏览器，如果浏览器可以识别数据，则直接显示
    writer.println("我是响应内容");
    writer.flush();
    writer.close();
}
```

#### Cookie的使用

- Cookie是在浏览器在访问web服务器上的某个资源时，由web服务器在响应浏览器时通过响应头附带给浏览器并且存储在浏览端的一小段数据
- 一旦浏览器保存来自某个服务器的Cookie，浏览器再次访问该服务器，会通过请求头将Cookie传递给web服务器
- 浏览器访问服务器的时候，只会携带当前服务器存储在浏览器的Cookie
- Cookie中缓存的数据是以键值对存储的（key-value）

##### 将Cookie写给浏览器

> 服务器可以向浏览器存储多个Cookie

```java
@Override
protected void doGet(HttpServletRequest req, HttpServletResponse resp) {

    /* 创建Cookie */
    Cookie cookie = new Cookie("key1", "value1");

    /* 设置生命周期 */
    // 1. 设置>0，表示Cookie有效时间，单位s
    // 2. 设置=0，表示浏览器关闭销毁Cookie
    // 3. 设置-1，表示内存存储
    cookie.setMaxAge(24 * 60 * 60);

    /* 将Cookie响应给客户端 */
    resp.addCookie(cookie);

    /* 设置响应信息 */
    resp.setContentType("text/html");
    resp.setHeader("connection", "keep-alive");

    resp.setCharacterEncoding("utf-8");
    PrintWriter writer = resp.getWriter();
    // 通过流学出的数据，就会以响应正文的形式传输给客户端浏览器，如果浏览器可以识别数据，则直接显示
    writer.println("我是响应内容");
    writer.flush();
    writer.close();
}
```

##### 从浏览器请求中读取Cookie

```java
@Override
protected void doGet(HttpServletRequest req, HttpServletResponse resp) {

    Cookie[] cookies = req.getCookies();
    for (Cookie cookie : cookies) {
        if (Objects.equals(cookie.getName(), "key1")) {
            System.out.println("存储的Cookie为" + cookie.getValue());
        }

    }
}
```

##### Cookie的优点与限制

**优点：**

- 可以配置过期时间
- 简洁，cookie中存储的是最简单的键值对格式的文本数据
- 数据的持久性，保存到浏览器中的cookie在过期之前是持久存储的

**缺点：**

- 存储数据的大小，大多数浏览器支持4K、8K的数据存储
- 用户可以通过浏览器设置禁用Cookie，限制Cookie的使用场景
- Cookie存储在客户端浏览器中，有被篡改的风险，有潜在的安全隐患

#### Session对象

##### Session概述

> Session对象，就是当浏览器向服务器发送请求建立连接后，由服务器穿件的存储在服务器用于记录用户状态的对象

- 服务器会为每个客户端连接分配一个Session对象，存储在服务器上
- 同一个浏览器发送多次请求，同属于一次回话，共享同一个Session对象

##### Session原理

>  HTTP是无状态，同一个浏览器可以借助Cookie实现多次请求共享一个Session

![image-20230308070807852](/Users/wangkun/Library/Application Support/typora-user-images/image-20230308070807852.png)

**Session原理**

1. 当客户端浏览器第一次请求服务器的时候，服务器会为客户端创建一个Session对象，同时将sessionID通过Cookie响应给客户端，并存储在客户端
2. 当客户端再次请求服务器的时候，请求头上会携带着存储着sessionID的cookie，服务器接收请求之后获取sessionID，通过这个sessionID获取第一次连接创建的Session对象

##### session的数据操作

###### 生成session

```java
@Override
protected void doGet(HttpServletRequest req, HttpServletResponse resp) {
    doPost(req, resp);
}

@Override
protected void doPost(HttpServletRequest req, HttpServletResponse resp) {
    /* 1. 获取当前用户连接 */
    HttpSession session = req.getSession();
    /* 2. 为当前连接生成sessionId */
    String sessionId = session.getId();
		session.setAttribute("key1", sessionId);
    System.out.println("sessionId" + sessionId);
}
```

###### 获取session

```java
@Override
protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    /* 获取连接 */
    HttpSession session = req.getSession();

    /* 取数据 */
    String s1 = (String) session.getAttribute("key1");
    System.out.println("SecondSessionServlet===  " + s1);
}
```

###### 修改session

```java
@Override
protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    /* 获取连接 */
    HttpSession session = req.getSession();

    /* 修改数据 */
    session.setAttribute("key1", "修改后的session");
}
```

###### 删除session

```java
@Override
protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    /* 获取连接 */
    HttpSession session = req.getSession();

    /* 删除数据 */
    session.removeAttribute("key1");
}
```

##### session对象失效

> 一个客户端的多次请求正常情况下获取到的是同一个session对象，也正是因为多次请求获取的同一个session对象才实现了session中存储的用户状态可以作用于请求
>
> 如果session失效，将会导致session失效后的请求无法获取之前的session对象，也就是说多次请求获取的不是同一个session

**导致session失效的原因有两种：**

- 客户端禁用Cookie，或者客户端在两次请求之间清楚了cookie，将导致下一次请求无法获取上一次请求的创建的session对象
- session是有生命周期的，当客户端请求服务器会为此创建session对象，如果客户端两次请求的间隔时间 > session过期时间（默认30min），服务器会将之前创建的session对象销毁

#### ServletContext对象

> 也叫application对象。是JavaWeb项目的全局对象，包含当前web项目在web服务器中的信息，同事他也是一个域对象，可以实现访问当前web项目的所有用户之间的数据共享

- Request对象作用于一个用户请求，获取用户数据

- session对象作用于一个用户的多个请求，实现数据共享

- ServletContext对象作用于整个web项目，实现多个用户之间数据共享

![image-20230309105244651](/Users/wangkun/Library/Application Support/typora-user-images/image-20230309105244651.png)

##### 获取wen应用信息

```java
@Override
protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {

    ServletContext servletContext = getServletContext();

    /* 1. 获取当前web应用在web服务器上的访问路径 */
    String contextPath = servletContext.getContextPath();
    /* 2. 获取web项目在服务器上的绝对路径 */
    String realPath = servletContext.getRealPath("/files");

    System.out.println(realPath);
    System.out.println("contextPath" + contextPath);
}
```

##### 实现全局数据贡献

**存储数据**

```java
@Override
protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {

    String param = req.getParameter("param");

    ServletContext servletContext = getServletContext();
  
    /* 通过application全局对象存储数据 */
    servletContext.setAttribute("aKey", param);
}
```

**取数据**

> 取数据的时候你会发现无论用什么浏览器去访问这个地址，都可以取到相同的数据！

```java
@Override
protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {

    ServletContext servletContext = getServletContext();

    /* 获取通过全局对象存储的值 */
    String aKey = (String) servletContext.getAttribute("aKey");
    System.out.println(aKey);
}
```

**修改数据**

```java
@Override
protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {

    ServletContext servletContext = getServletContext();

    /* 修改全局对象的数据 */
    servletContext.setAttribute("aKey","我是修改过后的数据");
}
```

**删数据**

```java
@Override
protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {

    ServletContext servletContext = getServletContext();

    /* 删除全局对象指定的数据 */
    servletContext.removeAttribute("aKey");
}
```

##### 总结：

- ServletContext是当前web项目的全局上下文对象，可以被多个用户请求共享

- ServletContext是服务器启动的时候创建的，当服务器关闭或者将项目从服务器移出时销毁ServletContext对象

- ServletContext可以实现在线人数统计等需要全局存储的场景

  
