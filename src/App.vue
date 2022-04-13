<template>
  <button @click="changeText"> 换个文本 </button>
  <div>
    位置：
    <button @click="changeErrorIndex" > 换个会报错的 </button>
    <button @click="changeRightIndex" > 换个不会报错的 </button>
    起始位置：<input :value="highLight[0].start" @input="startChange" />  结束位置：<input :value="highLight[0].end" @input="endChange"/>
  </div>
  <index :text="text" :highLight="highLight" v-on:ifError="errorAlert"/>
</template>

<script setup lang="ts">
  import { ref } from 'vue'
  import index from './components/index.vue'

  const text = ref('Sunfire666')
  const highLight = ref([
    {
      start: 0,
      end: 6
    }
  ])
  const changeText = () => {
    text.value = text.value + ' 啊对对对'
  }
  const changeErrorIndex = () => {
    highLight.value = [{
      start: -1,
      end: 2
    }]
  }
  const changeRightIndex = () => {
    highLight.value = [{
      start: 7,
      end: 9
    }]
  }
  const startChange = (e: any) => {
    highLight.value[0].start = e.target.value
  }
  const endChange = (e: any) => {
    highLight.value[0].end = e.target.value
  }
  const errorAlert = () => {
    alert('我错了')
  }

</script>

<style>
  #app {
    font-family: Avenir, Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: left;
    color: #2c3e50;
  }
  button {
    margin: 5px;
  }
</style>
