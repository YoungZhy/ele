<template>
  <div id="app">
    <v-header :seller="seller"></v-header>
    <div class="tab">
    	<div class="tab-item">
    		<router-link to="/goods">商品</router-link>
    	</div>
    	<div class="tab-item">
    		<router-link to="/ratings">评价</router-link>
    	</div>
    	<div class="tab-item">
    		<router-link to="/seller">商家</router-link>
    	</div>
    </div>
    <router-view></router-view>
    <div class="content">content</div>
  </div>
</template>

<script>

import header from './components/header/header.vue'

const ERR_OK = 0

export default {
	data(){
		return {
            seller: {}
        }
	},
	created(){
		this.$http.get('/api/seller').then((response) => {
            response = response.body
            if(response.errno === ERR_OK){
                this.seller = response.data
                console.log(this.seller)
            }
		})
	},
  components: {
    'v-header': header
  }
}

</script>

<style lang="less">

#app .tab {
    display: flex;
	width: 100%;
	height: 40px;
	line-height: 40px;
	border-bottom: 0.5px solid #E5E9F2;
    .tab-item {
        flex: 1;
        text-align: center;
        > a {
            display: block;
            color: #1F2D3D;
            font-size: 14px;
            &.active{
                color: #20A0FF;
            }
        }
    }
}
</style>
