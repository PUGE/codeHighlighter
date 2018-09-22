<template>
  <pre contenteditable="true" class="highlighter" spellcheck ="false">{{html}}</pre>
</template>

<script>
  export default {
    name: 'highlighter',
    props: {
      value: '',
      // 分隔符
      padding: {
        type: String,
        default: '  '
      },
      spacing: {
        type: String,
        default: ' '
      }
    },
    data () {
      return {
        html: ''
      }
    },
    mounted () {
      this.creatElement()
    },
    methods: {
      creatElement () {
        this.html = this.getJson(this.value, this.padding, true)
      },
      getJson (json, padding, atLast) {
        let formatted = ''
        if (typeof json === 'string') {
          json = JSON.parse(json)
        }
        const keyList = Object.keys(json)
        const keyListLestIndex = keyList.length - 1
        console.log(json)
        formatted += '{\r\n'
        keyList.forEach((key, index) => {
          const value = json[key]
          const valueType = typeof value
          // 是否是最后一项
          const atLast = index === keyListLestIndex
          formatted += `${padding}"${key}":`
          switch (valueType) {
            case 'string': {
              formatted += `${this.spacing}"${value}",\r\n`
              break
            }
            case 'object': {
              formatted += this.spacing + this.getJson(value, padding + this.padding, atLast)
              break
            }
            case 'boolean':
            case 'number': {
              if (atLast) formatted += `${this.spacing}${value}\r\n`
              else formatted += `${this.spacing}${value},\r\n`
              break
            }
          }
        })
        // 加上尾部括号
        // 判断是否为最后一项以便决定是否加上逗号
        if (atLast) formatted += `${padding.substring(2)}}\r\n`
        else formatted += `${padding.substring(2)}},\r\n`
        return formatted
      }
    },
    watch: {
      value () {
        this.creatElement()
      }
    }
  }
</script>

<style>
</style>
