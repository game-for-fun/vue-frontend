/*
 * @Author: paige.zhang 
 * @Date: 2019-05-26 21:29:31 
 * @Last Modified by: mikey.zhaopeng
 * @Last Modified time: 2019-05-29 06:59:08
 * 抽卡结果弹窗
 */


<template>

    <transition>
        <div id="turn-table-result" style="position:absolute;" v-show="visible">
            我是抽卡结果
            <button @click="iKnow" class="turn-table-btn">我知道了</button>
        </div>
        
    </transition>

</template>

<script>
export default {
    props:{
        visible:{
            type:Boolean,
            default:false
        }
    },
    data(){
        return {

        }
    },
    methods:{
        iKnow(){
            this.$emit('update:visible',false)
        }
    },

}
;
</script>

<style lang="scss">

    #turn-table-result{
        .turn-table-btn{
            width: 40px;
            height: 20px;
            border-radius: 5px;
            color: #fff;
            background-color: aqua;
        }
    }
</style>
