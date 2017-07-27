<template>
    <div class="cartcontrol">
        <transition name="move">
            <div class="cart-decrease " v-show="food.count > 0" @click="decreaseCart($event)">
                <i class="iconfont icon-jian-copy inner"></i>
            </div>
        </transition>
         <div class="cart-count" v-show="food.count > 0">{{food.count}}</div>  
        <div class="cart-add" @click="addCart($event)">
            <i class="iconfont icon-roundaddfill"></i>
        </div>
    </div>
</template>
<script>
import Vue from 'vue'

export default {
    props: {
        food:{
            type: Object
        }
    },
    created(){

    },
    methods: {
        addCart(event){
            if(!event._constructed){
                return
            }
            if(!this.food.count){
                Vue.set(this.food, 'count', 1)
                // this.food.count = 1
            }else{
                this.food.count++
            }
            this.$emit('add', event.target)

        },
        decreaseCart(event){
            if(!event._constructed){
                return
            }
            if(this.food.count){
                this.food.count--
            }
        }
    }
}
</script>
<style lang="less">
.cartcontrol{
    font-size: 0;
    .cart-decrease{
        display: inline-block;
        padding: 6px;
        opacity: 1;
        transform: translateX(0);
        .inner{
            display: inline-block;
            font-size: 22px;
            line-height: 22px;
            color: rgb(0, 160, 220);
            transition: all .4s ease;
            transform: scale(1);
        }
        &.move-enter-active, &.move-leave-active{
            transition: all .4s ease;
        }
        &.move-enter, &.move-leave-active{
            opacity: 0;
            transform: translateX(24px);
            .inner{
                transform: scale(0);
            }
        }
    }
    .cart-count{
        display: inline-block;
        vertical-align: top;
        width: 12px;
        padding-top: 6px;
        line-height: 22px;
        text-align: center;
        font-size: 10px;
        color: rgb(147, 153, 159);
    }
    .cart-add{
        display: inline-block;
        padding: 6px;
        .icon-roundaddfill{
            display: inline-block;
            font-size: 22px;
            line-height: 22px;
            color: rgb(0, 160, 220);
        }
    }
}
</style>

