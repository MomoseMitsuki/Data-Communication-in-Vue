<script setup>
    import { ref } from 'vue'
    import eventBus from '@/utils/eventbus';
    const str = ref('I get Message!')
    const sendMsg = () => {
       eventBus.emit('sendMessage',str)
    }
</script>

<template>
<div class="box">
    <h4>这里是SendMessage组件</h4>
    <button @click="sendMsg">发送消息</button>
</div>
</template>

<style>

</style>