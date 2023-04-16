## 前言

最近在做一个通用管理系统解决方案的项目，在配置eslint的时候遇到了一点小麻烦，所以就想写篇配置eslint的文章，来帮助想用eslint来规范自己的代码，却没有配置经验的朋友。

项目是用vite构建了，技术栈是vue3 + ts，

开发工具如果是vscode则可以下载ESLint和Volar插件

![image-20230416124006528](C:\Users\wangkun\AppData\Roaming\Typora\typora-user-images\image-20230416124006528.png)![image-20230416124024135](C:\Users\wangkun\AppData\Roaming\Typora\typora-user-images\image-20230416124024135.png)

webstorm内置了ESlint，直接配置就行

![image-20230416124210381](C:\Users\wangkun\AppData\Roaming\Typora\typora-user-images\image-20230416124210381.png)

## 配置eslint

**插件说明**

|               依赖               |                           作用描述                           |
| :------------------------------: | :----------------------------------------------------------: |
|              eslint              |                        ESLint 核心库                         |
|      eslint-config-prettier      |           关掉所有和 Prettier 冲突的 ESLint 的配置           |
|      eslint-plugin-prettier      |     将 Prettier 的 rules 以插件的形式加入到 ESLint 里面      |
|        eslint-plugin-vue         |                  为 Vue 使用 ESlint 的插件                   |
| @typescript-eslint/eslint-plugin |  ESLint 插件，包含了各类定义好的检测 TypeScript 代码的规范   |
|    @typescript-eslint/parser     | ESLint 的解析器，用于解析 TypeScript，从而检查和规范 TypeScript 代码 |



### 1. 下载插件

```shell
npm install -D eslint eslint-plugin-vue
```

这部分vue官方有说明，想了解的透彻点可以去官网阅读文档，[工具链 | Vue.js (vuejs.org)](https://cn.vuejs.org/guide/scaling-up/tooling.html#linting)

### 2. 初始化eslint配置文件



```shell
npm init @eslint/config
```

![image-20230416124842984](C:\Users\wangkun\AppData\Roaming\Typora\typora-user-images\image-20230416124842984.png)

这时会在项目根目录生成一份eslint的配置文件` .eslintrc.cjs`

```js
module.exports = {
  env: {
    browser: true,
    es2021: true,
    node: true
  },
  extends: [
    'plugin:vue/vue3-essential',
    'standard-with-typescript'
  ],
  overrides: [
  ],
  parserOptions: {
    ecmaVersion: 'latest',
    sourceType: 'module'
  },
  plugins: [
    'vue'
  ],
  rules: {
  }
}
```

### 3. 参照vue3官网提到的eslint-plugin-vue插件[用户指南 |eslint-plugin-vue (vuejs.org)](https://eslint.vuejs.org/user-guide/#usage)对新增一些配置项

```shell
module.exports = {
  ...
  overrides: [
  ],
  parser: 'vue-eslint-parser', // 新增
  parserOptions: {
    parser: '@typescript-eslint/parser', // 新增
    ...
  },
  ...
}
```

这时候在 VSCode 内编写代码就会有相应的 ESLint 提示，但 Vite 不能识别 ESLint 的相关配置进行代码检查。

也就是项目中虽然提示错误，但是项目还是可以启动起来，不会报错。

## Vite添加ESLint支持

### 安装插件

```shell
npm install vite-plugin-eslint --save-dev
```

在vite.config.ts中添加配置

```ts
...
import eslint from 'vite-plugin-eslint' // 新增

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [vue(), eslint()], // 新增 eslint()
})
```

### 在.eslintrc.cjs 添加配置

不添加这些配置项目在启动的时候可能会报错

```js
module.exports = {
    env: {
        browser: true,
        es2021: true,
        node: true
    },
    extends: [
        'plugin:vue/vue3-essential',
        'standard-with-typescript' // 使用standard规范
        // "plugin:vue/vue3-strongly-recommended"
    ],
    overrides: [],
    parserOptions: {
        ecmaVersion: 'latest',
        sourceType: 'module',
        parser: '@typescript-eslint/parser', // 新增
        project: ['tsconfig.json'], // 新增
        extraFileExtensions: ['.vue'], // 新增
    },
    plugins: [
        'vue'
    ],
    parser: 'vue-eslint-parser', // 新增
    rules: {
        // 不用显示的标注函数返回值
        "@typescript-eslint/explicit-function-return-type": "off",
        // 组件不强制用多个单词组成
        'vue/multi-word-component-names': 0,
    }
}
```

## 其他

### 不对某些文件检查

有一些文件我们是不希望eslint进行规范检查的，这时我们就可以在根目录新建.eslintignore文件，将不希望进行检查的文件写进去

```
.eslintrc.cjs
*.config.ts
*.d.ts
```

### .eslintrc.cjs中一些常见配置

如果引用其他组织的代码规范的时候，我们又想根据自己的习惯去做出一些修改的时候，我们就可以在`rules:{}`中添加自己想要的配置，此配置会覆盖我们引入的规范

```js
module.exports = {
	...
    extends: [
        'plugin:vue/vue3-essential',
        'standard-with-typescript' // 使用standard规范
        // "plugin:vue/vue3-strongly-recommended"
    ],
	...
    rules: {
        // 不用显示的标注函数返回值
        "@typescript-eslint/explicit-function-return-type": "off",
        // 组件不强制用多个单词组成
        'vue/multi-word-component-names': 0,
    }
}
```

