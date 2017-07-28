<template>
    <div class="shopcart">
        <div class="content" @click="toggleList">
            <div class="content-left">
                <div class="logo-wrapper">
                    <div class="logo" :class="{'highlight': totalCount > 0}">
                        <i class="iconfont icon-gouwuchefill" :class="{'highlight': totalCount > 0}"></i>
                    </div>
                    <div class="num" v-show="totalCount > 0">{{totalCount}}</div>
                </div>
                <div class="price" :class="{'highlight': totalPrice > 0}">￥{{totalPrice}}</div>
                <div class="desc">另需配送费￥{{deliveryPrice}}元</div>
            </div>
            <div class="content-right">
                <div class="pay" :class="payClass" @click.stop.prevent="pay">
                    {{payDesc}}
                </div>
            </div>
        </div>
        <div class="ball-container">
            <div v-for="(ball, index) in balls" :key="index">
                <transition name="drop" @before-enter="beforeEnter" @enter="enter" @after-enter="afterEnter">
                    <div class="ball" v-show="ball.show" >
                        <div class="inner inner-hook"></div>
                    </div>
                </transition>
            </div>
        </div>
        <transition name="fold">
            <div class="shopcart-list" v-show="listShow">
                <div class="list-header">
                    <h1 class="title">购物车</h1>
                    <span class="empty" @click="empty">清空</span>
                </div>
                <div class="list-content" ref="listContent">
                    <ul>
                        <li class="food" v-for="(food, index) in selectFoods" :key="index">
                            <span class="name">{{food.name}}</span>
                            <div class="price">
                                <span>￥{{food.price * food.count}}</span>
                            </div>
                            <div class="cartcontrol-wrapper">
                                <cartcontrol :food="food"></cartcontrol>
                            </div>
                        </li>
                    </ul>
                </div>
            </div>
        </transition>
        <transition name="fade">
            <div class="list-mask" v-show="listShow" @click.stop.prevent="hideList"></div>
        </transition>
    </div>
</template>
<script>

import cartcontrol from "../cartcontrol/cartcontrol.vue"
import BScroll from 'better-scroll'

export default {
    props: {
        deliveryPrice: {
            type: Number,
            default: 0
        },
        minPrice: {
            type: Number,
            default: 0
        },
        selectFoods: {
            type: Array,
            default(){
                return []
            }
        }
    },
    data(){
        return {
            balls: [
          {
            show: false
          },
          {
            show: false
          },
          {
            show: false
          },
          {
            show: false
          },
          {
            show: false
          }
        ],
            dropBalls: [],
            fold: true
        }
    },
    methods: {
        drop(el){
            for(let i=0; i<this.balls.length; i++){
                let ball = this.balls[i]
                if(!ball.show){
                    ball.show = true
                    ball.el = el
                    this.dropBalls.push(ball)
                    return
                }
            }
        },
        addFood(target){
            this.drop(target)
            console.log('addFood')
        },
        beforeEnter(el){
            let count = this.balls.length
            while(count--){
                let ball = this.balls[count]
                if(ball.show){
                    let rect = ball.el.getBoundingClientRect()
                    let x = rect.left - 32
                    let y = -(window.innerHeight - rect.top - 22)
                    el.style.display = ''
                    el.style.webkitTransform = `translate3d(0, ${y}px, 0)`
                    el.style.transform = `translate3d(0, ${y}px, 0)`
                    let inner = el.getElementsByClassName('inner-hook')[0]
                    inner.style.webkitTransform = `translate3d(${x}px, 0, 0)`
                    inner.style.transform = `translate3d(${x}px, 0, 0)`
                }
            }
        },
        enter(el, done){
            /* eslint-disable no-unused-vars */
            let rf = el.offsetHeight
            this.$nextTick(() => {
                el.style.webkitTransform = 'translate3d(0, 0, 0)'
                el.style.transform = 'translate3d(0, 0, 0)'
                let inner = el.getElementsByClassName('inner-hook')[0]
                inner.style.webkitTransform = 'translate3d(0, 0, 0)'
                inner.style.transform = 'translate3d(0, 0, 0)'
                el.addEventListener('transitionend', done)        
            })
            
        },
        afterEnter(el){
            let ball = this.dropBalls.shift()
            if(ball){
                ball.show = false
                el.style.display = 'none'
            }
        },
        toggleList(){
            if(!this.totalCount){
                return
            }
            this.fold = !this.fold
        },
        pay(){
            if(this.totalPrice < this.minPrice){
                return
            }
            window.alert(`请支付${this.totalPrice}元`)
        },
        hideList(){
            this.fold = true
        },
        empty(){
            this.selectFoods.forEach((food) => {
                food.count = 0
            })
        }
    },
    computed: {
        totalPrice(){
            let total = 0
            this.selectFoods.forEach((food) => {
                total += food.price * food.count
            })
            return total
        },
        totalCount(){
            let count = 0
            this.selectFoods.forEach((food) => {
                count += food.count
            })
            return count
        },
        payDesc(){
            if(this.totalPrice === 0){
                return `￥${this.minPrice}元起送`
            }else if(this.totalPrice < this.minPrice){
                let diff = this.minPrice - this.totalPrice
                return `还差￥${diff}元起送`
            }else{
                return '去结算'
            }
        },
        payClass(){
            if(this.totalPrice < this.minPrice){
                return 'not-enough'
            }else{
                return 'enough'
            }
        },
        listShow(){
            if(!this.totalCount){
                this.fold = true
                return false
            }
            let show = !this.fold
            if(show){
                this.$nextTick(() => {
                    if(!this.scroll){
                        this.scroll = new BScroll(this.$refs.listContent, {
                            click: true
                        })
                    }else{
                        this.scroll.refresh()
                    }
                })
            }
            return show
        }
    },
    components: {
        cartcontrol
    }
}
</script>
<style lang="less">
.shopcart{
    position: fixed;
    left: 0;
    bottom: 0;
    z-index: 20;
    width: 100%;
    height: 48px;
    background: #000;
    .content{
        display: flex;
        background-color: #141d27;
        font-size: 0;
        color: rgba(255, 255, 255, 0.4);
        .content-left{
            flex: 1;
            .logo-wrapper{
                display: inline-block;
                position: relative;
                top: -10px;
                width: 56px;
                height: 56px;
                margin: 0 12px;
                padding: 6px;
                box-sizing: border-box;
                vertical-align: top;
                border-radius: 50%;
                background-color: #141d27;
                .logo{
                    width: 100%;
                    height: 100%;
                    border-radius: 50%;
                    background-color: rgb(42, 52, 60);
                    text-indent: 9px;
                    &.highlight{
                        background-color: rgb(0, 160, 220);
                    }
                    .icon-gouwuchefill{
                        font-size: 24px;
                        line-height: 44px;
                        color: #80858a;
                        &.highlight{
                            color: #fff;
                        }
                    }                  
                }
                .num{
                    position: absolute;
                    top: 0;
                    right: 0;
                    width: 24px;
                    height: 16px;
                    line-height: 16px;
                    text-align: center;
                    border-radius: 16px;
                    font-size: 9px;
                    font-weight: 700;
                    color: #fff;
                    background-color: rgb(240, 20, 20);
                    box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.4);
                }
            }
            .price{
                display: inline-block;
                font-size: 16px;
                font-weight: 700;
                vertical-align: top;
                margin-top: 12px;
                line-height: 24px;
                box-sizing: border-box;
                padding-right: 12px;
                border-right: 1px solid rgba(255, 255, 255, 0.1);
                &.highlight{
                    color: #fff;
                }
            }
            .desc{
                display: inline-block;
                font-size: 10px;
                font-weight: 700;
                
                vertical-align: top;
                margin: 12px 0 0 12px;
                line-height: 24px;
            }
        }
        .content-right{
            flex: 0 0 105px;
            width: 105px;
            .pay{
                height: 48px;
                line-height: 48px;
                font-size: 12px;
                text-align: center;
                font-weight: 700;
                &.not-enough{
                    background-color: #2b333b;
                }
                &.enough{
                    background-color: #00b43c;
                    color: #fff;
                }
            }
        }
    }
    .ball-container{
        .ball{
            position: fixed;
            left: 32px;
            bottom: 22px;
            z-index: 200;
            transition: all 0.4s cubic-bezier(0.49, -0.29, 0.75, 0.41);
            .inner{
                width: 16px;
                height: 16px;
                border-radius: 50%;
                background-color: rgb(0, 160, 220);
                transition: all .4s;
            }
        }
    }
    .shopcart-list{
        position: absolute;
        top: 0;
        left: 0;
        width: 100%;
        z-index: -1;
        
        transform: translateY(-100%);
        &.fold-enter-active, &.fold-leave-active{
            transition: all .5s ease;
        }
        &.fold-enter, &.fold-leave-to{
            transform: translateY(0);
        }
        .list-header{
            height: 40px;
            line-height: 40px;
            padding: 0 18px;
            background-color: #f3f5f7;
            border-bottom: 1px solid rgba(7, 17, 27, 0.1);
            .title{
                float: left;
                font-size: 14px;
                color: rgb(7, 17, 27);
            }
            .empty{
                float: right;
                font-size: 12px;
                color: rgb(0, 120, 200);
            }
        }
        .list-content{
            padding: 0 18px;
            max-height: 217px;
            overflow: hidden;
            background-color: #fff;
            .food{
                position: relative;
                padding: 12px 0;
                box-sizing: border-box;
                border-bottom: 0.5px solid rgba(7, 17, 27, 0.1);
                .name{
                    line-height: 24px;
                    font-size: 14px;
                    color: rgb(7, 17, 27);
                }
                .price{
                    position: absolute;
                    right: 90px;
                    bottom: 12px;
                    line-height: 24px;
                    font-size: 14px;
                    font-weight: 700;
                    color: rgb(240, 20, 20);
                }
                .cartcontrol-wrapper{
                    position: absolute;
                    right: 0;
                    bottom: 6px;
                }
            }
        }
    }
    .list-mask{
        position: fixed;
        top: 0;
        left: 0;
        height: 100%;
        width: 100%;
        opacity: 1;
        z-index: -2;
        backdrop-filter: blur(10px);
        background-color: rgba(7, 17, 27, 0.6);
        &.fade-enter-active, &.fade-leave-active{
            transition: all .5s;
        }
        &.fade-enter, &.fade-leave-to{
            opacity: 0;
            background-color: rgba(7, 17, 27, 0);
        }
    }
}
</style>


