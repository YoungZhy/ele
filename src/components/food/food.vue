<template>
    <transition name="move">
        <div class="food" v-show="showFlag">
            <i class="iconfont icon-back" @click="hide"></i>
        </div>
    </transition>
</template>

<script>
export default {
    props: {
        food: {
            type: Object
        }
    },
    data(){
        return {
            showFlag: false
        }
    },
    methods: {
        show(){
            this.showFlag = true
        },
        hide(){
            this.showFlag = false
        }
    }
}
</script>
<style lang="less">
.food{
    position: fixed;
    top: 0;
    bottom: 48px;
    left: 0;
    width: 100%;
    background-color: red;
    transform: translateX(0);
    &.move-enter-active, &.move-leave-active{
        transition: all .5s ease;
    }
    &.move-enter, &.move-leave-to{
        transform: translateX(100%);
    }
    .icon-back{
        display: inline-block;
    }
}
</style>
