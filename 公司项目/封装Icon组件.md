## vue3优雅的使用icon图标

[Icon Sets • Iconify](https://icon-sets.iconify.design/)

### 安装依赖

```js
// vue-types
npm install vue-types --save
// element plus
yarn add element-plus  // Icon组件

// purge-icons/generated
npm i @purge-icons/generated  // Icon组件

// @types/node
npm i --save-dev @types/node  // vite

// vite-plugin-svg-icons
npm i vite-plugin-svg-icons  // vite
```

## 封装工具

**src/utils/proptypes.ts**

```ts
import { createTypes, VueTypesInterface, VueTypeValidableDef } from 'vue-types'
import { CSSProperties } from 'vue'

// 自定义扩展vue-types
type PropTypes = VueTypesInterface & {
  readonly style: VueTypeValidableDef<CSSProperties>
}

const propTypes = createTypes({
  func: undefined,
  bool: undefined,
  string: undefined,
  number: undefined,
  object: undefined,
  integer: undefined
}) as PropTypes

// 需要自定义扩展的类型
// see: https://dwightjack.github.io/vue-types/advanced/extending-vue-types.html#the-extend-method
propTypes.extend([
  {
    name: 'style',
    getter: true,
    type: [String, Object],
    default: undefined
  }
])

export { propTypes }

```

## 封装组件

**src/components/Icon/src/Icon.vue**

```vue
<script setup lang="ts">
import { computed, unref, ref, watch, nextTick } from 'vue'
import { ElIcon } from 'element-plus'
import { propTypes } from '../../../utils/propTypes'
import Iconify from '@purge-icons/generated'

type Nullable<T> = T | null
type ElRef<T extends HTMLElement = HTMLDivElement> = Nullable<T>
const props = defineProps({
  // icon name
  icon: propTypes.string,
  // icon color
  color: propTypes.string,
  // icon size
  size: propTypes.number.def(16)
})

const elRef = ref<ElRef>(null)

const isLocal = computed(() => props.icon.startsWith('svg-icon:'))

const symbolId = computed(() => {
  return unref(isLocal) ? `#icon-${props.icon.split('svg-icon:')[1]}` : props.icon
})

const getIconifyStyle = computed(() => {
  const { color, size } = props
  return {
    fontSize: `${size}px`,
    color
  }
})

const updateIcon = async (icon: string) => {
  if (unref(isLocal)) return

  const el = unref(elRef)
  if (!el) return

  await nextTick()

  if (!icon) return

  const svg = Iconify.renderSVG(icon, {})
  if (svg) {
    el.textContent = ''
    el.appendChild(svg)
  } else {
    const span = document.createElement('span')
    span.className = 'iconify'
    span.dataset.icon = icon
    el.textContent = ''
    el.appendChild(span)
  }
}

watch(
  () => props.icon,
  (icon: string) => {
    updateIcon(icon)
  }
)
</script>

<template>
  <ElIcon :size="size" :color="color">
    <svg v-if="isLocal" :width="size" :height="size" :fill="color" aria-hidden="true">
      <use :xlink:href="symbolId" />
    </svg>

    <span v-else ref="elRef" :class="$attrs.class" :style="getIconifyStyle">
      <span class="iconify" :data-icon="symbolId"></span>
    </span>
  </ElIcon>
</template>

```

**src/components/Icon/src/index.ts**

```ts
import Icon from './src/Icon.vue'

export { Icon }

```

**src/components/Icon/index.ts**

```ts
import type { App } from 'vue'
import { Icon } from './Icon'

export const setupGlobCom = (app: App<Element>): void => {
    app.component('Icon', Icon)
}

```

**src/compoment/index.ts**

用于注册全局组件

```ts
import type { App } from 'vue'
import { Icon } from './Icon'

export const setupGlobCom = (app: App<Element>): void => {
    app.component('Icon', Icon)
}
```

**src/main.ts**

挂载全局组件与注册脚本

```ts
import { createApp } from 'vue'
import './style.css'
import App from './App.vue'
import 'virtual:svg-icons-register'

import {setupGlobCom} from "./components";


const setupAll = () => {
    const app = createApp(App)

    setupGlobCom(app)

    app.mount('#app')
}

setupAll()
```

### 配置vite

```ts
import {defineConfig} from 'vite'
import vue from '@vitejs/plugin-vue'
import path from 'path'
import {createSvgIconsPlugin} from 'vite-plugin-svg-icons'

// https://vitejs.dev/config/
const root = process.cwd()


export default defineConfig({
    plugins: [
        // 图标相关
        createSvgIconsPlugin({
        		// 本地存放svg文件的路径
            iconDirs: [path.resolve(process.cwd(), "src/assets/icons")],
            symbolId: 'icon-[dir]-[name]',
            svgoOptions: true
        }),
        vue()
    ],
    server: {
        port: 3001
    }
})

```

**使用图标**

```
<template>
  <Icon icon="ep:aim" size="30" color="red" />
  <Icon icon="svg-icon:earth" size="30" color="red" />
  <Icon icon="svg-icon:up" size="30" />
</template>

<script lang="ts" setup>

</script>

```

1. 使用[Icon Sets • Iconify](https://icon-sets.iconify.design/)上的图标，只需要复制图标集与名称，传给icon属性`  <Icon icon="ep:aim" size="30" color="red" />`
2. 使用本地svg图标，只需要在名称前加`svg-icon:`，如：`<Icon icon="svg-icon:earth" size="30" color="red" />`

### 在element plus组件中使用icon

如 `ElButton` 传入 `icon` 属性

**src/hooks/useIcon.ts      封装hooks**

```ts
import {h} from 'vue'
import type {VNode} from 'vue'
import {Icon} from '../components/Icon'
interface IconTypes {
    size?: number
    color?: string
    icon: string
}

export const useIcon = (props: IconTypes): VNode => {
    return h(Icon, props)
}

```

**使用**

```vue
<template>
  <Icon icon="ep:aim" size="30" color="red" />
  <Icon icon="svg-icon:earth" size="30" color="red" />
  <Icon icon="svg-icon:up" size="30" style="width: 10px;" />
  <ElButton :icon="icon"> button</ElButton>
</template>

<script lang="ts" setup>
import { ElButton } from 'element-plus';
import { useIcon } from "../hooks/useIcon";

const icon = useIcon({ icon: 'svg-icon:earth', size: 20, color: 'red' })
</script>

```

## 用法

### 基本用法

如果以`svg-icon:` 开头，则会在本地中找到该 `svg` 图标，否则，会加载 `Iconify` 图标。

```
<template>
  <!-- 加载本地 svg -->
  <Icon icon="svg-icon:peoples" />
  
  <!-- 加载 Iconify -->
  <Icon icon="ep:aim" />
</template>
```

### useIcon

如果需要在其他组件中如 `ElButton` 传入 `icon` 属性，可以使用 `useIcon`

```
<script setup lang="ts">
import { useIcon } from '@/hooks/web/useIcon'
import { ElButton } from 'element-plus'

const icon = useIcon({ icon: 'svg-icon:save' })
</script>

<template>
  <ElButton :icon="icon"> button </ElButton>
</template>
```

#### 参数介绍

```
const icon = useIcon(props)
```

**props**

## Icon 属性

| 属性  | 说明     | 类型     | 可选值 | 默认值 |
| ----- | -------- | -------- | ------ | ------ |
| icon  | 图标名   | `string` | -      | -      |
| color | 图标颜色 | `string` | -      | -      |
| size  | 图标大小 | `number` | -      | 16     |

