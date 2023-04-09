## 创建基础的pinia结构

> state相当于组件中的data，getter相当于组件中的compute，actions相当于method

**src/store/index.ts**

```typescript
//  引入createPinia函数
import { createPinia } from 'pinia'

// 创建pinia实例
const pinia = createPinia()

// 导出实例
export default pinia
```

**src/store/use.ts**

```typescript
import { defineStore } from 'pinia'

export const useUserStore = defineStore('user', {
	state() {
		return {
			age: 18
		}
	}
})
```

## state

**修改单条数据**： 直接`state.属性名`

```typescript
<template>
  <div>
    你好，我是pinia-----{{ useStore.age }}
    <button @click="add">点击+1</button>
  </div>
</template>

<script setup lang="ts">
import { useUserStore } from './store/user'
import {storeToRefs} from "pinia"
const useStore = useUserStore()

// 注意这里不能通过ES解构，来解构出age，会使age失去响应式
// const { age } = useUserStore()

// 可以使用pinia的storeToRefs进行解构，这样不会使age失去响应式
// const { age } = storeToRefs(useStore)

const add = () => {
  useStore.age += 1
  // age += 1 // 这样修改视图是不会更新
}
</script>

<style scoped>

</style>
```

**修改多条数据**：可以用上面的方法改，但是用$patch性能更好

$patch()函数可以接受两个类型的参数

1. $patch + 对象
2. $patch + 函数

```vue
<template>
  <div>
    age ======== {{ age }}
    name ======== {{ name }}
  </div>
  <button @click="patchObj">通过$patch + 对象修改多个数据</button>
  <button @click="patchFun">通过$patch + 函数修改多个数据</button>

</template>

<script setup lang="ts">
import {useUserStore} from './store/user'
import {storeToRefs} from "pinia";

const useStore = useUserStore()

let {age, name} = storeToRefs(useStore)

const patchObj = () => {
  useStore.$patch({
    age: useStore.age + 1,
    name: "李四"
  })
}

const patchFun = () => {
  useStore.$patch((state) => {
    state.age = state.age + 1
    state.name = "李四"
  })
}

</script>

<style scoped>

</style>
```

## getter

**基础使用**

```typescript
import { defineStore } from 'pinia'

export const useUserStore = defineStore('user', {
	state() {
		return {
			age: 18
		}
	},
  
  getter: {
    doubleAge: (state) => {
      return state.age * 2
    }
  }
})

```

**特殊使用1：需要使用getter自身计算出来的属性**

> 不能使用箭头函数，并且要标注返回值类型

```typescript
import { defineStore } from 'pinia'

export const useUserStore = defineStore('user', {
	state() {
		return {
			age: 18,
      name: "张三"
		}
	},
  
  getter: {
    doubleAge: (state) => {
      return state.age * 2
    },
    // 需要用getter自身的属性
    getNameAndAge(): string {
      return this.doubleAge + this.name
    }
  }
})

```

**特殊使用2：需要接受参数**

> 返回一个处理函数；不能使用箭头函数

```typescript
import { defineStore } from 'pinia'

export const useUserStore = defineStore('user', {
	state() {
		return {
			age: 18,
      name: "张三"
		}
	},
  
  getter: {
    doubleAge: (state) => {
      return state.age * 2
    },
    
    // 需要用getter自身的属性
    getNameAndAge(): string {
      return this.doubleAge + this.name
    }
    
    // 需要接收参数
    getAddage() {
  		return (num: number) => this.age + num
		}
  }
})
```

## actions

**同步**

```typescript
import { defineStore } from 'pinia'

export const useUserStore = defineStore('user', {
	state() {
		return {
			name: "张三"
		}
	},

	actions: {
		// pinia中可以直接修改state中的状态
		saveName(name: string) {
			this.name = name
		}
	}
})
```

在组件中使用

```vue
<template>
  <div>
    {{ useStore.name }}
    <button @click="changeName">修改name</button>
  </div>
</template>

<script setup lang="ts">
import { useUserStore } from './store/user'
const useStore = useUserStore()


// 出发action修改name
const changeName = () => {
  useStore.saveName("李四")
  console.log(useStore.name) // 李四
}
</script>
```

**也支持异步**

```typescript
import { defineStore } from 'pinia'

export const useUserStore = defineStore('user', {
	state() {
		return {
			name: "张三"
		}
	},

	actions: {
		// pinia中可以直接修改state中的状态
		saveName(name: string) {
			setTimeout(() => {
				this.name = name	
			}, 1000)
		}
	}
})
```

在组件中使用

```vue
<template>
  <div>
    {{ useStore.name }}
    <button @click="changeName">修改name</button>
  </div>
</template>

<script setup lang="ts">
import { useUserStore } from './store/user'
const useStore = useUserStore()

// 出发action修改name
const changeName = () => {
  useStore.saveName("李四")
  console.log(useStore.name) // 张三 
}
</script>
```

