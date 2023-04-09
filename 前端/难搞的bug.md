### 1. **warning Replace `············` with `······`**

vue根目录下的 .eslintrc.js 文件 rules 添加

```
"prettier/prettier": "off"
```

### 2. 下载scss和less依赖一定要

`yarn add sass-loader sass -D`

### 3. element plus 日期选择器

一定要给`value-format`，否则会出现日期少8小时的情况

### 4. element-plus input 无法输入

双向数据绑定用`v-model`
