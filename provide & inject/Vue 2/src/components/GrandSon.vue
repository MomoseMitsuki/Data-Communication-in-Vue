<template>
    <div class="box">
        <h4>这里是GrandSon组件</h4>
        <p>从App组件获取的数据:{{ msg }}</p>
    </div>
</template>

<script>
export default {
    inject:['msg'],
}
</script>