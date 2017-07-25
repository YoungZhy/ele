<template>
    <div class="goods">
        <div class="menu-wrapper">
            <ul>
                <li v-for="(item, index) in goods" class="menu-item">
                    <span class="text">
                        <span v-show="item.type>0" class="icon" :class="classMap[item.type]"></span>{{item.name}}
                    </span>
                </li>
            </ul>
        </div>
        <div class="foods-wrapper">
            <ul>
                <li v-for="(item, index) in goods" class="food-list">
                    <h1 class="title">{{item.name}}</h1>
                    <ul>
                        <li v-for="(food, index) in item.foods" class="food-item">
                            <div class="icon">
                                <img :src="food.icon">
                            </div>
                            <div class="content">
                                <h2 class="name">{{food.name}}</h2>
                                <p class="desc">{{food.description}}</p>
                                <div class="extra">
                                    <span>月售{{food.sellCount}}份</span>
                                    <span>好评率{{food.rating}}%</span>
                                </div>
                                <div class="price">
                                    <span>￥{{food.price}}</span>
                                    <span v-show="food.oldPrice">￥{{food.oldPrice}}</span>
                                </div>
                            </div>
                        </li>
                    </ul>
                </li>
            </ul>
        </div>
    </div>
</template>

<script>
const ERR_OK = 0;
export default {
    props: {
        seller: {
            type: Object
        }
    },
    data(){
        return {
            goods: []
        }
    },
    created(){
        this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee']
        this.$http.get('/api/goods').then((response)=>{
            response = response.body
            if(response.errno === ERR_OK){
                this.goods = response.data
                console.log(this.goods)
            }
        })
    }
}
</script>

<style lang="less">
.goods{
    position: absolute;
    top: 174px;
    bottom: 46px;
    display: flex;
    overflow: hidden;
    width: 100%;
    .menu-wrapper{
        flex: 0 0 80px;
        width: 80px;
        background-color: #f3f5f7;
        .menu-item{
            display: table;
            width: 56px;
            height: 54px;
            line-height: 14px;
            padding: 0 12px;
            .icon{
                display: inline-block;
                width: 12px;
                height: 12px;
                margin-right: 2px;
                background-size: 12px 12px;
                background-repeat: no-repeat;
                vertical-align: top;
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
                display: table-cell;
                font-size: 12px;
                width: 56px;
                vertical-align: middle;
                border-bottom: 0.5px solid #E5E9F2;
            }
        }
    }
    .foods-wrapper{
        flex: 1;
        .title{
            padding-left: 14px;
            height: 26px;
            line-height: 26px;
            border-left: 2px solid #d9dde1;
            font-size: 12px;
            color: rgb(147,153,159);
            background-color: #f3f5f7;
        }
        .food-item{
            display: flex;
            margin: 18px;
            padding-bottom: 18px;
            border-bottom: 0.5px solid #E5E9F2;
            &:last-child{
                border-bottom: 0; 
                margin-bottom: 0;
            }
            .icon{
                flex: 0 0 57px;
                margin-right: 10px;
            }
            .content{
                flex: 1;
                .name{
                    margin: 2px 0 8px 0;
                    height: 14px;
                    line-height: 14px;
                    font-size: 14px;
                    color: rgb(7,17,27);
                }
            }
        }
    }
}
</style>
