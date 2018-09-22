<template>
  <pre :contenteditable="contenteditable" class="highlighter" spellcheck ="false" v-html="html"></pre>
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
      },
      // 是否可被编辑
      contenteditable: {
        type: Boolean,
        default: false
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
        this.html = this.getJsonText(this.value, this.padding, true)
      },
      getJsonText (json, padding, atLast) {
        const type = typeof json
        switch (type) {
          case 'object': {
            // 判断是数组还是对象
            if (Array.isArray(json)) return this.parseArray(json, padding, atLast)
            else return this.parseJson(json, padding, atLast)
            break
          }
          default: {
            const temp = JSON.parse(json)
            return this.parseJson(temp, padding, atLast)
          }
        }
      },
      parseJson (json, padding, atLast) {
        let formatted = ''
        const keyList = Object.keys(json)
        const keyListLestIndex = keyList.length - 1
        formatted += '<span class="object-brace">{</span>\r\n'
        keyList.forEach((key, index) => {
          const value = json[key]
          const valueType = typeof value
          // 是否是最后一项
          const atLast = (index === keyListLestIndex)
          formatted += `${padding}<span class="property-name">"${key}"</span>:`
          switch (valueType) {
            case 'string': {
              if (atLast) formatted += `${this.spacing}<span class="string">"${value}</span>"\r\n`
              else formatted += `${this.spacing}"<span class="string">${value}</span>",\r\n`
              break
            }
            case 'object': {
              if (Array.isArray(value)) formatted += this.spacing + this.parseArray(value, padding + this.padding, atLast)
              else formatted += this.spacing + this.parseJson(value, padding + this.padding, atLast)
              break
            }
            case 'boolean':{
              if (atLast) formatted += `${this.spacing}<span class="boolean">${value}</span>\r\n`
              else formatted += `${this.spacing}<span class="boolean">${value}</span>,\r\n`
              break
            }
            case 'number': {
              if (atLast) formatted += `${this.spacing}<span class="number">${value}</span>\r\n`
              else formatted += `${this.spacing}<span class="number">${value}</span>,\r\n`
              break
            }
          }
        })
        // 加上尾部括号
        // 判断是否为最后一项以便决定是否加上逗号
        if (atLast) formatted += `${padding.substring(2)}<span class="object-brace">}</span>\r\n`
        else formatted += `${padding.substring(2)}<span class="object-brace">}</span>,\r\n`
        return formatted
      },
      parseArray (array, padding, atLast) {
        let formatted = ''
        const arrayLestIndex = array.length - 1
        formatted += '<span class="array-brace">[</span>\r\n'
        array.forEach((value, index) => {
          const valueType = typeof value
          // 是否是最后一项
          const atLast = (index === arrayLestIndex)
          switch (valueType) {
            case 'string': {
              if (atLast) formatted += `${padding}<span class="string">"${value}</span>"\r\n`
              else formatted += `${padding}"<span class="string">${value}</span>",\r\n`
              break
            }
            case 'object': {
              if (Array.isArray(value)) formatted += padding + this.getJsonText(value, padding + this.padding, atLast)
              else formatted += padding + this.getJsonText(value, padding + this.padding, atLast)
              break
            }
            case 'boolean':{
              if (atLast) formatted += `${padding}<span class="boolean">${value}</span>\r\n`
              else formatted += `${padding}<span class="boolean">${value}</span>,\r\n`
              break
            }
            case 'number': {
              if (atLast) formatted += `${padding}<span class="number">${value}</span>\r\n`
              else formatted += `${padding}<span class="number">${value}</span>,\r\n`
              break
            }
          }
        })
        // 加上尾部括号
        // 判断是否为最后一项以便决定是否加上逗号
        if (atLast) formatted += `${padding.substring(2)}<span class="array-brace">]</span>\r\n`
        else formatted += `${padding.substring(2)}<span class="array-brace">]</span>,\r\n`
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
.highlighter {
  width: 100%;
  height: 100%;
  overflow: auto;
  display: block;
  font-size: 12.025px;
  line-height: 18px;
  word-break: break-all;
  word-wrap: break-word;
  font-family: Menlo,Monaco,Consolas,"Courier New",monospace;
}
.object-brace {
  color: #00AA00;
  font-weight: bold;
}
.array-brace {
  color: #0033FF;
  font-weight: bold;
}
.property-name {
  color: #CC0000;
  font-weight: bold;
}
.string {
  color: #007777;
}
.boolean {
  color: #0000FF;
}
.number {
    color: #AA00AA;
}
</style>
