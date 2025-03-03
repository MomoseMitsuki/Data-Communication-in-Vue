<script setup>
  import { ref } from 'vue'
  import Son from '@/components/Son.vue'
  const message = ref('')
  const SonComponent = ref(null)
  const sonSayHi = () => {
    console.log(`从子组件中获得暴露的属性${SonComponent.value.count}`)
    SonComponent.value.sayHi()
  }
  const changeMsg = (msg) => {
    console.log('在父组件中获得了msg')
    message.value = msg
  }
</script>

<template>
  <div class="box">
    <h4>这里是App.vue父组件</h4>
    <p></p>
    <button @click="sonSayHi">通过defineExpose获取子组件属性</button>
  </div>
  <Son ref="SonComponent" :message="message" @send-change="changeMsg"></Son>
</template>

<style>
  .box {
    width: 350px;
    height: 350px;
    padding: 20px;
    margin: 20px;
    border:2px solid #000;
    border-radius:20px;
  }
</style>