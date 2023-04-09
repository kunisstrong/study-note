# TypeScript

## 基础

### 编译器

源代码 ==> 编译器 ==> 运行时可执行的代码 ==> 运行时

编译器或解释器会将源代码转换成可被运行时执行的代码，运行时是一个真实的计算机或者一个虚拟机，例如java的JVM虚拟机或者JavaScript的浏览器引擎和node.js

当运行时是一台物理计算机时，转换的指令是CPU指令。当运行时是虚拟机时，则有自己的指令

## 安装

mac中安装报错没有权限

```
sudo npm i -g typescrip
```

输入电脑密码

`tsc -v`查看typescript版本出现版本号就安装成功了

## tsconfig.json常用18项配置详解

```
{
  "compilerOptions": {
    "target": "es2020", // 指定 TS 编译成 JS 后的js版本
    "module": "commonjs", // TS 编译成 JS 后采用的模块规范 commonjs amd cmd  es等         
    "lib": ["DOM","ES2020"], /*  指定 TS 编码期间可以使用的库文件版本 比如：ES5就不支持Set集合 */
    "outDir": "./dist", //     指定 TS 文件编译成 JS 后的输出目录                 /* Redirect output structure to the directory. */
    "rootDir": "./src", // 指定 TS 文件源码目录
    "strict": true, // 启用严格检查模式
    "strictNullChecks":false,// null 和 undefined即是值，也是类型, null 和 undefined 值 只能赋值给 any ,unknown和它们各自的类型
    "noImplicitAny": true, // 一般是指表达式或函数参数上有隐含的 any类型时报错
    "experimentalDecorators": true, /* 启用ES7装饰器实验开启选项 */
    "emitDecoratorMetadata": true, /* 启用装饰器元数据开启选项 */
    "declaration": true, // 指定 TS 文件编译后生成相应的.d.ts文件
    "removeComments": false, // TS 文件编译后删除所有的注释
    
    "baseUrl": "src", /* 工作根目录  解析非相对模块的基地址*/
    "paths": {
        "@/datatype/*": ["datatype/*"],
        "@/131/*": ["131/*"],
        "@/132/*": ["132/*"]
      },    
    // 有些依赖库底层 为了兼容CommonJs规范、AMD规范这二者的规范中相互兼容，
    // 使用了 export =，将二者规范统一。
    // "esModuleInterop":true表示允许依赖库中出现export = 这种兼容规范导出的格式，
    //  TS 可以用import from导入 
    "esModuleInterop": true,  
  },
  "include": [ // 需要编译的ts文件一个*表示文件匹配**表示忽略文件的深度问题
    "./src/**/*.ts" // 匹配src下所有的ts文件
, "src/datatype/typepsenumts"  ],
   "exclude": [
    "./src/**/test",
    "./src/**/premit", 
  ]
}
```

## 其他基础知识点

### Bigint

足够大的数

当我们使用`Number.MAX_SAFE_INTEGER`获取最大的数的时候

我们会发现在此基础上过加1 与 加2的结果是一样的，比较他们的结果会返回true

但是当我们`BigInt(Number.MAX_SAFE_INTEGER)`这样处理之后，加一与加二的结果 会返回false 

### 枚举	

```javascript
enum EnumItem {
	item1 = 1,
	item2
	item3
}
```

枚举的类型是数字类型，给第一个属性赋值，后面的属性会自动赋值，并且可以双向取值

```
console.log(EnumItem.item)  // 1
console.log(EnumItem.1)  // item1
```

但是如果枚举的是字符串，后面的属性不能自动赋值，并且不可以双向取值

### 元组

定义：本质就是一个数组，有一下几个特征

1. 定义时的元素的类型确定，但各个元素的类型不必相同
2. 为元素赋值时，该值必须是当前位置的类型

```
let msg: [string, number, number, string] = ['wangkun', 10, 20, 'chenghao']
```

### 接口和类型的区别

#### 接口：只能定义对象类型或接口当名字的函数类型

#### 类型：可以定义任意类型，包括基础类型、联合类型、交叉类型、元组

#### 接口当名字的函数类型

```
interface ActionContent {
  (state: string, age: number): void
}
let actionContent: ActionContent = (state, age) => {
  console.log('state', state)
}
actionContent('wangkun', 123)
```

### 声明文件

`d.ts`为后缀的文件就是声明文件，在文件里可以同过`declare、export`关键字声明全局方法，但是export不能重复声明

### TS文件引入JS

打开tsconfig.json中的`"allowJs": true,

## 	 		 TS自动重启+TS自动运行+Parcel自动打包

####   步骤如下：

（1）初始化 npm init --yes 出现 package.json

（2） 安装 typescript

​			全局安装 cnpm i typescript -g     或

​			本地安装： cnpm i typescript -D   或

​		    yarn安装 yarn global   add typescript 

​		 【cnpm i typescript -D 是 cnpm install typescript --save-dev的缩写】

  (3）生成tsconfig.json文件  

​			 tsc --init  

 （4）修改 tsconfig.json 中的配置
   		 “outDir:  "./dist"     --outDir是ts编译后生成js文件保存的目录
    		"rootDir": "./src", --rootDir是自己编写的ts源文件所在的目录
   		注意: dist src package.json 必须是在一个目录下

（5）编译src目录以及子目录下的ts文件

​			tsc 【在src当前目录下：输入tsc   注意直接写tsc命令即可】
 		  【会把src目录以及子目录下的ts文件全部编译成js文件，并全部输出到dist目录中】

（6）安装 ts-node

​		 	 ts-node让node能直接运行ts代码，无需使用tsc将ts代码编译成js代码。【ts-node则包装了node，它可以直接的运行ts代码】

​			全局安装     cnpm i ts-node -g     或

​			本地安装： cnpm i ts-node -D   或

​		    yarn安装：yarn global   add ts-node 

  （6）安装nodemon工具 【自动检测工具】
  		 nodemon作用：【nodemon可以自动检测到目录中的文件更改时通过重新启动应用程序来调试基于node.js的应用程序】

​		    全局安装  cnpm install -g nodemon     或

​			本地安装： cnpm i nodemon  -D  或

​		    yarn安装    yarn add nodemon  -D   

 （7） 在package.json中配置自动检测，自动重启应用程序

	   "scripts": {
		 "dev": "nodemon --watch src/ -e ts --exec ts-node ./src/app.ts"
	    }

​		  nodemon --watch src/  表示检测目录是package.json同级目录src

​		 -e ts   表示nodemon 命令准备将要监听的是ts后缀的文件

​		 --exec ts-node ./src/project/app.ts 表示检测到src目录下有任何变化 都要重新执行app.ts文件

#### 2.Parcel打包支持浏览器运行TS文件

​		（1）安装Parcel打包工具：npm install parcel-bundler --save-dev

​		（2）在package.json中给npm添加启动项，支持启动parcel工具包

	     "scripts": {
	        "start": "parcel ./index.html"
	      },

​		  (3)  启动parcel工具包

​				cnpm run start  【cnpm start】或  npm  run start  【npm start】或 yarn run start 【yarn start】

##### 安装yarn

```
npm install --global yarn
```

##### 查看版本

```
yarn --version
```

## TS模块化

ts采用es6 export语法，不支持node的CommonJS

但是为了支持CommonJS，提供了兼容方案

```
export = 模块导出

import 名字 = require('模块导入')
```

## class类

### 类基础

```
class Person {
  public name: string = ''
  public age: number = 0
  public phone: string = '123'

  // 构造器函数，当通过new调用Persion类的时候会自动执行constructor构造器函数
  constructor(name_: string, age_: number, phone_: string) {
    this.name = name_
    this.age = age_
    this.phone = phone_
  }

  toEat(otherName: string, address: string) {
    console.log(`${this.name}和${otherName}在${address}吃饭`)
  }
}

//new Person类后发生的事情
// 1. 在堆中创建实例对象
// 2. 调用constructor构造器函数

// const onePerson = new Person()
// 第一种方式，手动给实例属性赋值
// onePerson.name = 'wangkun'
// onePerson.age = 12
// onePerson.toEat('chenghao', '大庆市萨尔图区')

// 第二种方式，通过构造器函数给实例属性赋值
const onePerson = new Person('wangkun', 12, '12312312')
onePerson.toEat('chenghao', '大庆市萨尔图区')

```

###  类原理

1. 类的本质就是构造函数
2. 在类中定义的属性就是实例属性
3. 在类中声明的函数就是函数原型对象中的方法
4. 在类中构造器函数(constructor)中可以接收通过new 调用类时传的参数
4. 构造器函数(constructor)默认返回值是this.不需要写，这也很好的说明了，为什么new一个类，this就指向了等号左边的对象

### 类简写

```
class Person {
	//	会自动将传过来的参数添加到实例对象上成为实例属性
	constructor(public name: string,public age: number,public phone: string) {}
	
	// 原型对象上的方法
	toEat(otherName: string, address: string) {
    console.log(`${this.name}和${otherName}在${address}吃饭`)
  }
}

const onePerson = new Person('lisi', 18, '12312312312')
onePerson.toEat('zhangsan', '北京天安门')
```

## 函数签名

### 定义

由至少一个重载签名和一个实现签名组成，并且有一些规则的函数组合

### 规则

1. 至少一个重载签名和一个实现签名
2. 一般来说有函数有几种返回类型就有几个重载签名，并且重载签名没有函数体，只有函数名称+函数参数+函数参数类型+返回值类型
3. 实现签名是一个完成的函数，有函数体，并且统领所有重载签名（参数和函数返回值类型）
4. 调用函数时调用的是重载签名，但执行时执行的是实现签名的函数体，所以调用的函数 = 重载签名 + 实现签名的函数体 
5. 调用函数时，会根据传的参数判断调用的哪个重载签名

### 方法签名

```
class ArrayList {
  constructor(public element: Array<object>) { }

  // 根据索引来查询数组中指定元素
  get(index: number) {
    return this.element[index]
  }

  // 显示数组列表中所有元素
  show() {
    this.element.forEach(item => {
      console.log(item)
    })
  }

  // 如果是根据数字【元素索引】删除元素，romove方法返回一个数字
  // 如果是根据对象删除元素，remove方法返回一个对象
  remove(value: number): number
  remove(value: object): object
  remove(value: number | object): number | object {
    this.element = this.element.filter((item, index) => {
      if (typeof value === 'number') {
        return value !== index
      } else {
        return value !== item
      }
    })
    return value
  }
}

let stuOne = { stuname: 'wangwu', age: 12 }
let stuTwo = { stuname: 'lisi', age: 23 }
let stuThree = { stuname: 'liuxiang', age: 30 }

const arr = new ArrayList([stuOne, stuThree, stuTwo])
arr.show()
const removeEle = arr.remove(stuOne)
console.log('removeEle-->', removeEle)
arr.show()
```

### 构造器签名

```

type type_ChartParam = {
  width: number
  height: number
}

class Square {
  public width!: number
  public height!: number

  constructor(width_: number, height_: number)  // 重载签名
  constructor(paramObj_: type_ChartParam)  // 重载签名
  constructor(paramObjOrWidth_: number | type_ChartParam, height_: number = 0) {  // 实现签名
    if (typeof paramObjOrWidth_ === 'object') {
      this.width = paramObjOrWidth_.width
      this.height = paramObjOrWidth_.height
    } else {
      this.width = paramObjOrWidth_
      this.height = height_
    }
  }

  getArea() {
    return this.width * this.height
  }
}


const square1 = new Square(12, 20)
console.log('square1.getArea()-->', square1.getArea())

const obj: type_ChartParam = { width: 10, height: 10 }
const square2 = new Square(obj)
console.log('square2.getArea()-->', square2.getArea())
```

## 单例设计模式

### 优势



### 思想架构

```
// 构造单件设计模式
// 第一步：把构造器设置为私有的，不允许外部来创建类的实例对象
// 第二步：至少应该提供一个外部访问的方法或属性，外部可以通过这个方法或属性得到一个对象，所以应该把泽哥方法设置为静态方法
// 第三步：外部调用第二部提供的静态方法来获取一个对象

class MyLocalStorage {
	
	// 创建一个静态属性，用来控制只能创建一个实例对象（懒汉式单件设计模式） 
  static localstorage: MyLocalStorage
  // 饿汉式单件设计模式
  static localstorage: MyLocalStorage = new MyLocalStorage()
  
	
	// 将构造器函数设为私有，不允许在类的外部创建实例对象
  private constructor() {
    console.log('这是TS单设计模式的静态方法的构造器')
  }

  // 向外暴露的静态方法，并在类的内部创建实例对象，将实例对象赋值给静态属性
  public static getInstance() {
    if (!this.localstorage) {
      this.localstorage = new MyLocalStorage()
    }
    return this.localstorage
  }

  // 保存key值，原型上的方法
  public setItem() {
    console.log('我是保存方法')
  }

  // 获取key值 原型上的方法
  public getItem() {
    console.log('我是获取方法')
  }
}


let myLocalstorage = MyLocalStorage.getInstance()
console.log(myLocalstorage.getItem())

```

### 注意事项

1. 在TS中不可以new类中的函数（原型对象上的方法），这一点与js不同，js是可以的
2. 不可以在类外部添加原型方法，但是可以覆盖类上已有的方法

3. 静态方法可以传参，如：`Object.keys({name: 'lisi', age:12})`keys方法就是Object构造函数上的静态方法。（Object类的keys方法用来获取给定对象的自身可枚举行组成的数组）
4. `[1,2,3].push(1)` push方法就是Array类原型对象上的方法

## TS继承

### TS类继承

#### 在Ts中如何实现继承

1. 通过extends关键字实现子类和父类的继承关系
2. 通过构造器函数里使用super实现继承实例属性
3. 通过在类中使用同名方法实现对父类原型上方法的覆盖
4. 通过使用super可以跳过子类原型方法调用父类原型方法

```javascript
class Parent {
  public name!: string
  public age!: number

  constructor(name_: string, age_: number) {
    this.name = name_
    this.age = age_
  }

  sayHello() {
    return '你好'
  }
}

class Son extends Parent {
  public favor!: string

  constructor(name_: string, age_: number, favor_: string) {
    // 使用supper继承父类上的实例属性，相当于Parent.call(this, name_, age_)
    super(name_, age_)
    this.favor = favor_
  }

  // 方法覆盖 我不仅能说你好，我还能说 你好我是小明
  // 方法覆盖，社区上很多人叫方法重写，但是个人感觉方法覆盖更贴切,因为并不是重新定义了父类原型上的方法，而是用子类原型上同名方法覆盖
  // 就是在子类原型上定一个同名方法，以至于在原型链上查找方法的时候在子类原型上就找到这个方法，就不会在继续找父类原型上的方法
  sayHello(): string {
    // 使用super关键字来调用父类原型上的方法,相当于 Parent.prototype.sayHello()
    super.sayHello()
    return '我是小明'
  }
}

const son = new Son('小明', 18, '篮球')
son.sayHello()
```

##### 方法覆盖的定义规则

1. 与父类方法同名

2. 参数与父类相同

3. 父类方法的访问范围【访问修饰符】必须小于子类方法覆盖的的访问范围【访问修饰符】，但是父类不能是private（pubilc、protected、private）

#### 手写TS类继承

##### 以以下这段代码为例

```typescript
class Parent {
  public name!: string
  public age!: number
  static count: number = 3

  constructor(name_: string, age_: number) {
    this.name = name_
    this.age = age_
  }

  sayHello() {
    console.log('你好')
  }
}

class Son extends Parent {
  public favor!: string

  constructor(name_: string, age_: number, favor_: string) {
    super(name_, age_)
    this.favor = favor_
  }

  sayHello(): void {
    super.sayHello()
    '我是xiaoming'
  }
}

const son = new Son('xiaoming', 18, '篮球')
console.log('son.name-->', son.name)
son.sayHello()
console.log('Son.count', Son.count)
```

##### 用js实现

```javascript
let __extends = (function (Son_, Parent_) {
  // 继承静态属性部分
  // 1. 使用for in
  function getStaticExtendsWithForIn(Son_, Parent_) {
    for (const key in Parent_) {
      if (Object.hasOwnProperty.call(Parent_, key)) {
        Son_[key] = Parent_[key];
      }
    }
  }

  // 2. 使用__proto__
  function getStaticExtendsWithProto(Son_, Parent_) {
    Son_.__proto__ = Parent_
  }

  // 3. 使用object.setPrototypeof,方法本身就是函数 不用封装函数了 

  // 封装静态属性时使用恰当的继承方法，避免浏览器不支持某个方法
  let myExtendsStatic = function (Son_, Parent_) {
    myExtendsStatic = Object.setPrototypeOf || getStaticExtendsWithForIn || getStaticExtendsWithProto
    return myExtendsStatic(Son_, Parent_)
  }
  // 继承静态属性部分结束
  return function (Son_, Parent_) {
  	// 调用静态属性函数，实现继承静态属性
    myExtendsStatic(Son_, Parent_)
    
    // 通过寄生组合式实现继承原型对象
    function Middle() {
      this.constructor = Son_
    }
    // 防止传过来的父类是一个空对象
    if (Parent_) {
      Middle.prototype = Parent_.prototype
      return Son_.prototype = new Parent_
    } else {
      Son_.prototype = Object.create(null)
    }
  }
})()

let Parent = (function () {
  function Parent(name_, age_) {
    this.name = name_
    this.age = age_
  }

  Parent.prototype.sayHello = function () {
    console.log('你好')
  }
  Parent.count = 3
  return Parent
})()

let Son = (function (_super) {
  __extends(Son, _super)
  function Son(name_, age_, favor_) {
    let _this = _super.call(this, name_, age_) || this
    _this.favor = favor_
  }
  return Son
})(Parent)

const son = new Son('xiaoming', 18, '篮球')
console.log('son.name-->', son.name)
son.sayHello()
console.log('Son.count-->', Son.count)
```

## 类型断言、转换

类与类之间不是继承关系就必须是相等或者一个是另一个的子集

类与接口之间不是实现关系就必须是相等或者一个是另一个的子集

## 类型守卫

typeof：可以用来检测原始类型，检查引用类型除了function返回function，其他的都返回object

a instanceof b：b的原型是否出现在a的原型链上

Object.prototype.toString.call()：返回数据的类型

## 抽象类

```typescript
abstract class People {  // 抽象类
	public abstact eat(): void;	  // 抽象方法
}
```

1. 抽象类不能通过new关键进行实例化
2. 抽象方法不能有函数体，也就是不能有具体实现

### 使用技巧

1. 如果接口定义了超多方法，但是继承了这个接口就需要实现所有接口的所有方法，这时候就可以通过抽象类适配器来做一个缓冲

## 自定义守卫

通过自定义守卫来缩小参数范围，避免大量出现`a typeof b === 'string'`的冗余代码

```
// 用于判断值是否为字符串
function isString(str: any): str is string {
  return typeof str === 'string'
}

let value: string = 'wangkun'

if (isString(value)) {

  console.log('value-->', value.length);
} 
```

## 泛型

### 定义

1. 定义时不明确，但使用时必须明确成某种具体数据类型的数据类型
2. 编译期间进行数据类型安全检查的数据类型

### 泛型约束

缩小泛型的范围，让泛型的数据类型之后在一定范围内

```typescript
class People<T extends object> {

}

const people1 = new People<object>()
const people2 = new People<string>()  //  报错
```

#### keyof关键字

将对象中的所有属性的键列举出来，以键的字符串形成一个联合类型

```
const obj4 = {
  name: 'wangun',
  age: 12
}

type type1 = keyof typeof obj4
let allValue: type1 = 'name'
```



### 不能替换

#### object

1. 不能进行类型检查，比如我常见一个顾客类的实例，但是如果将类型定义成object，那么不能顾客类型的对象也可以传进类
2. object类型数据不能接受非object类型变量，
3. object 类型数据获取属性和方法时无法自定提示

#### any











## 番外

### TypeScript4新特性

#### const

const定义常量，并且定义了不能修改，但是定义的是引用类型的话可以修改里面的元素，可能通过设置`as const`让元素也不可能的修改

```
const arr2 = [1, 2, 3, 4] as const

arr2[0] = 3 // 报错， 无法分配到0，因为它是只读属性
```

#### 可变元组

元组是不可变得，但是有些情况可能前面几个属性是固定的，后面不知道还要添加什么信息，就要用到可变属性

```typescript
const [username, age, ...rest]: [string, number, ...any[]] = ['wangkun', 12, '大g车主']
console.log('username-->', username)
console.log('age-->', age)
console.log('rest-->', rest)
```

#### 元组标签

元组定义数据类型是会造成每个数据类型不知道对应的是哪个信息，就可以使用元组标签解决

```typescript
const [username, age, ...rest]: [name_: string, age_: number, ...rest: any[]] = ['wangkun', 12, '大g车主']
console.log('username-->', username)
console.log('age-->', age)
console.log('rest-->', rest)
```

### Object与object

1. Object可以接受任何类型

   ```javascript
   const obj1: Object = 1
   const obj2: Object = ''
   const obj3: Object = {}
   ```

2. object只能接受引用类型

   ```
   const obj1: object = 1 // 报错。不能讲number分配给object
   const obj2: object = []
   ```

看到第7章 13节
