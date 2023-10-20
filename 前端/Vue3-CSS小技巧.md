# :deep()—深度选择器

在使用组件库的时候很有用，如果使用deep了样式还是不生效，就在样式后面再加个 `! importment`

```css
.a :deep(.b) {
  	/* ... */
}
```

# :global()—全局选择器

设置全局样式。使用global定义全局变量就不需要多余创建一个不带scope的style标签来定义全局变量了

```css
<style scoped>
  :global(.red) {
    color: red;
}
</style>
```

# v-bind—将css值绑定到动态的组件状态

可以用来实现点击按钮 改变元素样式之类的功能。如，点击按钮改变文字颜色：

```css
<template>
  <div class="test-container">
    <div class="red">哈哈哈哈哈</div>
    <el-button type="primary" @click="changeColor">点击改变文字颜色</el-button>
  </div>
</template>

<script setup>
const color = ref("red");
const changeColor = () => {
  color.value = "#1ba035";
};
</script>

<style lang="scss" scoped>
.test-container {
  .red {
    color: v-bind(color);
  }
}
</style>
```

# 不知名技巧

```vue
<template>
<!-- todo.done 为true，添加.done样式，反之不添加 -->
  <span :class="{ done: todo.done }">{{ todo.text }}</span>
</template>

<script setup>
  const todo = ref(
    text: "若干文字"，
    done： true
  )
</script>

<style>
.done {
  text-decoration: line-through;
}
</style>
```

注重filter的使用！