# java

## 经典书籍

1. java核心技术
2. java编程思想
3. Effective Java
4. 深入理解Java虚拟机
4. java从入门到精通

## 未熟悉的点

- [ ] 反射
- [ ] 错误处理
- [ ] 多线程
- [ ] Map、ArrayList等

## java程序开发的三个步骤

1. 编写代码
2. 编译代码：`javac HelloWorld.java`
3. 运行代码：`java HelloWorld`

![image-20230223092302121](/Users/wangkun/Library/Application Support/typora-user-images/image-20230223092302121.png)

##  JDK组成

JDK > JRE > JVM

1. JVM：虚拟机，Java程序运行的地方

2. 核心类库：Java自己写好的程序

3. JRE：Java运行环境，包括JVM和核心类库

4. JDK：包括上面所有，Java开发工具包 

## 切换Java版本的方法

> 在命令行输入java8，就马上切换到java8的版本上
>
> 在命令行输入java11。就切换到java11的版本

1. 查看版本

   ```
   确认一下有没有存在下面的目录
   
   cd /Library/Java/JavaVirtualMachines/  #进入这个目录
   
   ls  #查看目录下的文件
   
   会看到你下载过的所有java版本
   ```

2. 添加环境变量

   `open .bash_profile`：打开环境变量

   ```
   # jdk8 
   java8=/Library/Java/JavaVirtualMachines/zulu-8.jdk/Contents/Home
   
   # jdk 11
   java11=/Library/Java/JavaVirtualMachines/jdk-11.0.2.jdk/Contents/Home
   
   # jdk 18
   java18=/Library/Java/JavaVirtualMachines/jdk-18.0.2.1.jdk/Contents/Home
   
   # default jdk8
   export JAVA_HOME=$java8
   
   alias java8="export JAVA_HOME=$java8"
   alias java11="export JAVA_HOME=$java11" 
   alias java11="export JAVA_HOME=$java18"
   ```

3. 加载配置文件

   `. ~/.bash_profile`：重新加载我们修改后的配置文件

4. 使用

   就可以通过java8 java11 java18切换版本了

## jar包

>  java程序达成的一种压缩包格式，你可以讲这些jar包引入到你的项目中



## 抽象类

> 用abstract描述的类就是抽象类

抽象类的注意事项：

1. 抽象类不能实例化
   - 如果抽象类允许创建对象，就可以调用内部没有方法体的，抽象方法了
2. 抽象类存在构造方法
   - 交给子类，通过supper进行访问
3. 抽象类中可以存在普通方法
   - 可以让子类继承到继续使用
4. 抽象类的子类
   - 要么重写抽象类中所有的抽象方法
   - 要么是抽象类

## Lambda表达式

> 就是JavaScript中的箭头函数

```java
package com.wangkun.mlambda;

public class LambdaTest1 {
    public static void main(String[] args) {
      
      	//。注意。使用Lambda表达式重写的方法不能带有方法体
        useInterA(() -> {
            System.out.println("我说话了");
        });

    }

    // 静态方法只能被静态方法引用
    public static void useInterA(InterA a) {
        a.say();
    }
}

interface InterA {
    void say();
}
```



## 接口

### 接口新特性

1. JDK8开始，方法可以有方法体
2. JDK8开始，方法可以是静态方法
3. JDK9开始，方法可以是私有的

```java
package com.wangkun.mpolymorphism;

public class PolymorphismTest2 {
    public static void main(String[] args) {
//        A.eat();

        B b = new B();
        b.say();

    }
}

interface A {
    // JDK9开始可以定义私有方法
    private void show() {
        System.out.println("A....shows");
    }

    // JDK8开始接口方法可以有方法体了
    default void say() {
        System.out.println("我会说话.....");
        show();
    }

    // JDK8开始接口方法可以是静态的了
    static void eat() {
        System.out.println("我可以吃饭");
    }

}

class B implements A {

}
```



### 类与接口之间的关系

1. 类与类： 继承关系，只能单继承，不支持多继承，但是可以深层次继承
2. 类与接口：实现关系，可以单实现，也可以多实现，甚至可以在继承一个类的同时，实现多个接口
3. 接口与接口：继承关系，可以单继承，也可以多继承。

### 抽象类与接口的对比

抽象类：对事物做抽象（描述事物）

接口：对行为抽象（制定规则）

## 注释

1. 单行注释

2. 多行注释

3. 文档注释

   > 可以用Java自带的工具生成文档

## IDEA项目结构

**project(项目) ==> module(模块) ==> pageage(包) ==> class(类)**

### 好用的设置

1. 自动导包
2. 代码提示忽略大小写
3. 背景设为自己的想用的图片

## final关键字

> 表示最终的意思，与js中const类似

**特点：**

1. 修饰方法，被修饰的方法不能被**重写**
2. 修饰类，被修饰的类不能被**继承**
3. 修饰属性，被修饰的属性不能被**赋值**

**命名规范：**

1. 如果是一个单词：全部大写
2. 多个单词：全部大写，并且每个单词之间用`_`分割

## 数字 类型转换

> 数字进行运算时数据类型不一样不能运算，需要转成一样的，才能运算

#### 隐式转换(自动类型提升)

> 取值范围小的 ==> 取值范围大的

```java
byte > short > int > long > float > double  
```

注意。byte short char 进行运算直接提升成int类型

#### 强制转换

> 取值范围大的 复制给 取值范围小的

```java
double y = 9.92;
System.out.println((int) y); // 9 直接截断后面的小数
```

![image-20230224104008377](/Users/wangkun/Library/Application Support/typora-user-images/image-20230224104008377.png)

## 变量分类

#### 按数据类型分  对于每一种数据都定义了明确的具体数据诶行(强类型语言)，在内存中分配了不同大小的内存空间

#### 获得键盘输入结果

```java
import java.util.Scanner;
public class first {
    public static void main(String[] args) {
        Scanner scan = new Scanner(System.in);

        System.out.println("请输入你的姓名");
        String uname = scan.next();
        System.out.println(uname);

        System.out.println("请输入你的年龄");
        int age = scan.nextInt();
        System.out.println(age);

        System.out.println("你是否相中我了");
        boolean isLove = scan.nextBoolean();
        System.out.println(isLove);
    }
}

```

## 数组

```java
// 定义一个int型数组，数组元素只能是int型(整型)
int[] arr = new int[]{1,2,3};

// 定义一个String型的二维数组，数组元素只能是String类型(字符串)
String[][] = new String[][]{{"wangkun","chenghao"},{"xuqi","xumingzhe"}};
```

## 集合 

> 常用的集合有List集合、Set集合和Map集合，其中List集合与Set集合继承了Collection接口，各接口还提供了不同的实现类

![image-20230315110744531](/Users/wangkun/Library/Application Support/typora-user-images/image-20230315110744531.png)

### ArrayList

```java
package second;

import java.util.ArrayList;
import java.util.Arrays;
import java.util.Collection;

public class CollectionTest {
    public static void main(String[] args) {
    		// 创建一个集合 数组列表
        Collection coll = new ArrayList();
				// 添加元素
        coll.add("chenghao");
        coll.add(123);
        coll.add(true);
      
      	// 遍历ArrayList
      	for(Object item : coll){
					System.out.printLn(item);
        }
			
        System.out.println(coll);
        // 删除指定元素
        System.out.println(coll.remove(123));
        System.out.println(coll);
				// Arrays.aList(123,456); 将数组转化成集合
        Collection coll1 = Arrays.asList(123, 456);
        System.out.println("containsAll-->" + coll.containsAll(coll1));
        
				// 将集合转换成数组
				Object[] arr = coll.toArray();
      	for(int i = 0; i < arr.length; i++){
          System.out.printLn(arr[i]);
        }
    }
}
```

### 常用的集合方法

![image-20230301143610222](/Users/wangkun/Library/Application Support/typora-user-images/image-20230301143610222.png)

### HashMap

```java
package second;

import java.util.*;

public class HashMapTest {

    public static void main(String[] args) {
        Map<String, String> map = new HashMap<>();

        map.put("name", "wangkun");
        map.put("age", "123");

        System.out.println(map);

        System.out.println("name-->" + map.get("name"));

        // 构建Map集合中所有key的Set集合
        Set<String> set = map.keySet();
        Iterator<String> it = set.iterator();
        // 构建Map集合中所有value的Set集合
        Collection<String> coll = map.values();
        Iterator<String> valIt = coll.iterator();
        while (it.hasNext()) {
            System.out.println(it.next() + ":" + valIt.next() + "    ");
        }
    }
}
```

## 方法重载

与TS不同，没有重载签名与实现签名，每一个函数都是实现签名

```java
				class Fun {
            public int mOL(int num) {
                return num * num;
            }

            public int mOL(int num1, int num2) {
                return num1 * num2;
            }

            public void mOL(String str) {
                System.out.println("str-->" + str);
            }
        }
        Fun fun = new Fun();
        System.out.println("fun.mOL(1)-->" + fun.mOL(1));
        System.out.println("fun.mOL(2, 3)-->" + fun.mOL(2, 3));
        fun.mOL("wangkun");
```

放大在没有被调用的时候，在方法区中的字节码文件中存放56

被调用的时候，需要进入到栈内存中运行

## Java内存分配

有五块：栈、堆、方法区、本地方法栈、寄存器

## 两个变量互换数据

```java
package com.wangkun.test;

public class Test5 {
    public static void main(String[] args) {

        int a = 10;
        int b = 20;

        // ^ 相等的数值互相抵消
        a = a ^ b; // 10 20
        b = a ^ b; // 10 20 20 ==> b = 10
        a = a ^ b; // 10 20 10 ==> a = 20

        System.out.println(a);
        System.out.println(b);
    }
}
```

## this内存图

![image-20230301071602981](/Users/wangkun/Library/Application Support/typora-user-images/image-20230301071602981.png)

## 封装

> 合理隐藏、合理暴露

## 四种修饰符

`private`：同一个类中

`(default)`：同一个类、同一个包中

`protected`：同一个类、同一个包、不同包的子类

`public`：任何位置

![image-20230301163704310](/Users/wangkun/Library/Application Support/typora-user-images/image-20230301163704310.png)

## 字符串

字符串会内存共享，用双引号创建的字符串，会被保存在字符串常量池

> JDK7之前常量池是在方法区中
>
> JDK7之后常量池是在堆内存中

```java
package com.wangkun.string;

public class StringTest1 {
    public static void main(String[] args) {
        String str1 = "asd";
        String str2 = "asd";

        System.out.println(str1 == str2);  // true
    }
}
```

### 考点1

字符串底层就是字节类型的数组

我们通过`new String()`创建字符串，如果常量池中已 经有了，就会将存在字符串的字节类型数据的地址拷贝一份到 自己new出来的的那个字符串对象中

![image-20230301103417457](/Users/wangkun/Library/Application Support/typora-user-images/image-20230301103417457.png)

### 考点2

字符串拼接原理

先在常量池创建"c"，然后在堆中创建StringBuilder类型的对象，将s2拷贝一份放到对象中，然后StringBuild调用toString方法将"abc"转换成String类型，将String类型的地址给s3

![image-20230301103746201](/Users/wangkun/Library/Application Support/typora-user-images/image-20230301103746201.png)

### 字符串常用的的API

1. `s1.equals(s2)`：比较字符串内容
2. `s1.equalsIgnoreCase(s2)`：比较字符串内容，忽略大小写
3. `s1.substring(2)`：截取字符串，从索引2开始截取到最后

### StringBuilder

> 提高字符串操作效率
>
> 是字符串的缓冲区，将其理解成容器，这个容器可以存储任意数据类型，但只要进入这个容器就会变成字符串

**字符串拼接很频繁，可以用StringBuilder提高效率**

### 常用的方法

1. append：拼接字符串，可以链式调用
2. reverse：反转
3. length()：返回长度
4. toString()：转换成字符串

## 类

### static关键字

static成员变量：共享数据

static成员方法：常用于工具类

### 特点

1. 被类的所有对象共享
2. 多了一种引用方式，可以通过类名引用（推荐使用类名进行调用）
3. 随着类的加载而加载，优先于对象存在

### 注意事项

1. static方法中，只能访问静态成员(直接访问)
2. statuc中不允许使用this关键字



### 构造器函数

```java
class Person {
	public String name;
  // ts中的是
  // public name!: string
	 
	public Person (String name_) {
		this.name = name_
	}
}
```

### this的两个用处

1. 跟ts一样

2. 根据参数调用构造器函数，必须放在构造器函数第一行

   注意点，不能形成循环调用，产生死循环

   ```java
   class Person {
   	class Person {
               private ivate String name;
               private int age;
   
               Person(String name_) {
                   name =  name_;
               }
               Person(int age_, String name_) {
                   this(name_);
                   this.age = age_;
               }
   
               public void say() {
                   System.out.println("我的年龄是" + this.age);
                   System.out.println("我的名字是" + this.name);
               }
           }
   
           Person person = new Person(12,"wangkun");
           person.say();
   }
   ```

### Java继承

Java继承是向上继承，但是js继承是基于原型链继承



在自身堆内存中有一块supper的区域专门存放父类属性，只是私有属性我们无权至今访问，但是属性是存在的

![image-20230301212828555](/Users/wangkun/Library/Application Support/typora-user-images/image-20230301212828555.png)

### 多态性

> 同一行为具有多个不同表现形式或者形态的能力

**多态的前提：**

1. 有继承、实现的关系
2. 有方法重写
3. 有父类引用指向子类对象

多态调用的是父类的方法，但是执行的是子类重写的方法

#### 多态的两种形式：对象多态与行为多态

**对象多态：**` 父类  父类名称 = new 子类() `

```java
package com.wangkun.mpolymorphism;

public class PolymorphismTest {
    public static void main(String[] args) {

        Animal dot = new Dot();
        dot.eat();

        Animal cat = new Cat();
        cat.eat();
    }
}


abstract class Animal {
    public abstract void eat();

}

class Dot extends Animal {
    @Override
    public void eat() {
        System.out.println("狗吃肉");
    }
}

class Cat extends Animal {
    @Override
    public void eat() {
        System.out.println("猫吃鱼");
    }
}
```

行为多态：`父类  父类名称 = new 子类()`

```java
package com.wangkun.mpolymorphism;

public class PolymorphismTest {
    public static void main(String[] args) {

        // useAnimal(new Cat());
        useAnimal(new Dot());

    }

    public static void useAnimal(Animal animal) {
        animal.eat();
    }
}


abstract class Animal {
    public abstract void eat();

}

class Dot extends Animal {
    @Override
    public void eat() {
        System.out.println("狗吃肉");
    }
}

class Cat extends Animal {
    @Override
    public void eat() {
        System.out.println("猫吃鱼");
    }
}

```

#### 多态成员访问的特点

1. 成员变量：编译看左边(父类)，运行看左边(父类)
2. 成员方法：编译看左边(父类)，运行看右边(子类)

## 泛型

### 集合中使用泛型

```
package second;

import java.lang.reflect.Array;
import java.util.*;

public class HashMapTest {

    public static void main(String[] args) {

        // ArrayList
        Collection<Integer> list = new ArrayList<Integer>();
        list.add(12);
        list.add(67);
        list.add(85);

        // 1. for循环
        for (int item : list) {
            System.out.println(item);
        }
        System.out.println("---------------------");
        // 迭代器
        Iterator<Integer> iterator = list.iterator();
        while (iterator.hasNext()) {
            System.out.println(iterator.next());
        }
        System.out.println("-------------------");

        // HashMap
        Map<String, Integer> map = new HashMap<String, Integer>();

        map.put("wangkun", 100);
        map.put("chenghao", 10);
        map.put("xumingzhe", 10);

        // 变量HashMap
        Set<String> key = map.keySet();
        Iterator<String> keyItem = key.iterator();

        Collection<Integer> value = map.values();
        Iterator<Integer> valueItem = value.iterator();
        while (keyItem.hasNext()) {
            String keyNext = keyItem.next();
            Integer valueNext = valueItem.next();
            System.out.println(keyNext + "-->" + valueNext);
        }
    }


}

```

### 自定义泛型类

```java
package last;

import java.util.ArrayList;

public class Person<T> {
    private T name;

    public Person() {
    }

    ;

    public <E> ArrayList<E> say(E[] arr) {
        ArrayList<E> list = new ArrayList<>();

        for (int i = 0; i < arr.length; i++) {
            list.add(arr[i]);
        }

        return list;
    }

    public static void main(String[] args) {
        Person<String> student = new Person<>();
        student.name = "wangkun";

        Integer[] arr = {1, 2, 3,};

        ArrayList<Integer> say = student.say(arr);
        System.out.println(say);
    }
}
```

