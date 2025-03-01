<template>
  <div>
    <GetMessage1></GetMessage1>
    <SendMessage></SendMessage>
    <GetMessage2></GetMessage2>
  </div>
</template>

<script>
import GetMessage1 from './components/GetMessage1.vue';
import GetMessage2 from './components/GetMessage2.vue';
import SendMessage from './components/SendMessage.vue';
export default {
  components:{
    GetMessage1,GetMessage2,SendMessage
  }
}
</script>

<style>

</style>