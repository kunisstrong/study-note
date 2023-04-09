# React项目

## 创建项目

1. `npm init vite`

2. 下载需要的依赖

   ```json
   "dependencies": {
     "react": "^18.2.0",
     "react-dom": "^18.2.0",
     "react-router-dom": "^6.3.0",
     "react-redux": "^7.2.8",
     "redux": "^4.1.2"
   },
   ```

3. 尝试修改`package.json`启动命令

   ```json
   "scripts": {
     // 本机。端口号 3002。 自动打开
     "dev": "vite --host --port 3002 --open",
     "build": "tsc && vite build",
     "preview": "vite preview"
   },
   ```

4. 初始化样式

   - `npm i reset-css`
   - 全局引入

5. 用@代替src

   - ` npm i -D @types/node`

   - ```json
     // 	vite.config.ts  
     import {defineConfig} from 'vite'
     import react from '@vitejs/plugin-react'
     // 如果报错，就  import * as path from 'path'
     import path from 'path'
     
     // https://vitejs.dev/config/
     export default defineConfig({
         plugins: [react()],
         resolve: {
             alias: {
                 "@": path.resolve(__dirname, './src')
             }
         }
     })
     
     ```

   - ```json
     // 虽然现在可以用@代替src了。但是在文件中输入@是没有提示路径的,
     // 需要在tsconfig.json中添加两项配置
     "compilerOptions": {
       "baseUrl": "./",
       "paths": {
         "@/*": [
           "src/*"
         ]
       }
     },
     ```

6. 使用样式模块化

   - ```css
     // 创建comp1.module.scss文件
     .box {
       color: red;
     
       p {
         margin-left: 100px;
       }
     }
     
     ```

     
     
   - ```js
     // 在组件中使用
     import React from 'react'
     import styles from './comp1.module.scss'
     
     const Comp1 = () => {
         return (
             <div className={styles.box}>
                 <p>
                     Comp1
                 </p>
             </div>
         );
     };
     
     export default Comp1;
     ```

## 配置路由

