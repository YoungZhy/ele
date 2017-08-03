<template>
    <div class="seller" ref="seller">
        <div class="seller-wrapper" ref="sellerWrapper">
            <div class="seller-content">
                <div class="overview">
                    <h1 class="title">{{seller.name}}</h1>
                    <div class="desc">
                        <star :size="36" :score="seller.score"></star>
                        <span class="text">({{seller.ratingCount}})</span>
                        <span class="text">月售{{seller.sellCount}}单</span>
                    </div>
                    <ul class="remark">
                        <li class="block">
                            <h2>起送价</h2>
                            <div class="content">
                                <span class="stress">{{seller.minPrice}}</span>元
                            </div>
                        </li>
                        <li class="block">
                            <h2>商家配送</h2>
                            <div class="content">
                                <span class="stress">{{seller.deliveryPrice}}</span>元
                            </div>
                        </li>
                        <li class="block">
                            <h2>平均配送时间</h2>
                            <div class="content">
                                <span class="stress">{{seller.deliveryTime}}</span>分钟
                            </div>
                        </li>
                    </ul>
                    <div class="favorite" @click="toggleFavorite">
                        <i class="iconfont icon-likefill" :class="{'active': favor}"></i>
                        <span class="text">{{favorText}}</span>
                    </div>
                </div>
                <split></split>
                <div class="bulletin">
                    <h1 class="title">公告与活动</h1>
                    <div class="content-wrapper">
                        <p class="content">{{seller.bulletin}}</p>
                    </div>
                    <ul v-if="seller.supports" class="supports">
                        <li class="support-item" v-for="(item, index) in seller.supports" :key="index">
                            <span class="icon" :class="classMap[seller.supports[index].type]"></span>
                            <span class="text">{{seller.supports[index].description}}</span>
                        </li>
                    </ul>
                </div>
                <split></split>
                <div class="pics">
                    <h1 class="title">商家实景</h1>
                    <div class="pic-wrapper" ref="picWrapper">
                        <ul class="pic-list" ref="picList">
                            <li class="pic-item" v-for="(pic, index) in seller.pics" :key="index" @click="show(index)">
                                <img :src="pic" width="120" height="90">
                            </li>
                        </ul>
                    </div>
                </div>
                <split></split>
                <div class="info">
                    <h1 class="title">商家信息</h1>
                    <ul>
                        <li class="info-item" v-for="(info, index) in seller.infos" :key="index">{{info}}</li>
                    </ul>
                </div>
                
            </div>
        </div>
        <transition name="picScale">
            <div class="pic-detail" v-show="picShow" @click="hide">
                <div class="swiper-container"  ref="swiper">
                    <div class="swiper-wrapper">
                        <div class="swiper-slide" v-for="(pic, index) in seller.pics" :key="index">
                            <img :src="pic" width="80%">
                        </div>
                    </div>
                    <div class="swiper-pagination" ref="swiperPagination"></div>
                </div>
            </div>
        </transition>
    </div>
</template>

<script>

import Vue from 'vue'
import BScroll from 'better-scroll'
import star from 'components/star/star.vue'
import split from 'components/split/split.vue'
import {saveToLocal} from 'common/js/store.js'
import {loadFromLocal} from 'common/js/store.js'

import Swiper from 'Swiper'

export default {
    props: {
        seller: {
            type: Object
        }
    },
    data(){
        return {
            favor: (() => {
                return loadFromLocal(this.seller.id, 'favor', false)
            })(),
            picShow: false
        }
    },
    created(){
        this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee']
        
    },
    watch: {
        'seller'(){
            this.$nextTick(() => {
                this._initScroll()
                this._initPics()
            })
        }
    },
    mounted(){
        this.$nextTick(() => {
            this._initScroll()
            this._initPics()
            this._initSwiper()
        })
    },
    methods: {
        _initSwiper(){
            if(!this.swiper){
                this.swiper = new Swiper(this.$refs.swiper, {
                    direction: 'horizontal',
                    pagination: '.swiper-pagination',
                    observer: true,  //修改swiper自己或子元素时，自动初始化swiper
                    observeParents: true  //修改swiper的父元素时，自动初始化swiper
                })
            }else{
                this.Swiper.update()
            }
        },
        _initScroll(){
            if(!this.scroll){
                this.scroll = new BScroll(this.$refs.sellerWrapper, {
                    click: true
                })
            }else{
                this.scroll.refresh()
            }
        },
        _initPics(){
            if(this.seller.pics){
                let picWidth = 120
                let margin = 6
                let width = (picWidth + margin) * this.seller.pics.length
                this.$refs.picList.style.width = width + 'px'
                this.$nextTick(() => {
                    if(!this.picScroll){
                        this.picScroll = new BScroll(this.$refs.picWrapper, {
                            click: true,
                            scrollX: true
                        })
                    }else{
                        this.picScroll.refresh()
                    }
                })

            }
        },
        show(index){
            this.picShow = true
            this.swiper.slideTo(index)
        },
        hide(){
            this.picShow = false
        },
        toggleFavorite(event){
            if(!event._constructed){
                return
            }
            this.favor = !this.favor
            saveToLocal(this.seller.id, 'favor', this.favor)
        }
    },
    computed: {
        favorText(){
            return this.favor ? '已收藏' : '收藏'
        }
    },
    components: {
        star,
        split
    }
}
</script>

<style lang="less">
.seller,.seller-wrapper{
    position: absolute;
    top: 174px;
    bottom: 0;
    left: 0;
    width: 100%;
    overflow: hidden;
    .seller-wrapper{top: 0;}
    .seller-content{
        .overview{
            position: relative;
            padding: 18px;
            .title{
                margin-bottom: 8px;
                line-height: 14px;
                color: rgb(7, 17, 27);
                font-size: 14px;
            }
            .desc{
                padding-bottom: 18px;
                border-bottom: 0.5px solid rgba(7, 17, 27, 0.1);
                font-size: 0;
                .star{
                    display: inline-block;
                    margin-right: 8px;
                    vertical-align: top;
                }
                .text{
                    display: inline-block;
                    margin-right: 12px;
                    line-height: 18px;
                    vertical-align: top;
                    font-size: 10px;
                    color: rgb(77, 85, 93);
                }
            }
            .remark{
                display: flex;
                padding-top: 18px;
                .block{
                    flex: 1;
                    text-align: center;
                    border-right: 1px solid rgba(7, 17, 27, 0.1);
                    &:last-child{
                        border: none;
                    }
                    h2{
                        margin-bottom: 4px;
                        line-height: 10px;
                        font-size: 10px;
                        color: rgb(147, 153, 159);
                    }
                    .content{
                        line-height: 24px;
                        font-size: 10px;
                        color: rgb(7, 17, 27);
                        .stress{
                            font-size: 24px;
                        }
                    }
                }
            }
            .favorite{
                position: absolute;
                right: 12px;
                top: 18px;
                width: 36px;
                text-align: center;               
                .icon-likefill{
                    display: block;
                    margin-bottom: 4px;
                    line-height: 24px;
                    font-size: 24px;
                    color: #d4d6d9;
                    &.active{
                        color: rgb(240, 20, 20);
                    }
                }
                .text{
                    line-height: 10px;
                    font-size: 10px;
                    color: rgb(77, 85, 93);
                }
            }
        }
        .bulletin{
            padding: 18px 18px 0 18px;
            .title{
                margin-bottom: 8px;
                line-height: 14px;
                color: rgb(7, 17, 27);
                font-size: 14px;
            }
            .content-wrapper{
                padding: 0 12px 16px 12px;
                border-bottom: 0.5px solid rgba(7, 17, 27, 0.1);
                .content{
                    line-height: 24px;
                    font-size: 12px;
                    color: rgb(240, 20, 20);
                }
            }
            .supports{
                .support-item{
                    padding: 16px 12px;
                    border-bottom: 0.5px solid rgba(7, 17, 27, 0.1);
                    font-size: 0;
                    &:last-child{
                        border: none;
                    }
                    .icon{
                        display: inline-block;
                        width: 16px;
                        height: 16px;
                        vertical-align: top;
                        margin-right: 6px;
                        background-size: 16px 16px;
                        background-repeat: no-repeat;
                        &.decrease{
                            background-image: url(decrease.png);
                        }
                        &.discount{
                            background-image: url(discount.png);
                        }
                        &.special{
                            background-image: url(special.png);
                        }
                        &.invoice{
                            background-image: url(invoice.png);
                        }
                        &.guarantee{
                            background-image: url(guarantee.png);
                        }
                    }
                    .text{
                        line-height: 16px;
                        font-size: 12px;
                        color: rgb(7, 17, 27);
                    }
                }
            }
        }
        .pics{
            padding: 18px;
            .title {
                margin-bottom: 12px;
                line-height: 14px;
                color: rgb(7, 17, 27);
                font-size: 14px;
            }
            .pic-wrapper{
                width: 100%;
                overflow: hidden;
                white-space: nowrap;
                .pic-list{
                    font-size: 0;
                    .pic-item{
                        display: inline-block;
                        margin-right: 6px;
                        width: 120px;
                        height: 90px;
                        &:last-child{
                            margin: 0;
                        }
                    }
                }
            }
        }
        .info{
            padding: 18px 18px 0 18px;
            color: rgb(7, 17, 27);
            .title{
                padding-bottom: 12px;
                line-height: 14px;
                border-bottom: 1px solid rgba(7, 17, 27, 0.1);
                font-size: 14px;
            }
            .info-item{
                padding: 16px 12px;
                line-height: 16px;
                border-bottom: 1px solid rgba(7, 17, 27, 0.1);
                font-size: 12px;
                &:last-child{
                    border: none;
                }
            }
        }
    }
    .pic-detail{
        position: fixed;
        top: 0;
        left: 0;
        width: 100%;
        height: 100%;
        display: flex;
        overflow: auto;
        -webkit-backdrop-filter: blur(10px);
        background: rgba(7, 17, 27, 0.8);
        justify-content: center;
        align-items: center;
        z-index: 5;
        transform: scale(1);
        &.picScale-enter-active, &.picScale-leave-active{
            transition: all 0.3s ease;
        }
        &.picScale-enter{
            transform: scale(0.5);
            opacity: 0;
        }
        &.picScale-leave-to{
            opacity: 0;
        }
        .swiper-container {
            width: 90%;
            height: 80%;
            text-align: center;
            .swiper-slide{
                display: flex;
                justify-content: center;
                align-items: center;
            }
            .swiper-pagination{
                .swiper-pagination-bullet{
                    background-color: #fff;
                    opacity: 0.5;
                    &.swiper-pagination-bullet-active{
                        opacity: 1;
                        background-color: rgb(0,160,220);
                    }
                }
            }
        }
    }
}
</style>
