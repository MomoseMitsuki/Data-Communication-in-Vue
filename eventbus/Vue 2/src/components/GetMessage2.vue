<template>
    <div class="box">
        <h4>这里是GetMessage2组件</h4>
        <p>从eventbus获取:{{ message }}</p>
    </div>
</template>

<script>
import Bus from '@/utils/eventBus'
export default {
    created(){
        Bus.$on('getMessage',msg => {
            console.log(`GetMessage2组件收到消息:${msg}`)
            this.message = msg
        }) 
    },
    data(){
        return { message:'' }
    }
}
</script>

<style></style>