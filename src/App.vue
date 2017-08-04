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
    <keep-alive>
      <router-view :seller="seller"></router-view>
    </keep-alive>
  </div>
</template>

<script>
import header from 'components/header/header.vue'
import { urlParse } from 'common/js/util.js'

const ERR_OK = 0

export default {
  data() {
    return {
      seller: {
        id: (() => {
          let queryParam = urlParse()
          return queryParam.id
        })()
      }
    }
  },
  created() {
    // this.$http.get('/api/seller?id=' + this.seller.id).then((response) => {
    //   console.log(response);
    //   console.log(response.body);
    //   response = response.body;
    //   if (response.errno === ERR_OK) {
    //     this.seller = Object.assign({}, this.seller, response.data);
    //   }
    // })
    this.$http.get('/static/data.json').then((response) => {
      this.seller = response.body.seller
    })
  },
  components: {
    'v-header': header
  }
}
</script>

<style lang="less">
@import 'common/less/base.less';
@import 'common/fonts/iconfont.css';
@import 'common/less/swiper-3.4.2.min.css';
@import '../static/css/reset.css';

#app {
  .tab {
    display: flex;
    width: 100%;
    height: 40px;
    line-height: 40px;
    border-bottom: 0.5px solid #E5E9F2;
    .tab-item {
      flex: 1;
      text-align: center;
      >a {
        display: block;
        color: #1F2D3D;
        font-size: 14px;
        &.active {
          color: #20A0FF;
        }
      }
    }
  }
}
</style>
