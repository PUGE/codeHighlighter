# @puge/highlight


> SVG动画效果选择框.
![Alt text](http://github-puge.oss-cn-beijing.aliyuncs.com/highlight/high.png)

## 安装 及 使用

```bash
$ npm install --save @puge/highlight
```

使用组件

```vue
<template>
  <Highlight v-model="jsonData">
</template>

<script>
  import Highlight from '@puge/highlight'
  export default {
    components: {
      Highlight
    },
    data () {
      return {
        jsonData: {
          "string": "12345",
          "array": [1, 2, 3, 4]
        }
      }
    }
  }
</script>
```

### 参数

#### padding
类型: `String`<br>
必要性: `false`<br>
默认值: `  `

间距

#### spacing
类型: `String`<br>
必要性: `false`<br>
默认值: `  `

分个符号

#### contenteditable
类型: `Boolean`<br>
必要性: `false`<br>
默认值: false

是否可被编辑
