# Maven

## IDEA项目构建

project constructure

项目文件中`.idea、.iml`后缀的文件就是项目构建文件，会告诉IDEA怎么运行打开我们的项目文件

我们从gGitHub中拉下来的代码都是源代码，所有IDEA的项目配置文件，所以项目在IDEA中运行不了，需要我们自己配置。

## maven下载与配置

### 检查java与maven是否下载

`java --version `与`mvn --version`检查是否下载配置过maven

### 下载maven

https://maven.apache.org/download.cgi，进入官网进行下载

![maven下载](/Users/wangkun/Desktop/日常文件/maven下载.png)

可以点击1下载最新版本，也可以点击2，下载以前的版本。

### 配置maven

#### 添加环境变量

1. 要确保之前配置过JAVA_HOME，没有配置过，操作如下

   没有配置会出现`java.language.....`的报错

   1. 在终端输入`/usr/libexec/java_home`获取java路径

   - `vim ~/.bash_profile `，点击`i`添加以下配置。JAVA_HOME后面添加1中获取的路径

     ```
     #!bin/bash
     export JAVA_HOME="/Library/Java/JavaVirtualMachines/jdk-11.0.10.jdk/Contents/Home"
     export PATH=$PATH:$JAVA_HOME/bin:$JAVA_HOME/lib/tools.jar:$JAVA_HOME/lib/dt.jar
     ```

   - Esc  `:wq`保存退出编辑，`source .bash_profile`是文件立即生效

   - `java --version`查看版本成功

2. 添加maven环境变量

   - `vim ~/.bash_profile `，点击`i`添加以下配置。（文件路径名可以直接拖拽文件夹到终端）

     ```
     export PATH=/Users/wangkun/Downloads/apache-maven-3.6.3/bin:$PATH
     export PATH=$PATH:$MAVEN_HOME/bin
     ```

   - Esc  `:wq`保存退出编辑，`source .bash_profile`是文件立即生效
   - mvn 查看版本

### 修改源文件

1. 阿里云镜像
2. jar包存放位置
3. java编译版本

上网查

### IDEA配置Maven

两个地方

> 把画红线的东西全部配置成自己的。最后一个是仓库，在你的其他盘找一个地方新建repository文件夹，自己要知道，选中，如果勾选不了就选择都选override。

![IDEA设置Maven1](/Users/wangkun/Desktop/日常文件/IDEA设置Maven1.png)

![IDEA设置Maven2](/Users/wangkun/Desktop/日常文件/IDEA设置Maven2.png)

> 两处都要配置，一个是当前项目的maven配置，一个是新建项目的maven配置。

### maven常见命令(生命周期)

| 命令                   | 说明                                                   |
| ---------------------- | ------------------------------------------------------ |
| mvn –version           | 显示版本信息                                           |
| mvn clean              | 清理项目生产的临时文件,一般是模块下的target目录        |
| mvn compile            | 编译源代码，一般编译模块下的src/main/java目录          |
| mvn package            | 项目打包工具,会在模块下的target目录生成jar或war等文件  |
| mvn test               | 测试命令,或执行src/test/java/下junit的测试用例         |
| mvn install            | 将打包的jar/war文件复制到你的本地仓库中,供其他模块使用 |
| mvn deploy             | 将打包的文件发布到远程参考,提供其他人员进行下载依赖    |
| mvn site               | 生成项目相关信息的网站                                 |
| mvn dependency:tree    | 打印出项目的整个依赖树                                 |
| mvn archetype:generate | 创建Maven的普通java项目                                |
| mvn tomcat:run         | 在tomcat容器中运行web应用                              |

target文件夹下的classes装的是编译后的文件，运行时会按需加载到内存

### 依赖范围

test：仅测试的时候有效，编译代码和项目运行时，无法使用依赖

```
<dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>4.7</version>
    <scope>test</scope>
</dependency>
```

provided：只对编译和测试的classpath有效，对运行的classpath无效

```
<dependency>
    <groupId>javax.servlet</groupId>
    <artifactId>javax.servlet-api</artifactId>
    <version>4.0.1</version>
    <scope>provided</scope>
</dependency>
```

### 聚合和继承

> 分布式开发必须要用

> **聚合模块（父模块）的打包方式必须为pom，否则无法完成构建**。

在聚合多个项目时，如果这些被聚合的项 目中需要引入相同的Jar，那么可以将这些Jar写入父pom中，各个子项目继承该pom即可。，父模块的打包方式必须为pom，否则无法构建项目。

> 通过在各个子模块中配置来表明其继承与哪一个父模块：

```xml
<parent>
    <artifactId>parent</artifactId>
    <groupId>org.example</groupId>
    <version>1.0-SNAPSHOT</version>
</parent>
<modelVersion>4.0.0</modelVersion>

<artifactId>childern-two</artifactId>
```

同一父模块下的所有子模块也可以相互依赖

#### 常见的聚合工程pom文件

##### 父模块通过`dependencyManagement、properties`管理依赖的版本

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>cn.itnanls</groupId>
    <artifactId>maven-study01</artifactId>
    <packaging>pom</packaging>
    <version>1.0-SNAPSHOT</version>
<!--拥有的子模块-->  
    <modules>
        <module>son2</module>
        <module>son3</module>
    </modules>

    <properties>
        <maven.compiler.source>8</maven.compiler.source>
        <maven.compiler.target>8</maven.compiler.target>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <!--依赖的版本管理-->
        <commons-lang-version>2.1</commons-lang-version>
        <mysql-version>8.0.21</mysql-version>
    </properties>

    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>commons-lang</groupId>
                <artifactId>commons-lang</artifactId>
                <version>${mysql-version}</version>
            </dependency>
        </dependencies>
    </dependencyManagement>

</project>
```

##### 子模块按需引入自己需要的依赖，因为父模块规定的版本所以不同功能的同一个依赖是同一版本的，不会出现版本冲突

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>maven-study01</artifactId>
        <groupId>cn.itnanls</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>son3</artifactId>

    <properties>
        <maven.compiler.source>8</maven.compiler.source>
        <maven.compiler.target>8</maven.compiler.target>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    </properties>

    <dependencies>
        <dependency>
            <groupId>commons-lang</groupId>
            <artifactId>commons-lang</artifactId>
        </dependency>
    </dependencies>

</project>
```

### pom经典基础配置

```
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0http://maven.apache.org/xsd/maven-4.0.0.xsd">

    <!-- 模型版本。必须是这样写，现在是maven唯一支持的版本 -->
    <modelVersion>4.0.0</modelVersion>
    <!-- 公司或者组织的唯一标志，并且配置时生成的路径也是由此生成， 如com.xinzhi，maven会将该项目打成的jar包放本地路径：/com/xinzhi/ -->
    <groupId>com.xinzhi</groupId>
    <!-- 本项目的唯一ID，一个groupId下面可能多个项目，就是靠artifactId来区分的 -->
    <artifactId>test</artifactId>
    <!-- 本项目目前所处的版本号 -->
    <version>1.0.0-SNAPSHOT</version>
    
    <!-- 打包的机制，如pom,jar,  war，默认为jar -->
    <packaging>jar</packaging>

    <!-- 为pom定义一些常量，在pom中的其它地方可以直接引用 使用方式 如下 ：${file.encoding} -->
    <!-- 常常用来整体控制一些依赖的版本号 -->
    <properties>
        <file.encoding>UTF-8</file.encoding>
        <java.source.version>1.8</java.source.version>
        <java.target.version>1.8</java.target.version>
    </properties>
        <!-- 定义本项目的依赖关系，就是依赖的jar包 -->
    <dependencies>
        <!-- 每个dependency都对应这一个jar包 -->
        <dependency>
            <!--一般情况下，maven是通过groupId、artifactId、version这三个元素值（俗称坐标）来检索该构件， 然后引入你的工程。如果别人想引用你现在开发的这个项目（前提是已开发完毕并发布到了远程仓库），--> 
            <!--就需要在他的pom文件中新建一个dependency节点，将本项目的groupId、artifactId、version写入， maven就会把你上传的jar包下载到他的本地 -->
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>

            <!-- 依赖范围 -->
            <scope>complie</scope>
            <!-- 设置  依赖是否可选，默认为false,即子项目默认都继承。如果为true,
                 则子项目必需显示的引入  -->
            <optional>false</optional>
            
            <!-- 依赖排除-->
            <exclusions>
                <exclusion>
                    <groupId>org.slf4j</groupId>
                    <artifactId>slf4j-api</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
    </dependencies>

    ...
</project>
```

### 多环境配置

```xml
<profiles>
        <profile>
            <id>dev</id>
            <build>
                <finalName>dev</finalName>
            </build>
            <repositories>
                <repository>
                    <id>ali</id>
                    <name>ali repo</name>
                    <url>https://maven.aliyun.com/repository/central</url>
                    <releases>
                        <enabled>true</enabled>
                    </releases>
                    <snapshots>
                        <enabled>true</enabled>
                    </snapshots>
                </repository>
            </repositories>
        </profile>
        <profile>
            <id>test</id>
            <build>
                <finalName>test</finalName>
            </build>
            <repositories>
                <repository>
                    <id>ali</id>
                    <name>ali repo</name>
                    <url>https://mirrors.huaweicloud.com/repository/maven/</url>
                    <releases>
                        <enabled>true</enabled>
                    </releases>
                    <snapshots>
                        <enabled>true</enabled>
                    </snapshots>
                </repository>
            </repositories>
        </profile>
        <profile>
            <activation>
                <activeByDefault>true</activeByDefault>
            </activation>
            <id>pro</id>
            <build>
                <finalName>pro</finalName>
            </build>
        </profile>
    </profiles>
```

### maven仓库

maven项目使用的仓库一共有如下几种方式：

1. 中央仓库，这是默认的仓库
2. 镜像仓库，通过 sttings.xml 中的 settings.mirrors.mirror 配置
3. 全局profile仓库，通过 settings.xml 中的 settings.repositories.repository 配置
4. 项目仓库，通过 pom.xml 中的 project.repositories.repository 配置
5. 项目profile仓库，通过 pom.xml 中的 project.profiles.profile.repositories.repository 配置
6. 本地仓库

搜索顺序如下：

local_repo > settings_profile_repo > pom_profile_repo > pom_repositories > settings_mirror > central

### 插件

在pom文件中，在build中plugins标签中加入

```
<plugin>                                       
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-compiler-plugin</artifactId>
    <version>3.1</version>
    <configuration>
        <source>1.8</source> <!-- 源代码使用的JDK版本 -->
        <target>1.8</target> <!-- 需要生成的目标class文件的编译版本 -->
        <encoding>UTF-8</encoding><!-- 字符集编码 -->
    </configuration>
</plugin>
```







