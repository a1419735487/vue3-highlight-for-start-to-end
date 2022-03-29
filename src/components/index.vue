<template>
  <div style="position: relative;">
    <svg id="svg-container" version="1.1" xmlns="http://www.w3.org/2000/svg"></svg>
    <div id="dom-container" class="container" :style="{fontSize: fontSize, lineHeight: lineHeight, letterSpacing: letterSpacing}" v-ContainerWidthChange:50="onResize"></div>
    <div id="text-container" class="container" :style="{fontSize: fontSize, lineHeight: lineHeight, letterSpacing: letterSpacing}">{{text}}</div>
  </div>
</template>

<script setup lang="ts">
  import { ref, defineEmits, watch } from 'vue'

  const props = defineProps({
    // 文本内容
    text: {
      type: String,
      default: ''
    },
    // 高亮数据（start、end）
    highLight: {
      type: Array,
      default: []
    },
    fontSize: {
      type: String,
      default: '20px'
    },
    lineHeight: {
      type: String,
      default: '1.8em'
    },
    letterSpacing: {
      type: String,
      default: '4px'
    },
    // 高亮样式
    fillColor: {
      type: String,
      default: 'yellow'
    },
    fillOpacity: {
      type: String,
      default: '0.6'
    }
  })

  const emit = defineEmits(['ifError'])

  // 将文本拆分成单字
  const createDom = () => {
    let text = props.text
    ,index = 0
    ,domHtml = ''
    for (let i = 0; i < text.length; i++) {
      if (text[i] === '\n') {
        domHtml = domHtml + `<span>${text[i]}</span>`
        index++
      } else {
        domHtml = domHtml + `<span id="sf-${i + index}">${text[i]}</span>`
      }
    }
    let domContainer: any = document.getElementById('dom-container')
    domContainer.innerHTML = ''
    domContainer.innerHTML = domHtml
  }

  // 创建文本高亮
  const createHighLight = () => {
    let svgContainer: any = document.getElementById('svg-container')
    ,domContainer: any = document.getElementById('dom-container')
    svgContainer.style.height = domContainer.clientHeight
    svgContainer.innerHTML = ''
    let container = svgContainer.getBoundingClientRect()
    let highLightData: any = props.highLight
    ,fillColor = props.fillColor
    ,fillOpacity = props.fillOpacity
    try{
      for (let opt of highLightData) {
        let start:any = document.getElementById(`sf-${opt.start}`)
        ,end:any = document.getElementById(`sf-${opt.end}`)
        ,startLocation = start.getBoundingClientRect()
        ,endLocation = end.getBoundingClientRect()
        let svgNS = 'http://www.w3.org/2000/svg'
        if (startLocation.y === endLocation.y) {
          // 单行标注
          let oSvg = document.createElementNS(svgNS, 'rect')
          oSvg.setAttribute('x', startLocation.x - container.x + '')
          oSvg.setAttribute('y', startLocation.y - container.y + '')
          oSvg.setAttribute('width', endLocation.x + endLocation.width - startLocation.x - 4 + '')
          oSvg.setAttribute('height', startLocation.height)
          oSvg.setAttribute('fill-opacity', fillOpacity)
          oSvg.setAttribute('fill', fillColor)
          svgContainer.append(oSvg)
        } else {
          // 多行标注
          let line = (endLocation.y - startLocation.y) / 120
          line--
          for (line; line > 0; line--) {
            let aSvg = document.createElementNS(svgNS, 'rect')
            aSvg.setAttribute('x', '0')
            aSvg.setAttribute('y', startLocation.y + 96 * line - container.y + '')
            aSvg.setAttribute('width', container.width - 4 + '')
            aSvg.setAttribute('height', startLocation.height)
            aSvg.setAttribute('fill-opacity', fillOpacity)
            aSvg.setAttribute('fill', fillColor)
            svgContainer.append(aSvg)
          }
          // 第一行和最后一行高亮
          let sSvg = document.createElementNS(svgNS, 'rect')
          sSvg.setAttribute('x', startLocation.x - container.x + '')
          sSvg.setAttribute('y', startLocation.y - container.y + '')
          sSvg.setAttribute('width', container.x + container.width - startLocation.x - 4 + '')
          sSvg.setAttribute('height', startLocation.height)
          sSvg.setAttribute('fill-opacity', fillOpacity)
          sSvg.setAttribute('fill', fillColor)
          svgContainer.append(sSvg)
          let eSvg = document.createElementNS(svgNS, 'rect')
          eSvg.setAttribute('x', '0')
          eSvg.setAttribute('y', endLocation.y - container.y + '')
          eSvg.setAttribute('width', endLocation.x + endLocation.width - container.x - 4 + '')
          eSvg.setAttribute('height', startLocation.height)
          eSvg.setAttribute('fill-opacity', fillOpacity)
          eSvg.setAttribute('fill', fillColor)
          svgContainer.append(eSvg)
        }
      }
    } catch(e) {
      console.error('[error]高亮位置有误，请检查')
      emit('ifError')
    }
  }

  // @监听
  // 文本变化
  watch(
    () => props.text,
    () => {
      createDom()
      createHighLight()
    }
  )
  // 高亮数据
  watch(
    () => props.highLight,
    () => {
      createHighLight()
    },{deep: true}
  )
  // 窗口变化
  const vContainerWidthChange = {
    mounted(el: any, binding: any) {
      // 防抖处理
      function debounce(fn: any, delay = 16) {
        let t: any = null
        return function (this: any) {
          if (t) {
            clearTimeout(t)
          }
          const context = this
          const args = arguments
          t = setTimeout(() => {
            fn.apply(context, args)
          }, delay)
        }
      }
      el._resizer = new window.ResizeObserver(debounce(binding.value, Number(binding.arg) || 16))
      el._resizer.observe(el)
    },
    unmounted(el: any) {
      el._resizer.disconnect()
    },
  }
  const widthTemp = ref(null)
  const onResize = () => {
    let domContainer: any = document.getElementById('dom-container')
    if (widthTemp.value !== domContainer.clientWidth) {
      widthTemp.value ??= domContainer.clientWidth
      createDom()
      createHighLight()
    }
  }
  
</script>

<style scoped>
  #svg-container {
    width: 100%;
    position: absolute;
    top: 0;
    left: 0;
    display: block;
    pointer-events: none;
  }
  #text-container {
    position: absolute;
    top: 0px;
    user-select: none;
  }
  .container {
    white-space: pre-wrap;
    word-break: break-all;
    text-align: left;
    font-family: SimHei, SimSun, fangsong;
  }
</style>