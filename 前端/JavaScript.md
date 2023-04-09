# JavaScript

## Proxy(代理与反射)

通过操作代理对象影响目标对象

通过Proxy构造函数实现，接受两个参数，目标对象与处理程序对象

对两个对象进行的操作都会同步到另一个对象

可以用全局反射对象替代处理程序对象

## JSON

就是一种数据类型，跟javascript对象很像，但有三点不同

1. 没有变量声明
2. 要使用双引号将属性名包裹起来
3. 结尾不能有分号(JS也可以没有分号)

有两个方法：

1. `JSON.stringify()`：将JS对象序列化成JSON对象
2. `JSON.parse()`： 将JSON对象解析成JS对象

`JSON.stringify()`

1. 接受两个参数，第一个参数是要序列化的对象或数组，第二个参数是数组或者函数，

   - 数组：只会序列化数组中的属性

   - 函数：函数有两个参数，一个key，一个value，可以根据switch控制每个key对应的value以什么形式进行序列化

`JSON.parse`：也可以接受一个函数作为参数，对每一个属性进行处理

## 继承

#### 原型与原型链

要完全理解继承，一定要对原型与原型链的知识非常熟悉，要达到脑中有图，看到`__proto__、constructor、prototype`脑中就能立马出现他们的指向关系

这时就要推荐我的原型与原型链文章了，简单易懂，非常透彻

https://juejin.cn/post/7042181321338126373

#### 需要懂的方法

想要详细了解可以去MDN查看，这里只是做简单说明

1. Object.create(Fun.prototype)：创建一个新对象，并将新对象的`__proto__`属性指向Fun.prototype，
2. Object.setPrototypeof(obj, otherObj.prototype)：将obj的`__proto__`属性指向otherObj.prototype
3. object.keys(obj)：返回obj自身可枚举属性的数组
4. Object.prototype.hasOwnProperty()：指示对象自身属性中是否具有指定的属性，有返回true，没有返回false
5. call()：首先会调用函数，然后将调用者的this指向第一个参数，剩下的参数会当作调用者的参数，也可以用apply，只不过第二个参数是一个数组

### JS继承

继承分为两种一种是继承原型方法和实例属性，另一种是继承静态属性和方法。因为TS类继承中这两种都会用到，所以都要掌握

#### 继承原型方法和实例属性

##### 原型链继承

1. 用父构造函数的实例对象代替子构造函数的原型
2. 在子构造函数的原型上添加的construtor属性，并将它指回Son函数

```javascript
function Parent(name_, age_) {  
  this.name = name_
  this.age = age_
}

Parent.prototype.eat = () => {
  console.log(`我能吃饭`)
}

function Son(favor_, sex_) {
  this.favor = favor_
  this.sex = sex_
}

// 将Son的原型属性指向Parent的实例对象，就是用Parent的实例对象替换Son的原型对象
Son.prototype = new Parent('xiaoli', 18)
// 在Parent的实例对象(Son构造函数的原型)上添加constructor属性，并将它指向Son构造函数
Son.prototype.constructor = Son

const son = new Son('篮球', '男')
console.log('Son.prototype-->', Son.prototype)
son.eat()
```

缺陷：不能给父构造函数传参数，也就不能按需修改父构造函数实例对象上的属性

##### 借用构造函数继承

1. 使用call或者apply方法调用父构造函数并将this指向子构造函数

```javascript
function Parent(name_, age_) {
  this.name = name_
  this.age = age_
}

Parent.prototype.eat = () => {
  console.log(`我能吃饭`)
}

function Son(name_, age_, favor_, sex_) {
  Parent.call(this, name_, age_) // 对于此案例来说，相当于添加下面两行代码
  // this.name = name_
  // this.age = age_

  this.favor = favor_
  this.sex = sex_
}

const son = new Son('小明', '18', '篮球', '男')
console.log('son-->', son)
// 调用父构造函数上的方法会报错
son.eat() //报错  Uncaught TypeError: son.eat is not a function
```

缺陷：无法使用父构造函数原型上的方法

##### 原型链+借用构造函数继承

结合上面两种形式

1. 用父构造函数的实例对象代替子构造函数的原型
2. 使用call或者apply方法调用父构造函数并将this指向子构造函数

```javascript
function Parent(name_, age_) {
  this.name = name_
  this.age = age_
}

Parent.prototype.eat = () => {
  console.log(`我能吃饭`)
}

function Son(name_, age_, favor_, sex_) {
  Parent.call(this, name_, age_) // 相当于在下面添加这两行代码，但这句是动态的，会根据夫构造函数变化而变化
  // this.name = name_
  // this.age = age_

  this.favor = favor_
  this.sex = sex_
}

// 将Son的原型属性指向Parent的实例对象，就是用Parent的实例对象替换Son的原型对象
Son.prototype = new Parent('xiaoli', 18)
// 在Parent的实例对象(Son构造函数的原型)上添加constructor属性，并将它指向Son构造函数，虽然添加与否不影响结果，但为了完整性和不惧迷惑性最好修改过来
Son.prototype.constructor = Son

const son = new Son('篮球', '男')
console.log('Son.prototype-->', Son.prototype)
son.eat()
```

缺陷： 调用了两次父构造函数(Parent.call(this, name_, age_)和new Parent('xiaoli', 18))，这是完全没必要的

##### 寄生组合式继承

寄生组合式继承有四种实现方式，主要的区别在于继承父构造函数原型上方法的实现的不同，但是原理都是差不多的

###### 方式一

手动创建一个函数

1. 封装一个函数，实现继承父构造函数原型上的方法
2. 使用call或者apply方法将父构造函数的this指向子构造函数

```javascript
function Parent(name_, age_) {
  this.name = name_
  this.age = age_
}

Parent.prototype.eat = () => {
  console.log(`我能吃饭`)
}

function Son(name_, age_, favor_, sex_) {
  Parent.call(this, name_, age_) // 相当于在下面添加这两行代码，但这句是动态的，会根据父构造函数实例属性数量的变化而变化
  // this.name = name_
  // this.age = age_

  this.favor = favor_
  this.sex = sex_
}

// 区别
function _extends(parent_, son_) {
  // 新建一个函数，作为继承的媒介
  function Middle() {
    // 当new Middle构造函数时在实例对象(也就是Son的原型上)添加一个constructor属性，并且指向Son构造函数
    this.constructor = son_
  }
  // 用父构造函数的原型替代媒介构造函数的原型，实现子构造函数可以访问父构造函数
  Middle.prototype = parent_.prototype
  return new Middle()
}

// 在Parent的实例对象(Son构造函数的原型)上添加constructor属性，并将它指向Son构造函数
Son.prototype = _extends(Parent, Son)

const son = new Son('xiaoming', 18, '篮球', '男')
console.log('Son.prototype-->', Son.prototype)
console.log('son-->', son)
```

###### 方式二

通过Object.creat()实现

1. 封装一个函数，使用Object.creat()方法实现继承父构造函数的原型
2. 使用call或者apply方法将父构造函数的this指向子构造函数

```javascript
// 关键步骤
// 1. 用父构造函数的实例对象代替子构造函数的原型
// 2. 在子构造函数的原型上添加的construtor属性，并将它指回Son函数
// 3. 使用call或者apply方法将父构造函数的this指向子构造函数

function Parent(name_, age_) {
  this.name = name_
  this.age = age_
}

Parent.prototype.eat = () => {
  console.log(`我能吃饭`)
}

function Son(name_, age_, favor_, sex_) {
  Parent.call(this, name_, age_) // 相当于在下面添加这两行代码，但这句是动态的，会根据夫构造函数变化而变化
  // this.name = name_
  // this.age = age_

  this.favor = favor_
  this.sex = sex_
}

// 区别
function _extends(parent_, son_) {
  return Object.create(parent_.prototype, {
    constructor: {
      writable: true,
      value: son_,
    },
  })
}

// 在Parent的实例对象(Son构造函数的原型)上添加constructor属性，并将它指向Son构造函数
Son.prototype = _extends(Parent, Son)

const son = new Son('xiaoming', 18, '篮球', '男')
console.log('Son.prototype-->', Son.prototype)
console.log('son-->', son)
```

###### 方式三

自己创建伪实例对象

前两种方式其实就是通过new构造函数或者使用方法的方式创建一个新的函数，这种方式就是自己手动创建一个空对象

1. 封装一个函数，手动创建一个空对象并继承父构造函数的原型
2. 使用call或者apply方法将父构造函数的this指向子构造函数

```javascript
function Parent(name_, age_) {
  this.name = name_
  this.age = age_
}

Parent.prototype.eat = () => {
  console.log(`我能吃饭`)
}

function Son(name_, age_, favor_, sex_) {
  Parent.call(this, name_, age_) // 相当于在下面添加这两行代码，但这句是动态的，会根据夫构造函数变化而变化
  // this.name = name_
  // this.age = age_

  this.favor = favor_
  this.sex = sex_
}

// 区别
function _extend(parent_, son_, ) {
  let newObj = {
	constructor: son_
	}
	newObj.prototype = parent_.prototype
	son_.prototype = newObj
}

_extend(Parent, Son)
const son = new Son('xiaoming', 18, '篮球', '男')
console.log('Son.prototype-->', Son.prototype)
console.log('son-->', son)

```

以上都有一个缺陷，继承了父类构造函数之后，因为子类的原型变成了新对象，自然而然原来子类原型上定义的方法也就没了，可以用下面这种方法解决

###### 方式四

使用`Object.setPrototypeOf`

```javascript
function Parent(name_, age_) {
  this.name = name_
  this.age = age_
}

Parent.prototype.eat = () => {
  console.log(`我能吃饭`)
}

function Son(name_, age_, favor_, sex_) {
  Parent.call(this, name_, age_) // 相当于在下面添加这两行代码，但这句是动态的，会根据夫构造函数变化而变化
  // this.name = name_
  // this.age = age_

  this.favor = favor_
  this.sex = sex_
}

function _extends(parent_, son_) {
  // 修改子构造函数的原型上__proto__属性，并将它指向父构造函数的原型(__proto__原来指向的是Function的原型对象)
  return Object.setPrototypeOf(son_.prototype, parent_.prototype)
}

const son = new Son('xiaoming', 18, '篮球', '男')
console.log('Son.prototype-->', Son.prototype)
console.log('son-->', son)
```

保留了子构造函数的原型，完美解决上面的问题

#### 继承静态属性

```javascript
function Parent() {}

Parent.uname = 'xiaoming'
Parent.age = 18

function Son() {}

console.log('Son1-->', Son.age)

// 方式一  ES6之前，注意使用for..in循环遍历可枚举属性会沿着原型链一直查找，如果有影响使用可以Object.prototype.hasOwnProperty避免发生这种情况
// for (let key in Parent) {
//   if (Object.prototype.hasOwnProperty.call(Parent, key)) {
//     Son[key] = Parent[key]
//   }
// }

//方式二 使用object.keys()
// 筛选出参数中的可枚举属性
// Object.keys(Parent).forEach(item => {
//   Son[item] = Parent[item]
// })

// 方式三 手动将修改Son的__proto__属性
// Son.__proto__ = Parent

// 方式四 使用es6新出的Object.setPrototypeOf()
// Object.setPrototypeOf(Son, Parent)

const son = new Son()
console.log('son.ball-->', son.ball)
console.log('Son2-->', Son.age)
```

## ES6模块

### 模块导出

ES6模块导出有两种方式：命名导出与默认导出

1. 命名导出

   - 方式一：**变量声明的同时将变量导出**

     ```javascript
     export const foo = 'foo'
     
     export const bar = () => {}
     ```

   - 方式二：**先声明，然后将标识符导出**

     ```java
     const foo = 'foo'
     const bar = () => {}
     
     // 这种方式可以设置别名
     export {foo as myFoo, bar}
     ```

     值得注意的是尽量不要把别名设置成default，这样就会被识别成默认导出

     ```javascript
     const foo = 'foo'
     
     export {foo as default}
     // 相当于
     export default foo
     ```

2. 默认导出

   ```javascript
   const foo = 'foo'
   
   export default foo
   ```

3. 在一个模块两种方式可以混用

   ```javascript
   const foo = 'foo'
   const bar = () => {}
   
   export {foo}
   export default bar
   ```


### 模块导入

ES6模块导入使用`import`关键字，形如：`import *** from '***.js'`。

> 值得注意的是如果通过构建工具和第三方模块加载器 打包或解析ES6模块，则可以不加文件扩展名；但是在浏览器中通过标识符原生加载模块，则必须加文件扩展名。**所以推荐无论何时都把文件扩展名加上**

#### 实际开发中常见的三种情况

1. 导入自定义的模块：使用相对路径导入，也可以使用绝对路径，在工作中基本都是使用相对路径，如：

   ```javascript
   import foo from './foo.js'
   ```

2. 导入内置模块：直接使用标识符，如：

   ```javascript
   import {computed, ref} from "vue"
   ```

3. 不需要特定导入，只是想加载和执行模块并利用模块的副作用，如：

   ```javascript
   import './foo.js'
   
   import 'element-plus/dist/index.css'
   ```

#### 每种模块导出对应的模块导入

1. 命名导出

   - 方式一：**变量声明的同时将变量导出**

     ```javascript
     // moduleA.js
     export const foo = 'foo'
     
     export const bar = () => {}
     ```

     导入

     ```javascript
     import {foo, bar} from './moduleA.js'
     
     console.log(foo)
     console.log(bar)
     ```

     也可以设置别名导入整个模块

     ```javascript
     import * as Module1 from './moduleA.js'
     
     console.log(Module1.foo)
     console.log(Module1.bar)
     ```

   - 方式二：**先声明，然后将标识符导出**

     ```javascript
     // moduleA.js
     const foo = 'foo'
     const bar = () => {}
     
     // 这种方式可以设置别名
     export {foo as myFoo, bar}
     ```

     导入，导入时也可以设置别名

     ```javascript
     import {myFoo, bar as myBar} from './moduleA.js'
     
     console.log(myFoo)
     console.log(myBar)
     ```

   2. 默认导出

      ```javascript
      // moduleA.js
      const foo = 'foo'
      
      export default foo
      ```

      导入

      ```javascript
      import foo from './moduleA.js'
      
      console.log(foo)
      ```




## 深拷贝

1. 使用JSON的stringify与parse

   > 局限性：无法处理 function、无法处理正则等等——只有当你的对象是一个严格的 JSON 对象时，可以顺利使用这个方法

   ```javascript
   let person = {
       name: 'wangkun',
       age: 12,
       children: [
           {
               name: 'chenghao',
               age: 1
           },
           {
               name: 'xuqi',
               age: 3
           }
       ]
   }
   
   const personString = JSON.stringify(person)
   const personCode = JSON.parse(personString)
   person.children = []
   console.log(personCode.children)
   ```

2. 递归

   ```javascript
   const person = {
       name: 'wangkun',
       age: 12,
       children: [
           {	
               name: 'chenghao',
               age: 1
           },
           {
               name: 'xuqi',
               age: 3
           }
       ]
   }
   
   function deepClone(obj) {
     // 如果是原始类型或者是null就直接返回对象
     if(typeof obj !== 'object' || obj == null) {
       return obj
     }
     
     // 判断是不是数组
   	const copy = Array.isArray(obj) ? [] : {}
     for(let key in obj) {
       if(obj.hasOwnProperty(key)) {
       	copy[key] = deepClone(obj[key])
       }
     }
     return copy
   }
   ```

## 















