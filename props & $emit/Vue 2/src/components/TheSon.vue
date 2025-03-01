<template>
    <div class="box">
        <p>这里是子组件</p>
        <div>
            <button @click="subCount">-</button>{{ count }}<button @click="addCount">+</button>
        </div>
    </div>
</template>

<script>
export default {
    props:{
        count:{
            type:Number,
            default:10,
            required:true,
            validator(value){
                console.log(`从子组件中得到了数据为:${value}`)
                return value < 0 ? false : true
            }
        }
    },
    methods:{
        addCount(){
            this.$emit('getCount',this.count + 1)
        },
        subCount(){
            this.$emit('getCount',this.count - 1)
        }
    }
}
</script>

<style>
.box {
    width: 300px;
    height: 300px;
    border:1px solid #000;
    padding:10px;
    margin:20px;
}
</style>