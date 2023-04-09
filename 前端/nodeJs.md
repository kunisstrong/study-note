# nodeJs

## 简介

浏览器是javascript前端运行环境

node是javascript后端运行环境

## 网络理论

#### 知名端口

1. http：80    
2. https：443
3. mysql：3306
4. ftp：20 21
5. mongodb：27017

## http

```js
// 引入http
const http = require("http")
// 创建实例
const server = http.createServer()
// 创建服务
server.on("request", (req, res) => {  
  const str = `你请求的URL地址是${req.url}, 请求的method类型是${res.method}`
  res.setHeader("Content-type", "text/html; charset=utf-8")
  res.end(str)
})
// 启动服务
server.listen(80, () => {
  console.log("server running at http://127.0.0.1")
})
```

## module

#### commonJS

1. 每一个模块都是一个module对象

   ```
   console.log(module)
   //Module {
   //   id: '.',
   //   path: '/Users/wangkun/Desktop/test/node/module',
   //   exports: {},
   //   filename: '/Users/wangkun/Desktop/test/node/module/01-first-moudle.js',
   //   loaded: false,
   //   children: [],
   //   paths: [
   //     '/Users/wangkun/Desktop/test/node/module/node_modules',
   //     '/Users/wangkun/Desktop/test/node/node_modules',
   //     '/Users/wangkun/Desktop/test/node_modules',
   //     '/Users/wangkun/Desktop/node_modules',
   //     '/Users/wangkun/node_modules',
   //     '/Users/node_modules',
   //     '/node_modules'
   //   ]
   // }
   
   ```

2. 使用`require`导入模块时，永远是以`module.exports`对象为准

3. 模块化规范
   - 每个模块内部，module变量代表当前模块
   - module变量是一个对象，他的exports属性(即module.exports)是对外的接口
   - 加载某个模块，其实是加载该模块的module.exports属性。require()方法用于加载模块

#### 使用

```js
// exports.js
module.exports = {
	name:'孙悟空',
	age: 12,
	gender: '男'
}
```

```js
const {name，age} = require('./export')
```

#### ES6模块化

1. 单个向外暴露

   ```
   // 第一种方式
   const moduleA = {}
   export default moduleB
   import moduleA = '....'
   // 第二种
   export const moduleB = {}
   
   import {moduleB} from '...'
   ```

2. 向外暴露多个

   ```
   const moduleA = {}
   const moduleB = {}
   export default {
   	moduleA,
   	moduleB
   }
   ```


## npm与包

node_modules文件夹用来保存所有已安装到项目的包

Package-lock.json配置文件用来记录node_modules目录下每一个包的下载信息，例如包的名字，版本号，下载地址

#### 包版本规范

包的版本号以“点分十进制”形式进行定义，总共有三位数字，例如`2.24.0`

第一位数字：大版本

第二位数字：功能版本

第三位数字：BUG修复版本

#### dependencies

专门记录使用`npm init`命令安装了哪些包

#### devDependencies

一些包只在开发中能用到，项目上线的时候不回用到，要按在devDependencies节点中

`npm i 包名 -D`

完整写法

`npm install 包名 --save-dev`

#### npm包下载

` npm config get registry`：查看当前的下包地址

`npm config set registry= http:....`：设置包下载镜像

#### npm init与npm install

1. `npm init`：生成package.json文件
2. `npm install`：生成node_modules文件夹，并下载包

## 身份认证

##### cookie

1. 有时效，有时间限制，会过期
2. 服务器发给客户端的
3. 4kb
4. 没有安全性
5. 客户端给服务器发送请求会自动携带cookie，在请求头中

##### jwt（token）

1. 由三部分组成：Header、Payload、Signature
2. Payload是真正的用户信息，是用户信息经过加密之后生成的字符串
3. Header和Signture是安全性相关部分，为了保护Token的安全性

## koa

#### 基础使用

```js
// 引入koa
const Koa = require('koa')
// 创建koa实例
const app = new Koa()

app.use(async (ctx, next) => {
    if (ctx.url === '/favicon.ico') return
    console.log(11111)
    const myToken = await next()
    console.log(444444)
    ctx.body = myToken
})

app.use(async (ctx, next) => {
    console.log(222222)
    // 异步
    await delay(1000)
    console.log(333333)
    return 'ashdjaghsdjgasjhdgahs'
})

function delay(time) {
    return new Promise((resolve, reject) => {
        setTimeout(resolve, time)
    })
}
// 开启3000端口
app.listen(3000, () => {
    console.log('3000端口以启用')
})
```

#### 路由

1. `npm i koa-router`下载包

2. 使用

   ```
   const Koa = require('koa')
   const Router = require('koa-router')
   const app = new Koa()
   const router = new Router()
   
   router.get('/list', (ctx, next) => {
       // get请求
       ctx.body = '获取get数据'
   }).post('/list', (ctx, next) => {
       // post请求
       ctx.body = [1, 2, 3]
   }).del('/list/:id', (ctx, next) => {
       // delete请求
       // 获取前端传的参数
       console.log(ctx.params)
       ctx.body = {
           ok: 1,
           info: 'del list success'
       }
   })
   // 将路由挂载中间件
   app.use(router.routes()).use(router.allowedMethods)
   app.listen(3000, () => {
       console.log('3000端口以启用')
   })
   ```

##### 实际开发

1. 将路由单独放一个文件夹，别写入逻辑

2. 统一归到index.js中

   ```
   const Router = require('koa-router')
   const listRouter = require('./list')
   const homeRouter = require('./home')
   const router = new Router()
   
   router.prefix('/api')
   // 注册路由级别组件
   router.use('/list', listRouter.routes(), listRouter.allowedMethods())
   router.use('/home', homeRouter.routes(), homeRouter.allowedMethods())
   // 重定向
   router.redirect('/', '/home')
   module.exports = router
   
   ```

3. 在总index.js中引用

   ```
   const Koa = require('koa')
   const app = new Koa()
   const router = require('./routes')
   
   // 应用级中间件
   app.use(router.routes()).use(router.allowedMethods)
   app.listen(3000, () => {
       console.log('3000端口以启用')
   })
   ```

#### 日志

1. `npm i koa-logger@3.2.1`

2. 引入使用

   ```
   const logger = require('koa-logger')
   const Koa = require('koa')
   const app = new Koa()
   aoo.use(logger())
   ```

#### 处理错误

1. `npm i koa-onerror@4.3.0`

2. 引入使用

   ```
   const Koa = require('koa')
   const onerror = require('koa-onerror')
   
   const app = new Koa()
   onerror(app)
   app.use((ctx,next)=>{
   	const err = new Error('你没有权限访问')
   	err.status = 401
   	throw err
   })
   ```

#### get两种传参方式

1. params

   ```js
   router.get('/user/del/:id', ctx => {
   	console.log(ctx.params.id)
   })	
   ```

   前端：

   ```js
   http://127.0.0.1:3000/user/del/1	
   ```

2. query

   ```
   router.get('/user/add',ctx => {
   	console.log(ctx.query)
   })
   ```

   前端：

   ```
   http://127.0.0.1:3000/user/add?name='chenghao&age='13'
   ```

#### Post传参

1. `npm i koa-bodyparser@4.3.0`

2. 配置

   ```
   const bodyParser = require('koa-bodyparser')
   
   app.use(bodyParser())
   ```

3. 使用

   ```
   router.post('/user/add',ctx => {
   	console.log(ctx.request.body)
   })
   ```

前端：

1. body
2. 选择对应方式

## koa项目

koa2 + mysql + typescript

#### 注意点

1. 内部模块和通过npm下载的依赖一定要用require(CommonJS)
2. 获取post请求的请求体，要用`ctx.request.body`

#### 初始化项目

1. `npm init`创建package.json

2. 安装依赖

   @types的含义

   当我们用 npm 等包管理工具安装第三方包的时候，有些包并不是 TypeScript 编写的，自然也不会导出 TypeScript 声明文件。这种情况下，如果我们在 TypeScript 项目中引入了这种包，则会编译报错(没有设置 allowJS)。举个例子，当我们通过`npm install jquery --save` 安装 jquery 包并引用的时候，TypeScript 会报错。

   - `npm add koa @types/koa` ：最基础的依赖

   - `npm add koa-router @types/koa-router`： koa路由的依赖

   - `npm add ts-node typescript -D`： ts node依赖

     在package.json => scripts => `"start": "ts-node index.ts"`

   - `npm i nodemon -g` ：全局安装nodemon，热启动
   
   - `npm add jest @types/jest ts-jest -D`：单元测试
   
   - `npm add supertest @types/supertest -D`：网络测试
   
   - `npm add dotenv @types/dotenv`：加载配置文件
   
   - `npm add log4js @types/log4js`：日志文件
   
   - 数据库(sequelize)
   
     - `npm add @types/node @types/validator -D`：sequelize
     - `npm add sequelize reflect-metadata sequelize-typescript` ：操作数据库
     - `npm add mysql2`：mysql驱动
     - `npm add jsonwebtoken @types/jsonwebtoken`：jwt认证
     - `npm add async-validator`：表单校验
     - `npm add koa-body`：koa-body  获取post请求体数据要用的依赖

#### 文件相关含义

- app：
  - controller： 书写响应前端的逻辑，路由的下一层
  - db：数据库配置文件
  - logger：日志配置文件
  - middleware：中间件相关文件
  - model：数据库实体，一个ts文件对应一个表
  - router：路由级中间件
  - service：根据实体类查找数据库
- .env：放一些配置信息，如数据库端口号，用户，密码，服务器端口号等
- logs：存放log日志文件
- index.ts：程序的入口文件
- tests：存放测试文件

JDK与ECMAScript是一类	

JVM与node.js是一类

**大概流程**：index.ts启动服务 ==>  去db文件读取配置文件连接数据库 ==> 服务启动成功 ==> 前端发来请求 ==> 遇到中间件就前往中间件执行逻辑 ==> 进入路由级中间件 ==> 进入controller执行相关逻辑 ==>  进入service根据实体类查找数据库返回数据 ==> 原路返回
