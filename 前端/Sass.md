# Sass

## 安装与相关配置

### vscode安装***\*Live Sass Compiler插件\****

### 在配置文件中加入

```jsx
"liveSassCompile.settings.formats": [
    {
      // :nested - 嵌套格式
      // :expanded - 展开格式 
      // :compact - 紧凑格式
      // :compressed - 压缩格式
      
      "format": "compact", // 可定制的出口css样式 (expanded,compact,compressed,nested)
      "extensionName": ".css",
      "savePath": "~/../css" //  为null 表示当前目录
    }
  ],
  // 排除目录
  "liveSassCompile.settings.excludeList": [
    "**/node_modules/**",
    ".vscode/**"
  ],
  // 是否生成对应的map
  "liveSassCompile.settings.generateMap": true,
  // 是否添加兼容前缀 例如： -webkit- -moz-
  "liveSassCompile.settings.autoprefix": [
    

    "> 1%",
    "last 2 versions"
  ],
  "explorer.confirmDelete": false,
```

## 语言功能拓展

### 选择器嵌套

### 父选择器 &

```jsx
.box {
	width: 100px;
	height: 100px;
	background-color: red;
	// & 代表父元素 .box
	&:hover {
		width: 200px;
	}
}
// 编译成css代码就是
.box {
  width: 100px;
  height: 100px;
  background-color: red;
}

.box:hover {
  width: 200px;
}
```

### 属性嵌套

```jsx
p {
	width: 100px;
	height: 20px;
	font: {
		size: 12px;
		weight: 900;
	}
}
// 编译成css代码就是
p {
  width: 100px;
  height: 20px;
  font-size: 12px;
  font-weight: 900;
}
```

### 占位符选择器`%foo` 必须通过`@extend` 继承

```jsx
.button%base {
	width: 100px;
	height: 100px;
	background-color: red;
}
.box {
	@extend %base;
	border: 1px;
}
.header {
	@extend %base;
	margin-top: 100px;
}
// 编译成css代码
.button.box, .button.header {
  width: 100px;
  height: 100px;
  background-color: red;
}

.box {
  border: 1px;
}

.header {
  margin-top: 100px;
}
```

## Sass变量

### 注意点

1. 变量要先声明后使用
2. 变量名不能以数字开头，但可以包含数字、字母、下划线、横线（横线与下划线相同，后写的会覆盖前面的定义）
3. 以`$` 开头，后面跟变量名

### 局部变量

```jsx
.box {
	// 局部变量
	$font-size: 12px;
	font-size: $font-size;
	.left {
		font-size: $font-size
	}
}
// 编译成css代码
.box {
  font-size: 12px;
}

.box .left {
  font-size: 12px;
}
// 在与.box同级标签下使用
.header {
	font-size: $font-size;  //报错 Error: Undefined variable: "$font-size".
}
```

### 全局变量

1. `$变量名: 变量属性值;`  形式

   ```jsx
   $font-size: 13px;
   .header {
   	font-size: $font-size
   }
   .main {
   	// 这里也能用
   	font-size: $font-size
   }
   // 编译成css代码
   .header {
     font-size: 13px;
   }
   
   .main {
     font-size: 13px;
   }
   ```

2. `!global`  标识符

   ```jsx
   .header {
   	// 使用!global定义sass全局变量
   	$font-size: 12px !global;
   	font-size: $font-size;
   }
   .main {
   	// 这里也可以使用
   	font-size: $font-size;
   }
   // 编译成css代码
   .header {
     font-size: 12px;
   }
   
   .main {
     font-size: 12px;
   }
   ```

### 变量属性值类型

1. 数字
2. 字符串，又引号字符串与无引号字符串：”foo”,bar
3. 颜色：blue、#fff、rgba(0, 0, 0, 0.5)
4. 布尔值：true、false
5. 空值：null
6. 数组：用空格或者逗号作分隔符：1.3em, 1e, 2 Arial
7. maps：相当于JS中的object，(key1: value1, key2: value2)

### 默认值`!default`

```jsx
$color: red;
// 定义默认值，如果前面没有定义color，就使用这个值
$color: blue !default;
.box {
	width: 100px;
	height: 100px;
	background-color: $color;
}
// 编译成css代码
.box {
  width: 100px;
  height: 100px;
  background-color: red;
}
```

## 导入模块`@import`

### sass会检测scss文件，在对应文件夹生成css文件，要想不被sass检测，在文件名前面加`_` ，这样就不会生成对应的css文件

### 语法

```jsx
// _header.scss 文件   // 在对应文件夹不会生成css文件
$width: 12px;
p {
	width: 100px;
}
// index.scss 文件
@import 'header'   // 可以省略_与scss后缀，sass自动补上
.box {
	width: $width;  // 可以使用_header.scss中的变量
}

// 编译成css文件
p {
  width: 100px;
}

.box {
  width: 12px;
}
```

## 混合指令

### 使用简介

```
@mixin制定公用样式   @inclued引入公用样式
```

### 用于封装css中共用的样式

```jsx
@mixin name{
	width: 100px;
	height: 100px;
	background-color: red;
}
.header {
	@include name();
}
// 编译成css文件
.header {
  width: 100px;
  height: 100px;
  background-color: red;
}
```

### 可以传入一个或多个参数，以及设置默认值

```jsx
@mixin block-padding($top: 0, $right: 0, $bottom: 0, $left: 0) {
  padding-top: $top;
  padding-right: $right;
  padding-bottom: $bottom;
  padding-left: $left;
}
.box {
  @include block-padding(10, 10);
}
// 编译成css文件
.box {
  padding-top: 10;
  padding-right: 10;
  padding-bottom: 0;
  padding-left: 0;
}
```

### 当参数数量不确定的时候使用`...` 操作符

```jsx
@mixin linearGradient($direction, $gradients...) {
  background-color: nth($gradients, 1);
  background-image: linear-gradient($direction, $gradients);
}
.header {
  @include linearGradient(to right, #f00, blue, pink);
}
// 编译成css样式
.header {
  background-color: #f00;
  background-image: -webkit-gradient(linear, left top, right top, from(#f00), color-stop(blue), to(pink));
  background-image: linear-gradient(to right, #f00, blue, pink);
}	
```

## 继承指令

### 使用简介

```
@extend引入要继承的样式
```

### 如果用不到要继承的那个样式，可以用占位符不生成css样式

```jsx
// 不用占位符
.base {
  width: 100px;
  height: 100px;
  background-color: red;
}
.header {
  @extend .base;
}
.main {
  @extend .base;
}
// 编译成css代码
.base, .header, .main {
  width: 100px;
  height: 100px;
  background-color: red;
}
// 使用占位符的
%base {
	width: 100px;
	height: 100px;
	background-color: red;
}
.header {
	@extend: %base;
}
.main {
	@extend: %base;
}
// 编译成css代码
// 注意这里相较于不用占位符的少了一个.base
.header, .main {
  width: 100px;
  height: 100px;
  background-color: red;
}
```

### 慎用继承指令（）

> 它不仅会继承父选择器本身的属性，还会继承与父元素相关的组合选择器属性

```jsx
.base {
  width: 100px;
  height: 100px;
  background-color: red;
  .box {
    border: 1px;
  }
}
.header {
  @extend .base;
}
.main {
  @extend .base;
}
// 编译成css代码
.base, .header, .main {
  width: 100px;
  height: 100px;
  background-color: red;
}

.base .box, .header .box, .main .box {
  border: 1px;
}
```

## 运算操作符

### 等号操作符（`== !=`）

```jsx
$index: 1;
.box {
  @if $index == 10 {
    width: 100px;
  } @else {
    width: 200px;
  }
}
// 编译成css文件
.box {
  width: 200px;
}
```

### 比较操作符（`> < >= <=`）

```jsx
$index: 10;
.box {
	@if $index >= 1{
		width: 100px;
	} @else {
		width: 200px;
	}
}
// 编译成css文件
.box {
  width: 100px;
}
```

### 逻辑运算符（`and or not`）

```jsx
$index: 10;
.box {
  @if $index >= 10 and $index <= 1 {
    width: 100px;
  } @else {
    width: 200px;
  }
}
.header {
  @if $index >= 10 or $index <= 1 {
    width: 100px;
  } @else {
    width: 200px;
  }
}
.main {
  @if not($index = 10) {
    width: 100px;
  } @else {
    width: 200px;
  }
}
// 编译成css文件
.box {
  width: 200px;
}

.header {
  width: 100px;
}

.main {
  width: 200px;
}
```

### 数字运算符

> 注意：1. % 与 单位不能一起运算 2. 纯数字与单位或者%进行运算，结果会自动补上单位

1. - 运算符

   ```jsx
   	width: 10 + 10px; //width: 20px;
     width: 10px + 10; //width: 20px;
   ```

2. - 运算符

   ```jsx
   	width: 20 - 10px; // width: 10px;
     width: 20px - 10; //width: 10px;
   ```

3. - 乘运算符

   ```jsx
   	width: 20 * 20px; //width: 400px;
     // width: 20px * 20px;   报错 两个px单位不能相乘
     // width: 20% * 20%;     报错 两个%单位不能相乘
     width: 2 * 20%; //width: 40%;
     width: 2 * 2rem; //width: 4rem;
     width: 2 * 2em; //width: 4em;
   ```

4. /  除运算符

   1. 只有以下三种情况会按除法来计算
      1. 值或者值的一部分是变量或者函数的返回值
      2. 运算式被小括号包裹着
      3. 值是算术表达式的一部分

   ```jsx
   	// width: (4 / 2px); 报错 Error: 2/px isn't a valid CSS value.
     $index: 10px;
     width: (4px / 2); // width: 2px;
     width: 4px / 2px; // width: 4px / 2px;
     width: 4px / 2px + 2px; // width: 4px;
     width: $index / 2; //width: 5px;
   ```

5. % 取模运算符

   ```jsx
   	width: 40 % 2px; //width: 0px;
     width: 40px % 2; //width: 0px;
   ```

### 字符运算符

> 注意：如果有一个值是函数返回的。情况会不一样

```jsx
	content: foo + "bar"; //content: foobar;
  content: "foo" + bar; //content: "foobar";
  content: foo + bar; //content: foobar;
  content: "foo" + "bar"; //content: "foobar";
```

## 插值语句

### 主要应用场景

1. 选择器
2. 属性名
3. 属性值
4. 注释
5. 除法运算

### 语法`#{}`

```jsx
$className: "danger";
$atter: color;
$author: wang;
a.#{$className} {
  background-#{$atter}: red;
}

.box {
	$width: 50px;
	$height: 50px;
	font: $width // $height Helvetica // font: 1 Helvetica
	font: #{$width} // #{$height} Helvetica // font: 50px/50px Helvetica
}
// 编译成css代码
a.danger {
  background-color: red;
}

.box {
  font: 50px / 50px Helvetica;
}
```

## 流程控制指令

### `if` 指令

```jsx
$index: 10;
.box {
	@if $index > 11 {
		width: 100px;
	}@else if $index > 2 {
		width: 200px;
	}@else {
		width:300px;
	}
}
// 编译成css
.box {
  width: 200px;
}
```

### `for` 指令

1. ```
   to
   ```

    与

   ```
   through
   ```

    的区别

   1. `to`：范围[start,end)

      ```jsx
      @for $i from 1 to 4 {
        p#{$i} {
          width: 10px * $i;
          height: 20px;
          background-color: red;
        }
      }
      // 编译成css文件
      p1 {
        width: 10px;
        height: 20px;
        background-color: red;
      }
      
      p2 {
        width: 20px;
        height: 20px;
        background-color: red;
      }
      
      p3 {
        width: 30px;
        height: 20px;
        background-color: red;
      }
      ```

   2. `through`：范围[start,end]

      ```jsx
      @for $i from 1 through 4 {
      	p#{$i} {
      		width: 10px * $i;
      		height: 20px;
      		background-color: red;
      	}
      }
      // 编译成css文件
      p1 {
        width: 10px;
        height: 20px;
        background-color: red;
      }
      
      p2 {
        width: 20px;
        height: 20px;
        background-color: red;
      }
      
      p3 {
        width: 30px;
        height: 20px;
        background-color: red;
      }
      
      p4 {
        width: 40px;
        height: 20px;
        background-color: red;
      }
      ```