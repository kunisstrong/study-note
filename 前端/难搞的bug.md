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

### 5.git [commit](https://so.csdn.net/so/search?q=commit&spm=1001.2101.3001.7020) 遇到 “#modified: xxx/xxx (modified content)”问题的解决

##### 1、出现这样现象的原因

```
主要是因为xxx/xxx目录下有隐藏的 “.git"目录
1
```

##### 2、解决方法

```
删掉“.git”目录，重新进行conmmit 
```
