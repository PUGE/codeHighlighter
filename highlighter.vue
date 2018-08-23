<template>
  <div class="highlighter"></div>
</template>

<script>
  export default {
    name: 'highlighter',
    props: {
      value: ''
    },
    data () {
      return {
        TAB: '    '
      }
    },
    mounted () {
      // 获取DOM元素
      const JSONDom = this.$el
      // 获取到格式化后的JSON字符串
      let json = this.getJson(this.value)
      JSONDom.style.display = 'block'
      /* eslint-disable no-eval */
      let obj = eval('[' + json + ']')
      let html = this.ProcessObject(obj[0], 0, false, false, false)
      JSONDom.innerHTML = `<PRE class='CodeContainer'>${html}</PRE>`
    },
    methods: {
      getJson (json, options) {
        let reg = null
        let formatted = ''
        let pad = 0
        let PADDING = '    '
        options = options || {}
        if (options.newlineAfterColonIfBeforeBraceOrBracket === true) {
          options.newlineAfterColonIfBeforeBraceOrBracket = true
        } else {
          options.newlineAfterColonIfBeforeBraceOrBracket = false
        }
        if (options.spaceAfterColon === false) {
          options.spaceAfterColon = false
        } else {
          options.spaceAfterColon = true
        }
        if (typeof json !== 'string') {
          json = JSON.stringify(json)
        } else {
          json = JSON.parse(json)
          json = JSON.stringify(json)
        }
        /* eslint-disable no-useless-escape */
        reg = /([\{\}])/g
        json = json.replace(reg, '\r\n$1\r\n')
        reg = /([\[\]])/g
        json = json.replace(reg, '\r\n$1\r\n')
        reg = /(\,)/g
        json = json.replace(reg, '$1\r\n')
        reg = /(\r\n\r\n)/g
        json = json.replace(reg, '\r\n')
        reg = /\r\n\,/g
        json = json.replace(reg, ',')
        if (!options.newlineAfterColonIfBeforeBraceOrBracket) {
          reg = /\:\r\n\{/g
          json = json.replace(reg, ':{')
          reg = /\:\r\n\[/g
          json = json.replace(reg, ':[')
        }
        if (options.spaceAfterColon) {
          reg = /\:/g
          json = json.replace(reg, ':')
        }
        (json.split('\r\n')).forEach(function (node, index) {
          let i = 0,
            indent = 0,
            padding = ''

          if (node.match(/\{$/) || node.match(/\[$/)) {
            indent = 1
          } else if (node.match(/\}/) || node.match(/\]/)) {
            if (pad !== 0) {
              pad -= 1
            }
          } else {
            indent = 0
          }

          for (i = 0; i < pad; i++) {
            padding += PADDING
          }

          formatted += padding + node + '\r\n'
          pad += indent
        })
        return formatted
      },
      GetRow (indent, data, isPropertyContent) {
        let tabs = ''
        /* eslint-disable no-unmodified-loop-condition */
        for (let i = 0; i < indent && !isPropertyContent; i++) {
          tabs += this.TAB
        }
        if (data !== null && data.length > 0 && data.charAt(data.length - 1) !== '\n') {
          data = data + '\n'
        }
        return tabs + data
      },
      FormatLiteral (literal, quote, comma, indent, isArray, style) {
        if (typeof literal === 'string') {
          literal = literal.split('<').join('&lt;').split('>').join('&gt;')
        }
        let str = `<span class='' + style + ''>` + quote + literal + quote + comma + '</span>'
        if (isArray) {
          str = this.GetRow(indent, str)
        }
        return str
      },
      // 数组
      IsArray (obj) {
        return obj && typeof obj === 'object' && typeof obj.length === 'number' && !(obj.propertyIsEnumerable('length'))
      },
      FormatFunction (indent, obj) {
        let tabs = ''
        for (let i = 0; i < indent; i++) {
          tabs += this.TAB
        }
        let funcStrArray = obj.toString().split('\n')
        let str = ''
        for (let i = 0; i < funcStrArray.length; i++) {
          str += ((i === 0) ? '' : tabs) + funcStrArray[i] + '\n'
        }
        return str
      },
      ProcessObject (obj, indent, addComma, isArray, isPropertyContent) {
        let html = ''
        let comma = (addComma) ? `<span class='Comma'>,</span>` : ''
        let type = typeof obj
        if (this.IsArray(obj)) {
          if (obj.length === 0) {
            html += this.GetRow(indent, `<span class='ArrayBrace'>[ ]</span>` + comma, isPropertyContent)
          } else {
            html += this.GetRow(indent, `<span class='ArrayBrace'>[</span>`, isPropertyContent)
            for (let i = 0; i < obj.length; i++) {
              html += this.ProcessObject(obj[i], indent + 1, i < (obj.length - 1), true, false)
            }
            html += this.GetRow(indent, `<span class='ArrayBrace'>]</span>` + comma)
          }
        } else {
          if (type === 'object' && obj == null) {
            html += this.FormatLiteral('null', '', comma, indent, isArray, 'Null')
          } else {
            if (type === 'object') {
              let numProps = 0
              /* eslint-disable no-unused-vars */
              for (let prop in obj) {
                numProps++
              }
              if (numProps === 0) {
                html += this.GetRow(indent, `<span class='ObjectBrace'>{ }</span>` + comma, isPropertyContent)
              } else {
                html += this.GetRow(indent, `<span class='ObjectBrace'>{</span>`, isPropertyContent)
                let j = 0
                for (let prop in obj) {
                  html += this.GetRow(indent + 1, `<span class='PropertyName'>${prop}</span>: ` + this.ProcessObject(obj[prop], indent + 1, ++j < numProps, false, true))
                }
                html += this.GetRow(indent, `<span class='ObjectBrace'>}</span>` + comma)
              }
            } else {
              if (type === 'number') {
                html += this.FormatLiteral(obj, '', comma, indent, isArray, 'Number')
              } else {
                if (type === 'boolean') {
                  html += this.FormatLiteral(obj, '', comma, indent, isArray, 'Boolean')
                } else {
                  if (type === 'function') {
                    obj = this.FormatFunction(indent, obj)
                    html += this.FormatLiteral(obj, '', comma, indent, isArray, 'Function')
                  } else {
                    if (type === 'undefined') {
                      html += this.FormatLiteral('undefined', '', comma, indent, isArray, 'Null')
                    } else {
                      html += this.FormatLiteral(obj, `"`, comma, indent, isArray, 'String')
                    }
                  }
                }
              }
            }
          }
        }
        return html
      }
    }
  }
</script>

<style scoped>
  .highlighter {
    display: block;
  }
</style>
