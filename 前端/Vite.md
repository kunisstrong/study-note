# 什么是构建工具

很难用一句话来描述什么是构建工具
构建工具都做了哪些工作

1. 模块化开发支持: 支持直接从node_modules里引入代码 + 多种模块化支持, 
2. 处理代码兼容性: 比如babel语法降级, less,ts 语法转换(**不是构建工具做的,
   构建工具将这些语法对应的处理工具集成进来自动化处理**)
3. 提高项目性能: 压缩文件, **代码分割**
4. 优化开发体验:

- 构建工具会帮你自动监听文件的变化, 当文件变化以后自动帮你调用对应的集成工具进行重新打包, 然后再浏览器重新运行（整个过程叫做热更新,
  hot replacement
- 开发服务器: 跨域的问题, 用react-cli create-react-element vue-cli 解决跨域的问题

> 打包: 将我们写的浏览器不认识的代码, 交给构建工具进行编译处理的过程就叫做打包, 打包完成以后会给我们一个浏览器可以认识的文件

市面上常见的构建工具

- webpack
- vite
- parcel
- esbuild
- rollup
- grunt
- gulp

# 前置知识

**我们敲了`npm create vite`, 会发生两个事情**

1. 帮我们全局安装一个东西: create-vite(vite的脚手架)
2. 直接运行create-vite bin目录下的一个执行配置

**create-vite**: 脚手架, 为我们下载好我们需要的东西, (vite、vue、post-css、less),
并做好了最佳实践的配置。跟vue-cli/create-react-app一样,只不过这俩内置的是webpack

**vite与webpack最核心的区别**

- webpack
    1. 将所有文件全都打包, 之后在启动服务
- vite
    1. 先启动服务, 在将项目用到的文件进行打包, 没有用到的不会打包

# vite依赖预构建

vite只能识别ESModule的依赖, 不能识别其他的模块化规范, 但是react axios都是用的commonJS的模块化, 这个问题可以通过依赖预构建解决

生产 和 开发
yarn dve ---> 开发
生产环境下, vite会全权交给rollup的库去完成环境的打包

> vite寻找依赖会从当前目录开始寻找, 一直寻找到当前电脑的跟目录, 如: /user

**依赖预构建: **

1. vite会将第三方依赖全部转换成ESModule模块化规范;
2. 将依赖全部放到node_modules/.vite/deps下;
3. 将第三方依赖所依赖的包都集成到一个文件, 尽可能

解决了三个问题:

1. 第三方依赖使用除ESModules以外的模块化规范
2. 对路径的处理可以直接使用.vite/deps, 方便路径重写
3. 解决了网络多包传输的问题(也是原生ESModules规范不敢支持node_modules的原因), 有了依赖预构建, 无论它引用了多少其他的依赖,
   vite都会尽可能的将他们集成, 也就是将他们的代码全放到一个文件里, 最终直生成一个模块或者几个模块

# vite配置文件处理细节

**vite.config.js代码提示**

```js
// vite.config.js
import { defineConfig } from "vite"

export default defineConfig({
    ...
})
```

还有其他方式，具体看官网，比如 加注释

**环境配置**

```js
import { defineConfig } from "vite"
import viteBaseConfig from "./vite.base.config"
import viteDevConfig from "./vite.dev.config"
import viteProdConfig from "./vite.prod.config"

// 策略模式
const envResolver = {
    "serve": ({ ...viteBaseConfig, ...viteDevConfig }),
    "build": () => Object.assign({}, viteBaseConfig, viteProdConfig)
}


export default defineConfig(({ command }) => {
    // 进入开发环境还是生产环境取决与我们敲的命令 --> yarn dev || yarn build
    return envResolver[command]()
})

```

# vite环境变量配置

**涉及到的知识**

- 涉及到vite.config.js中的一些配置:
    - root
    - envDir: 用来配置当前环境变量的文件地址
- process.cwd(): 返回当前node进程的工作目录

> 环境变量: 会根据当前的代码环境产生值的变化的变量就是环境变量

> 代码环境:

1. 开发环境
2. 测试环境
3. 预发布环境
4. 灰度环境
5. 生产环境

**针对不同的环境存在的问题**

- 我们使用高德地图, 针对开发和生产环境会有不同的APP_KEY, 如:
    - 开发环境: 110
    - 生产环境: 111
- 与后端进行通信, 开发与生产环境的API也是不同的: 如:
    - 开发环境: http://test.api/
    - 生产环境: https://api
      我们需要手动去改这些变量, 万一上线的时候忘记修改了, 可能会造成很严重的后果

**在vite中环境变量的处理**
vite内置了dotenv库, 用于处理环境变量问题

> dotenv会自动读取.env文件, 解析里面的内容, 并注入到node的process对象下(但vite考虑到和其他配置的一些冲突问题,
> 不会直接注入到process对象)
> vite给我们提供了一些补偿措施: 我们可以调用vite的loadEnv来手动确认env文件

- .env: 所有环境
- .env.development: 开发环境
- .env.production: 生产环境

```js
import { defineConfig, loadEnv } from "vite"

export default defineConfig(({ command, mode }) => {

    // 获取.env.development中的变量 yarn dev   mode属性后面说
    const env = loadEnv(mode, process.cwd(), "")
    console.log("env ===", env.APP_KEY) // env === 110

})
```

当我们 `yarn dev`时, vite会默认将development作为参数传给mode, 相当于: `yarn dev --mode development`, 我们也可以通过这种方式修改名字,
如: `yarn dev --mode develop`

当我们 `yarn build`时, vite会默认将production作为参数传给mode, 相当于: `yarn dev --mode production`, 我们也可以通过这种方式修改名字,
如: `yarn dev --mode prod`

当我们调用 `loadenv()` 的时候，它会做如下几件事

1. 直接找到.env, 解析里面的环境变量, 并放进一个对象里面
2. 对根据传进来mode值, 访问对应的env文件, 如: yarn dev ==> mode = development ==> .env.development ==> 读取文件里面的环境变量

**在服务端获取对应的环境变量**

```js
import { defineConfig, loadEnv } from "vite"
import viteBaseConfig from "./vite.base.config"
import viteDevConfig from "./vite.dev.config"
import viteProdConfig from "./vite.prod.config"

// 策略模式
const envResolver = {
    "serve": () => ({ ...viteBaseConfig, ...viteDevConfig }),
    "build": () => Object.assign({}, viteBaseConfig, viteProdConfig)  // 没有特别意义, 只是第二种写法
}


export default defineConfig(({ command, mode }) => {

    // 获取.env中的变量
    const env = loadEnv(mode, process.cwd(), "")
    console.log("env ===", env.APP_KEY)

    // 获取参数  yarn dev
    console.log("mode ====", mode)   // development

    // 根据环境变量决定采用哪个配置文件
    return envResolver[command]()
})

```

**在客户端获取对应的环境变量**

`VITE_SOME_KEY=123`

在客户端我们可以通过 `import.meta.env` 取得变量, 为了防止我们把隐私性的属性暴露出去，vite做了一层拦截, 不是以 `VITE_`
开头的变量, 不会注入到import.meta.env, 我们想要更改这个前缀, 可以在vite.config.js中修改envPrefix属性

```js
// request.js   yarn dev
console.log("import.meta.env=====", import.meta.env)  // {....., ENV_APP_KEY: 110}  
```

# vite是怎么让浏览器能够识别.vue文件的

1. vite将App.vue全部编译成纯JS代码
2. vite会让浏览器以解析JS文件一样解析.vue为后缀的文件(浏览器不会看文件是以什么为后缀, 只看ctx.response.set("
   Content-Type", "text/javascript"))

> 在浏览器和服务器眼里, 你的文件都是字符串

# postcss

> postcss是想通过less插件来涵盖预处理器的功能, 做出一套完整处理css流程的工具, 这时的postcss是比预处理器less scss高一个等级的,
> 但是以后因为插件的维护成本太高, 就让开发者去使用预处理器, 把预处理器的结果给postcss, 它去做接下来的工作,
> 所以现在有很多人称他为css「后处理器」
> vite天生对postcss有非常良好的支持

- postcss --> 保证css执行起来万无一失
  我们写的css代码(怎么爽怎么来) --> postcss --> 将语法进行编译(嵌套语法, 函数, 变量)变成原生css, less scss等预处理器都可以做
  --> 语法降级 --> 将新出的css语法进行降级
- babel --> 保证js执行起来万无一失
  我们学的js代码(怎么爽怎么来) --> babel --> 将ts编译成js代码 --> 将高级js语法进行降级 --> 交给浏览器执行

**使用postcss**

1. 安装依赖
   `yarn add postcss-cli postcss -D`
2. 在postcss.config.js中添加配置
3. 敲命令 `npx postcss index.css -o result.css`

# 在vite中处理css

> vite天生就支持对css的直接处理

**分为这几个步骤**

1. vite读取到main.js中的index.css标签
2. 使用fs模块读取index.css文件中的内容
3. 创建style标签, 并将index.css中的内容插入进去
4. 将style标签插入到index.html文件的header标签
5. 将css文件中的内容直接替换为JS脚本(方便热更新和css模块化), 同时设置Context-Type为JS, 从而让浏览器以JS脚本的形式来执行

**多个css文件可能就会存在类名冲突情况, 可以使用css模块化解决, 如:**

```js
// 引入css模块代码
import componentACSS from './componentA.module.css'

console.log(componentACSS)  // {footer: _footer_1ughu_9}
const div = document.createElement('div')
// 给标签添加类名
div.className = componentACSS.footer
document.body.appendChild(div)
```

**css模块的基本原理**

1. module.css(module.css是种约定, 表示需要开启css模块化)
2. 它会将文件中所有类名进行替换, 以原始类名为key, 它生成的类名为value, 如: (footer 替换为 _footer_1ughu_9)
3. 创建一个映射对象(`{ footer: _footer_1ughu_9 }`)
4. 将替换后的内容放如style标签, 插入到index.html中的header标签
5. 将componentA.module.css内容全部删除, 替换成JS脚本
6. 将创建的映射对象在脚本中进行默认导出

> 所有文件的内容在浏览器都是字符串, 想要替换style标签的内容只需要字符串替换就行了

vite同样支持scss模块化, 只需要下载个scss依赖就行了 `yarn add scss`

# vite配置文件中css配置(module篇)

在vite.config.js中我们通过css属性去控制真个vite对于css的处理行为

- localConvention: 修改生成的配置对象的key的展示形式(驼峰还是中划线形式)
- scopeBehaviour: 配置当前的模块化行为是模块化还是全局化 (有hash就是开启了模块化的一个标志,
  因为他可以保证产生不同的hash值来控制我们的样式类名不被覆盖)
- generateScopedName: 生成的类名的规则(可以配置为函数,
  也可以配置成字符串规则: https://github.com/webpack/loader-utils#interpolatename)
- hashPrefix: 生成hash会根据你的类名 + 一些其他的字符串(文件名 + 他内部随机生成一个字符串)去进行生成,
  如果你想要你生成hash更加的独特一点, 你可以配置hashPrefix, 你配置的这个字符串会参与到最终的hash生成, （hash:
  只要你的字符串有一个字不一样, 那么生成的hash就完全不一样, 但是只要你的字符串完全一样, 生成的hash就会一样）
- globalModulePaths: 代表你不想参与到css模块化的路径

# vite配置文件中css配置流程(preprocessorOptions篇)

主要是用来配置css预处理的一些全局参数
假设没有使用构建工具, 我们又想去编译less文件的话

```r
yarn add less # lessc的编译器
```

你只要安装了node, 你就可以使用node index.js
你只要安装了less 你就可以使用lessc去编译less文件
less是可以定义变量的
**sourceMap**
文件之间的索引:
假设我们的代码被压缩或者被编译过了, 这个时候假设程序出错, 他将不会产生正确的错误位置信息 如果设置了sourceMap,
他就会有一个索引文件map
sourceMap解决的问题极其的微小, 但是他的实现过程非常的复杂

# 使用path.resolve的原因

> 无论是客户端还是服务端代码中的的相对路径, 最后都会转换成绝对路径

node端去读取文件或者操作文件的时候, 如果发现用的是相对路径, node就会用process.cwd(), 去进行拼接

process.cwd(): 获取当前的node执行目录
__dirname会返回当前所在目录的绝对路径

```js
const path = require("path")  // path本质上是一个字符串处理模块, 他里面有非常多的路径字符串处理方式

path.resolve(__dirname, "./variable.css") // 会将当前所在目录的绝对路径与/variable.css进行拼接
```

# vite在生产环境对静态资源的处理

- 正确的路径
  http://localhost:63342/Vite/test-vite/dist/index.html
- 浏览器去找的路径
  http://localhost:63342/assets/index-b90ca64a.css

**为什么文件名有hash**
因为浏览器有缓存机制, 发现如果文件名没有变的话, 浏览器会去缓存里取, 但是如果我们改了文件中的内容, 浏览器还是去缓存里取,
就无法显示改后的内容.
当使用hash算法, 当我们打包生成文件的时候, vite会判断文件是否发生改变, 发生改变, vite会生成与上次不同的hash值, 没有改变,
就会继续使用上次的hash值, 这样就完美解决了上面的问题

# vite插件

> 插件 中间件是什么: 在生命周期的不同阶段去做不同的事情

## vite常见插件-vite-plugin-mock

**使用mock**

1. 安装插件: `yarn add vite-plugin-mock mockjs -D`
2. 配置
   ```js
   // vite.config.js
    import {defineConfig} from "vite";
    import {viteMockServe} from "vite-plugin-mock";
    
    export default defineConfig({
    plugins: [
        viteMockServe()
    ]
    })
   ```
3. 在项目根目录创建mock/index.js文件

```js
import mockJS from "mockjs"

const userList = mockJS.mock({
    "data|100": [{
        name: "@cname",  //  随机生成姓名, 并且不重复
        "id|+1": 1,  // id自增一
        time: "@time",
        data: "@date"
    }]
})

console.log("userList", userList)

module.exports = [
    {
        method: "post",
        url: "/api/users",
        response: ({ body }) => {
            return {
                code: 200,
                msg: "success",
                data: []
            }
        }
    }
]
```

# vite常见配置
```js
import { defineConfig } from "vite";
import path from "path";

export default defineConfig({
    optimizeDeps: {
        exclude: [], // 将指定数组中的依赖不进行依赖预构建
    },
    envPrefix: "ENV_", // 配置vite注入客户端环境变量校验的env前缀
    css: { // 对css的行为进行配置
        // modules配置最终会丢给postcss modules
        modules: { // 是对css模块化的默认行为进行覆盖
            localsConvention: "camelCaseOnly", // 修改生成的配置对象的key的展示形式(驼峰还是中划线形式)
            scopeBehaviour: "local", // 配置当前的模块化行为是模块化还是全局化 (有hash就是开启了模块化的一个标志, 因为他可以保证产生不同的hash值来控制我们的样式类名不被覆盖)
            // generateScopedName: "[name]_[local]_[hash:5]" // https://github.com/webpack/loader-utils#interpolatename
            // generateScopedName: (name, filename, css) => {
            //     // name -> 代表的是你此刻css文件中的类名
            //     // filename -> 是你当前css文件的绝对路径
            //     // css -> 给的就是你当前样式
            //     console.log("name", name, "filename", filename, "css", css); // 这一行会输出在哪？？？ 输出在node
            //     // 配置成函数以后, 返回值就决定了他最终显示的类型
            //     return `${name}_${Math.random().toString(36).substr(3, 8) }`;
            // }
            hashPrefix: "hello", // 生成hash会根据你的类名 + 一些其他的字符串(文件名 + 他内部随机生成一个字符串)去进行生成, 如果你想要你生成hash更加的独特一点, 你可以配置hashPrefix, 你配置的这个字符串会参与到最终的hash生成, （hash: 只要你的字符串有一个字不一样, 那么生成的hash就完全不一样, 但是只要你的字符串完全一样, 生成的hash就会一样）
            globalModulePaths: ["./componentB.module.css"], // 代表你不想参与到css模块化的路径
        },
        preprocessorOptions: { // key + config key代表预处理器的名
            less: { // 整个的配置对象都会最终给到less的执行参数（全局参数）中去
                // 在webpack里就给less-loader去配置就好了
                math: "always",
                globalVars: { // 全局变量
                    mainColor: "red",
                }
            },
        },
        devSourcemap: true, // 开启css的sourceMap（文件索引）
        postcss: {}, // 配置postcss相关
    },
    resolve: {
        alias: {
            "@": path.resolve(__dirname, "./src"), // 设置别名, 以后我们在其他组件中可以使用@来代替src这个目录
        }
    },
    build: { // 构建生产包时的一些配置策略
        rollupOptions: { // 配置rollup的一些构建策略
            output: { // 控制输出
                // 在rollup里面, hash代表将你的文件名和文件内容进行组合计算得来的结果
                assetFileNames: "[hash].[name].[ext]"
            }
        },
        assetsInlineLimit: 4096000, // 4000kb 
        outDir: "dist", // 配置输出目录
        assetsDir: "static", // 配置输出目录中的静态资源目录
        emptyOutDir: true, // 清除输出目录中的所有文件
    }
});
```

# vite与ts结合

## 企业级项目怎么配置

1. 让报错现在控制台
   - 安装依赖: `yarn add vite-plugin-checker -D`
   - 在vite.config.js中进行配置 
2. 报错的时候不让打包
   - 在package.json中配置
   ```json
   {
      "script": {
        "build": "tsc --noEmit && vite build"
      }
   }
   ```
