<script setup>
  import { ref,defineExpose,defineEmits,defineProps } from 'vue'
  const count = ref(10)
  const sayHi = () => {
    console.log('Hello, I am from The Son Component by defineExpose to you')
  }
  const props = defineProps({
    message:{
      type:String,
      required:true
    }
  })
  const emits = defineEmits(['sendChange'])
  const sendChange = () => {
    emits('sendChange','Hello, I am from the Father Component by defineProps to come')
  }
  defineExpose({
    count,sayHi
  })
</script>

<template>
  <div class="box">
    <h4>这里是Son.vue子组件</h4>
    <button @click="count--">-</button><span> count = {{ count }} </span><button @click="count++">+</button>
    <button @click="sendChange">在子组件触发自定义事件发送数据给父组件</button>
    <p>从父组件通过属性接收的消息: {{ message }}</p>
  </div>
</template>