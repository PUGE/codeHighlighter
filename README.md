# checkbox

> SVG动画效果选择框.
![Alt text](http://puge-10017157.cossh.myqcloud.com/github8600/check-puge/%E5%BD%95%E5%88%B6_2018_02_23_17_09_21_270.gif)
## 安装 及 使用

```bash
$ npm install --save check-puge
```

使用组件

```vue
<template>
  <CheckBox v-model="checkValue" :size="12">
</template>

<script>
  import CheckBox from 'check-puge'
  export default {
    components: {
      CheckBox
    }
  }
</script>
```

### 参数

#### value
类型: `Boolean`<br>
必要性: `true`<br>
默认值: `无`

定义组件是否被选中.

```html
<CheckBox :value="false">
```

#### size
类型: `Number`<br>
必要性: `false`<br>
默认值: `20`

选择框大小.

#### borderWidth
类型: `Number`<br>
必要性: `false`<br>
默认值: `1`

#### borderColor
类型: `String`<br>
必要性: `false`<br>
默认值: `#333333`

```html
<CheckBox :size="18">
```
