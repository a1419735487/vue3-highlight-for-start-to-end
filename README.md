# vue3-highlight-for-start-to-end

vue3根据文字起始位置和结束位置高亮

### 安装

```
npm i vue3-highlight-for-start-to-end
```

### 在vue3中使用

```vue
<template>
	<highlight :text="text" :highLight="highLight"></highlight>
</template>

<script setup lang="ts">
    import { highlight } from 'vue3-highlight-for-start-to-end';
	import 'vue3-highlight-for-start-to-end/dist/style.css'
</script>
```

### 插槽

| 名称          | 解释       | 类型   | 默认值   | 示例                                  |
| ------------- | ---------- | ------ | -------- | ------------------------------------- |
| text          | 文本内容   | string | ''       | Sunfire666                            |
| highLight     | 高亮数据   | array  | []       | [{start: 0,end: 1},{start: 2,end: 3}] |
| fontSize      | 字体大小   | string | ‘20px’   | -                                     |
| lineHeight    | 行高       | string | '1.8em'  | -                                     |
| letterSpacing | 字间距     | string | '4px'    | -                                     |
| fillColor     | 高亮颜色   | string | 'yellow' | -                                     |
| fillOpacity   | 高亮透明度 | string | '0.6'    | -                                     |

### 事件

| 名称         | 解释                                     |
| ------------ | ---------------------------------------- |
| v-on:ifError | 高亮数据有误导致无法正常高亮时调用的事件 |

