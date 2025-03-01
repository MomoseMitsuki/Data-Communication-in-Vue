<template>
    <div class="box">
        <h4>这里是GetMessage1组件</h4>
        <p>从eventbus获取:{{ msg }}</p>
    </div>
</template>

<script>
import Bus from '@/utils/eventBus'
export default {
    created(){
        Bus.$on('getMessage',msg => {
            console.log(`GetMessage1组件收到消息:${msg}`)
            this.msg = msg
        }) 
    },
    data(){
        return { msg:'' }
    }
}
</script>

<style></style>