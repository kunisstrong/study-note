# Vue3

## 选项式API

### 不知道的一些小技巧

- 样式穿透

  ```
  :deep(.el-input__wrapper) {
    background-color: #07172e;
  }
  
  ```

- 静态资源 + v-bind

  ```vue
  improt logo from "@/assets/logo.png"
  
  <img :src="logo" />
  ```

### 计算属性与监听器

- 计算属性适合多个值影响一个值；监听器适合一个值影响多个值
- 计算属性不支持异步程序；监听器支持异步程序；

### 类名与行内样式

- 可以使用动态绑定`v-bind:` + 字符串、数组、对象的方式设置样式

### 双向数据绑定

- 通过动态绑定与事件绑定实现

  ```html
  <imput type="text" :value="message" @input="message = $event.target.value">
  ```

- 输入框、多选框、单选框都可以双向数据绑定显示数据

###  生命周期钩子

- 在`create`钩子中发AJAX请求
- `beforeUpdate`：数据更新了，但是DOM没有更新；`updated`：数据和DOM都更新了
- `beforeUnmount`：数据和DOM都能访问到；`unmount`：数据能访问，DOM访问不了

### 组件

- 可以通过`app.component("MyComponent", {...})`创建组件
- `this.$attrs` 、`this.$ref`、自定义属性(父传子)、自定义事件(子传父)等都可以实现组件间通信

### 插槽

- 具名插槽

  ```vue
  <my-header>
  	<template #logo>
  		<h2>logo</h2>
  	</template>
  	
  	<template #list>
      <ul>
        <li>JavaScript</li>
        <li>Java</li>
        <li>Golang</li>
        <li>Rust</li>
      </ul>
  	</template>
  </my-header>
  
  
  <template>
  	<slot name="logo"/>
  	<slot name="list"/>
  </template>
  ```
  
- 作用域插槽

  ```vue
  <my-header>
  	<template #logo>
  		<h2>logo</h2>
  	</template>
  	
  	<template #list="{ list }">
      <ul>
        <li v-for="item in list"></li>
      </ul>
  	</template>
  
  </my-header>
  
  
  app.component("MyHeader", {
      data() {
          return {
              list: ["JavaScript", "Go", "Rust"]
          } 
      },
      template: `
        <header>
        <slot name="logo"/>
        <slot name="list" :list="list"/>
        </header>
      `
  })
  
  ```

### nextTick

> 将回调推迟到下一个DOM更新周期之后执行。在更改了一些数据以等待DOM更新后立即使用它

```vue
<template>
  <h2>Hello nextTick</h2>
  <p ref="message">{{ message }}</p>

</template>

<script>
export default {
  name: "nextTick",
  data() {
    return {
      message: "Hello Word"
    }
  },
  mounted() {
    setTimeout(() => {
      this.message = "Hello Vue"
      console.log(this.$refs.message.innerHTML)  // Hello Word
      this.$nextTick(() => {
        console.log(this.$refs.message.innerHTML)  // Hello Vue
      })
    }, 1000)
  },

  updated() {
    console.log(this.$refs.message.innerHTML)
  }
}
</script>

<style scoped>

</style>

```

### 自定义指令

**真是使用场景：**

前端根据后端传过来的权限，显示和隐藏组件

```vue
<template>
  <h2>自定义指令</h2>
  <button v-auth="'edit'">编辑</button>
</template>

<script>
export default {
  name: "02_自定义指令",
  data() {
    return {}
  },
  // 注册局部自定义指令
  directives: {
    auth: (el, binding) => {
      const auths = ["edit","add"]
      // const auths = ["add"]
      const res = auths.includes(binding.value)
      // 当有edit的权限，显示按钮；没有权限，隐藏按钮
      if (!res) {
        el.style.display = "none"
      }
    }
  }
}
</script>
```

**全局自定义指令：**

```js
import { createApp } from 'vue'
import './style.css'
import App from './components/02_自定义指令.vue'

const app = createApp(App)

app.directive("auth", (el, binding) => {
    // const auths = ["edit", "add"]
    const auths = ["add"]
    const res = auths.includes(binding.value)
    // 当有edit的权限，显示按钮；没有权限，隐藏按钮
    if (!res) {
        el.style.display = "none"
    }
})
app.mount('#app')

```

### transition动画

```vue
<template>
  <h2>transition</h2>
  <button @click="isShow = !isShow">点击切换状态</button>
  <!-- 先进后出 -->
  <transition name="fade" mode="out-in">
    <div v-if="isShow" class="box"></div>
    <div v-else class="box1"></div>
  </transition>
</template>

<script>
export default {
  name: "03_transition",
  data() {
    return {
      isShow: true
    }
  }
}
</script>

<style scoped>
.box {
  width: 200px;
  height: 150px;
  background-color: pink;
}

.box1 {
  width: 200px;
  height: 150px;
  background-color: blue;
}

.fade-enter-from {
  opacity: 1;
  transform: translateX(200px);
}

.fade-enter-to {
  opacity: 0;
  transform: translateX(0);
}

.fade-enter-active {
  transition: 2s;
}

.fade-leave-from {
  opacity: 1;
  transform: translateX(0);
}

.fade-leave-to {
  opacity: 0;
  transform: translateX(200px);
}

.fade-leave-active {
  transition: 1s;
}

</style>

```

### component组件与keep-alive组件缓存

> 渲染一个「元组件」为动态组件。依is的值，来决定哪个组件被渲染
>
> keep-alive 可以让动态组件保持状态，切出去再切回来，保持之前的状态

```vue
<template>
  <div>
    <h2>动态组件</h2>
    <button @click="nowCom = 'MyCom1'">com1</button>
    <button @click="nowCom = 'MyCom2'">com2</button>
    <button @click="nowCom = 'MyCom3'">com3</button>
    <keep-alive>
      <component :is="nowCom"></component>
    </keep-alive>
  </div>
</template>

<script>
import MyCom1 from '../components/MyCom1.vue'
import MyCom2 from '../components/MyCom2.vue'
import MyCom3 from '../components/MyCom3.vue'

export default {
  name: "04_动态组件",
  data() {
    return {
      nowCom: 'MyCom1'
    }
  },
  components: {
    MyCom1,
    MyCom2,
    MyCom3
  }
}
</script>

<style scoped>

</style>

```

### 异步组件与Suspense一起使用

> 异步组件(defineAsyncComponent)：在大型应用中，我们可能需要将应用分割成小一些的代码块，并且只在需要的时候才从服务器加载一个模块，按需加载
>
> Suspense：Vue的内置组件，在异步组件没有加载出来的时候   用来占位的

```vue
<template>
  <div>
    <h2>异步组件</h2>
    <button @click="nowCom = 'MyCom1'">com1</button>
    <button @click="nowCom = 'MyCom2'">com2</button>
    <button @click="nowCom = 'MyCom3'">com3</button>
    <keep-alive>
      <!-- 在异步组件没有加载出来的时候 来站位的 -->
      <suspense>
        <component :is="nowCom"></component>
        <template #fallback>loading</template>
      </suspense>
    </keep-alive>
  </div>
</template>

<script>
// import MyCom1 from '../components/MyCom1.vue'
// import MyCom2 from '../components/MyCom2.vue'
// import MyCom3 from '../components/MyCom3.vue'
import { defineAsyncComponent } from 'vue'

export default {
  name: "04_动态组件",
  data() {
    return {
      nowCom: 'MyCom1'
    }
  },
  components: {
    // 异步加载组件   按需加载
    MyCom1: defineAsyncComponent(() => import("../components/MyCom1.vue")),
    MyCom2: defineAsyncComponent(() => import("../components/MyCom2.vue")),
    MyCom3: defineAsyncComponent(() => import("../components/MyCom3.vue"))
  }
}
</script>
```

### 子孙组件通信

> Provide 与 Inject

```vue
<template>
  <h2>Provide</h2>
  <Middle></Middle>

</template>

<script>
import Middle from "./07_Middle.vue"

export default {
  name: "06_Provide",
  data() {
    return {
      count: 123
    }
  },
  provide() {
    return {
      message: "Message",
      count: this.count,
      getInfo(data) {
        console.log(data)
      }
    }
  },
  components: {
    Middle
  }
}
</script>

<style scoped>

</style>
```

```vue
<template>
  <h2>Inject {{ message }}{{ count }}</h2>
</template>

<script>
export default {
  name: "08_Inject",
  inject: ["message", "count", "getInfo"],
  mounted() {
    this.getInfo("Hello Inject")
  }
}
</script>

<style scoped>

</style>

```

### render函数

```vue
<script>
import { h } from "vue"
export default {
    render() {
        return h("h2", "hello render函数")
    }

}
</script>

<style lang="scss" scoped>

</style>
```

## CompositionAPI

### toRefs

> toRefs可以通过结构的方式将reactive响应式数据转换成ref响应式数据

```vue
<template>
    <h2>ref and reactive</h2>
    <button @click="count += 1">点击+1</button>
    {{ state.count }} {{ count }}
</template>

<script setup>
import { reactive, toRefs } from "vue"

let state = reactive({ count: 1 })
let { count } = toRefs(state)
</script>
```

### lifecycle

> 组合式API生命周期钩子可以重复调用

```vue
<template>
    <div ref="elem">{{ count }}</div>
</template>

<script setup>
import { ref, onMounted } from 'vue';

let count = ref(0)
const elem = ref()

onMounted(() => {
    console.log(count.value)
})
onMounted(() => {
    console.log(elem.value)
})
</script>
```

### 生命周期watchEffect与watch

> 为了根据响应式状态自动应用和重新应用副作用， 我们可以使用watchEffect函数。他立即执行传入的一个函数，同时响应式追踪其依赖，并在其依赖变更是重新运行该函数

1. 当watchEffect回调函数中使用到的响应式是数据发生改变，这个生命周期就会重新执行一遍
2. 可以通过stop停止监听

```vue
<template>
    <div>
        {{ count }}
        <p ref="ele">程浩</p>
    </div>
</template>

<script setup>
import { ref, watchEffect } from 'vue'

let count = ref(0)
let ele = ref()

const stop = watchEffect(() => {
    console.log(count.value)
})

setTimeout(() => {
    stop()
}, 1000)

setTimeout(() => {
    count.value = 1
}, 2000)
</script>

```

#### watch

```js
import { ref, watch } from 'vue'

const selected = ref(0);
watch(selected, ( newValue, oldValue ) => {
  triggerLog(newValue);
})
```

### 跨组件通信方案provide inject

注意不要在inject中修改响应式数据，所有逻辑代码在父组写，子组件只负责接收和调用

1. 可利用回调函数修改
2. 为了防止可设置成readonly

 **procide.vue**

```vue


<template>
    <h2>provide</h2>
    <Inject />

</template>

<script setup>
import { provide, ref, readonly } from 'vue'
import Inject from "./05_inject.vue"

let count = ref(0)
provide("count", readonly(count))
provide("changeCount", () => {
    count.value = 1
})

</script>
```

**inject.vue**

```vue
<template>
    <h2>inject</h2>
    {{ count }}
</template>

<script setup>
import { inject } from 'vue'

let count = inject("count") 
const changeCount = inject("changeCount")
setTimeout(() => { changeCount() }, 2000)

</script>
```

### 父子通信props emit

**Prop.vue**

父传子自定义属性、子传父自定义事件

```vue
<template>
    <h2>props</h2>
    <Emit :count="1" :message="'hello world'" @sendDate="getEmitDate" />
</template>

<script setup>
import Emit from "./07_emit.vue"

const getEmitDate = (data) => {
    console.log(data)
}
</script>
```

**Emit.vue**

```vue
<template>
    <h2>emit {{ count }} {{ message }}</h2>

</template>

<script setup>
import { defineProps, defineEmits, onMounted } from 'vue'

const state = defineProps({
    count: {
        type: Number
    },
    message: {
        type: String
    }
})

const emit = defineEmits(["sendDate"])
onMounted(() => {
    emit("sendDate", "Emit传来的数据")
})
</script>
```

## Vue-Router

### 嵌套路由

子路由如果前面不加`/`，路由跳转的时候就需要写全路径，如：`/about/foo`，推荐 

子路由如果前面加`/`，路由跳转的时候就只需要写，如：`/foo`

### 动态路由

> 也就是路由传参

```
children: {
	{
		path: "bar/:id"
	}
}
```

点击跳转路由时

```
const router = router.push("/anout/bai/123")
```

在组件中接收

```
const route = route()

console.log(route.params.id)
```

### 命名路由

可能通过给路由命名，然后通过 name 进行路由跳转

```vue
<template>
    <h2>About</h2>
    <button @click="toSub1">Sub1</button> |
    <button @click="toSub2">Sub2</button>
    <router-view></router-view>
</template>

<script setup>
import { useRouter } from 'vue-router'
const router = useRouter()

const toSub1 = () => {
    router.push({
        name: 'sub1',
        // 可以携带路由参数
        params: {
            id: 123
        }
    }
    )
    // router.push("/about/sub1")
} 

const toSub2 = () => {
    router.push({ name: 'sub2' })
    // router.push("/about/sub2")
}
</script>
```

### 路由传参

1. query方式(显示)：`route.query` : `const router = router.push("/anout/bai/?123")`
2. params方式(显示)：route.params: `const router = router.push("/anout/bai/123")`
3. params方式(隐式)：route.params：在写路由表的时候不写动态路由，如：`path: "bar/:id"`改写成`path: "bar`

### route与router对象

 route：获取路由信息

router：调用路由方法

## 路由守卫

**路由表中配置**

```js
router.beforeEach((to, from, next) => {
	if(to.meta.auth){
		// 如果有权限跳转到想去的页面
			next()
	}else {
		//  没有权限强制跳转到login页面
		next("/login")
	}
})
```

**组件内的守卫**

```js
router.beforeRouteEach((to, from, next) => {
	if(to.meta.auth){
		// 如果有权限跳转到想去的页面
			next()
	}else {
		//  没有权限强制跳转到login页面
		next("/login")
	}
})
```

# TypeScript

## 初体验

`npm i typescript -g` ：`tsc xxx.ts -w`   将ts文件转换成js文件

`tsc --init`：生成tsconfig.json

## TS特有的数据类型

1. never：用不存在的值

2. any：任意类型

   ```ts
   let a: any = "string"
   a = 123
   a.map(() => {  // 这句不会报错
       
   })
   ```

3. unknow：any类型对应的安全类型

   ```ts
   let a: unknown = "string"
   a = 123
   a.map(() => {  //这句会报错
       
   })
   ```


## 断言与非空断言

```ts
// 断言
const a = undefined
console.log((a as []).length)


// 非空断言    ！
const a: number | undefined = undefined
a!.length

```

## 数组与元祖

### 数组

```
1.  const  arr1: (number | string)[] = {123, "string"}

2.  const arr2: Array<number> = [1, 2, 3 ]
```

### 元祖

元祖类型允许表示一个已知元素数量类型和类型的数组，各元素的类型不必相同

``` 
const arr3: [number, string] = [1, "hello"]

arr3[2] = 2 // eror
```

## 索引签名

> 后端传过来的数据不确定是什么，我们定义类型的时候就可以使用索引签名

### 对象索引签名

对象索引用string类型

```ts
type A = {
    username: string
    age?: number
    [index: string]: any
}

const a: A = {
    username: "张三",
    age: 12,
    sex: "男",
    other: 1
}
```

### 数组索引签名

数组索引用number类型

```
type A = {
    [index: number]: any
}

const a: A = [1, 2, 3, "zhangsan"]
```

### 小技巧

可以使用断言让对象初始值为空，并且限制对象的类型

```
type A = {
    username: string
    age?: number
}
// const a: A = {}  error  
const a = {} as A

a.sex = "男"  // error
```

## 函数重载与可调用注解

### 函数重载

> 函数重载是只函数约束传入不用的参数，返回不同类型的数据，而且可以清晰的知道传入不同的参数得到不同的结果

```ts
function foo(n: number, m: string): any
function foo(n: number, m: number): any
function foo(n: string|number, number|string) {

}

foo(1,2)
foo("张三", "李四")
foo(1, "张三")  // error
```

### 可调用注解

```ts
// 定义函数类型
type A = () => void;

// 可调用注解定义函数类型，可以针对函数重载进行类型注解
type A = {
  () => void
}

const a: A = () => {}
```

函数重载与调用注解配合使用

```ts
type A = {
	(n: number, m: number): any
	(n: string, m: string): any
}

function foo(n: number, m: number): any
function foo(n: string, m: string): any
function foo(n: number|string, m: number|string) {

}

const a: A = foo
===================================================
function foo(n: number, m: number): any  
function foo(n: number|string, m: number|string) {

}

const a: A = foo; // error
```

## 枚举类型与const枚举

> 美剧是组织手机有关联集合的一种方式，是代码更加易于阅读

**特点：  **

1. 自动累加
2. 反向映射
3. 类型方式
4. const枚举

const枚举性能更好

```ts
const enum Roles {
	.....
}
```

## 接口与类型别名的区别

> 接口：多个类型需要合并、类型之间需要继承用接口
>
> type：定义基本类型、映射类型、条件类型

1. 对象类型
2. 接口合并
3. 接口继承
4. 映射类型

接口只能用在对象类型，接口具备合并功能，接口具备继承能力

类型别名具有映射类型的能力

```ts
// 1. 接口只能定义对象类型
type A = string
// interface A string  error


// 2. 接口可以合并，类型别名不可以
interface B {
    username: string
}

interface B {
    age: number
}

// type C = {
//     username: string
// }
// type C = {  // error
//     age: number
// }


// 3. 接口可以继承
interface D {
    username: string
}
interface E extends D {
    age: number
}

const obj1: E = {
    username: "张三",
    age: 12
}

// 4. 类型别名的映射类型
type F = {
    [P in "username" | "age"]: string
}

// interface G {   error
//     [P in "username" | "age"]: string
// }
```

## 字面量类型与keyof关键字

```
interface A {
    username: '张三'
    age: 123
}

// 通过keyof取到  接口A的key
const a: keyof A = 'username'
```

## 类型保护与自定义类型

### 类型保护

> 缩小类型范围

1. typeof：只能检查a
2. instanceof
3. in
4. 字面量类型

#### in

```ts
function foo(a: { username: string } | { age: number }) {
    if ("username" in a) {
        a.username = "xiaoming"
    }
}
```

#### instanceof

```ts
// instanceof
class Foo {
    username = "zhangsan"
}

class Bar {
    age: 12
}

// instanceof 左边是不是有右边的实例   或者  右边的原型是否出现在左边的原型链上
function baz(a: Foo | Bar) {
    if (a instanceof Foo) {
        a.username
    }
}
```

#### 自定义类型保护

> 使用is类型为此

```
function isString(): n is string{
	return typeof n === 'string'
}

function foo(n: string|number) {
	if( isString(n) ){
		n.length
	}
}
```

## 泛型与泛型常见操作

> 泛型是指在定义函数、接口或者类时，未指定其参数类型，只有在运行时传入才能确定

```ts
// 基础类型
type A<T = string> = T
const a: A = "zhangsan"
const b: A<number> = 1

// 数组类型
type MyArray<T> = T[]
const arr: MyArray<number> = [1, 2, 3]
const arr1: number[] = [1, 2, 3]
```

**函数与接口结合**

```ts
interface A<T> {
    (n: T): void
    default?: T
}

const foo: A<string> = (n) => { }

// foo(123) error
// foo.default = 123 error
foo("张三")
foo.default = "李四"
```

### 泛型约束

**只能是字符串类型**

```ts
// 泛型约束
type FunType = string
function bar<T extends FunType>(n: T) { }

// bar(123) error
bar("张三")
```

**参数必须拥有length属性**

```ts
type FunType = {
    length: number
}
function bar<T extends FunType>(n: T) { }

// bar(123) error
bar("张三")

```

## 类型兼容性

> 类型兼容性用于确定一个类型是否能赋值给其他类型

```
let obj1 = { username: "zhangsan" }
let obj2 = { username: "lisi", age: 12 }

// obj1 = obj2
obj2 = obj1 // error
// 大的能赋值给小的
// 小的不能赋值给大的
```

## 映射类型与内置工具类

### 映射类型

> 将已知类型的每个属性都变为可选的或者只读的
>
> 只能应用与类型别名。接口不支持

```ts
type A = {
    username: string
    age: number
}

type B<T> = {
    [P in keyof T]: T[P]
}

type C = B<A>  // 相当于下面的代码
// type C = {
//     username: string
//     age: number
// }
```

### 内置工具类

> TypeScript内置了很多映射类型的工具

**Readonly**

```ts
type A = {
    username: string
    age: number
}

type B = Readonly<A>
```

## 条件类型与infer关键字

### 条件类型

> 在初始状态并不直接确定具体类型，而是通过一定的类型运算得到最终的变量类型

```
type A = string
type B = number | string
type C = A extends B ? {} : []  // {}
```

**内置工具类**

```ts
type A = Exclude<string|number|boolean, string>
```

# Vue3与TypeScript配合使用

## d.ts声明文件与declare关键字

> 在TypeScript中以`.d.ts`为后缀的文件，我们称之为TypeSCript声明文件。它的主要作用是描述JavaScript模块内所有导出接口的类型信息
>
> 使js文件能够在ts文件进行使用

```ts 
// demo.d.ts
export declare function foo(n: number): void
```

## @types和DefinitelyTyped仓库

> Definitely是一个高质量的TypeScript类型定义的仓库。通过@types方式来安装常见的第三方JavaScript库的声明适配模块

有一些第三方库没有`.d.ts`文件。可以通过`npm i --save-dev @types/jquery`来自动生成

## lib.d.ts与global.d.ts

> 当你安装TypeScript时，会顺带安装一个lib.d.ts声明文件。这个文件包含JavaScript运行时以及DOM中存在各种常见的环境声明

### global.d.ts

在global.d.ts文件中定义的类型，在全局都可以使用

## 详解tsconfig.json

1. compilerOptions:{}——编译选项
2. files:[]——包含在程序中的文件的允许列表
3. extends:" "——继承的另一个配置文件
4. include:[] —— 指定的进行编译解析
5. exclude:[] ——指定的不进行编译解析
6. references:[]——项目引用，提高性能

### 初始化默认添加的配置

![image-20230213143606169](/Users/wangkun/Library/Application Support/typora-user-images/image-20230213143606169.png)

## 组合式API与TS

### 与ref 配合

```ts
let count = ref<number|string>(0)
count.value = "hello"

interface List {
	username: string
	age: number
}

let list = ref<List[]>([])  

list.value.push({
	username: "world",
	age: 12
})
```

### 与defineProps配合

```ts
// 1.
defineProps({
	count: [Number, String]
})

// 2.
interface Props {
	count: number|string
	list: {username: string; age: number}[]
}
defineProps<Props>()
```

### 与defineEmits配合

```ts
interface Emits {
	(e: 'getData', payload: string): void
}

let emit = defineEmits<Emits>()
emit("getData", "hello")
```

## Vue_Router与TS配合

### routes

```ts
import type { RouteReCordRaw } from 'vue-router'

const routes: Array<RouteReCordRaw> = []
```

### routes中的meta

```ts
declare module 'vue-router' {
	interface RouteMeta {
		isAdmin?: boolean
		requiresAuth: boolean
	}
}
```

## Element Plus与TS















