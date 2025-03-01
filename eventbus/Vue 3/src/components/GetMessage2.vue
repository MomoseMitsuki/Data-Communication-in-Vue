<script setup>
    import { ref } from 'vue'
    import eventBus from '@/utils/eventbus';
    const message = ref('')
    const getMessage = msg => {
        message.value = msg
    }
    eventBus.on('sendMessage',getMessage)
</script>

<template>
    <div class="box">
        <h4>这里是GetMessage2组件</h4>
        <p>消息接收:{{ message }}</p>
    </div>
</template>

<style>

</style>