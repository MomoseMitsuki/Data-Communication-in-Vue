<template>
  <div>
    <div class="box">
      <p>这里是父组件</p>
      <div>从子组件中得到了数据为:{{ msg ? msg : '' }}</div>
    </div>
  <TheSon @getCount="handleCount" :count="msg"></TheSon>
  </div>
</template>

<script>
import TheSon from './components/TheSon.vue';
  export default {
    components:{
      TheSon,
    },
    data(){
      return { msg:10 }
    },
    methods:{
      handleCount(val){
        console.log(`从父组件中得到了数据为:${val}`)
        this.msg = val
      }
    }
  }
</script>