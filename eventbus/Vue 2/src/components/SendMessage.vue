<template>
    <div class="box">
        <h4>这里是SendMessage组件</h4>
        <button @click="sendMsg">发送信息</button>
    </div>
</template>

<script>
import Bus from '@/utils/eventBus'
export default {
    data(){
        return { str:'I get message!' }
    },
    methods:{
        sendMsg:function(){
            console.log(`SendMessage组件发出了消息,内容是:${this.str}`)
            Bus.$emit('getMessage',this.str)
        }
    }
}
</script>

<style>
.box {
    display:inline-block;
    width: 300px;
    height: 300px;
    border:1px solid #000;
    padding:10px;
    box-sizing: border-box;
}
</style>