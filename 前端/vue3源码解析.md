# vue3设计与实现

## 插槽

### 默认插槽

App.vue

```vue
<HelloWorld>
  <div>我是插槽的自定义的内容</div>
</HelloWorld>
```

子组件

```vue
<div class="box">
  <div>page</div>
  <slot>我是默认的内容</slot>
</div>
```

### 具名插槽

App.vue

```

```

### 作用域插槽

## 框架设计概述

作为学习者，我们在学习框架的时候，要先从全局的角度对框架的设计拥有清晰的认知，否则很容易被细节困住，看不清全貌。

所以这篇文章作为学习vue3源码细节的前篇，带大家对vue3有个全面大体的认识

### 声明式与命令式

从范式的角度来看，我们的框架应该设计成命令式还是声明式的、要设计成纯运行时还是纯编译型的，甚至是运行时+编译时的呢

**我们先举一个现实中的例子：**

张三的妈妈让张三去买酱油

张三需要这么做：

1. 拿起钱
2. 打开门
3. 到商店
4. 拿钱买酱油
5. 回到家

这里张三妈妈的行为就是声明式，张三就是命令式

**我们再用一个编程的例子来理解声明式与命令式，需求：**

```
1. 获取 id 为 app 的 div 标签
2. 它的文本内容为 hello world
3. 为其绑定点击事件
4. 当点击时弹出提示：ok
```

命令式：看重过程，指示编译器每一步该怎么做，最终实现结果

```javascript
const div = document.querySelector('#app') // 获取 div
div.innerText = 'hello world' // 设置文本内容
div.addEventListener('click', () => { alert('ok') }) // 绑定点击事件
```

声明式：看重结果，将结果告诉编译器，编译器帮你完成过程

```vue
<div @click="() => alert('ok')">hello world</div>
```

两者差异：对于用户来说，声明式更快捷方便，可维护性更强，命令式不方便快捷，但性能更好

**举例：**

假设现在我们要将 div 标签的文本内容修改为 hello vue3

**命令式：**

```javascript
div.textContent = 'hello vue3' // 直接修改需要改动的地方
```

**声明式：**

```javascript
01 <!-- 之前： -->
02 <div @click="() => alert('ok')">hello world</div>
03 <!-- 之后： -->
04 <div @click="() => alert('ok')">hello vue3</div>  // 需要全部重新渲染
```

这里我们可以发现两者主要的区别是后者无论修改了什么，整条语句都会重新执行

所以为了实现最优的更新性能，声明式需要找到前后的差异并只更新变化的地方，但是最终完成这次更新的代码仍然是：

```javascript
01 div.textContent = 'hello vue3' // 直接修改需要改动的地方
```

这也是vue3相较于vue2做出的调整—diff算法的优化

### 运行时与编译时

**我们用一个例子来理解声明式与命令式**

#### 纯运行时：

我们设计一个框架，它提供一个 Render 函数，用户可以为该函数提供一个树型结构的数据对象，然后 Render 函数会根据该对象递归地将数据渲染成 DOM 元素。我们规定树型结构的数据对象如下： 

```javascript
const obj = {
  tag: 'div',
  children: [
    { tag: 'span', children: 'hello world' }
  ]
}
```

**Render函数：**

```javascript
function Render(obj, root) {
  const el = document.createElement(obj.tag)
  if (typeof obj.children === 'string') {
    const text = document.createTextNode(obj.children)
    el.appendChild(text)
  } else if (obj.children) {
    // 数组，递归调用 Render，使用 el 作为 root 参数
    obj.children.forEach((child) => Render(child, el))
  }

  // 将元素添加到 root
  root.appendChild(el)
}
```

**用户可以这样使用：**

```javascript
const obj = {
  tag: 'div',
  children: [
    { tag: 'span', children: 'hello world' }
  ]
}
// 渲染到 body 下
Render(obj, document.body)
```

**这就是纯运行时框架**：让用户生成树型结构对象，框架提供reader函数将对象渲染成页面，最重要的是等代码执行的时候，也就是render函数执行的时候才会生成html标签。

  **缺点**：是html对象需要用户提供，并且不能进行差别更新

#### 运行时 + 编译时：

于是有一天，你的用户抱怨说：“手写树型结构的数据对象太麻烦了，而且不直观，能不能支持用类似于HTML 标签的方式描述树型结构的数据对象

为此，你编写了一个叫作 Compiler 的程序，它的作用就是把 HTML 字符串编译成树型结构的数据对象

**用户可以这么用：**

```javascript
const html = `
<div>
  <span>hello world</span>
</div>
`
// 调用 Compiler 编译得到树型结构的数据对象
const obj = Compiler(html)
// 再调用 Render 进行渲染
Render(obj, document.body)
```

**这就是运行时+编译时：**框架提供Compiler函数「将html标签生成html对象」和Render对象，这样我们可以根据html标签的不同，进行差别更新html对象，不需要全部更新。用户可以直接提供数据对象从而无须编译；又支持编译时，用户可以提供 HTML 字符串，我们将其编译为数据对象后再交给运行时处理。

Vue采用的就是这种方式

#### 纯编译时：

简单来说就是将HTML字符串直接编译成命令时代码

![image-20221214101544835](/Users/wangkun/Library/Application Support/typora-user-images/image-20221214101544835.png)

优缺点：直接编译成可执行的 JavaScript 代码，因此性能会更好，但有损灵活性，即用户提供的内容必须编译后才能用

### Tree Shaking

> 消除那些永远不会执行的代码

vue.js对于那些永远不会用到的代码，在打包的时候会将它清除，减少包的体积

### 编译器与渲染器

#### 编译器

>  作用：将模版(template标签)编译成虚拟DOM，交给渲染器，在script标签中，所有在vue中使用模版或者虚拟DOM对象编写页面最后的结果是一样

**举例：**

```vue
<template>
  <div @click="handler">
    click me
  </div>
</template>

<script>
export default {
  data() {/* ... */},
  methods: {
    handler: () => {/* ... */}
  }
}
</script>
```

其中` <template>` 标签里的内容就是模板内容，编译器会把模板内容编译成渲染函数并添加到 `<script> `标签块的组件对象上，所以最终在浏览器里运行的代码就是：

```javascript
01 export default {
02   data() {/* ... */},
03   methods: {
04     handler: () => {/* ... */}
05   },
06   render() {
07     return h('div', { onClick: handler }, 'click me')
08   }
09 }
```

如果你学过`react`，你会觉得编译后的代码很react很相似。

所以，无论是使用模板还是直接手写渲染函数，对于一个组件来说，它要渲染的内容最终都是通过渲染函数产生的，然后渲染器再把渲染函数返回的虚拟 DOM 渲染为真实 DOM

#### 渲染器

> 渲染器(reader函数)将虚拟DOM渲染成真实DOM

```javascript
01 function renderer(vnode, container) {
02   // 使用 vnode.tag 作为标签名称创建 DOM 元素
03   const el = document.createElement(vnode.tag)
04   // 遍历 vnode.props，将属性、事件添加到 DOM 元素
05   for (const key in vnode.props) {
06     if (/^on/.test(key)) {
07       // 如果 key 以 on 开头，说明它是事件
08       el.addEventListener(
09         key.substr(2).toLowerCase(), // 事件名称 onClick ---> click
10         vnode.props[key] // 事件处理函数
11       )
12     }
13   }
14
15   // 处理 children
16   if (typeof vnode.children === 'string') {
17     // 如果 children 是字符串，说明它是元素的文本子节点
18     el.appendChild(document.createTextNode(vnode.children))
19   } else if (Array.isArray(vnode.children)) {
20     // 递归地调用 renderer 函数渲染子节点，使用当前元素 el 作为挂载点
21     vnode.children.forEach(child => renderer(child, el))
22   }
23
24   // 将元素添加到挂载点下
25   container.appendChild(el)
26 }
```

编译器和渲染器本质都是函数

**如前所述，组件的实现依赖于渲染器，模板的编译依赖于编译器**

### 总结

1. 声明式：Vue.js 是一个声明式的框架，好处在于，它直接描述结果，用户不需要关注过程。vue.js同样支持使用虚拟DOM来描述UI。
   1. 运行时+编译时：vue.js采  用 运行时+编译时的方式 来降低用户的心智负担，并且保证框架的性能
2. TreeShaking： vue.js对于那些永远不会用到的代码，在打包的时候会将它清除，减少包的体积
3. 编译器：vue.js的模版会被编译器编译成渲染函数
4. 渲染器：将虚拟 DOM 对象渲染为真实 DOM 元素

## 响应系统

### 简介proxy

vue3使用es6新出的api，`Proxy`实现响应式

proxy是构造函数，接收两个参数，一个是目标对象，一个是最重要的捕捉器，他是实现响应式的关键，他可以在对目标对象进行某种操作的时候拦截，进而做一些操作，比如读取get操作，设定值set操作

```javascript
const target = {
	foo: 'bar'
}

const handler = {
	get() {
		return 'handler override'
	}
}

const proxy = new Proxy(target, handler)

console.log(target.foo)  // bar
console.log(proxy.foo)  // handler override
```

上面这段代码我们用`proxy`对`target`进行代理，并重写`get`方法，目的是在我们通过代理对象读取目标对象的值时，可以自定义返回值

**对于`Proxy`更详细信息可以看红宝书或者MDN相关章节**

### 副作用函数

副作用函数：指会产生副作用的函数

```
// 全局变量
let val = 1

function effect() {
 val = 2 // 修改全局变量，产生副作用
}
```

副作用函数很好理解，当调用了一个函数，这个函数会对其他变量造成影响，就可以称它为副作用函数。这个effect函数，调用它时，他会改变全局变量val的值，他就是一个副作用函数

### 响应式数据

举个例子

```javascript
const obj = {
  text: 'hello word'
}

function effect(){
		docment.body.innerText = obj.text
}

obj.text = 'hello vue3'
```

如上面的代码所示，副作用函数 effect 会设置 body 元素的 innerText 属性，其值为 obj.text，如果 `obj.text `的值发生变化时，副作用函数自动重新执行，那么对象 obj 就是响应式数据

### 响应式数据基本实现

```javascript
const obj = {
  text: 'hello word'
}

function effect(){
		docment.body.innerText = obj.text
}

obj.text = 'hello vue3'
```

我们希望对`obj.text`赋值之后，`docment.body`的内容会更新，也就是在赋值之后紧接着调用一次effect函数，这时我们就需要proxy构造函数来帮我们了

```javascript
// 存储副作用函数的桶
const bucket = new Set()

// 原始数据
const data = { text: 'hello world' }
// 对原始数据的代理
const obj = new Proxy(data, {
 // 拦截读取操作
 get(target, key) {
   // 将副作用函数 effect 添加到存储副作用函数的桶中
   bucket.add(effect)
   // 返回属性值
   return target[key]
 },
 // 拦截设置操作
 set(target, key, newVal) {
   // 设置属性值
   target[key] = newVal
   // 把副作用函数从桶里取出并执行
   bucket.forEach(fn => fn())
   // 返回 true 代表设置操作成功
   return true
 }
})
```

我们试验一下

```javascript
// 副作用函数
function effect() {
    document.body.innerText = obj.text
}
// 执行副作用函数，出发读取
effect()
// 2秒后修改响应式数据
setTimeout(() => {
    obj.text = 'hello vue3'
}, 2000)
```

在浏览器上执行这两段代码，就会得到期待的效果

但是目前的实现还有很多缺陷，接下来我们实现一个完善的响应系统

### 完善的响应系统

#### 存在的问题

1. 我们硬编码了副作用函数的名字（effect），导致一旦副作用函数的名字不叫 effect，那么这段代码就不能正确地工作了

#### 解决问题

##### 硬编码副作用函数

我们可以提供一个注册副作用函数的解决

```javascript
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>响应式原理</title>
    <style>
    </style>
</head>
<body>
<script>
    // 设置装副作用函数的桶
    const bucket = new Set()

    // 设置原始数据
    let data = {text: 'hello word'}
    
    // 设置全局变量存储被注册的副作用函数
    let activeEffect
    
    // 对原始数据进行代理
    const obj = new Proxy(data, {
        // 读取时
        get(target, key) {
          	// 将activeEffect中存储的注册函数收集到桶中
            if (activeEffect) {  // 新增
                bucket.add(activeEffect)  // 新增
            }
            return target[key]
        },
        set(target, key, value) {
            target[key] = value
            bucket.forEach(item => item())
            return true
        }
    })


    function effect(fn) {
        activeEffect = fn
        fn()
    }

    effect(
      // 一个匿名的副作用函数
      () => {
        document.body.innerText = obj.text
    })

    setTimeout(() => {
        obj.text = '我的值改变了'
    }, 2000)
</script>
</body>
</html>
```

我们使用一个匿名的副作用函数作为 effect 函数的参数。当 effect 函数执行时，首先会把匿名的副作用函数 fn 赋值给全局变量 activeEffect。接着执行被注册的匿名副作用函数 fn，这将会触发响应式数据 obj.text 的读取操作，进而触发代理对象 Proxy 的 get 拦截函数

这样我们就通过在`调用时将副作用函数传入`解决了副作用函数硬编码的问题

### 总结

一个响应式数据最基本的实现依赖于对“读取”和“设置”操作的拦截，从而在副作用函数与响应式数据之间建立联系。当“读取”操作发生时，我们将当前执行的副作用函数存储到“桶”中；当“设置”操作发生时，再将副作用函数从“桶”里取出并执行。这就是响应系统的根本实现原理。

## 渲染器

> 渲染器(reader函数)将虚拟DOM渲染成真实DOM

```javascript
function render(domString, container) {
	container.innerHTML = domString
}
```

这就是一个最简单的渲染器

我们可以这么使用

```javascript
render(`<h1>最简单的渲染器</h1>`,document.getElementById('app'))
```

如果页面上有id为app的DOM元素，那么对应的内容就会插入到该DOM元素上

将响应式数据与渲染器融合

```javascript
let count = ref(1)

effect(() => {
	render(`<h1>{{ count }}</h1>`,document.getElementById('app'))
})
```

