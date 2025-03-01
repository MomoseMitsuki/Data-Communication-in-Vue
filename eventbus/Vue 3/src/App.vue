<script setup>
import GetMessage1 from './components/GetMessage1.vue';
import GetMessage2 from './components/GetMessage2.vue';
import SendMessage from './components/SendMessage.vue';
</script>

<template>
  <div>
    <GetMessage1></GetMessage1>
    <SendMessage></SendMessage>
    <GetMessage2></GetMessage2>
  </div>
</template>

<style>
.box {
    display: inline-block;
    width: 300px;
    height: 300px;
    padding: 10px;
    margin-left:20px;
    border:1px solid #000;
}

</style>