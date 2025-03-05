<template>
  <div>
    <div class="box">
      <h4>这里是App组件</h4>
      <input type="text" v-model="msg">
    </div>
    <Son></Son>
  </div>
</template>

<script>
  import Son from '@/components/Son.vue'
  import { computed } from 'vue';
  export default {
    data(){
      return {
        msg:''
      }
    },
    provide(){
      return {
        msg:computed(() => this.msg)
      }
    },
    components:{
      Son
    }
  }
</script>

<style>
.box {
  width: 300px;
  height: 300px;
  padding: 20px;
  margin: 20px;
  border:2px solid #000;
  border-radius:20px;
}
</style>