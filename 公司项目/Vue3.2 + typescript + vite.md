# Vue3.2 + typescript + vite 

## 创建项目

1. `npm create vite@latest`
2. 选择信息
3. 

## 项目待处理

- [ ] 项目图标
- [ ] tabBar与menuBar样式状态没关联
- [ ] 主页用table表单代替
- [ ] 个人中心echarts再次切换不显示
- [ ] 头像附近功能
- [ ] 自定义svg图标？

## vite配置文件

```typescript
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import path from 'path'

// https://vitejs.dev/config/
export default defineConfig({
	// 重新设置端口号
  server: {
    port: 3001
  },
  resolve: {
    // 设置别名
    alias: {
      // 用@代替src目录，com代替组件目录
      "@": path.resolve(__dirname, "src"),
      "com": path.resolve(__dirname, "src/components")
    }
  },
  css: {
    // css与处理器
    preprocessorOptions: {
      scss: {
        // 引入varibales.scss全局与定义变量
        additionalData: `@import "./src/styles/varibales.scss";`
      }
    }
  },
  plugins: [vue()]
})
```

## 设置全局scss样式文件

1. 在src/styles放入全局scss样式文件

2. 在vite配置文件中加入配置

   ```
     css: {
       // css与处理器
       preprocessorOptions: {
         scss: {
           // 引入varibales.scss全局与定义变量
           additionalData: `@import "./src/styles/varibales.scss";`
         }
       }
     },
   ```

3. 在目标文件使用，如`background-color: $menuBg;`

## element plus技巧

### 图标

1. 图标必须包裹在`<el-icon><el-icon/>`中

2. 使用步骤

   1. 引入包`npm install @element-plus/icons-vue`
   2. 在目标文件引入想要使用的图表`import { Expand, Fold } from '@element-plus/icons-vue'`
   3. 直接使用`<el-icon><location></el-icon>`

3. 也可以通过这样使用图标

   ```
   <el-icon>
   		<component :is="item.icon"></component>
   </el-icon>
   ```

   

## @代替src

在设置之前要确保安装了依赖

`npm install -D @types/node`



1. 支持.js与.vue文件使用@：在vite.config.ts中添加配置

   ```
   import path from 'path'
   
   export default defineConfig({
       resolve: {
           // 设置别名
           alias: {
               // 用@代替src目录，com代替组件目录
               "@": path.resolve(__dirname, "src"),
               "com": path.resolve(__dirname, "src/components")
           }
       },
   })
   ```
   
   
   
2. 在vite中配置完只能用@引入`.js`或者`.vue`的文件，想要引入`.ts`的文件：在`tsconfig.json`中加入配

   ```
   {
     "compilerOptions": {
       "target": "ESNext",
       "strict": true,
       "paths": {
         "@":["./src"],
         "@/*":["./src/*"] // 支持别名
       }
     },
     "include": ["src/**/*.ts", "src/**/*.d.ts", "src/**/*.tsx", "src/**/*.vue"],
     "references": [{ "path": "./tsconfig.node.json" }]
   }
   
   ```

## 使用sass

`npm i sass`

`npm i sass-loader`











