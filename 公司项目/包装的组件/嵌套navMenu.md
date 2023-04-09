### index.js

```vue
<template>
  <div class="nav-menu">
    
    <!-- style="min-width: 300px; height: calc(100vh - 50px);"  用el-row做适配-->
    <!-- 组件 -->
    <el-scrollbar style="height: 100%"> 
      <el-menu
        class="el-menu" 
        style="height: calc(100vh - 126px);"
      >
        
      <!-- -->
      <subMenu :menu="menu" v-for="menu in menuList" :key="menu.id"/>
      </el-menu>
      
    </el-scrollbar>
  </div>
</template>

<script>
import subMenu from "@/components/NavMenu/subMenu"

export default {
  props: ['menuList'],
  components: {
    subMenu
  }
};
</script>

```

### SubMenu.vue

```vue
<template>
  <el-submenu :index="menu.id" v-if="menu.children">
    <template #title>
      <span>{{ menu.label }}</span>
    </template>
    <sub-menu
      :menu="menuItem"
      v-for="menuItem in menu.children"
      :key="menuItem.id"
    >
    </sub-menu>
  </el-submenu>
  <el-menu-item :index="menu.id" v-else>
    <template #title>{{ menu.label }}</template>
  </el-menu-item>
</template>

<script>
let allNode = {};
export default {
  name: "subMenu",
  data() {
    return {
      initMore: [],
    };
  },
  props: ["menu"],
};
</script>

```

#### 数据结构

```
const menuList = [
	id: 0,
	label: '黑龙江',
	children: [
		id: 01,
		label: '哈尔滨',
		children: null
	]
]
```

